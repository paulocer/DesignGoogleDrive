# Iteração [Número] - [Nome Descritivo]

## Informações Gerais
- **Data:** [DD/MM/YYYY]
- **Arquiteto(s):** [Nome(s)]
- **Elementos do Sistema:** [Elemento(s) sendo refinado(s)]

---

## Passo 1: Revisar Entradas (Review Inputs)

### Propósito do Design Round
[Descrever o objetivo desta iteração específica]

### Requisitos Funcionais Primários
| ID | Descrição | Prioridade |
|----|-----------|------------|
| RF-01 | | Alta/Média/Baixa |
| RF-02 | | |

### Cenários de Atributos de Qualidade (QA)
| ID | Atributo | Cenário | Prioridade |
|----|----------|---------|------------|
| QA-01 | Performance | | Alta/Média/Baixa |
| QA-02 | Segurança | | |

### Restrições (Constraints)
- [ ] **CON-01:** [Descrição da restrição]
- [ ] **CON-02:** [Descrição da restrição]

### Preocupações (Concerns)
- [ ] **CRN-01:** [Descrição da preocupação]
- [ ] **CRN-02:** [Descrição da preocupação]

---

## Passo 2: Estabelecer Objetivo da Iteração

### Objetivo da Iteração
[Descrição clara do que se pretende alcançar nesta iteração]

### Elementos Selecionados para Refinamento
- **Elemento:** [Nome do elemento]
  - **Razão:** [Por que este elemento foi selecionado - risco, incerteza, benefício de negócio]
  - **Estado Atual:** [Descrição do estado atual do elemento]

---

## Passo 3: Escolher Elementos do Sistema para Refinar

### Elementos a Decompor/Refinar
| Elemento | Justificativa | Complexidade Esperada |
|----------|---------------|----------------------|
| [Nome] | [Por que refinar este elemento] | Alta/Média/Baixa |

---

## Passo 4: Escolher Conceitos de Design

### Alternativas Consideradas

#### Alternativa 1: [Nome da Alternativa]
- **Descrição:** 
- **Prós:**
  - 
- **Contras:**
  - 
- **Drivers Atendidos:** [IDs]

#### Alternativa 2: [Nome da Alternativa]
- **Descrição:** 
- **Prós:**
  - 
- **Contras:**
  - 
- **Drivers Atendidos:** [IDs]

### Decisão Final
**Alternativa Selecionada:** [Nome]

**Justificativa:** [Explicação detalhada da escolha]

### Conceitos de Design Aplicados

#### Padrões Arquiteturais
- [ ] **[Nome do Padrão]** - [Justificativa de uso]

#### Táticas de Atributos de Qualidade
- [ ] **[Nome da Tática]** para [Atributo] - [Como será aplicada]

#### Frameworks e Tecnologias
- [ ] **[Nome]** - [Propósito e justificativa]

#### Decisões de Referência/Deployment
- [ ] **[Decisão]** - [Impacto e justificativa]

---

## Passo 5: Instanciar Elementos e Alocar Responsabilidades

### Elementos Instanciados

#### Elemento: [Nome do Elemento]
- **Tipo:** [Módulo/Componente/Serviço/Layer]
- **Responsabilidades:**
  1. [Responsabilidade 1]
  2. [Responsabilidade 2]
- **Requisitos Funcionais Atendidos:** [IDs]
- **Atributos de Qualidade Atendidos:** [IDs]
- **Restrições Consideradas:** [IDs]

#### Elemento: [Nome do Elemento]
- **Tipo:** 
- **Responsabilidades:**
  1. 
- **Requisitos Funcionais Atendidos:** 
- **Atributos de Qualidade Atendidos:** 
- **Restrições Consideradas:** 

---

## Passo 6: Definir Interfaces

### Interface: [Nome da Interface]

#### Recursos Providos (Provided Interface)
```
[Assinatura do método/endpoint]
- Input: 
- Output: 
- Comportamento: 
- Atributos de QA: [Ex: Tempo de resposta < 200ms]
```

#### Recursos Requeridos (Required Interface)
```
[Dependências necessárias]
- Interface requerida: 
- Propósito: 
```

#### Propriedades de Qualidade
- **Performance:** 
- **Disponibilidade:** 
- **Segurança:** 

---

## Passo 7: Esboçar Visões e Registrar Decisões

### Diagramas de Arquitetura

#### Visão de Módulos
```
[Inserir diagrama ou link para imagem]
```
**Descrição:** [Explicar o que o diagrama representa]

#### Visão de Componentes e Conectores (C&C)
```
[Inserir diagrama ou link para imagem]
```
**Descrição:** [Explicar o que o diagrama representa]

#### Visão de Alocação
```
[Inserir diagrama ou link para imagem]
```
**Descrição:** [Explicar o que o diagrama representa]

### Decisões de Design

| ID | Decisão | Alternativas Consideradas | Justificativa | Impacto |
|----|---------|---------------------------|---------------|---------|
| DD-01 | | | | |
| DD-02 | | | | |

### Backlog de Decisões Pendentes
- [ ] **DP-01:** [Descrição da decisão pendente]
  - **Razão do adiamento:** 
  - **Impacto se não for resolvida:** 

---

## Passo 8: Análise do Design Atual

### Análise de Drivers

| Driver ID | Tipo | Status | Análise |
|-----------|------|--------|---------|
| RF-01 | Funcional | ✅ Completamente Endereçado / 🔄 Parcialmente Endereçado / ⏳ Não Endereçado | [Explicação] |
| QA-01 | Qualidade | | |

### Riscos Identificados

| ID | Descrição do Risco | Probabilidade | Impacto | Estratégia de Mitigação |
|----|-------------------|---------------|---------|------------------------|
| R-01 | | Alta/Média/Baixa | Alto/Médio/Baixo | |

### Decisões Técnicas que Requerem Validação
- [ ] **V-01:** [Decisão que precisa de prototipagem ou análise adicional]
  - **Método de validação:** 
  - **Critério de sucesso:** 

### Dívida Técnica Introduzida
- [ ] **DT-01:** [Descrição da dívida técnica]
  - **Justificativa:** 
  - **Plano de resolução:** 

---

## Próximos Passos

### Drivers para Próxima Iteração
1. [Driver ID e descrição breve]
2. [Driver ID e descrição breve]

### Elementos Candidatos para Refinamento
- **[Nome do Elemento]** - [Razão para refinamento]

### Ações Pendentes
- [ ] [Ação específica que precisa ser tomada]
- [ ] [Pesquisa ou prototipagem necessária]

---

## Notas Adicionais
[Quaisquer observações, insights ou contexto adicional relevante para esta iteração]

---

## Anexos
- [Links para diagramas detalhados]
- [Links para protótipos]
- [Referências a documentação externa]
- [Atas de reuniões relacionadas]
