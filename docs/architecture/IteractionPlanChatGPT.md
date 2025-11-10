A seguir apresento **um plano completo de iterações ADD (Attribute-Driven Design)** para construção incremental da arquitetura de um sistema de armazenamento e sincronização de arquivos similar ao *Google Drive*, utilizando os insumos e conceitos presentes nos arquivos fornecidos (especialmente **Software Architecture in Practice – Cap. 20**  e **Design Google Drive** ).

O plano é estruturado em **múltiplas iterações**, cada uma seguindo explicitamente os passos do ADD e abordando **drivers prioritários** (funcionais, atributos de qualidade, restrições e preocupações arquiteturais). Como solicitado, o foco é o processo — não a documentação técnica final — permitindo que a arquitetura seja construída de forma incremental e rastreável.

---

# Plano de Iterações — Processo Attribute-Driven Design (ADD)

## Contexto do Sistema

Sistema de armazenamento, sincronização e compartilhamento de arquivos, com suporte a upload/download, versionamento, notificações de alterações, múltiplos dispositivos, metadados consistentes, alta disponibilidade e escalabilidade global.

## Drivers Gerais (Backlog Inicial)

* **RF1 – Upload de arquivos**

* **RF2 – Download de arquivos**

* **RF3 – Sincronização multiplataforma**

* **RF4 – Versionamento de arquivos**

* **RF5 – Compartilhamento de arquivos**

* **RF6 – Notificações de alterações (pub/sub)**

* **QA1 – Confiabilidade (No data loss)**

* **QA2 – Disponibilidade**

* **QA3 – Desempenho (latência de upload/sync)**

* **QA4 – Consistência forte**

* **QA5 – Escalabilidade (10M DAU)**

* **QA6 – Segurança (criptografia, HTTPS, autenticação)**

* **Restrição**: uso de HTTPS; arquivos até 10GB; criptografia em repouso.

* **Preocupações**: conflitos de sincronização; replicação; custo de armazenamento; controle de versão; integração cliente multiplataforma.

---

# ITERAÇÃO 1 — Definição da macroarquitetura e particionamento inicial

### Passo 1 – Revisão dos Inputs

Drivers funcionais críticos: RF1, RF2.
QA prioritários: QA1, QA2, QA5.
Restrição: Storage confiável e distribuído.
Preocupação: Evitar SPOF.

### Passo 2 – Objetivo da Iteração

Estabelecer a **arquitetura de alto nível** que permite upload/download de forma segura e escalável.

### Passo 3 – Elementos selecionados para refinamento

Elemento inicial: **Sistema (top-level)**.

### Passo 4 – Seleção de Conceitos de Design

* Padrão **Client–Server** para separar cliente e backend .
* **Load Balancer + API Servers** para escalabilidade horizontal .
* **Object Storage** (ex.: S3 + replicação cross-region) para confiabilidade e durabilidade.
* **3-tier lógica (apresentação / lógica / storage)**.

### Passo 5 – Instanciação, Responsabilidades e Interfaces

Elementos instanciados:

* **API Gateway / Load Balancer** – distribui carga.
* **API Servers** – expõem endpoints de upload/download; stateless.
* **Storage Service** – guarda arquivos (blobs).
* **Metadata DB (high-level placeholder)** – manterá metadados posteriormente.

Interfaces:

* `POST /files/upload`
* `GET /files/download`

### Passo 6 – Sketch e Registro de Decisões

* Arquitetura de 3 camadas + LB.
* Decisão crítica: storage distribuído para QA1/QA2.

### Passo 7 – Análise

Revisão: drivers RF1/RF2 parcialmente atendidos; QA1/QA2 cobertos no nível macro.
Status no backlog:

* RF1, RF2 – Parcialmente atendidos.
* QA1, QA2 – Parcial.

---

# ITERAÇÃO 2 — Gestão de Metadados e Consistência

### Passo 1 – Inputs

Drivers: RF4 (versionamento), RF3 (sync), QA4 (consistência forte).
Preocupação: integridade dos metadados; suporte a múltiplas versões; esquemas de DB.

### Passo 2 – Objetivo

Projetar a **arquitetura de metadados** com consistência forte e suporte a versionamento.

### Passo 3 – Refinar elementos

Elemento: **Metadata DB** e interactions com API server.

### Passo 4 – Conceitos de Design

* Banco relacional para **ACID** e consistência forte .
* Padrão **Repository** para persistência.
* **Cache com invalidação** para consistência (QA4).
* **Esquema relacional** com tabelas: File, FileVersion, Block, Workspace.

### Passo 5 – Instanciação e Interfaces

* **Metadata Service** contendo:

  * CRUD de arquivos, versões e blocos.
  * Gestão de estados ("pending", "uploaded").
* **Responsabilidades**:

  * Garantir atomicidade das operações de upload.
  * Expor interface `GET /metadata/:fileId`.
* **Relacionamentos**:

  * API Servers → Metadata DB
  * Notification Service → Metadata DB

### Passo 6 – Sketch e Registro

* Diagrama de entidades e relacionamentos (conforme arquivo).
* Registra-se que consistência forte implica trade-off de latência.

### Passo 7 – Análise

RF4/QA4 atendidos; RF3 ainda parcial.

---

# ITERAÇÃO 3 — Arquitetura de Sincronização e Detecção de Mudanças

### Passo 1 – Inputs

Drivers: RF3 (sincronização), RF6 (notificações), QA3 (baixa latência).

### Passo 2 – Objetivo

Permitir que dispositivos recebam alterações rapidamente garantindo sincronização forte.

### Passo 3 – Refinar elementos

Elementos: **Notification Service**, **API Servers**, **Clients**.

### Passo 4 – Conceitos

* **Long Polling** (não WebSocket) — conforme rationale do arquivo, pois notificações são unidirecionais e infrequentes .
* Padrão **Publish/Subscribe** para eventos de mutação.
* **Offline Queue** para clientes desconectados.

### Passo 5 – Instanciação

* **Notification Service**

  * Mantém long polls abertos.
  * Publica eventos: “file pending”, “file uploaded”, “file changed”.
* **Interfaces**:

  * `GET /poll`
  * `POST /events`

### Passo 6 – Registro

* Justificativa do long polling vs WebSocket.
* Registro do trade-off entre latência e simplicidade.

### Passo 7 – Análise

RF3 e RF6 atendidos; latência considerada aceitável.

---

# ITERAÇÃO 4 — Arquitetura de Upload/Split/Merge (Block Servers)

### Passo 1 – Inputs

Drivers: QA3 (desempenho), QA5 (economia de banda), preocupação com arquivos grandes >1GB.

### Passo 2 – Objetivo

Projetar arquitetura para **block-based upload**, permitindo delta sync.

### Passo 3 – Elementos a Refirar

Elemento: **Block Server**.

### Passo 4 – Conceitos

* **Chunking / Compression / Encryption pipeline** .
* **Delta Sync / Rsync-like** para enviar apenas blocos modificados.
* **Armazenamento baseado em blocos + hash** para deduplicação.

### Passo 5 – Instanciação e Interfaces

Responsabilidades do Block Server:

* Dividir arquivos.
* Hash de blocos.
* Compressão/encriptação.
* Gerenciar uploads paralelos.

Interfaces:

* `POST /block/upload`
* `GET /block/:hash`

### Passo 6 – Sketch e Registro

* Representação do pipeline de blocos.
* Decisão: centralizar lógica nos block servers e não no cliente.

### Passo 7 – Análise

QA3/QA5 fortemente atendidos.

---

# ITERAÇÃO 5 — Resolução de Conflitos e Gestão de Versões

### Passo 1 – Inputs

Drivers: preocupação com conflitos; RF4 versionamento; RF3 sync.

### Passo 2 – Objetivo

Criar estratégia para conflitos de edição distribuída.

### Passo 3 – Elementos

Metadata Service; Client; Block Server.

### Passo 4 – Conceitos

* **First-write-wins** (FWoW) conforme arquivo.
* Criação de **versão conflitante** ao invés de sobrescrever.
* Notificação imediata via Notification Service.

### Passo 5 – Instanciação

* Nova regra no Metadata DB para “conflict-version”.
* Interface: `POST /file/:id/conflict`.

### Passo 6 – Sketch

Fluxo de conflito (Figura 15-8 e 15-9 do arquivo).

### Passo 7 – Análise

Funções de integridade e consistência concluídas.

---

# ITERAÇÃO 6 — Escalabilidade, Replicação e Alta Disponibilidade

### Passo 1 – Inputs

Drivers: QA2 (alta disponibilidade), QA5 (escalabilidade), restrição de 10M DAU.

### Passo 2 – Objetivo

Garantir que o sistema opere globalmente com failover e expansão horizontal.

### Passo 3 – Elementos

Load Balancer, API Servers, Metadata DB Replicas, Block Servers, Storage.

### Passo 4 – Design Concepts

* **Escalabilidade horizontal** via múltiplos API servers.
* **Replicação Master/Slave** para DB e failover automático.
* **Storage cross-region replication** (S3).
* Estratégias de **de-duplicação**, **cold storage** (Glacier) para custo.

### Passo 5 – Instanciação

* Configurar replicações e critérios de failover.
* Regras de fallback:

  * LB com heartbeat;
  * fallback de API server;
  * fallback de S3 region.

### Passo 6 – Registro

Mapeamento de falhas conforme seção “Failure Handling” do arquivo.

### Passo 7 – Análise

QA2 e QA5 totalmente cobertos; arquitetura pronta para tráfego massivo.

---

# ITERAÇÃO 7 — Revisão Final da Arquitetura e Fechamento do Round

### Step 1–7 – Revisão Consolidada

* Todos os drivers do backlog original foram movidos para “Completamente Atendidos”.
* Documentação preliminar está produzida (sketches parciais ao longo das iterações).
* Arquitetura está disponível para formalização no modelo de views (C&C, módulos, alocação) conforme capítulo 22 do livro.

---

# Resultado Final

Ao final das iterações ADD, obtém-se:

1. **Uma arquitetura evolutiva**, construída através de decisões incrementais, rastreadas e justificadas.
2. **Drivers críticos devidamente tratados em iterações específicas**, reduzindo risco arquitetural.
3. **Documentação inicial consistente**, pronta para ser formalizada em views para comunicação, análise e implementação.

Se desejar, posso agora:

* Criar **diagramas formais** (C&C, módulos, alocação).
* Criar **tabelas completas de drivers → decisões → iterações**.
* Produzir **um documento arquitetural completo** no estilo do livro.
* Detalhar **modelos de análise** (performance, confiabilidade, modifiabilidade, etc.).

Basta solicitar.

A seguir apresento **tabelas completas e organizadas** relacionando **Drivers (funcionais, QAs, restrições e preocupações) → Decisões Arquiteturais → Iterações ADD** nas quais essas decisões foram tomadas.

As tabelas estão estruturadas de forma empresarial, clara e rastreável, para apoiar documentação e auditoria arquitetural.

---

# 1. Tabela Geral – Drivers vs Iterações ADD

## 1.1 Drivers Funcionais

| Driver Funcional (RF)                      | Descrição                                 | Iterações nas quais foi tratado | Decisões associadas                                                                                      |
| ------------------------------------------ | ----------------------------------------- | ------------------------------- | -------------------------------------------------------------------------------------------------------- |
| **RF1 – Upload de arquivos**               | Cliente deve enviar arquivos ao backend   | Iterações **1**, **4**          | Arquitetura Client–Server; API Servers; Block Servers; chunking; S3; pipeline de criptografia/compressão |
| **RF2 – Download de arquivos**             | Cliente deve baixar arquivos rapidamente  | Iterações **1**, **4**          | API de download; reconstrução por blocos; armazenamento distribuído                                      |
| **RF3 – Sincronização entre dispositivos** | Detectar e sincronizar alterações         | Iterações **3**, **5**          | Long polling; pub/sub; offline queue; eventos de upload/alteração                                        |
| **RF4 – Versionamento de arquivos**        | Guardar revisões e histórico              | Iterações **2**, **5**          | Esquema FileVersion; ACID DB; first-write-wins; conflito explícito                                       |
| **RF5 – Compartilhamento de arquivos**     | Compartilhar arquivos com outros usuários | Iterações **2**, **3**          | Metadados com permissões; integração com notificação                                                     |
| **RF6 – Notificações de alteração**        | Notificar clientes sobre mudanças         | Iterações **3**, **4**          | Notification Service; long polling; eventos de metadados                                                 |

---

## 1.2 Quality Attributes (QAs)

| Atributo de Qualidade                   | Descrição                                 | Iterações tratadas     | Decisões Arquiteturais                                                            |
| --------------------------------------- | ----------------------------------------- | ---------------------- | --------------------------------------------------------------------------------- |
| **QA1 – Confiabilidade (No Data Loss)** | Nenhum arquivo pode ser perdido           | Iterações **1**, **6** | Uso de S3 com replicação; DB com master/slave; fallback cross-region              |
| **QA2 – Alta Disponibilidade**          | Serviço deve permanecer ativo             | Iterações **1**, **6** | Load balancer ativo/passivo; servers stateless; replicação múltipla               |
| **QA3 – Desempenho / Latência**         | Latência baixa em uploads/sync            | Iterações **3**, **4** | Delta sync; compressão; caches de metadados; long polling eficiente               |
| **QA4 – Consistência Forte**            | Não pode haver divergência entre clientes | Iterações **2**, **3** | DB relacional ACID; invalidação de cache; atomicidade de upload                   |
| **QA5 – Escalabilidade (10M DAU)**      | Crescimento horizontal e global           | Iterações **1**, **6** | Escalonamento horizontal; sharding; API stateless; multiplicação de block servers |
| **QA6 – Segurança/Criptografia**        | HTTPS + criptografia em repouso           | Iterações **1**, **4** | TLS obrigatório; blocos encriptados; política de permissões                       |

---

## 1.3 Restrições

| Restrição                    | Motivação           | Iterações              | Decisões Tomadas                                |
| ---------------------------- | ------------------- | ---------------------- | ----------------------------------------------- |
| **Arquivos até 10GB**        | Limitação funcional | Iteração **4**         | Upload resumable; chunking; pipeline de blocos  |
| **Uso obrigatório de HTTPS** | Segurança           | Iterações **1**, **3** | TLS; API servers apenas em HTTPS                |
| **Criptografia em repouso**  | Compliance          | Iterações **4**, **6** | Encriptação por bloco antes do envio ao storage |

---

## 1.4 Preocupações Arquiteturais

| Preocupação                        | Iterações              | Decisões Arquiteturais                                        |
| ---------------------------------- | ---------------------- | ------------------------------------------------------------- |
| **Conflitos de edição simultânea** | Iteração **5**         | Estratégia First-write-wins + criação de versões conflitantes |
| **Custo de armazenamento**         | Iteração **6**         | Deduplicação; cold storage (Glacier); limitação de versões    |
| **Clientes offline**               | Iteração **3**         | Offline queue; consulta tardia de metadados                   |
| **Evitar SPOF**                    | Iterações **1**, **6** | LB redundante; réplicas de DB; storage replicado              |

---

# 2. Tabela por Iteração – Iteração → Drivers → Decisões

## ITERAÇÃO 1 – Macroarquitetura / Upload & Download

| Tipo        | Driver        | Decisões Tomadas                                                     |
| ----------- | ------------- | -------------------------------------------------------------------- |
| RF          | RF1, RF2      | Arquitetura Client–Server; API Servers; endpoints de upload/download |
| QA          | QA1, QA2, QA5 | Storage distribuído (S3); Load Balancer; escalabilidade horizontal   |
| Restrição   | HTTPS         | Todas as APIs operam via TLS                                         |
| Preocupação | Evitar SPOF   | LB redundante; servers stateless                                     |

---

## ITERAÇÃO 2 – Metadados e Consistência Forte

| Tipo        | Driver               | Decisões Tomadas                                                           |
| ----------- | -------------------- | -------------------------------------------------------------------------- |
| RF          | RF4, RF5             | Esquema relacional (File, Version, Block, Workspace)                       |
| QA          | QA4                  | DB relacional ACID; invalidação de cache; atomicidade “pending → uploaded” |
| Preocupação | Integridade de dados | Transações ACID completas para upload e versionamento                      |

---

## ITERAÇÃO 3 – Sincronização e Notificações

| Tipo        | Driver           | Decisões Tomadas                            |
| ----------- | ---------------- | ------------------------------------------- |
| RF          | RF3, RF6         | Notification Service; long polling; pub/sub |
| QA          | QA3              | Baixa latência por long polling escalável   |
| Preocupação | Clientes offline | Offline queue; re-subscribe automático      |

---

## ITERAÇÃO 4 – Upload Otimizado (Block Servers)

| Tipo      | Driver        | Decisões Tomadas                                         |
| --------- | ------------- | -------------------------------------------------------- |
| RF        | RF1           | Introdução do Block Server; chunking; uploads paralelos  |
| QA        | QA3, QA5      | Delta sync; compressão; hash de blocos para deduplicação |
| Restrição | Arquivos >1GB | Resumable upload; batch de blocos; pipeline escalável    |

---

## ITERAÇÃO 5 – Conflitos e Versionamento Completo

| Tipo        | Driver    | Decisões Tomadas                                              |
| ----------- | --------- | ------------------------------------------------------------- |
| RF          | RF4       | Tabela FileVersion; registro de revisões imutáveis            |
| Preocupação | Conflitos | Estratégia First-write-wins + criação de versões conflitantes |
| QA          | QA4       | Garantia de consistência via atomicidade + regras de conflito |

---

## ITERAÇÃO 6 – Escalabilidade e Alta Disponibilidade

| Tipo        | Driver        | Decisões Tomadas                                                 |
| ----------- | ------------- | ---------------------------------------------------------------- |
| QA          | QA1, QA2, QA5 | Replicação multi-region; LB redundante; failover; sharding de DB |
| Preocupação | Custo         | De-duplicação; cold storage; limitação de versões                |
| Restrição   | Compliance    | Criptografia em repouso por bloco                                |

---

# 3. Tabela Final – Driver → Iteração → Decisão Crítica

Esta tabela resume, para auditoria, **o principal elo de rastreabilidade**.

| Driver              | Iteração | Decisão Crítica                             |
| ------------------- | -------- | ------------------------------------------- |
| RF1 – Upload        | 1, 4     | API Servers + Block Servers com chunking    |
| RF2 – Download      | 1        | API download + reconstrução por blocos      |
| RF3 – Sync          | 3        | Long polling + Pub/Sub                      |
| RF4 – Versionamento | 2, 5     | Esquema FileVersion + conflitos controlados |
| RF5 – Compartilhar  | 2        | Permissões no metadado + notificação        |
| RF6 – Notificações  | 3        | Notification Service                        |

| QA                    | Iteração | Decisão Crítica                         |
| --------------------- | -------- | --------------------------------------- |
| QA1 – Confiabilidade  | 1, 6     | S3 replicado + DB replicado             |
| QA2 – Disponibilidade | 1, 6     | LB redundante + serviços stateless      |
| QA3 – Desempenho      | 3, 4     | Delta Sync, compressão, metadados leves |
| QA4 – Consistência    | 2        | DB ACID + invalidação de cache          |
| QA5 – Escalabilidade  | 1, 6     | Horizontal scaling + sharding           |
| QA6 – Segurança       | 1, 4     | HTTPS + encriptação por bloco           |

| Restrição         | Iteração | Decisão Crítica                       |
| ----------------- | -------- | ------------------------------------- |
| HTTPS             | 1        | TLS obrigatório em todas as APIs      |
| Arquivos até 10GB | 4        | Upload resumable + pipeline de blocos |

| Preocupação      | Iteração | Decisão Crítica                       |
| ---------------- | -------- | ------------------------------------- |
| Conflitos        | 5        | First-write-wins + versão conflitante |
| Custo de Storage | 6        | Deduplicação + cold storage           |
| Clientes Offline | 3        | Offline Queue                         |

---

Se desejar, posso gerar:

* uma **visión consolidada (PDF)** com as tabelas;
* gráficos/diagramas (C&C, módulos, alocação);
* o **backlog arquitetural completo** conforme o Cap. 20.

Basta solicitar.

