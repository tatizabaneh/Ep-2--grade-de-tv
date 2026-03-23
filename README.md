# Ep-2--grade-de-tv
# Otimização de Grade de Programação de TV

---

## 1. Contexto

Uma emissora de televisão aberta está reformulando sua grade de programação semanal. Com a crescente concorrência do streaming, a emissora precisa distribuir seu catálogo de programas de forma estratégica para maximizar a audiência estimada, respeitar restrições regulatórias e contratuais e garantir uma programação equilibrada e atraente ao longo de toda a semana.

Você foi contratado para desenvolver um sistema baseado em **Algoritmo Genético (AG)** capaz de montar automaticamente a grade semanal de programação, alocando programas a faixas horárias de forma a maximizar a audiência total estimada e satisfazer todas as restrições operacionais e regulatórias da emissora.

---

## 2. Descrição do Problema

A grade de programação cobre **7 dias da semana** e **6 faixas horárias diárias**, totalizando **42 slots** a serem preenchidos. Cada slot recebe exatamente um programa.

| Faixa | Horário | Perfil de audiência |
|---|---|---|
| Madrugada | 00h00 – 06h00 | Baixa |
| Manhã | 06h00 – 12h00 | Média |
| Almoço | 12h00 – 14h00 | Alta |
| Tarde | 14h00 – 18h00 | Média |
| Prime Time | 18h00 – 22h00 | Muito Alta |
| Noite | 22h00 – 00h00 | Alta |

### 2.1 Catálogo de Programas

A emissora dispõe de **20 programas** em seu catálogo, cada um com gênero, classificação indicativa, público-alvo, audiência estimada por faixa e número de exibições semanais contratadas:

| Prog. | Nome | Gênero | Classif. | Público-alvo | Exib./semana | Audiência estimada por faixa (pontos) |||||
|---|---|---|---|---|---|---|---|---|---|---|
| | | | | | | Madrugada | Manhã | Almoço/Noite | Tarde | Prime Time |
| P01 | Jornal Nacional | Jornalismo | Livre | Geral | 5 | 2 | 6 | 12 | 8 | 18 |
| P02 | Novela das 21h | Dramaturgia | 12 anos | Adulto | 5 | 1 | 3 | 7 | 10 | 20 |
| P03 | Esporte Total | Esporte | Livre | Geral | 3 | 1 | 4 | 8 | 6 | 14 |
| P04 | Show de Humor | Variedades | 10 anos | Família | 2 | 2 | 5 | 9 | 8 | 16 |
| P05 | Documentário Nat. | Documentário | Livre | Família | 2 | 3 | 5 | 7 | 6 | 10 |
| P06 | Série Policial | Ficção | 14 anos | Adulto | 2 | 2 | 3 | 6 | 7 | 15 |
| P07 | Culinária ao Vivo | Entretenimento | Livre | Família | 3 | 1 | 7 | 14 | 9 | 8 |
| P08 | Reality Show | Variedades | 12 anos | Jovem | 3 | 2 | 4 | 8 | 7 | 17 |
| P09 | Filme de Ação | Cinema | 16 anos | Adulto | 2 | 3 | 2 | 5 | 5 | 14 |
| P10 | Desenho Animado | Infantil | Livre | Criança | 5 | 1 | 8 | 6 | 10 | 3 |
| P11 | Telejornal Local | Jornalismo | Livre | Geral | 5 | 2 | 9 | 11 | 7 | 12 |
| P12 | Série de Comédia | Ficção | 10 anos | Jovem | 2 | 2 | 3 | 7 | 6 | 13 |
| P13 | Talk Show | Variedades | Livre | Adulto | 2 | 2 | 4 | 8 | 5 | 11 |
| P14 | Filme de Romance | Cinema | 12 anos | Adulto | 1 | 2 | 3 | 6 | 7 | 12 |
| P15 | Programa Religioso | Variedades | Livre | Geral | 2 | 3 | 8 | 5 | 3 | 2 |
| P16 | Esporte ao Vivo | Esporte | Livre | Geral | 1 | 1 | 2 | 5 | 4 | 16 |
| P17 | Série de Suspense | Ficção | 16 anos | Adulto | 2 | 2 | 2 | 4 | 5 | 14 |
| P18 | Infantil Musical | Infantil | Livre | Criança | 3 | 1 | 9 | 7 | 11 | 2 |
| P19 | Debate Político | Jornalismo | Livre | Adulto | 1 | 1 | 3 | 5 | 4 | 9 |
| P20 | Filme de Terror | Cinema | 18 anos | Adulto | 1 | 3 | 1 | 2 | 3 | 10 |

> **Total de exibições semanais contratadas:** $\sum = 42$ exibições, distribuídas nos 42 slots disponíveis com possibilidade de reexibição em slots restantes.

---

## 3. Restrições

**R1 – Classificação indicativa por faixa:**
Programas com classificação indicativa restrita não podem ser exibidos em determinadas faixas:
- Classificação **16 anos**: proibido nas faixas Manhã, Almoço e Tarde.
- Classificação **18 anos**: permitido apenas nas faixas Noite e Madrugada.
- Classificação **14 anos**: proibido na faixa Manhã.

**R2 – Cumprimento de exibições contratadas:**
Cada programa deve ser exibido **pelo menos** o número de vezes semanal contratado (coluna "Exib./semana"). Exibições adicionais são permitidas para completar os 42 slots.

**R3 – Não repetição diária:**
O mesmo programa não pode ser exibido mais de **uma vez por dia**.

**R4 – Alternância de gênero em Prime Time:**
Na faixa de Prime Time, não podem ocorrer dois programas do **mesmo gênero** em dias consecutivos. A programação de Prime Time deve ser variada ao longo da semana.

**R5 – Programação infantil restrita:**
Programas de público-alvo **Criança** (P10 e P18) só podem ser exibidos nas faixas Manhã, Almoço e Tarde, nunca em Prime Time, Noite ou Madrugada.

**R6 – Jornalismo em horários fixos:**
Programas de gênero **Jornalismo** devem ser exibidos nas faixas Almoço, Tarde ou Prime Time. Jornalismo na Madrugada não é permitido.

**R7 – Receita publicitária mínima semanal por faixa:**
Cada faixa horária deve atingir uma **receita publicitária mínima acumulada** ao longo dos 7 dias da semana. Programas de gênero **Infantil** reduzem a receita da faixa em que são exibidos em 50% (regulamentação do CONAR). A solução deve garantir que a receita semanal de cada faixa não fique abaixo do mínimo exigido:

| Faixa | Receita base por dia (R$ mil) | Receita mínima semanal (R$ mil) |
|---|---|---|
| Madrugada | 10 | 60 |
| Manhã | 40 | 250 |
| Almoço | 80 | 500 |
| Tarde | 50 | 320 |
| Prime Time | 200 | 1.200 |
| Noite | 90 | 560 |

> Exemplo: se P10 (Infantil) for alocado na faixa Manhã em 3 dias da semana, a receita da Manhã nesses 3 dias cai de R$ 40 mil para R$ 20 mil cada, totalizando R$ 220 mil — abaixo do mínimo de R$ 250 mil, violando R7.

**R8 – Equilíbrio de audiência entre dias da semana:**
A audiência total estimada de cada dia (soma dos pontos dos 6 programas exibidos) não pode variar mais de **20 pontos** entre o dia de maior e o de menor audiência semanal. Isso garante uma programação consistente ao longo da semana.

---

## 4. Função de Fitness

O objetivo é **maximizar a audiência total estimada** somada à receita publicitária semanal:

$$\text{maximizar} \quad F = \alpha \cdot A_{total} + \beta \cdot R_{total}$$

onde:
- $A_{total} = \sum_{d=1}^{7} \sum_{f=1}^{6} audiencia(prog_{d,f}, f)$ é a soma de pontos de audiência de todos os slots;
- $R_{total} = \sum_{d=1}^{7} \sum_{f=1}^{6} receita\_pub(f, prog_{d,f})$ é a receita publicitária total, considerando a redução para programas infantis;
- $\alpha = 1{,}0$ e $\beta = 0{,}005$ são os fatores de ponderação padrão sugeridos (o aluno pode ajustá-los com justificativa no relatório — note que $\beta$ deve ser pequeno para não deixar a receita dominar sobre a audiência).

**Avaliação (Função de Aptidão):**

$$f = F - \sum_{i} p_i \cdot n_i$$

onde $p_i$ e $n_i$ são o peso e o número de violações da restrição $i$. Restrições regulatórias (R1, R5, R6) devem ter penalidades muito altas. Restrições contratuais (R2) também devem ser fortemente penalizadas. Restrições de qualidade de grade (R4, R8) podem ter penalidades moderadas.

---

## 5. Saída Esperada

**A saída deve apresentar:**

- Grade completa (dia × faixa) com nome do programa e audiência estimada.
- Audiência total e receita publicitária estimada.
- Contagem de exibições por programa.
- Número de violações por restrição (deve ser zero na solução final).

```
Grade Semanal de Programação:

Faixa       | Seg        | Ter        | Qua        | Qui        | Sex        | Sáb        | Dom
------------|------------|------------|------------|------------|------------|------------|--------
Madrugada   | P15 (3pts) | P20 (3pts) | P15 (3pts) | P05 (3pts) | P09 (3pts) | P20 (3pts) | P05 (3pts)
Manhã       | P10 (8pts) | P18 (9pts) | P10 (8pts) | P18 (9pts) | P10 (8pts) | P18 (9pts) | P10 (8pts)
Almoço      | P11(11pts) | P07(14pts) | P01(12pts) | P11(11pts) | P07(14pts) | P01(12pts) | P11(11pts)
Tarde       | P07 (9pts) | P04 (8pts) | P08 (7pts) | P03 (6pts) | P04 (8pts) | P08 (7pts) | P13 (5pts)
Prime Time  | P01(18pts) | P02(20pts) | P04(16pts) | P08(17pts) | P02(20pts) | P16(16pts) | P01(18pts)
Noite       | P02(10pts) | P06 (7pts) | P17 (5pts) | P09 (5pts) | P06 (7pts) | P02(10pts) | P08 (7pts)
------------|------------|------------|------------|------------|------------|------------|--------
Total/dia   |   59 pts   |   61 pts   |   51 pts   |   51 pts   |   60 pts   |   57 pts   |   54 pts

Audiência Total: XXX pontos
Receita Publicitária: R$ X.XXX mil
Violações: 0
```

---

## 6. Dicas para Implementação

### 6.1 Cromossomo/Indivíduo

Inicialize cada indivíduo preenchendo os 42 slots (6 faixas × 7 dias) com os programas na exata quantidade contratada (R2). Distribua primeiro os programas com mais episódios e maior audiência nos melhores horários. Slots restantes podem ser preenchidos com reexibições dos programas mais populares para aquela faixa horária.

### 6.2 Mutação

Use **troca de slots** (*swap mutation*): selecione dois slots aleatórios e troque os programas entre eles. Isso preserva automaticamente a contagem de exibições de cada programa (R2), eliminando a necessidade de reparação pós-mutação. Para R3 (restrição de gênero), verifique apenas o par de slots trocado, não o cromossomo inteiro.

--- 

## 7. Instruções para execução

O algoritmo foi implementado em Python.

### Requisitos

- Python 3.x
- Biblioteca padrão (`random`, `sys`, `collections`)

### Execução

Ao executar o programa, o usuário deve informar os hiperparâmetros:

- POP = tamanho da população
- GER = número de gerações
- MUT = taxa de mutação
- CROSS = taxa de crossover

Formato:

```bash
100 200 0.05 0.8
