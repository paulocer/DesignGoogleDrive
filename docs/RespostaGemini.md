Com base nos documentos fornecidos, estruturei um plano de pesquisa detalhado para cada uma das Perguntas de Pesquisa (RQ) listadas no arquivo `Perguntas de Pesquisa.txt` , contextualizando-as com o experimento descrito no `README.md` e o `Survey`.

O experimento consiste em estudantes trabalhando em pares, assistidos pelo Gemini 3.0, para projetar a arquitetura do "Google Drive" usando o método ADD (Attribute-Driven Design).

---

### **Área de Educação**

**RQ1: "Quais estratégias de interação os estudantes adotam ao utilizar LLMs no desenvolvimento de arquiteturas de software? Como essas estratégias se relacionam com o desenvolvimento de competências profissionais?"** 

1. **Objetivo Geral e Específicos:**
* **Geral:** Analisar o comportamento dos estudantes na engenharia de prompt e na orquestração da IA.
* **Específicos:** Classificar os tipos de prompts utilizados; identificar se os estudantes agem como "pilotos" (ativos) ou "passageiros" (passivos); correlacionar a qualidade da interação com a percepção de vantagem relatada no survey.

2. **Como fazer para atingir o objetivo:**
* Analisar os arquivos `LLM-` gerados para ver os *inputs* (contexto adicionado pelos alunos além dos templates).
* Comparar os arquivos `LLM-` (saída bruta) com os `Expert-` (refinados) para ver o grau de intervenção técnica.
* Cruzar com as respostas do Survey, especificamente a questão sobre a frequência de uso de LLMs e a experiência prévia.

3. **Metodologia:**
* **Análise Qualitativa:** Codificação dos prompts e logs de interação para identificar padrões (ex: refinamento iterativo vs. aceitação cega).
* **Métricas:** Uso do *Krippendorff_Alpha*  para validar a concordância entre pesquisadores na classificação das estratégias dos alunos.

4. **Contribuição Esperada:**
* Um guia de melhores práticas para ensino de Engenharia de Software com IA, identificando quais estratégias de interação levam a melhores resultados de aprendizado e produtos de software.

---

**RQ2: "Como o uso de LLMs nas etapas do processo ADD influencia a curva de aprendizagem dos estudantes em relação aos conceitos e práticas de arquitetura de software?"** 

1. **Objetivo Geral e Específicos:**
* **Geral:** Avaliar se o LLM atua como um andaime cognitivo (*scaffolding*) ou uma muleta.
* **Específicos:** Medir a redução da dificuldade percebida em etapas complexas do ADD (como táticas e *drivers*); verificar se o conhecimento prévio de ADD influencia a eficácia do uso da ferramenta.

2. **Como fazer para atingir o objetivo:**
* Utilizar os dados do Survey onde os alunos avaliam a "Qualidade da saída do LLM" vs. "Esforço de refinamento" para cada etapa (Histórias de Usuário, Contexto, Atributos de Qualidade, etc.).

* Comparar o desempenho de grupos com conhecimento teórico prévio vs. nenhum conhecimento.

3. **Metodologia:**
* **Análise Correlacional:** Relacionar a variável "Conhecimento prévio de ADD" com o "Esforço de refinamento".
* **Métricas Cognitivas:** Uso de *Bloom_Similarity* e *Bloom_Wasserstein*  para medir se os artefatos finais (`Expert_Student`) demonstram um nível cognitivo (Taxonomia de Bloom) superior ao que seria esperado sem assistência, ou se há regressão.

4. **Contribuição Esperada:**
* Evidências sobre como integrar LLMs em currículos de computação sem prejudicar a aquisição de conceitos fundamentais, sugerindo onde a IA acelera o aprendizado e onde ela pode obstruí-lo.

---

**RQ3: "De que forma a utilização de LLMs como ferramenta de apoio afeta o desenvolvimento do pensamento crítico e da autonomia dos estudantes em decisões arquiteturais?"** 

1. **Objetivo Geral e Específicos:**
* **Geral:** Investigar o risco de "viés de automação" (aceitação passiva) versus o exercício de crítica técnica.
* **Específicos:** Quantificar o quanto os alunos alteram a sugestão da IA; identificar se os alunos corrigem alucinações ou erros sutis de design.

2. **Como fazer para atingir o objetivo:**
* O foco principal é a comparação entre o arquivo `LLM_Student` (o que a IA sugeriu) e o `Expert_Student` (o que o aluno entregou).
* Se a distância entre os dois for próxima de zero, indica baixa autonomia/pensamento crítico (ou uma saída perfeita da IA, o que é raro em design complexo).

3. **Metodologia:**
* **Distância Semântica e Textual:** Calcular *Cosine Distance* e *Jaccard*  entre `LLM_Student` e `Expert_Student`. Uma alta similaridade sugere pouca crítica.
* **Análise de Sentimento/Survey:** Analisar a resposta "O uso de LLM forneceu uma clara vantagem"  cruzada com o "Esforço de refinamento". Baixo esforço com alta percepção de vantagem pode indicar baixa crítica.

4. **Contribuição Esperada:**
* Entendimento sobre a "ilusão de competência" gerada por IA e diretrizes para avaliações acadêmicas que forcem o aluno a justificar *por que* aceitou ou rejeitou a sugestão da IA.

---

### **Área Científica**

**RQ1: "Como o nível de abstração e complexidade das tarefas de design arquitetural influencia a qualidade das respostas geradas por LLMs no contexto do processo ADD?"** 

1. **Objetivo Geral e Específicos:**
* **Geral:** Mapear as capacidades do Gemini 3.0 em diferentes níveis de granularidade do design de software.
* **Específicos:** Verificar se o modelo é melhor em tarefas criativas/abstratas (Histórias de Usuário) ou estruturais/lógicas (Restrições, Táticas, Esquema de Banco de Dados).

2. **Como fazer para atingir o objetivo:**
* Segmentar a análise das métricas por "Etapa do ADD" (Fase 1: Drivers vs. Fase 3: Rodadas de Design/Estrutura).
* Comparar a qualidade (`LLM_Student` vs. `Expert_Solution`) em etapas de texto natural (Contexto) versus etapas técnicas (Esqueleto da Arquitetura).

3. **Metodologia:**
* **Análise Estatística Comparativa:** Calcular a média de *BERTScore* e *ROUGE-L*  para cada etapa do projeto.

* Testar a hipótese de que a qualidade cai conforme a complexidade e a interdependência das decisões aumentam (nas rodadas finais do ADD).

4. **Contribuição Esperada:**
* Identificação dos limites atuais dos LLMs em Arquitetura de Software, indicando em quais etapas do processo ADD a intervenção humana é crítica e onde a automação é segura.

---

**RQ2: "Qual a relação entre o uso de LLMs e a qualidade dos artefatos produzidos em cada etapa do processo ADD, desde a elicitação de requisitos até a definição de táticas arquiteturais?"** 

1. **Objetivo Geral e Específicos:**
* **Geral:** Avaliar a eficácia técnica dos artefatos produzidos no fluxo assistido por IA.
* **Específicos:** Comparar a solução final dos estudantes (`Expert_Student`) com o Gabarito (`Expert_Solution`). O uso do LLM aproximou os estudantes da solução ideal?

2. **Como fazer para atingir o objetivo:**
* Utilizar o "Gabarito" (`Expert_Solution`) derivado do Capítulo 15 do livro *System Design Interview*  como *Ground Truth*.

* Calcular a similaridade dos artefatos finais dos alunos contra esse gabarito.

3. **Metodologia:**
* **Métricas de Similaridade Profunda:** *BERTScore*  (significado) e *Hellinger Distance*  (distribuição de probabilidade/tópicos).
* **Análise de Cluster:** Verificar se os projetos dos alunos convergem para a solução do livro ou se o LLM induziu soluções alternativas válidas (mas distantes vetorialmente).

4. **Contribuição Esperada:**
* Validação empírica do uso de LLMs para produção industrial de documentação de arquitetura, medindo a confiabilidade técnica do output final.

---

**RQ3: "Quais dimensões de qualidade e métricas, que associadas, são mais relevantes para avaliar o impacto do uso de LLMs no processo de design de arquitetura de software baseado em ADD?"** 

1. **Objetivo Geral e Específicos:**
* **Geral:** Determinar quais métricas automatizadas (NLP) realmente refletem a qualidade arquitetural.
* **Específicos:** Descobrir se um alto *BLEU* score (precisão de n-gramas)  correlaciona com uma boa arquitetura, ou se métricas semânticas (*BERTScore*, *Cosine*) são preditores melhores.

2. **Como fazer para atingir o objetivo:**
* Correlacionar as 12 métricas calculadas com a avaliação qualitativa dos alunos no Survey (escalas Likert de qualidade da saída).
* Verificar discrepâncias: Onde a métrica diz que é "ruim" (baixa similaridade textual) mas o aluno diz que é "excelente" (conceito correto, palavras diferentes).

3. **Metodologia:**
* **Meta-Avaliação:** Análise de correlação (Pearson/Spearman) entre as métricas objetivas (ex: *Euclidean Distance* ) e as subjetivas (Survey).
* Avaliação da utilidade das métricas cognitivas (*Bloom*) para software.

4. **Contribuição Esperada:**
* Proposição de um framework de avaliação específico para *Software Engineering with LLMs*, argumentando que métricas de tradução (como BLEU) podem ser insuficientes e sugerindo um conjunto híbrido (Semântica + Cognitiva).

### Resumo dos Dados para o Experimento

Para responder a todas essas perguntas, você utilizará a triangulação de três fontes de dados principais definidas no `README.md`:

1. **Logs de Interação:** `LLM-` files (o que a IA disse).
2. **Artefatos Refinados:** `Expert-` files (o que o humano validou).
3. **Percepção Humana:** Respostas do `Survey`.

Deseja que eu detalhe o script de análise para alguma métrica específica (como a configuração do BERTScore ou a análise das distâncias vetoriais)?
