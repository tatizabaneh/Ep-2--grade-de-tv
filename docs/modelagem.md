# Modelagem do Problema — Algoritmo Genético para Grade de Programação de TV

## Cromossomo

Cada indivíduo (cromossomo) representa uma **grade semanal de programação de TV**.

* O cromossomo é um vetor de tamanho **42 genes**.
* Isso corresponde a:

  * **7 dias da semana**
  * **6 faixas horárias por dia**

### Representação

Cada gene representa **um programa de TV** (ex: P01, P02, ..., P20).

A posição do gene define:

* **Dia:** `d = índice // 6`
* **Faixa horária:** `f = índice % 6`

### Domínio dos valores

Os valores possíveis para cada gene são os identificadores dos programas:

```
{P01, P02, ..., P20}
```

### Restrições implícitas

* Cada programa possui um número mínimo de exibições (`exib`)
* Há restrições de classificação indicativa, público-alvo e faixa horária

---

## Função de Aptidão

A função de aptidão busca **maximizar audiência e receita**, penalizando violações.

### Fórmula

```
Fitness = ALPHA * Audiência_Total + BETA * Receita_Total - Penalidades
```

Onde:

* `ALPHA = 1`
* `BETA = 0.005`

### Componentes

#### 1. Audiência Total

* Soma da audiência de todos os programas
* A audiência depende da faixa horária:

  * Prime Time usa valor máximo
  * Almoço/Noite usam valor intermediário
  * Outras faixas usam valores específicos

#### 2. Receita Total

* Baseada na faixa horária:

  ```
  [10, 40, 80, 50, 200, 90]
  ```
* Programas infantis têm receita reduzida (50%)

#### 3. Penalidades

Aplicadas para violações:

* Classificação indicativa fora do horário:

  * +1000 por violação
* Público infantil fora do horário adequado:

  * +1000
* Jornalismo na madrugada:

  * +800
* Não atender número mínimo de exibições:

  * +1000 por programa
* Repetição de programa no mesmo dia:

  * +500
* Receita mínima por faixa não atingida:

  * +600
* Desequilíbrio de audiência entre dias:

  * +200

### Justificativa dos pesos

* `ALPHA = 1`: prioriza audiência como objetivo principal
* `BETA = 0.005`: receita tem menor impacto relativo
* Penalidades altas garantem respeito às restrições

---

## Operadores

### Seleção

* Método: **Torneio (tamanho 3)**
* Seleciona o melhor entre 3 indivíduos aleatórios
* Favorece indivíduos mais aptos, mantendo diversidade

---

### Crossover

* Tipo: **Crossover de um ponto**
* Probabilidade: `CROSS`

Procedimento:

* Escolhe ponto aleatório
* Combina partes de dois pais

```
Filho1 = p1[:pt] + p2[pt:]
Filho2 = p2[:pt] + p1[pt:]
```

Não garante diretamente as restrições — estas são tratadas via penalização.

---

### Mutação

* Tipo: **Troca (swap)**
* Probabilidade: `MUT`

Procedimento:

* Seleciona dois genes aleatórios
* Troca suas posições

Ajuda a:

* Explorar novas soluções
* Evitar mínimos locais

---

### Tratamento de restrições

* Não há reparação explícita
* Restrições são tratadas via **penalização na função de aptidão**

---

## Inicialização

### Estratégia

* Inicialização **semi-aleatória**

Procedimento:

1. Cada programa é inserido respeitando seu número mínimo de exibições (`exib`)
2. Slots restantes são preenchidos aleatoriamente
3. Lista é embaralhada

### Garantias iniciais

* Garante presença mínima de cada programa
* Outras restrições são tratadas posteriormente via penalidade

---

## Critério de Parada

* Número fixo de gerações (`GER`)

O algoritmo:

* Executa por um número pré-definido de iterações
* Não utiliza critério de convergência

### Observação

Poderia ser melhorado com:

* Parada por estagnação do fitness
* Limite de tempo

---

## Conclusão

O modelo utiliza um algoritmo genético clássico com:

* Representação direta da solução
* Penalização para restrições
* Operadores simples e eficientes

O foco principal é maximizar audiência mantendo viabilidade da grade de programação.
