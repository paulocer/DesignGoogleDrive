# Plano de Iterações ADD para Google Drive

## Contexto do Sistema

**Propósito do Design:** Criar uma arquitetura escalável para um sistema de armazenamento e sincronização de ficheiros na cloud, suportando 10M DAU com 500 PB de armazenamento total.

**Drivers Arquiteturais Identificados:**
- **Funcionais:** Upload/download de ficheiros, sincronização multi-dispositivo, versionamento, partilha, notificações
- **Atributos de Qualidade:** Fiabilidade (zero perda de dados), performance (sincronização rápida), escalabilidade, alta disponibilidade
- **Restrições:** Limite de 10 GB por ficheiro, encriptação obrigatória, suporte web e mobile
- **Preocupações:** Utilização eficiente de largura de banda, gestão de conflitos de sincronização

---

## Iteração 1: Estrutura Base e Separação de Responsabilidades

### Objetivo
Estabelecer a estrutura de alto nível do sistema, definindo os principais subsistemas e suas responsabilidades fundamentais.

### Drivers Selecionados
- **RF-01:** Upload e download de ficheiros
- **QA-01:** Escalabilidade (10M DAU, 480 peak QPS)
- **QA-02:** Modificabilidade (suporte para web e mobile)
- **Restrição:** Encriptação obrigatória dos ficheiros

### Elementos a Refinar
Sistema completo (contexto inicial)

### Conceitos de Design Selecionados
- **Padrão:** Arquitetura em camadas (Layered Architecture)
- **Padrão:** Separação Cliente-Servidor
- **Tática de Escalabilidade:** Distribuição de recursos

### Instanciação

**Estrutura Modular:**
```
- Camada de Apresentação (Client Layer)
  - Web Application
  - Mobile Application
  
- Camada de Serviços (Service Layer)
  - API Servers (RESTful services)
  - Load Balancer
  
- Camada de Processamento (Processing Layer)
  - Block Servers (chunking, compression, encryption)
  
- Camada de Dados (Data Layer)
  - Metadata Database
  - Cloud Storage (S3)
```

**Responsabilidades:**
| Elemento | Responsabilidades |
|----------|-------------------|
| Client Layer | Interface com utilizador, gestão de sessão local, detecção de alterações em ficheiros |
| API Servers | Autenticação, gestão de metadados, orquestração de operações, validação de requisitos |
| Block Servers | Divisão de ficheiros em blocos, compressão, encriptação, upload para storage |
| Metadata DB | Persistência de informação sobre utilizadores, ficheiros, versões e blocos |
| Cloud Storage | Armazenamento físico dos blocos de ficheiros |

**Interfaces Identificadas:**
- Cliente → API Server: HTTPS REST API
- API Server → Metadata DB: SQL queries
- Block Server → Cloud Storage: S3 API
- Cliente → Block Server: Binary upload/download protocol

### Decisões de Design
1. **Separação entre metadados e conteúdo:** Metadados em BD relacional, conteúdo em object storage para otimizar custo e performance
2. **Load balancer:** Para distribuir carga entre API servers e permitir escalabilidade horizontal
3. **Block servers dedicados:** Isolar lógica de processamento de ficheiros (chunking, compression, encryption) para reutilização entre plataformas

**Rationale:** A arquitetura em camadas facilita modificabilidade e manutenção. A separação cliente-servidor permite escalabilidade independente.

---

## Iteração 2: Gestão de Metadados e Consistência

### Objetivo
Definir o modelo de dados para metadados e garantir consistência forte no sistema.

### Drivers Selecionados
- **RF-03:** Versionamento de ficheiros
- **RF-04:** Partilha de ficheiros
- **QA-03:** Fiabilidade (zero perda de dados)
- **QA-04:** Consistência forte (ficheiro não pode aparecer diferente em clientes simultâneos)

### Elementos a Refinar
Metadata Database, API Servers

### Conceitos de Design Selecionados
- **Padrão:** Database sharding
- **Padrão:** Master-Slave replication
- **Tática de Disponibilidade:** Redundância ativa
- **Tática de Consistência:** Write-through cache invalidation

### Instanciação

**Schema de Base de Dados:**
```sql
User (user_id, email, name, storage_quota)
Device (device_id, user_id, push_id, last_sync)
Workspace (workspace_id, user_id, root_path)
File (file_id, workspace_id, name, path, size, current_version)
File_Version (version_id, file_id, timestamp, block_list, size)
Block (block_id, hash, size, storage_path, encrypted)
```

**Estrutura C&C:**
```
[API Server] → [Metadata Cache] → [Master DB]
                                      ↓
                                  [Slave DB 1]
                                  [Slave DB 2]
```

**Responsabilidades Alocadas:**
- **Metadata Cache:** Cache de leituras frequentes, invalidação em writes
- **Master DB:** Todas as operações de escrita
- **Slave DBs:** Operações de leitura, backup

### Decisões de Design
1. **BD Relacional com ACID:** Garantir consistência transacional entre User, File, Version e Block
2. **Sharding por user_id:** Distribuir carga e permitir escalabilidade horizontal
3. **Cache invalidation em writes:** Garantir que cache e DB têm sempre o mesmo valor
4. **File_Version como append-only:** Rows existentes são read-only para manter integridade do histórico

**Rationale:** ACID properties são essenciais para garantir consistência. Sharding permite escalar horizontalmente. Master-slave garante alta disponibilidade.

---

## Iteração 3: Otimização de Sincronização e Largura de Banda

### Objetivo
Implementar mecanismos para minimizar transferência de dados e acelerar sincronização.

### Drivers Selecionados
- **RF-02:** Sincronização multi-dispositivo
- **QA-05:** Performance (fast sync speed)
- **QA-06:** Eficiência de largura de banda (minimizar uso de rede)
- **Restrição:** Ficheiros até 10 GB

### Elementos a Refinar
Block Servers, Cloud Storage

### Conceitos de Design Selecionados
- **Algoritmo:** Delta sync (rsync algorithm)
- **Tática de Performance:** Compressão de dados
- **Tática de Performance:** Deduplicação
- **Padrão:** Content-Addressed Storage

### Instanciação

**Fluxo de Upload Otimizado:**
```
1. Cliente calcula hash de cada bloco (256KB chunks)
2. Cliente consulta API: "já tens estes hashes?"
3. API verifica Block table por hash
4. Cliente só envia blocos novos/modificados
5. Block Server comprime e encripta
6. Upload para Cloud Storage
```

**Estrutura de Block Processing:**
```
[File] → [Chunking Service] → [Hash Calculator]
           ↓
       [Compression Service] → [Encryption Service] → [S3 Upload]
           ↓
    [Deduplication Check] (consulta Metadata DB)
```

**Responsabilidades:**
- **Chunking Service:** Dividir ficheiro em blocos de 256KB
- **Hash Calculator:** Calcular SHA-256 de cada bloco
- **Deduplication Check:** Verificar se bloco já existe (por hash)
- **Compression Service:** Comprimir blocos usando gzip/lz4
- **Encryption Service:** Encriptar blocos usando AES-256

### Decisões de Design
1. **Tamanho de bloco 256KB:** Balanço entre granularidade de delta sync e overhead de metadados
2. **Content-addressed storage:** Blocos identificados por hash SHA-256 permite deduplicação automática
3. **Delta sync apenas para blocos modificados:** Reduz drasticamente transferência em updates parciais
4. **Compressão antes de encriptação:** Maximizar redução de tamanho

**Rationale:** Delta sync pode reduzir transferência em 90%+ para ficheiros parcialmente modificados. Deduplicação economiza storage em ficheiros duplicados entre utilizadores.

---

## Iteração 4: Sistema de Notificações e Sincronização em Tempo Real

### Objetivo
Implementar notificações push para sincronização automática entre dispositivos.

### Drivers Selecionados
- **RF-05:** Notificações de alterações
- **RF-02:** Sincronização automática multi-dispositivo
- **QA-07:** Disponibilidade (sistema funcional com clientes offline)

### Elementos a Refinar
API Servers, introdução de Notification Service

### Conceitos de Design Selecionados
- **Padrão:** Publish-Subscribe
- **Padrão:** Long Polling
- **Padrão:** Message Queue
- **Tática de Disponibilidade:** Offline backup queue

### Instanciação

**Estrutura C&C:**
```
[API Server] → [Notification Service] → [Long Poll Connections]
                        ↓                         ↓
                [Message Queue]            [Connected Clients]
                        ↓
              [Offline Backup Queue]
```

**Componentes:**
- **Notification Service:** Servidor de notificações com conexões long-poll
- **Message Queue:** Buffer de eventos de alteração de ficheiros
- **Offline Backup Queue:** Persistência de eventos para clientes offline

**Fluxo de Notificação:**
```
1. Upload completo → API Server publica evento
2. Notification Service recebe evento
3. Identifica clientes subscritos (mesmo user, outros devices)
4. Para clientes online: push via long-poll
5. Para clientes offline: guarda em Offline Backup Queue
6. Cliente online recebe notificação → fetch metadata → download blocos
7. Cliente volta online → consulta Offline Backup Queue → sync
```

### Decisões de Design
1. **Long polling em vez de WebSocket:** Comunicação unidirecional, notificações infrequentes
2. **Separate notification service:** Permite escalar independentemente, facilita integration com outros serviços
3. **Offline backup queue com persistência:** Garante que clientes offline não perdem updates
4. **Subscribe por workspace_id:** Cliente subscreve workspaces relevantes, não todos os ficheiros

**Rationale:** Long polling mais simples que WebSocket para este caso de uso. Queue persistente garante eventual consistency para clientes offline.

---

## Iteração 5: Gestão de Conflitos e Versionamento

### Objetivo
Implementar estratégia de resolução de conflitos e preservação de histórico de versões.

### Drivers Selecionados
- **RF-03:** Versionamento de ficheiros
- **Preocupação:** Conflitos de sincronização (dois users editam simultaneamente)
- **QA-03:** Fiabilidade (preservar histórico, permitir recuperação)

### Elementos a Refinar
API Servers, Metadata Database

### Conceitos de Design Selecionados
- **Estratégia:** Last-Write-Wins com conflict detection
- **Padrão:** Version Control
- **Tática de Modificabilidade:** Conflict resolution UI

### Instanciação

**Mecanismo de Detecção:**
```
1. Cliente envia upload com base_version_id
2. API Server verifica: current_version == base_version?
3. Se SIM: aceita upload, cria nova versão
4. Se NÃO: conflito detectado
   - Aceita upload do primeiro (Last-Write-Wins)
   - Segundo recebe conflito + ambas versões
   - User resolve manualmente (merge ou override)
```

**Estrutura de Versões:**
```
File_Version:
  - version_id (PK)
  - file_id (FK)
  - parent_version_id (FK, pode ser NULL)
  - timestamp
  - author_device_id
  - is_conflict (boolean)
  - block_list (array de block_ids)
```

**Políticas de Retenção:**
```
- Últimas 30 versões: sempre mantidas
- Versões 31-100: apenas marcos (1/semana)
- Versões > 100: apenas marcos mensais
- Versão 1: sempre mantida
```

### Decisões de Design
1. **Last-Write-Wins com notificação:** Estratégia simples, user informado de conflitos
2. **Versionamento explícito:** Cada upload cria nova version row imutável
3. **Retenção inteligente:** Limita custos de storage mas preserva histórico importante
4. **Conflict resolution na UI:** User decide como resolver (merge/override)

**Rationale:** Last-Write-Wins evita complexidade de merge automático. Versionamento imutável facilita auditoria e recuperação.

---

## Iteração 6: Alta Disponibilidade e Tolerância a Falhas

### Objetivo
Garantir que sistema permanece operacional mesmo com falhas de componentes.

### Drivers Selecionados
- **QA-08:** Alta disponibilidade (sistema funcional com servers offline)
- **QA-03:** Fiabilidade (zero perda de dados)
- **QA-01:** Escalabilidade (handle server failures gracefully)

### Elementos a Refinar
Todos os componentes (análise de pontos de falha)

### Conceitos de Design Selecionados
- **Padrão:** Multi-region replication
- **Tática de Disponibilidade:** Redundância passiva (load balancer)
- **Tática de Disponibilidade:** Health monitoring e heartbeat
- **Tática de Disponibilidade:** Graceful degradation

### Instanciação

**Estratégias por Componente:**

| Componente | Tática | Implementação |
|------------|--------|---------------|
| Load Balancer | Active-passive | Secondary LB com heartbeat, VIP failover |
| API Server | Stateless replication | N instâncias, LB distribui, auto-scaling |
| Block Server | Job queue retry | Jobs em queue, outros servers pegam se falha |
| Metadata Cache | Replication | Cache distribuído (Redis Cluster), N réplicas |
| Metadata DB | Master-slave + failover | Slave promovido a master, novo slave provisionado |
| Cloud Storage | Multi-region | S3 com cross-region replication |
| Notification Service | Connection retry | Cliente reconecta automaticamente, queue persiste eventos |

**Deployment Multi-Region:**
```
Region 1 (Primary):          Region 2 (Secondary):
- API Servers                - API Servers
- Block Servers              - Block Servers
- Master DB                  - Slave DB
- S3 Bucket (replicated) ←→  S3 Bucket (replicated)
```

### Decisões de Design
1. **S3 cross-region replication:** Protege contra falha regional completa
2. **Stateless API servers:** Permite adicionar/remover instâncias sem perda de estado
3. **Database failover automático:** Slave promovido a master em <1 minuto
4. **Health checks e circuit breakers:** LB remove automaticamente servers não saudáveis
5. **Graceful degradation:** Operações read-only se master DB falha temporariamente

**Rationale:** Redundância em múltiplos níveis garante SLA 99.9%+. Stateless services facilitam recovery rápido.

---

## Iteração 7: Otimização de Custos de Storage

### Objetivo
Reduzir custos de armazenamento mantendo requisitos de versionamento e fiabilidade.

### Drivers Selecionados
- **Preocupação:** Custos operacionais (500 PB é caro)
- **QA-03:** Fiabilidade (manter versões importantes)
- **RF-03:** Versionamento (histórico necessário)

### Elementos a Refinar
Cloud Storage, Metadata Database, Background Services (novo)

### Conceitos de Design Selecionados
- **Tática:** Data lifecycle management
- **Tática:** Cold storage tiering
- **Padrão:** Content deduplication
- **Padrão:** Batch processing

### Instanciação

**Componentes Novos:**
```
[Storage Optimizer Service]
  ├── Deduplication Engine
  ├── Lifecycle Manager
  └── Cold Storage Migrator
```

**Políticas de Otimização:**

1. **Deduplicação Global:**
   - Blocos identificados por SHA-256
   - Block table mantém reference count
   - Mesmo bloco usado por múltiplos ficheiros/users
   - Delete físico apenas quando ref_count = 0

2. **Tiering Automático:**
   ```
   Hot Storage (S3 Standard):
     - Versões acedidas nos últimos 30 dias
     - Versão atual sempre
   
   Warm Storage (S3 Infrequent Access):
     - Versões 30-90 dias
     - Custo 50% menor
   
   Cold Storage (S3 Glacier):
     - Versões > 90 dias sem acesso
     - Custo 80% menor
     - Retrieval time: 1-12 horas
   ```

3. **Política de Retenção:**
   - Limite configurável por workspace
   - Default: 100 versões ou 1 ano
   - Versões excedentes movidas para cold/deletadas
   - Sempre manter: primeira versão + última versão

**Background Jobs:**
```
[Daily Job] → Identificar blocos órfãos → Marcar para delete
[Weekly Job] → Migrar versões antigas para cold storage
[Monthly Job] → Aplicar política de retenção
```

### Decisões de Design
1. **Deduplicação a nível de bloco com hash:** Economiza storage significativo (estimativa 30-50%)
2. **Tiering automático baseado em acesso:** Balanço entre custo e performance
3. **Lazy deletion:** Blocos marcados para delete, job periódico limpa
4. **Metadata mantida mesmo com blocos em cold:** Permite browse histórico sem retrieval

**Rationale:** Deduplicação + tiering pode reduzir custos em 60-70% mantendo funcionalidade completa.

---

## Resumo do Backlog e Progresso

### Backlog Inicial de Drivers

| ID | Driver | Prioridade | Status | Iteração |
|----|--------|------------|---------|----------|
| RF-01 | Upload/Download ficheiros | Alta | ✅ Completo | 1 |
| RF-02 | Sincronização multi-dispositivo | Alta | ✅ Completo | 3, 4 |
| RF-03 | Versionamento | Média | ✅ Completo | 2, 5, 7 |
| RF-04 | Partilha de ficheiros | Média | ✅ Completo | 2 |
| RF-05 | Notificações | Alta | ✅ Completo | 4 |
| QA-01 | Escalabilidade (10M DAU) | Alta | ✅ Completo | 1, 6 |
| QA-02 | Modificabilidade | Média | ✅ Completo | 1 |
| QA-03 | Fiabilidade | Alta | ✅ Completo | 2, 5, 6, 7 |
| QA-04 | Consistência forte | Alta | ✅ Completo | 2 |
| QA-05 | Performance (fast sync) | Alta | ✅ Completo | 3 |
| QA-06 | Eficiência de bandwidth | Alta | ✅ Completo | 3 |
| QA-07 | Disponibilidade | Alta | ✅ Completo | 4, 6 |
| QA-08 | Alta disponibilidade | Alta | ✅ Completo | 6 |
| C-01 | Limite 10 GB por ficheiro | Alta | ✅ Completo | 1, 3 |
| C-02 | Encriptação obrigatória | Alta | ✅ Completo | 1, 3 |
| P-01 | Gestão de conflitos | Média | ✅ Completo | 5 |
| P-02 | Custos operacionais | Média | ✅ Completo | 7 |

### Propósito de Design Alcançado

✅ **Arquitetura completa e validada** para sistema de armazenamento cloud com:
- Estrutura modular escalável horizontalmente
- Sincronização eficiente com delta sync e deduplicação
- Consistência forte e versionamento robusto
- Alta disponibilidade multi-region
- Otimização de custos com tiering e deduplicação
- Gestão de conflitos user-friendly

**Próximos Passos:** Documentação formal das views, prototipagem de componentes críticos (delta sync, notification service), análise de performance (Capítulo 21).
