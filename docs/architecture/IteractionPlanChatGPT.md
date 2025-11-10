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

* uma **visión consolidada (PDF)** com as tabelas;
* gráficos/diagramas (C&C, módulos, alocação);
* o **backlog arquitetural completo** conforme o Cap. 20.

Basta solicitar.

