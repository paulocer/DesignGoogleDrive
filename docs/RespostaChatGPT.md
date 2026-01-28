Excelente conjunto de perguntas — isso aqui já está com **cara de artigo/journal** 😄
Vou responder **de forma sistemática**, alinhando **cada RQ** aos quatro itens que você pediu, **explicitamente considerando o experimento descrito no README.md**, os **artefatos ADD**, as **três comparações**, as **12 métricas** e o **survey com 64 estudantes**.

Vou separar em **Área de Educação** e **Área Científica**, mantendo **coerência metodológica entre todas**.

---

# 🧑‍🏫 ÁREA DE EDUCAÇÃO

---

## 🎓 RQ1 — Educação

**“Quais estratégias de interação os estudantes adotam ao utilizar LLMs no desenvolvimento de arquiteturas de software?
Como essas estratégias se relacionam com o desenvolvimento de competências profissionais?”**

### 🔹 Objetivo geral

Identificar **padrões de uso e estratégias cognitivas** adotadas pelos estudantes ao interagir com LLMs durante o processo ADD e analisar **como essas estratégias se relacionam com competências profissionais em arquitetura de software**.

### 🔹 Objetivos específicos

* Classificar os estudantes segundo **tipologias de uso da LLM** (dependente, assistido, crítico/refinador).
* Relacionar essas tipologias com:

  * Qualidade dos artefatos ADD.
  * Perfil cognitivo (Bloom).
  * Percepção de aprendizagem e autonomia (survey).
* Comparar estratégias humanas vs. estratégias mediadas por LLM.

### 🔹 Como atingir o objetivo

1. **Análise dos artefatos README.md concatenados**, usando:

   * Expert_Solution como gabarito.
   * Expert_Student e LLM_Student como produções observadas.
2. **Comparações-chave**:

   * Expert_Solution × Expert_Student
   * Expert_Solution × LLM_Student
   * Expert_Student × LLM_Student
3. **Inferência de estratégias** a partir de:

   * Divergência semântica (BERTScore, Cosine, Angular).
   * Cobertura conceitual (ROUGE, Jaccard).
   * Complexidade cognitiva (Bloom_Similarity, Bloom_Wasserstein).
4. **Corroboração com survey** (64 estudantes):

   * Autoavaliação de estratégia, confiança, dependência e reflexão.

### 🔹 Metodologia de pesquisa

* **Método misto (mixed-methods)**:

  * Quantitativo: métricas NLP + Bloom + Krippendorff.
  * Qualitativo: análise de padrões de interação + survey.
* **Análise comparativa baseada em artefatos**.
* **Triangulação**: métricas automáticas × survey × análise pedagógica.

### 🔹 Contribuição esperada

* Um **modelo empírico de estratégias de interação humano–LLM**.
* Evidência de quais estratégias **promovem competências profissionais reais** (pensamento arquitetural, justificativa de decisões).
* Subsídios para **uso pedagógico responsável de LLMs**.

---

## 🎓 RQ2 — Educação

**“Como o uso de LLMs nas etapas do processo ADD influencia a curva de aprendizagem dos estudantes?”**

### 🔹 Objetivo geral

Analisar o impacto do uso de LLMs ao longo das **etapas do ADD** na **progressão conceitual e cognitiva** dos estudantes.

### 🔹 Objetivos específicos

* Medir a evolução da qualidade dos artefatos ADD ao longo das fases.
* Avaliar mudanças no nível cognitivo atingido (Bloom).
* Comparar trajetórias de aprendizagem:

  * Sem LLM (Expert_Student).
  * Com LLM (LLM_Student + refinamento).

### 🔹 Como atingir o objetivo

1. Analisar artefatos **por etapa do ADD**:

   * Requisitos, QAs, cenários, arquitetura, decisões.
2. Calcular métricas **por fase**:

   * Similaridade semântica (BERTScore).
   * Estrutura e completude (ROUGE-L).
   * Complexidade cognitiva (Bloom metrics).
3. Construir **curvas de aprendizagem**:

   * Evolução Expert_Student vs. LLM_Student.
4. Relacionar com respostas do survey:

   * Dificuldade percebida por etapa.
   * Clareza conceitual.

### 🔹 Metodologia de pesquisa

* **Estudo longitudinal intra-artefatos**.
* Análise de progressão cognitiva baseada em Bloom.
* Comparação controlada entre grupos.

### 🔹 Contribuição esperada

* Evidência empírica de **onde LLMs ajudam e onde atrapalham** no ADD.
* Identificação de etapas com maior risco de **aprendizagem superficial**.
* Base para **redesenho curricular** de disciplinas de arquitetura.

---

## 🎓 RQ3 — Educação

**“De que forma a utilização de LLMs afeta o desenvolvimento do pensamento crítico e da autonomia?”**

### 🔹 Objetivo geral

Avaliar o impacto do uso de LLMs no **pensamento crítico**, **autonomia decisória** e **capacidade de justificar escolhas arquiteturais**.

### 🔹 Objetivos específicos

* Medir divergência criativa vs. mera reprodução do gabarito.
* Avaliar justificativas arquiteturais explícitas.
* Relacionar autonomia percebida (survey) com métricas objetivas.

### 🔹 Como atingir o objetivo

* Analisar **distância controlada**:

  * Nem cópia (alta similaridade),
  * Nem devaneio (alta distância).
* Usar:

  * BERTScore + Bloom_Wasserstein.
* Relacionar com survey:

  * “Confiei demais na LLM?”
  * “Questionei as respostas?”

### 🔹 Metodologia de pesquisa

* Análise inferencial de autonomia cognitiva.
* Cruzamento métricas–percepção.
* Análise pedagógica interpretativa.

### 🔹 Contribuição esperada

* Evidência concreta sobre **dependência cognitiva vs. ampliação crítica**.
* Base científica para políticas educacionais sobre LLMs.

---

# 🔬 ÁREA CIENTÍFICA

---

## 🧪 RQ1 — Científica

**“Como o nível de abstração e complexidade das tarefas influencia a qualidade das respostas das LLMs no ADD?”**

### 🔹 Objetivo geral

Investigar como a **natureza da tarefa arquitetural** afeta o desempenho das LLMs.

### 🔹 Objetivos específicos

* Comparar desempenho da LLM em tarefas:

  * Estruturadas (requisitos).
  * Abstratas (táticas, decisões).
* Identificar limites cognitivos da LLM.

### 🔹 Como atingir o objetivo

* Segmentar artefatos por nível de abstração.
* Aplicar métricas semânticas + Bloom.
* Analisar degradação ou melhoria por tipo de tarefa.

### 🔹 Metodologia

* Estudo experimental comparativo.
* NLP + análise cognitiva.

### 🔹 Contribuição esperada

* Caracterização científica das **fronteiras de atuação das LLMs** em arquitetura.

---

## 🧪 RQ2 — Científica

**“Qual a relação entre uso de LLMs e a qualidade dos artefatos em cada etapa do ADD?”**

### 🔹 Objetivo geral

Avaliar quantitativamente o impacto das LLMs na **qualidade arquitetural** dos artefatos ADD.

### 🔹 Como atingir o objetivo

* Comparações sistemáticas nas 3 relações.
* Análise multivariada das 12 métricas por etapa.

### 🔹 Metodologia

* Análise estatística multivariada.
* Correlação entre métricas e etapas ADD.

### 🔹 Contribuição esperada

* Evidência empírica do **valor real (ou ilusório)** das LLMs no design arquitetural.

---

## 🧪 RQ3 — Científica

**“Quais métricas combinadas são mais relevantes para avaliar o impacto do uso de LLMs no ADD?”**

### 🔹 Objetivo geral

Identificar um **núcleo mínimo de métricas robustas** para avaliação arquitetural mediada por LLM.

### 🔹 Como atingir o objetivo

* Análise de correlação e redundância.
* Validação cruzada com Krippendorff’s Alpha.
* Construção de índices compostos (ex.: IACE).

### 🔹 Metodologia

* Análise estatística + confiabilidade.
* Seleção de métricas baseada em evidência.

### 🔹 Contribuição esperada

* Um **framework métrico replicável** para pesquisas futuras.
* Avanço metodológico na avaliação de LLMs.

---

# 📊 Papel do Survey (64 estudantes)

O survey atua como:

* **Validação externa** das métricas automáticas.
* Fonte de variáveis latentes (autonomia, confiança, percepção).
* Base para triangulação pedagógica.

---

## 🧠 Em síntese

Você tem aqui:

* Um **experimento educacional sólido**.
* Um **framework científico original**.
* Um pipeline **replicável, mensurável e publicável**.

Se quiser, no próximo passo posso:

* Converter isso diretamente em **seção de artigo (IMRaD)**
* Ou desenhar um **modelo conceitual visual (diagrama)**
* Ou formalizar hipóteses (H1–Hn) com testes estatísticos associados
