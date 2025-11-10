O planejamento da arquitetura do Google Drive usando o processo **Attribute-Driven Design (ADD)** permite abordar os requisitos significativos da arquitetura (ASRs), como escalabilidade e confiabilidade, de forma organizada e incremental.

Com base nos requisitos estabelecidos para o design do Google Drive, a seguir está o plano de iterações em formato markdown.

---

# 🗺️ Plano de Iterações do Attribute-Driven Design (ADD) para o Google Drive

O design do sistema é dividido em três iterações principais, priorizando a **Confiabilidade**, a **Escalabilidade** e o **Desempenho** (velocidade de sincronização), que são cruciais para um serviço de armazenamento em nuvem de 10M DAU.

## Tabela Resumo das Iterações

| # | Objetivo da Iteração (Foco) | Drivers Prioritários Abordados | Estruturas Focais | Conceitos de Design (Padrões/Táticas) |
| :---: | :--- | :--- | :--- | :--- |
| **1** | **Fundação e Escalabilidade do Armazenamento e Metadados** | **Confiabilidade** (Zero perda de dados), **Alta Disponibilidade**, **Escalabilidade** (10M DAU) | Estruturas C&C e de Alocação | Amazon S3 (Replicação), Sharding (por `user_id`), Load Balancing |
| **2** | **Otimização de Upload/Download e Sincronização** | **Fast Sync Speed**, **Bandwidth Usage**, **Consistência Forte** | Estruturas C&C | Block Servers (Chunking/Compressão/Criptografia), Delta Sync, Long Polling (Notificação), ACID DB |
| **3** | **Gerenciamento de Versões, Otimização de Custo e Resiliência** | **Save Storage Space** (Versões), **Resolução de Conflitos**, **Resiliência** (Failure Handling) | Estruturas de Módulo e de Alocação | De-duplicação de Blocos, Cold Storage (S3 Glacier), Estratégias de Failover e Replicação |

---

## 🚀 Iteração 1: Fundação e Escalabilidade

### 🎯 Meta da Iteração
Criar a arquitetura distribuída básica para o armazenamento de arquivos e metadados, focada em **escalabilidade**, **alta disponibilidade** e **confiabilidade** (zero perda de dados).

### 📝 Drivers Selecionados (ASRs)
* **Funcionalidade:** Upload e Download de arquivos (nível básico).
* **Atributos de Qualidade:** Escalabilidade (10M DAU), Alta Disponibilidade, Confiabilidade (Data loss is unacceptable).
* **Restrições:** Arquivos de **10 GB ou menor**.

### 💡 Conceitos de Design Escolhidos
1.  **Armazenamento de Arquivos:** Utilizar um serviço de **Cloud Storage** como o **Amazon S3** para aproveitar a escalabilidade e a durabilidade.
2.  **Confiabilidade:** Implementar **replicação de arquivos** entre regiões (cross-region replication) para garantir que os arquivos possam ser recuperados em caso de falha de uma região.
3.  **Balanceamento de Carga:** Adicionar um **Load Balancer** para distribuir uniformemente as requisições para os **API Servers** e fornecer failover.
4.  **Banco de Dados de Metadados:** Implementar **sharding** (fragmentação) do banco de dados, possivelmente baseado em `user_id`, para lidar com o volume e o tráfego de metadados.

### 🧱 Estruturas Produzidas
* **Estrutura C&C (Componente e Conector):** Diagrama de alto nível mostrando a interação entre Cliente, Load Balancer, API Servers, Metadata DB e File Storage.

---

## ⚡ Iteração 2: Otimização de Upload/Download e Sincronização

### 🎯 Meta da Iteração
Otimizar a transferência de dados para alcançar a **Fast Sync Speed** e o **baixo uso de banda**, e garantir a **forte consistência** dos metadados entre os clientes.

### 📝 Drivers Selecionados (ASRs)
* **Funcionalidades:** Sincronizar arquivos entre dispositivos, Upload resumível.
* **Atributos de Qualidade:** Velocidade de sincronização rápida, Uso otimizado da largura de banda, **Consistência Forte** (dados idênticos para todos os clientes).

### 💡 Conceitos de Design Escolhidos
1.  **Otimização de Transferência (Economia de Banda):** Introduzir **Block Servers** para tarefas pesadas, incluindo **chunking** (divisão em blocos), **compressão** e **criptografia**.
2.  **Sincronização Eficiente:** Implementar **Delta Sync**, onde apenas os blocos modificados são transferidos para a nuvem em vez do arquivo inteiro.
3.  **Consistência de Metadados:** Utilizar um **Banco de Dados Relacional** com propriedades **ACID** para metadados e invalidar caches na escrita (`Invalidate caches on database write`) para garantir que o cache e o DB sejam consistentes.
4.  **Notificação de Sincronização:** Usar **Long Polling** no **Serviço de Notificação** para alertar os clientes sobre alterações de arquivos de forma assíncrona, reduzindo conflitos.

### 🧱 Estruturas Produzidas
* **Estrutura C&C:** Refinamento do diagrama de alto nível com a adição explícita dos **Block Servers**, **Notification Service** e **Offline Backup Queue**.
* **Estrutura do Módulo:** Diagramas de sequência para os fluxos detalhados de **Upload** e **Download** de arquivos.

---

## 🛡️ Iteração 3: Versões, Otimização de Custo e Resiliência

### 🎯 Meta da Iteração
Otimizar o custo de armazenamento através do gerenciamento de versões e transferir dados inativos para armazenamento mais frio. Além disso, estabelecer estratégias de **Failure Handling** para aumentar a resiliência do sistema.

### 📝 Drivers Selecionados (ASRs)
* **Funcionalidades:** Ver revisões de arquivo, lidar com conflitos de sincronização.
* **Preocupações:** Economizar espaço de armazenamento (**Save storage space**), Resiliência e Tolerância a Falhas (**Failure Handling**).

### 💡 Conceitos de Design Escolhidos
1.  **Otimização de Custo:**
    * **De-duplicação de Blocos de Dados:** Eliminar blocos idênticos (com base no hash) para reduzir a redundância no nível da conta.
    * **Cold Storage:** Mover dados pouco ativos (não acessados por meses ou anos) para armazenamento de baixo custo como o S3 Glacier.
2.  **Suporte à Versão:** Design do esquema de BD com as tabelas `File_version` e `Block` para armazenar o histórico de revisão de arquivos.
3.  **Tratamento de Conflitos:** Adotar a estratégia **"the first version that gets processed wins"** (a primeira versão processada vence) e apresentar ao usuário ambas as cópias para resolução manual.
4.  **Estratégias de Failover:** Definir mecanismos de failover (coração/heartbeat) para o Load Balancer e estratégias de promoção de *slaves* para *master* em caso de falha do Metadata DB.

### 🧱 Estruturas Produzidas
* **Estrutura do Módulo:** Esboço do esquema detalhado do **Metadata Database** (tabelas User, Device, Namespace, File, File\_version, Block).
* **Estrutura de Alocação:** Inclusão explícita do componente de **Cold Storage** no mapeamento de dados.
