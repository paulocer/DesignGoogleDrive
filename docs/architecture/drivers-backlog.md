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

