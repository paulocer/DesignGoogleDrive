# 📘 **Roteiro Completo de Pesquisa Revisado: Impacto de LLMs no Processo de Design Arquitetural Baseado em ADD**

## 🎯 **1. CONTEXTO E MOTIVAÇÃO**

**Tema geral:**  
Uso de Large Language Models (LLMs) como assistentes no processo de design de arquitetura de software baseado no método Attribute-Driven Design (ADD).

**Problema de pesquisa:**  
Ainda não está claro como o uso de LLMs afeta:  
1. **A aprendizagem** dos conceitos e práticas de arquitetura por estudantes.  
2. **A qualidade** dos artefatos arquiteturais produzidos.  
3. **O desenvolvimento** de competências profissionais como pensamento crítico e autonomia.

**Experimento realizado:**  
- 64 estudantes em duplas.  
- Cada dupla usou o Gemini 3.0 como "assistente de arquitetura".  
- Seguiram o processo ADD 3.0 para projetar um sistema similar ao Google Drive.  
- Geraram dois conjuntos de artefatos: 
  - `LLM-` (saída bruta do Gemini)
  - `Expert-` (versão refinada pelos estudantes a partir da saída do LLM)
- Um **gabarito especialista** (`Expert_Solution`) foi fornecido como referência ideal.  
- Um **survey** foi aplicado ao final para coletar percepções sobre o processo.

---

## 📊 **2. DADOS DISPONÍVEIS**

### 2.1 Artefatos
1. **`Expert_Solution.md`** – Solução de referência (gabarito completo).  
2. **`LLM-` files** – Saídas brutas do Gemini em cada etapa (prompts seguidos).  
3. **`Expert-` files** – Versões refinadas pelos estudantes (após revisão crítica).
   - **Importante**: Não há grupo "estudantes sem LLM". Todos os estudantes usaram LLM.
   - A comparação é entre: **LLM bruto** vs **Estudante refinado** vs **Solução especialista**

### 2.2 Survey (64 respondentes)
- **Identificação**: experiência prévia, conhecimento de ADD, uso de LLM.  
- **Avaliação por etapa**: qualidade da saída do LLM, esforço de refinamento, vantagem percebida.  
- **Avaliação geral**: aprendizado, pensamento crítico, autonomia, tempo gasto.  
- **Comparações**: preferência de uso de LLM, impacto no aprendizado.

### 2.3 Métricas calculadas
12 métricas de similaridade/texto para **três comparações principais**:
1. **Comparação A**: `Expert_Solution` × `Expert-` files (estudantes refinados)
2. **Comparação B**: `Expert_Solution` × `LLM-` files (saída bruta do Gemini)
3. **Comparação C**: `Expert-` files × `LLM-` files (gap de refinamento)

Métricas:
1. BERTScore  
2. Cosine Distance  
3. Euclidean Distance  
4. Angular Distance  
5. ROUGE-L  
6. ROUGE-1  
7. Jaccard  
8. BLEU  
9. Hellinger  
10. Bloom_Similarity  
11. Bloom_Wasserstein  
12. Krippendorff_Alpha

---

## 🔬 **3. PERGUNTAS DE PESQUISA (REVISADAS)**

### **ÁREA DE EDUCAÇÃO**

#### **RQ1**:  
*"Quais estratégias de interação e refinamento os estudantes adotam ao utilizar LLMs no desenvolvimento de arquiteturas de software? Como essas estratégias se relacionam com o desenvolvimento de competências profissionais?"*

#### **RQ2**:  
*"Como o uso de LLMs nas etapas do processo ADD influencia a curva de aprendizagem dos estudantes em relação aos conceitos e práticas de arquitetura de software?"*

#### **RQ3**:  
*"De que forma a utilização de LLMs como ferramenta de apoio afeta o desenvolvimento do pensamento crítico e da autonomia dos estudantes em decisões arquiteturais?"*

### **ÁREA CIENTÍFICA**

#### **RQ4**:  
*"Como o nível de abstração e complexidade das tarefas de design arquitetural influencia a qualidade das respostas geradas por LLMs no contexto do processo ADD?"*

#### **RQ5**:  
*"Qual a relação entre o uso de LLMs e a qualidade dos artefatos produzidos em cada etapa do processo ADD, desde a elicitação de requisitos até a definição de táticas arquiteturais?"*

#### **RQ6**:  
*"Quais dimensões de qualidade e métricas, que associadas, são mais relevantes para avaliar o impacto do uso de LLMs no processo de design de arquitetura de software baseado em ADD?"*

---

## 🧪 **4. METODOLOGIA DE PESQUISA REVISADA**

### 4.1 Tipo de estudo
- **Misto (quantitativo + qualitativo)**.
- **Análise comparativa**: Três níveis de qualidade:
  1. **Nível Ideal**: `Expert_Solution` (gabarito)
  2. **Nível Estudante Refinado**: `Expert-` files (LLM + refinamento humano)
  3. **Nível LLM Bruto**: `LLM-` files (apenas IA)
- **Correlacional**: Relacionando percepções do survey com métricas objetivas.

### 4.2 Coleta de dados
| Fonte | Dados coletados | Uso nas RQs |
|-------|-----------------|-------------|
| **Artefatos LLM** | `LLM-` files (saída bruta do Gemini) | RQ4, RQ5, RQ6 |
| **Artefatos Estudantes** | `Expert-` files (refinados) | RQ1, RQ2, RQ5 |
| **Solução Referência** | `Expert_Solution.md` | Benchmark para todas RQs |
| **Survey** | 64 respostas | RQ1, RQ2, RQ3 |
| **Métricas** | 12 métricas × 3 comparações | RQ4, RQ5, RQ6 |

### 4.3 Análise de dados

#### **Para RQ1 (estratégias de interação e refinamento)**
1. **Análise delta qualitativa**: Comparar `LLM-` vs `Expert-` files:
   - Categorizar tipos de modificações: correções técnicas, expansões, reorganizações, validações.
   - Identificar padrões: onde os estudantes mais corrigiram vs onde aceitaram o output do LLM.
2. **Triangulação com survey**: Cruzar com "refinement effort" e "LLM output quality".
3. **Análise de correlação**: Esforço de refinamento × experiência prévia dos estudantes.

#### **Para RQ2 (curva de aprendizagem)**
1. **Análise longitudinal implícita**: Comparar refinamentos ao longo das etapas do ADD.
2. **Correlação**: Frequência de uso de LLM (survey) × proximidade com `Expert_Solution` (métricas).
3. **Análise por experiência**: Dividir estudantes por experiência prévia e comparar aprendizado.

#### **Para RQ3 (pensamento crítico e autonomia)**
1. **Métrica de autonomia**: Diferença entre `Expert-` e `LLM-` files (quanto foi alterado).
2. **Correlação**: Autonomia medida × autonomia percebida (survey).
3. **Análise de regressão**: Experiência prévia → uso de LLM → autonomia.

#### **Para RQ4 (complexidade vs qualidade do LLM)**
1. **Classificação das etapas ADD**: 7 etapas classificadas por especialistas em complexidade.
2. **ANOVA**: Testar diferenças nas métricas da **Comparação B** (`Expert_Solution` × `LLM-`) entre etapas.
3. **Correlação ranking**: Complexidade da etapa × rank de qualidade do LLM.

#### **Para RQ5 (LLM vs qualidade dos artefatos)**
1. **Comparação hierárquica**:
   - Nível 1: `Expert_Solution` vs `Expert-` (quão perto os estudantes chegaram do ideal)
   - Nível 2: `Expert_Solution` vs `LLM-` (quão bom é o LLM sozinho)
   - Nível 3: `Expert-` vs `LLM-` (valor agregado pelo refinamento humano)
2. **Teste t pareado**: Comparar métricas entre os três níveis.
3. **Correlação cruzada**: Percepção de qualidade (survey) × métricas objetivas.

#### **Para RQ6 (seleção de métricas)**
1. **Matriz de correlação** entre as 12 métricas.
2. **Análise de componentes principais (PCA)**: Identificar dimensões latentes.
3. **Validação externa**: Correlacionar cada métrica com avaliação humana do survey.
4. **Seleção ótima**: Escolher subconjunto mínimo que maximize informação.

---

## 📈 **5. CRONOGRAMA DE EXECUÇÃO REVISADO**

| Fase | Atividades | Duração |
|------|-----------|---------|
| **1. Preparação** | - Estruturar 3 conjuntos: LLM-, Expert-, Expert_Solution<br>- Calcular todas 12 métricas para 3 comparações<br>- Preparar dados do survey | 2 semanas |
| **2. Análise quantitativa** | - Estatísticas descritivas por etapa<br>- ANOVA para diferenças entre etapas<br>- Testes t para diferenças entre níveis<br>- PCA para redução de métricas | 3 semanas |
| **3. Análise qualitativa** | - Análise de conteúdo dos refinamentos (LLM- vs Expert-)<br>- Categorização de tipos de modificação<br>- Triangulação com respostas do survey | 2 semanas |
| **4. Integração** | - Cruzar dados quantitativos e qualitativos<br>- Responder cada RQ com evidências múltiplas<br>- Identificar padrões e contradições | 2 semanas |
| **5. Redação** | - Paper completo<br>- Figuras e tabelas de resultados<br>- Discussão integrada<br>- Submissão | 3 semanas |

**Total**: 12 semanas

---

## 📋 **6. PLANO DE ANÁLISE ESTATÍSTICA REVISADO**

### 6.1 Variáveis
- **Independentes**:  
  - Etapa do ADD (1-7, classificada por complexidade)  
  - Experiência prévia do estudante (survey)  
  - Frequência de uso de LLM (survey)  
  - Tipo de artefato (LLM- vs Expert-)

- **Dependentes**:  
  - **Métricas objetivas** (12 valores por comparação)  
  - **Gap de refinamento** (diferença Expert- - LLM-)  
  - **Percepções subjetivas** (survey Likert scales)

### 6.2 Comparações estatísticas principais
| Comparação | Objetivo | Teste estatístico |
|------------|----------|-------------------|
| **Expert_Solution vs Expert-** | Qualidade final dos estudantes | Teste t, Correlação |
| **Expert_Solution vs LLM-** | Qualidade do LLM sozinho | ANOVA por etapa |
| **Expert- vs LLM-** | Valor do refinamento humano | Teste t pareado |
| **Entre etapas** | Variação por complexidade | ANOVA de medidas repetidas |

### 6.3 Análises específicas por RQ
- **RQ1**: Análise de conteúdo + correlação com "refinement effort"
- **RQ2**: Regressão: experiência → uso de LLM → proximidade com Expert_Solution
- **RQ3**: Diferença Expert- vs LLM- como proxy de autonomia
- **RQ4**: ANOVA: etapa × métricas (usando apenas dados LLM-)
- **RQ5**: Comparação dos 3 níveis + correlação com percepções
- **RQ6**: PCA + regressão para seleção de métricas

---

## 📝 **7. ESTRUTURA DO PAPER (ESBOÇO REVISADO)**

```
TÍTULO: From AI Output to Expert Refinement: Assessing LLM Assistance in 
        Software Architecture Design Learning

1. INTRODUCTION
   - Rise of LLMs in software engineering education
   - Gap: Unknown impact on architectural thinking development
   - Our study: LLM-assisted ADD process with refinement
   - 6 Research Questions
   - Key contributions

2. BACKGROUND & RELATED WORK
   - ADD 3.0 methodology
   - LLMs in software design education
   - Human-AI collaboration patterns
   - Evaluation metrics for architectural artifacts

3. RESEARCH METHODOLOGY
   3.1 Experimental Design
        - Participants: 64 students in pairs
        - Case: Google Drive architecture design
        - LLM: Gemini 3.0 as assistant
    
   3.2 Data Collection
        - Three artifact levels: Expert_Solution, Expert- (refined), LLM- (raw)
        - Survey: perceptions, experiences, self-assessment
        - 12 similarity metrics for pairwise comparisons
    
   3.3 Analysis Framework
        - Quantitative: statistical comparisons of three levels
        - Qualitative: analysis of refinement strategies
        - Mixed: triangulation of objective and subjective data

4. RESULTS
   4.1 RQ1: Refinement Strategies and Professional Competencies
        - Taxonomy of refinement types
        - Correlation with self-reported skill development
    
   4.2 RQ2: Learning Curve in ADD with LLM Assistance
        - Progression through ADD stages
        - Experience level × learning outcomes
    
   4.3 RQ3: Critical Thinking and Autonomy Development
        - Refinement gap as autonomy measure
        - Survey perceptions of independence
    
   4.4 RQ4: Task Complexity vs. LLM Output Quality
        - Stage-by-stage analysis of LLM performance
        - Complexity thresholds for reliable LLM assistance
    
   4.5 RQ5: LLM Usage vs. Artifact Quality
        - Three-level quality comparison
        - Human refinement value quantification
    
   4.6 RQ6: Optimal Evaluation Metrics
        - Metric correlation analysis
        - PCA-derived quality dimensions
        - Recommended metric subset

5. DISCUSSION
   5.1 Synthesis of Findings
        - When LLMs help vs. hinder learning
        - Optimal human-AI collaboration patterns
    
   5.2 Implications for Education
        - Pedagogical guidelines for LLM integration
        - Assessment strategies for AI-assisted work
    
   5.3 Implications for Practice
        - LLM as architectural assistant: promises and pitfalls
        - Validation frameworks for AI-generated designs
    
   5.4 Limitations and Threats to Validity
        - Sample characteristics
        - Single case study
        - Self-report biases

6. CONCLUSION AND FUTURE WORK
   6.1 Summary of Contributions
   6.2 Recommendations for Educators and Practitioners
   6.3 Future Research Directions

7. REFERENCES

APPENDICES
   A. Complete Survey Instrument
   B. ADD Stage Complexity Ratings
   C. Refinement Taxonomy with Examples
   D. Complete Statistical Results
   E. Ethics Approval and Consent Forms
```

---

## ⚠️ **8. LIMITAÇÕES E MITIGAÇÕES REVISADAS**

### 8.1 Limitações principais
1. **Não há grupo controle sem LLM** - Não podemos isolar efeito do LLM vs. aprendizagem tradicional.
2. **Auto-seleção de refinamento** - Estudantes escolhem quanto e onde refinar.
3. **Caso único** - Apenas Google Drive, generalização limitada.
4. **LLM único** - Apenas Gemini 3.0, outros LLMs podem ter desempenho diferente.

### 8.2 Mitigações
1. **Análise de três níveis**: Comparamos solução ideal, LLM bruto e refinado.
2. **Análise de gap**: Diferença Expert- vs LLM- revela intervenção humana.
3. **Triangulação**: Métricas objetivas + survey + análise qualitativa.
4. **Transparência**: Reportar limitações claramente.

### 8.3 Novas oportunidades de análise
1. **Trajetórias de refinamento**: Como os estudantes evoluíram através das etapas.
2. **Padrões de aceitação/rejeição**: Que partes do output do LLM foram mais aceitas.
3. **Aprendizagem vicária**: Estudantes aprendendo com os erros/sucessos do LLM.

---

## 🎯 **9. CONTRIBUIÇÕES ESPERADAS (REVISADAS)**

### Para Educação em Engenharia de Software
1. **Modelo pedagógico** para ensino de arquitetura com assistentes de IA.
2. **Exercícios estruturados** que ensinem a criticar e melhorar outputs de IA.
3. **Rubricas de avaliação** para trabalhos feitos com assistência de LLM.

### Para Pesquisa em IA Aplicada à Engenharia
1. **Benchmark** de desempenho de LLM em tarefas arquiteturais complexas.
2. **Taxonomia de refinamentos** humano-IA em design de software.
3. **Métricas validadas** para avaliação de artefatos gerados com IA.

### Para Prática Profissional
1. **Padrões de colaboração** eficazes entre arquitetos e assistentes de IA.
2. **Checklists de validação** para designs sugeridos por IA.
3. **Alertas sobre riscos**: quando confiar e quando duvidar do LLM.

---

## 🔮 **10. PRÓXIMOS PASSOS IMEDIATOS**

1. **Estruturar o dataset**:
   ```python
   # Estrutura sugerida
   artifacts = {
       'expert_solution': '.../Expert_Solution.md',
       'llm_outputs': {
           'stage_1': '.../LLM-UserStories.md',
           'stage_2': '.../LLM-SystemContext.md',
           # ... todas as etapas
       },
       'expert_refined': {
           'stage_1': '.../Expert-UserStories.md',
           # ... todas as etapas
       }
   }
   ```

2. **Calcular métricas automatizadas**:
   - Script para calcular 12 métricas × 3 comparações × 7 etapas.

3. **Codificar dados do survey**:
   - Converter Likert para numérico.
   - Preparar para análise estatística.

4. **Começar análise exploratória**:
   - Estatísticas descritivas básicas.
   - Visualizações iniciais.

---

## 📚 **11. REFERÊNCIAS-CHAVE ATUALIZADAS**

1. **ADD e Educação**: Cervantes & Kazman (2016) + seu próprio material `AttributeDrivenDesign.md`.
2. **LLMs em Educação**: Kasneci et al. (2023) *ChatGPT for Good?*
3. **Avaliação de Texto**: Reimers & Gurevych (2019) *Sentence-BERT*.
4. **Colaboração Humano-IA**: Bansal et al. (2021) *Beyond Accuracy*.
5. **Design do Google Drive**: Material do experimento (`CaseStudyDescription.md`).

---

## 💡 **12. INSIGHTS ESPERADOS**

Baseado na estrutura do experimento, antecipamos:

1. **O LLM será melhor em tarefas estruturadas** (ex.: QA scenarios templates) do que em decisões criativas.
2. **Estudantes experientes refinarão menos** (sabem o que querem) mas com mais precisão.
3. **O maior valor do LLM será na geração de ideias** iniciais, não na decisão final.
4. **A autonomia percebida correlacionará negativamente** com dependência do LLM, mas **positivamente** com qualidade final.

---

Este roteiro **completo e revisado** incorpora a correção sobre os grupos de estudo e fornece um plano detalhado para sua pesquisa. Agora você tem um mapa claro desde a organização dos dados até a redação do paper final.

**Pronto para implementar?** Comece organizando os arquivos e calculando as primeiras métricas! 🚀
