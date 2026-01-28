Vou analisar cada pergunta de pesquisa considerando o experimento descrito no README.md e fornecer uma explicação estruturada.

## **Perguntas de Pesquisa - Área de Educação**

---

### **RQ1: "Quais estratégias de interação os estudantes adotam ao utilizar LLMs no desenvolvimento de arquiteturas de software? Como essas estratégias se relacionam com o desenvolvimento de competências profissionais?"**

#### 1. Objetivos

**Objetivo Geral:**
Identificar e caracterizar os padrões de interação entre estudantes e LLMs durante o processo de design arquitetural, relacionando-os ao desenvolvimento de competências profissionais em arquitetura de software.

**Objetivos Específicos:**
- Mapear os diferentes tipos de estratégias de prompting utilizadas pelos estudantes (e.g., exploratória, incremental, validação)
- Identificar os momentos do processo ADD onde os estudantes mais dependem do LLM
- Analisar como a refinação dos artefatos (LLM → Expert) revela desenvolvimento de competências críticas
- Relacionar estratégias de interação com indicadores de competência profissional (pensamento crítico, tomada de decisão, justificação técnica)

#### 2. Como atingir o objetivo

- **Análise dos logs de prompts:** Examinar o histórico completo de prompts fornecidos ao LLM em cada fase do ADD
- **Análise comparativa LLM vs Expert:** Comparar os arquivos `LLM-*.md` com `Expert-*.md` para identificar padrões de refinação
- **Categorização de estratégias:** Desenvolver taxonomia de estratégias de interação (e.g., delegação total, co-criação, validação, refinação iterativa)
- **Análise do survey:** Correlacionar percepções dos estudantes (PDF do survey) com comportamentos observados
- **Análise das decisões arquiteturais:** Examinar os arquivos de Rounds para identificar como as decisões evoluem

#### 3. Metodologia

**Abordagem:** Pesquisa qualitativa com elementos quantitativos (métodos mistos)

**Métodos:**
- **Análise de conteúdo temática:** Codificação dos prompts e refinações para identificar padrões
- **Grounded Theory:** Desenvolver teoria emergente sobre estratégias de interação a partir dos dados
- **Análise de protocolo:** Reconstruir o processo de pensamento através dos artefatos
- **Survey descritivo:** Análise das 64 respostas para triangulação
- **Comparative Case Analysis:** Comparar casos de diferentes duplas

**Dados:**
- Histórico completo de prompts
- Pares de arquivos LLM/Expert em todas as fases
- Respostas do survey
- Artefatos intermediários (Rounds, Design Plans)

#### 4. Contribuição Esperada

- **Taxonomia de estratégias de interação** estudante-LLM em contexto educacional de arquitetura de software
- **Framework** relacionando estratégias de interação com desenvolvimento de competências (e.g., pensamento crítico, autonomia, justificação técnica)
- **Recomendações pedagógicas** sobre como estruturar atividades com LLMs para maximizar aprendizagem
- Evidências sobre **quando e como** a mediação do LLM apoia (ou dificulta) o desenvolvimento profissional

---

### **RQ2: "Como o uso de LLMs nas etapas do processo ADD influencia a curva de aprendizagem dos estudantes em relação aos conceitos e práticas de arquitetura de software?"**

#### 1. Objetivos

**Objetivo Geral:**
Avaliar o impacto do uso de LLMs na progressão de aprendizagem dos estudantes ao longo das diferentes etapas do processo ADD.

**Objetivos Específicos:**
- Medir a evolução da qualidade dos artefatos Expert ao longo das fases do ADD
- Identificar em quais etapas o LLM mais contribui para a aprendizagem
- Analisar a redução (ou não) da dependência do LLM ao longo do projeto
- Avaliar a apropriação de conceitos arquiteturais evidenciada nas refinações

#### 2. Como atingir o objetivo

- **Análise longitudinal:** Examinar a evolução da qualidade dos artefatos Expert desde User Stories até os últimos Rounds
- **Métricas de independência:** Calcular o "gap" entre LLM e Expert em cada fase (usando as 12 métricas)
- **Análise de conceitos:** Identificar uso correto de terminologia e aplicação de conceitos ADD nos artefatos Expert
- **Comparação com gabarito:** Usar Expert_Solution para estabelecer baseline de qualidade
- **Análise de erros:** Categorizar tipos de erros corrigidos em cada fase

#### 3. Metodologia

**Abordagem:** Estudo longitudinal quasi-experimental

**Métodos:**
- **Análise quantitativa:** Aplicar as 12 métricas (BERTScore, ROUGE, etc.) em cada fase do processo
- **Análise de séries temporais:** Modelar a evolução da qualidade ao longo das etapas
- **Análise qualitativa de conteúdo:** Avaliar profundidade conceitual nos artefatos
- **Learning Analytics:** Identificar padrões de progressão e estagnação
- **Análise de correlação:** Relacionar survey com desempenho observado

**Dados:**
- Todos os artefatos Expert em ordem cronológica
- Métricas computadas para comparações 1, 2 e 3
- Respostas do survey sobre percepção de aprendizagem
- Expert_Solution como referência

#### 4. Contribuição Esperada

- **Modelo de curva de aprendizagem** específico para uso de LLMs em design arquitetural
- Identificação de **fases críticas** onde intervenção pedagógica é mais necessária
- **Evidências empíricas** sobre transferência de conhecimento do LLM para o estudante
- **Recomendações** sobre scaffolding ideal em cada fase do ADD
- Insights sobre **quando reduzir assistência** do LLM para promover autonomia

---

### **RQ3: "De que forma a utilização de LLMs como ferramenta de apoio afeta o desenvolvimento do pensamento crítico e da autonomia dos estudantes em decisões arquiteturais?"**

#### 1. Objetivos

**Objetivo Geral:**
Avaliar o impacto do uso de LLMs no desenvolvimento do pensamento crítico e autonomia decisória dos estudantes em contexto arquitetural.

**Objetivos Específicos:**
- Identificar evidências de pensamento crítico nas refinações Expert
- Avaliar a qualidade das justificativas arquiteturais fornecidas pelos estudantes
- Medir o grau de autonomia nas decisões (aceitação vs. questionamento do LLM)
- Analisar a capacidade de identificar e corrigir erros/alucinações do LLM

#### 2. Como atingir o objetivo

- **Análise de refinações:** Examinar sistematicamente as mudanças de LLM para Expert, categorizando-as
- **Análise de justificativas:** Avaliar a profundidade das decisões nos arquivos de Rounds
- **Indicadores de criticidade:** 
  - Rejeição fundamentada de sugestões do LLM
  - Identificação de inconsistências
  - Proposição de alternativas
  - Questionamento de trade-offs
- **Análise Bloom:** Usar métricas Bloom_Similarity e Bloom_Wasserstein para avaliar níveis cognitivos
- **Survey:** Analisar autopercepção sobre autonomia e pensamento crítico

#### 3. Metodologia

**Abordagem:** Estudo misto com ênfase qualitativa

**Métodos:**
- **Análise de discurso crítico:** Examinar justificativas e comentários nos artefatos Expert
- **Taxonomia de Bloom:** Classificar operações cognitivas evidenciadas nas refinações
- **Análise de decisões:** Usar framework de pensamento crítico (e.g., Paul-Elder) para avaliar qualidade decisória
- **Análise quantitativa:** Métricas Bloom + proporção de mudanças críticas vs. cosméticas
- **Triangulação:** Cruzar dados comportamentais com percepções do survey

**Dados:**
- Diferenças LLM → Expert com anotação de tipo de mudança
- Arquivos de Rounds (justificativas de decisões)
- Métricas Bloom_Similarity e Bloom_Wasserstein
- Survey (questões sobre autonomia e pensamento crítico)
- Casos de erro/alucinação do LLM e correções

#### 4. Contribuição Esperada

- **Framework** para avaliar pensamento crítico em contexto de uso de LLMs
- Evidências sobre **riscos de dependência** vs. **benefícios de scaffolding**
- **Indicadores** de desenvolvimento de autonomia profissional
- **Guidelines** para promover uso crítico (não passivo) de LLMs
- Insights sobre **paradoxo da automação** em educação de arquitetura de software

---

## **Perguntas de Pesquisa - Área Científica**

---

### **RQ1: "Como o nível de abstração e complexidade das tarefas de design arquitetural influencia a qualidade das respostas geradas por LLMs no contexto do processo ADD?"**

#### 1. Objetivos

**Objetivo Geral:**
Investigar a relação entre características das tarefas arquiteturais (abstração, complexidade) e a qualidade das respostas do LLM.

**Objetivos Específicos:**
- Caracterizar níveis de abstração e complexidade em cada fase do ADD
- Medir qualidade das respostas do LLM em diferentes tipos de tarefa
- Identificar tipos de tarefa onde LLMs apresentam melhor/pior desempenho
- Analisar padrões de erro relacionados à complexidade

#### 2. Como atingir o objetivo

- **Taxonomia de tarefas:** Classificar cada etapa do ADD por nível de abstração (alto/médio/baixo) e complexidade
- **Métricas de qualidade:** Comparar LLM_Student com Expert_Solution usando as 12 métricas
- **Análise de erros por tipo de tarefa:** Categorizar erros do LLM segundo características da tarefa
- **Análise de completude:** Avaliar se LLM fornece respostas completas em tarefas complexas
- **Análise de consistência:** Verificar coerência interna em tarefas de alta abstração

#### 3. Metodologia

**Abordagem:** Estudo experimental controlado

**Métodos:**
- **Design fatorial:** Tarefa (múltiplos níveis) × Métrica (12 métricas)
- **Análise de variância (ANOVA/MANOVA):** Testar efeito de abstração/complexidade na qualidade
- **Regressão:** Modelar relação complexidade → qualidade
- **Análise qualitativa:** Estudos de caso de tarefas extremas (muito simples/muito complexas)
- **Benchmarking:** Comparar performance em diferentes fases ADD

**Dados:**
- Classificação de tarefas por abstração/complexidade
- Arquivos LLM_Student por fase
- Expert_Solution como ground truth
- 12 métricas computadas
- Análise de erros categorizados

#### 4. Contribuição Esperada

- **Mapa de capacidades** do LLM em design arquitetural (onde funciona bem/mal)
- **Modelo preditivo** de qualidade baseado em características da tarefa
- **Recomendações** sobre quando confiar vs. validar outputs do LLM
- Insights sobre **limitações atuais** de LLMs em raciocínio arquitetural
- Base para **melhorias de prompting** adaptadas à complexidade da tarefa

---

### **RQ2: "Qual a relação entre o uso de LLMs e a qualidade dos artefatos produzidos em cada etapa do processo ADD, desde a elicitação de requisitos até a definição de táticas arquiteturais?"**

#### 1. Objetivos

**Objetivo Geral:**
Avaliar sistematicamente a qualidade dos artefatos produzidos com assistência de LLM em cada etapa do processo ADD.

**Objetivos Específicos:**
- Medir qualidade absoluta dos artefatos LLM em cada fase ADD
- Comparar qualidade entre fases (identificar pontos fortes/fracos)
- Avaliar ganho de qualidade da refinação Expert sobre LLM
- Identificar fases onde LLM agrega mais/menos valor

#### 2. Como atingir o objetivo

- **Análise por fase:** Calcular as 12 métricas para cada etapa ADD separadamente
- **Comparação tripla:** Expert_Solution × Expert_Student × LLM_Student por fase
- **Análise de gap:** Medir distância LLM→Expert em cada fase
- **Análise de consistência:** Verificar alinhamento entre artefatos de fases consecutivas
- **Benchmarking com gabarito:** Expert_Solution como padrão-ouro

#### 3. Metodologia

**Abordagem:** Estudo comparativo multi-nível

**Métodos:**
- **Análise quantitativa multinível:** Métricas agregadas por fase e por dupla
- **ANOVA de medidas repetidas:** Comparar qualidade através das fases ADD
- **Análise de mediação:** LLM → Expert_Student → Qualidade final
- **Análise de conteúdo:** Avaliar completude, correção e adequação por fase
- **Análise de rastreabilidade:** Verificar consistência entre artefatos relacionados

**Dados:**
- Todos os artefatos organizados por fase ADD
- 12 métricas × 3 comparações × N fases
- Expert_Solution completo
- Matriz de rastreabilidade entre artefatos

#### 4. Contribuição Esperada

- **Perfil de qualidade** detalhado do LLM ao longo do ciclo ADD
- **Ranking** de fases segundo benefício do uso de LLM
- **Indicadores de qualidade** específicos para artefatos arquiteturais
- Evidências sobre **valor agregado** da refinação humana
- **Recomendações** sobre alocação de esforço em diferentes fases

---

### **RQ3: "Quais dimensões de qualidade e métricas, que associadas, são mais relevantes para avaliar o impacto do uso de LLMs no processo de design de arquitetura de software baseado em ADD?"**

#### 1. Objetivos

**Objetivo Geral:**
Identificar e validar as métricas e dimensões de qualidade mais adequadas para avaliar artefatos arquiteturais produzidos com assistência de LLM.

**Objetivos Específicos:**
- Avaliar a relevância e sensibilidade das 12 métricas no contexto arquitetural
- Identificar redundâncias e complementaridades entre métricas
- Propor um conjunto mínimo de métricas com máximo poder discriminativo
- Relacionar métricas com dimensões de qualidade arquitetural (correção, completude, adequação)

#### 2. Como atingir o objetivo

- **Análise de correlação:** Examinar correlações entre as 12 métricas
- **Análise fatorial:** Reduzir dimensionalidade e identificar fatores latentes
- **Análise de sensibilidade:** Testar capacidade discriminativa de cada métrica
- **Validação com especialistas:** Comparar métricas com avaliação humana qualitativa
- **Análise de casos extremos:** Verificar comportamento das métricas em casos bons/ruins

#### 3. Metodologia

**Abordagem:** Estudo metodológico/psicométrico

**Métodos:**
- **Análise de Componentes Principais (PCA):** Identificar dimensões latentes
- **Análise de correlação (Pearson/Spearman):** Mapear redundâncias
- **Análise de concordância:** Correlacionar métricas com avaliação humana
- **Curvas ROC/AUC:** Avaliar poder discriminativo (artefato bom vs. ruim)
- **Validação cruzada:** Testar estabilidade das métricas

**Dados:**
- Matriz completa: 12 métricas × 3 comparações × todos os artefatos
- Avaliações qualitativas de especialistas (se disponível)
- Categorização binária de qualidade (expert vs. não expert)
- Survey (percepções podem complementar)

#### 4. Contribuição Esperada

- **Framework de avaliação** validado para artefatos arquiteturais com LLM
- **Conjunto recomendado** de métricas (balanceando custo-benefício)
- **Interpretação** de cada métrica no contexto arquitetural específico
- **Ferramenta de benchmarking** para futuros estudos
- Insights sobre **limitações das métricas** tradicionais de NLP em contexto técnico

---

## **Síntese das Metodologias**

| RQ | Tipo | Abordagem Principal | Dados Chave | Análise Principal |
|---|---|---|---|---|
| **Ed1** | Qualitativa | Grounded Theory | Prompts + Refinações | Análise temática |
| **Ed2** | Longitudinal | Learning Analytics | Artefatos temporais | Séries temporais |
| **Ed3** | Mista | Análise crítica | LLM→Expert diffs | Taxonomia Bloom |
| **Ci1** | Experimental | Correlacional | Métricas × Complexidade | ANOVA/Regressão |
| **Ci2** | Comparativa | Multi-nível | Artefatos por fase | ANOVA medidas repetidas |
| **Ci3** | Metodológica | Psicométrica | Matriz de métricas | PCA/Correlação |

Todas as RQs se beneficiam da **triangulação** entre dados quantitativos (métricas), qualitativos (análise de conteúdo) e perceptuais (survey).
