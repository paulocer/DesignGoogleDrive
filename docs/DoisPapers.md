# 📚 **DOIS PAPERS SEPARADOS: ROTEIROS COMPLETOS**

## 📘 **PAPER 1: ÁREA DE EDUCAÇÃO**

### **Título provisório:**  
*"Enhancing Software Architecture Education with LLM Assistance: A Study on Learning Outcomes and Pedagogical Strategies in ADD-based Design"*

---

## 🎯 **1. CONTEXTO E MOTIVAÇÃO (Educação)**

**Problema educacional:**  
Como integrar LLMs no ensino de arquitetura de software de forma a **maximizar a aprendizagem** enquanto **desenvolve competências profissionais** críticas (pensamento crítico, autonomia, tomada de decisão)?

**Gap na literatura:**  
Falta de estudos empíricos sobre:
- Estratégias pedagógicas para uso de LLMs em design arquitetural
- Impacto no desenvolvimento de competências profissionais
- Curvas de aprendizagem em metodologias como ADD com assistência de IA

**Contribuição principal:**  
Fornecer evidências empíricas e diretrizes pedagógicas para o uso responsável de LLMs no ensino de arquitetura de software.

---

## 🔬 **2. PERGUNTAS DE PESQUISA (Educação)**

#### **RQ1-EDU**:  
*"Quais estratégias de interação e refinamento os estudantes adotam ao utilizar LLMs no desenvolvimento de arquiteturas de software?"*

#### **RQ2-EDU**:  
*"Como essas estratégias se relacionam com o desenvolvimento de competências profissionais (pensamento crítico, autonomia, tomada de decisão)?"*

#### **RQ3-EDU**:  
*"Como o uso de LLMs nas etapas do processo ADD influencia a curva de aprendizagem dos estudantes em relação aos conceitos e práticas de arquitetura de software?"*

#### **RQ4-EDU**:  
*"Quais fatores (experiência prévia, conhecimento de ADD, frequência de uso de LLM) moderam a relação entre uso de LLM e resultados de aprendizagem?"*

---

## 🧪 **3. METODOLOGIA (Educação)**

### 3.1 Design da pesquisa
- **Estudo misto** com foco qualitativo-dominante
- **Abordagem**: Estudo de caso instrumental (64 estudantes)
- **Teoria fundamentante**: Aprendizagem experiencial com tecnologia

### 3.2 Participantes
- 64 estudantes de engenharia de software
- Trabalho em pares (32 duplas)
- Diversidade em: experiência prévia, conhecimento de ADD, familiaridade com LLMs

### 3.3 Fontes de dados
| Fonte | Coleta | Análise |
|-------|--------|---------|
| **Artefatos de refinamento** | Comparação `LLM-` vs `Expert-` | Análise de conteúdo, análise delta |
| **Survey (64 respostas)** | Percepções, estratégias, aprendizagem | Estatística descritiva, correlações |
| **Diários reflexivos** | Se disponíveis | Análise temática |
| **Observações do instrutor** | Notas durante o experimento | Análise qualitativa |

### 3.4 Análise de dados

#### **Para RQ1-EDU (estratégias de interação)**
1. **Análise de conteúdo das modificações**:
   - Categorização: correção técnica, expansão conceitual, reorganização, validação
   - Frequência de cada tipo por etapa do ADD
2. **Padrões de prompting** (inferidos das modificações)
3. **Estratégias emergentes**: aceitação crítica, rejeição seletiva, hibridização

#### **Para RQ2-EDU (competências profissionais)**
1. **Indicadores quantitativos**:
   - *Autonomia*: proporção de conteúdo alterado (`Expert-` vs `LLM-`)
   - *Pensamento crítico*: frequência de correções conceituais
   - *Tomada de decisão*: justificativas documentadas nas modificações
2. **Correlação** com autoavaliação do survey
3. **Análise qualitativa** das competências demonstradas

#### **Para RQ3-EDU (curva de aprendizagem)**
1. **Análise longitudinal** através das etapas do ADD
2. **Mudanças nas estratégias** ao longo do projeto
3. **Aproximação progressiva** à solução especialista (métricas de similaridade)

#### **Para RQ4-EDU (fatores moderadores)**
1. **Análise por subgrupos**:
   - Experiência alta vs baixa
   - Conhecimento de ADD prévio vs novo
   - Uso frequente vs ocasional de LLM
2. **Regressão múltipla**: fatores → estratégias → resultados

### 3.5 Validação
- **Triangulação** múltipla de fontes
- **Validação por membros** (estudantes revisam categorias)
- **Revisão por pares** especialistas em educação

---

## 📊 **4. ANÁLISE ESTATÍSTICA (Educação)**

### Variáveis principais:
- **Independentes**: Experiência, conhecimento, uso de LLM
- **Dependentes**: 
  - Estratégias de refinamento (categorias)
  - Competências demonstradas (indicadores)
  - Aprendizagem percebida (survey)
- **Moderadoras**: Etapa do ADD, complexidade da tarefa

### Testes estatísticos:
1. **ANOVA**: Diferenças entre grupos de experiência
2. **Correlação de Pearson**: Uso de LLM × aprendizagem
3. **Análise de cluster**: Tipologias de estudantes
4. **Regressão linear**: Modelo preditivo de sucesso

---

## 📝 **5. ESTRUTURA DO PAPER (Educação)**

```
1. INTRODUCTION
   1.1 The Challenge of Teaching Software Architecture
   1.2 LLMs as Pedagogical Tools: Promise and Peril
   1.3 Research Gap and Questions
   1.4 Contributions to Software Engineering Education

2. THEORETICAL FRAMEWORK
   2.1 Experiential Learning in Software Architecture
   2.2 ADD as a Pedagogical Methodology
   2.3 Human-AI Collaboration in Learning Contexts
   2.4 Professional Competency Development

3. METHODOLOGY
   3.1 Research Design: Mixed-Methods Case Study
   3.2 Participants and Context
   3.3 The ADD-based Learning Activity
   3.4 Data Collection: Artifacts, Survey, Observations
   3.5 Analysis Procedures
   3.6 Ethical Considerations

4. FINDINGS
   4.1 RQ1: Student Interaction Strategies with LLMs
        - Taxonomy of Refinement Approaches
        - Stage-by-Stage Strategy Evolution
    
   4.2 RQ2: Professional Competency Development
        - Critical Thinking Patterns
        - Autonomy and Decision-Making
        - Correlation with Self-Assessment
    
   4.3 RQ3: Learning Trajectories in ADD
        - Knowledge Acquisition Patterns
        - Skill Development Progressions
        - LLM's Role in Learning Progression
    
   4.4 RQ4: Moderating Factors
        - Experience Level Effects
        - Prior Knowledge Impact
        - Usage Pattern Influences

5. DISCUSSION
   5.1 Pedagogical Implications
        - Effective LLM Integration Strategies
        - Balancing Assistance and Independence
        - Assessment in AI-Assisted Learning
    
   5.2 Theoretical Contributions
        - Model of LLM-Assisted Learning
        - Competency Development Framework
    
   5.3 Practical Guidelines for Educators
        - Activity Design Principles
        - Facilitation Strategies
        - Assessment Approaches
    
   5.4 Limitations and Future Research

6. CONCLUSION
   6.1 Key Takeaways for Software Engineering Education
   6.2 Recommendations for Curriculum Integration
   6.3 Future Directions

REFERENCES
APPENDICES
   A. Learning Activity Description
   B. Survey Instrument
   C. Refinement Taxonomy with Examples
   D. Sample Student Artifacts
   E. Instructor Guidelines
```

---

## ⏱️ **6. CRONOGRAMA (Educação)**

| Semana | Atividade |
|--------|-----------|
| 1-2 | Análise qualitativa dos refinamentos |
| 3-4 | Codificação e categorização |
| 5-6 | Análise estatística do survey |
| 7-8 | Integração dos resultados |
| 9-10 | Redação do paper |
| 11-12 | Revisão e submissão |

---

## 📈 **7. CONTRIBUIÇÕES ESPERADAS (Educação)**

1. **Taxonomia de estratégias** de uso de LLM no ensino de arquitetura
2. **Framework pedagógico** para integração de LLMs
3. **Instrumentos de avaliação** para aprendizagem assistida por IA
4. **Diretrizes curriculares** para educadores
5. **Modelo de desenvolvimento** de competências com IA

---

# 🔬 **PAPER 2: ÁREA CIENTÍFICA**

### **Título provisório:**  
*"Quantifying LLM Performance in Software Architecture Design: A Multi-Metric Evaluation of Gemini in ADD-based Architectural Decision-Making"*

---

## 🎯 **1. CONTEXTO E MOTIVAÇÃO (Científica)**

**Problema científico:**  
Como **avaliar objetivamente** o desempenho de LLMs em tarefas complexas de design arquitetural? Quais **métricas e dimensões** são mais informativas para entender capacidades e limitações?

**Gap na literatura:**  
- Avaliação fragmentada de LLMs em engenharia de software
- Foco em código vs. design arquitetural
- Falta de benchmarks padronizados para tarefas de arquitetura
- Poucas métricas validadas para avaliação multidimensional

**Contribuição principal:**  
Framework de avaliação multidimensional e benchmark para LLMs em design arquitetural baseado em ADD.

---

## 🔬 **2. PERGUNTAS DE PESQUISA (Científica)**

#### **RQ1-SCI**:  
*"Como o nível de abstração e complexidade das tarefas de design arquitetural influencia a qualidade das respostas geradas por LLMs no contexto do processo ADD?"*

#### **RQ2-SCI**:  
*"Qual a relação entre o uso de LLMs e a qualidade dos artefatos produzidos em cada etapa do processo ADD?"*

#### **RQ3-SCI**:  
*"Quais dimensões de qualidade (semântica, estrutura, completude, coerência) e métricas, que associadas, são mais relevantes para avaliar o impacto do uso de LLMs no processo de design de arquitetura de software?"*

#### **RQ4-SCI**:  
*"Como o refinamento humano modifica a qualidade dos artefatos gerados por LLMs, e quais padrões de modificação são mais comuns?"*

---

## 🧪 **3. METODOLOGIA (Científica)**

### 3.1 Design da pesquisa
- **Estudo quantitativo** comparativo
- **Design experimental**: 3 níveis de qualidade comparados
- **Abordagem**: Avaliação baseada em métricas múltiplas

### 3.2 Conjuntos de dados
1. **Nível 1 (Referência)**: `Expert_Solution.md` (gabarito)
2. **Nível 2 (LLM Bruto)**: `LLM-` files (Gemini 3.0 output)
3. **Nível 3 (Refinado)**: `Expert-` files (human-refined)

### 3.3 Métricas de avaliação
**12 métricas agrupadas em 4 dimensões:**

| Dimensão | Métricas | Propósito |
|----------|----------|-----------|
| **Semântica** | BERTScore, Hellinger | Similaridade de significado |
| **Estrutura** | ROUGE-L, ROUGE-1, BLEU | Similaridade estrutural e lexical |
| **Distância** | Cosine, Euclidean, Angular | Distância no espaço vetorial |
| **Complexidade** | Bloom_Similarity, Bloom_Wasserstein | Nível cognitivo |
| **Consistência** | Jaccard, Krippendorff_Alpha | Concordância e sobreposição |

### 3.4 Análise de dados

#### **Para RQ1-SCI (complexidade vs qualidade)**
1. **Classificação das etapas ADD** por:
   - Abstração (1-5 escala)
   - Complexidade cognitiva (Taxonomia de Bloom)
   - Especialistas independentes (3 avaliadores, Krippendorff's α > 0.8)
2. **ANOVA de medidas repetidas**: Etapa × Métricas (usando apenas dados LLM-)
3. **Correlação de Spearman**: Rank complexidade × Rank qualidade

#### **Para RQ2-SCI (qualidade por etapa)**
1. **Comparação pareada** para cada etapa:
   - LLM- vs Expert_Solution (desempenho bruto do LLM)
   - Expert- vs Expert_Solution (efetividade após refinamento)
2. **Análise de gap**: (Expert- - LLM-) = valor do refinamento humano
3. **Ranking de etapas**: Onde o LLM performa melhor/pior

#### **Para RQ3-SCI (dimensões e métricas)**
1. **Análise de correlação inter-métricas**
2. **Análise de componentes principais (PCA)**:
   - Redução para dimensões latentes
   - Variância explicada por cada componente
3. **Validação cruzada**:
   - Correlação com avaliações humanas (survey)
   - Consistência através de etapas
4. **Seleção de métricas ótimas**:
   - Maximizar informação, minimizar redundância
   - Algoritmo de seleção forward-backward

#### **Para RQ4-SCI (padrões de refinamento)**
1. **Análise delta quantitativa**:
   - Distância edit (Expert- vs LLM-)
   - Proporções de adição/remoção/modificação
2. **Padrões por tipo de conteúdo**:
   - Conceitual vs técnico
   - Estrutural vs descritivo
3. **Correlação** com qualidade final

### 3.5 Validação experimental
- **Consistência inter-avaliador** para classificação de complexidade
- **Teste-reteste** para estabilidade das métricas
- **Validação externa** com benchmark público (se disponível)

---

## 📊 **4. ANÁLISE ESTATÍSTICA (Científica)**

### Modelos estatísticos principais:
1. **Modelo linear misto**:
   ```
   Metric_ijk = μ + Stage_i + Group_j + (Stage×Group)_ij + ε_ijk
   ```
   Onde: Stage = etapa ADD, Group = (LLM-, Expert-)

2. **Análise de caminho (Path Analysis)**:
   ```
   Complexity → LLM_Performance → Human_Refinement → Final_Quality
   ```

3. **Análise de clusters**:
   - Agrupar etapas por perfil de desempenho do LLM
   - Identificar tipos de tarefas onde LLMs se saem bem/mal

### Testes específicos:
- **Teste de Friedman**: Diferenças entre etapas (não paramétrico)
- **Correlação canônica**: Relação entre múltiplas métricas e avaliações humanas
- **Análise de sensibilidade**: Robustez das conclusões às escolhas métricas

---

## 📝 **5. ESTRUTURA DO PAPER (Científica)**

```
1. INTRODUCTION
   1.1 The Rise of LLMs in Software Engineering
   1.2 The Challenge of Evaluating LLMs in Architectural Design
   1.3 Research Questions and Contributions
   1.4 Paper Structure

2. RELATED WORK
   2.1 LLM Evaluation in Software Engineering
   2.2 Software Architecture Quality Metrics
   2.3 Multi-Metric Evaluation Frameworks
   2.4 Benchmarks for AI-Assisted Design

3. METHODOLOGY
   3.1 Experimental Design
        - Three-Level Quality Comparison
        - ADD Process as Evaluation Framework
    
   3.2 Dataset Description
        - Expert_Solution (Ground Truth)
        - LLM Outputs (Gemini 3.0)
        - Human-Refined Artifacts
    
   3.3 Metric Suite
        - 12 Metrics Across 5 Dimensions
        - Rationale for Metric Selection
        - Calculation Procedures
    
   3.4 Analysis Framework
        - Statistical Comparison Methods
        - Dimensionality Reduction
        - Validation Approaches

4. RESULTS
   4.1 RQ1: Task Complexity vs. LLM Performance
        - Stage Complexity Classification
        - Performance Variation Across Stages
        - Complexity-Performance Correlation
    
   4.2 RQ2: Artifact Quality Across ADD Stages
        - LLM Raw Performance Benchmark
        - Human Refinement Effectiveness
        - Stage-by-Stage Quality Analysis
    
   4.3 RQ3: Optimal Metric Selection
        - Inter-Metric Correlation Analysis
        - Principal Component Extraction
        - Recommended Metric Subset
        - Validation Against Human Judgment
    
   4.4 RQ4: Human Refinement Patterns
        - Quantitative Delta Analysis
        - Refinement Type Distribution
        - Impact on Final Quality

5. DISCUSSION
   5.1 LLM Capabilities and Limitations in Architectural Design
        - Where LLMs Excel
        - Where LLMs Struggle
        - Complexity Thresholds
    
   5.2 The Evaluation Framework
        - Metric Selection Guidelines
        - Dimensional Coverage
        - Practical Implementation
    
   5.3 Implications for LLM Development
        - Training Data Requirements
        - Architectural Knowledge Integration
        - Evaluation Protocol Suggestions
    
   5.4 Limitations and Future Work
        - Dataset Limitations
        - Metric Scope
        - Generalizability Concerns

6. CONCLUSION
   6.1 Key Findings
   6.2 Framework Contributions
   6.3 Practical Applications
   6.4 Research Agenda

REFERENCES
APPENDICES
   A. Complete Metric Formulas
   B. Statistical Test Details
   C. Correlation Matrices
   D. PCA Loadings and Scores
   E. Raw Performance Data
```

---

## ⏱️ **6. CRONOGRAMA (Científica)**

| Semana | Atividade |
|--------|-----------|
| 1-2 | Cálculo sistemático de todas as métricas |
| 3-4 | Análise estatística preliminar |
| 5-6 | PCA e redução dimensional |
| 7-8 | Modelagem estatística avançada |
| 9-10 | Redação dos resultados |
| 11-12 | Validação e submissão |

---

## 📈 **7. CONTRIBUIÇÕES ESPERADAS (Científica)**

1. **Benchmark** para avaliação de LLMs em design arquitetural
2. **Framework multidimensional** de métricas validadas
3. **Análise sistemática** do desempenho do Gemini em ADD
4. **Protocolo de avaliação** reproduzível para pesquisas futuras
5. **Insights** sobre capacidades e limitações de LLMs em arquitetura

---

## 🔄 **8. SINERGIA ENTRE OS PAPERS**

| Aspecto | Paper Educação | Paper Científico |
|---------|----------------|------------------|
| **Foco** | Processos de aprendizagem | Avaliação de desempenho |
| **Métodos** | Qualitativo-dominante | Quantitativo-dominante |
| **Dados** | Estratégias, percepções | Métricas, comparações |
| **Contribuição** | Diretrizes pedagógicas | Framework de avaliação |
| **Audience** | Educadores, pesquisadores em educação | Pesquisadores em IA, engenharia de software |
| **Venue** | SIGCSE, CSEE&T, TSE Education | ICSE, FSE, ESEC/FSE, JSS |

---

## 🎯 **9. PRÓXIMOS PASSOS PARA AMBOS**

1. **Organizar repositório** com estrutura clara para ambos os papers
2. **Definir autoria** e contribuições de cada autor
3. **Escolher venues** alvo para submissão
4. **Criar cronograma compartilhado** com marcos conjuntos
5. **Designar responsabilidades** específicas para cada paper

---

## 💡 **10. DIFERENCIAIS DOS PAPERS**

### **Paper Educação:**
- Contribuição única na **interseção educação+IA+arquitetura**
- **Aplicabilidade imediata** para educadores
- **Base teórica sólida** em aprendizagem experiencial

### **Paper Científico:**
- **Rigor metodológico** na avaliação de LLMs
- **Framework reproduzível** para pesquisas futuras
- **Análise multidimensional** inovadora

---

## 📋 **CHECKLIST DE PRONTIDÃO**

Para **Paper Educação**:
- [ ] Análise qualitativa dos refinamentos concluída
- [ ] Taxonomia de estratégias validada
- [ ] Correlações com survey calculadas
- [ ] Implicações pedagógicas delineadas

Para **Paper Científico**:
- [ ] Todas as métricas calculadas sistematicamente
- [ ] Análises estatísticas principais realizadas
- [ ] PCA e redução dimensional completas
- [ ] Framework de avaliação definido

---

**Pronto para implementar ambos os roteiros?** Cada um tem foco distinto mas compartilha a base de dados, permitindo economia de esforço na análise inicial! 🚀
