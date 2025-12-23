# Exercícios - Capítulo 10: GROUP BY e HAVING

## Exercício 1: GROUP BY básico

Você trabalha em uma loja online e tem uma tabela `vendas` com as colunas: `id`, `produto`, `quantidade`, `valor`, `categoria`, `data_venda`.

Escreva uma consulta SQL que mostre quantas vendas foram realizadas para cada categoria de produto.

---

## Exercício 2: GROUP BY com SUM

Usando a mesma tabela `vendas` do exercício anterior, escreva uma consulta que mostre o faturamento total (soma dos valores) para cada categoria de produto.

---

## Exercício 3: Múltiplas funções agregadoras

Crie um relatório completo por categoria que mostre:
- Total de vendas;
- Faturamento total;
- Ticket médio (valor médio por venda), arredondado para 2 decimais;
- Menor e maior valor de venda.

---

## Exercício 4: GROUP BY com múltiplas colunas

A tabela `vendas` tem uma coluna `regiao` que indica onde a venda foi realizada (Norte, Sul, Leste, Oeste).

Escreva uma consulta que mostre o total de vendas e o faturamento para cada combinação de categoria e região.

---

## Exercício 5: GROUP BY com função de data

A tabela `vendas` tem uma coluna `data_venda` do tipo DATE.

Escreva uma consulta que mostre o total de vendas e o faturamento por mês em 2024. Use a função `DATE_TRUNC('month', data_venda)` para extrair o mês.

---

## Exercício 6: Identificando erro - Coluna não agregada

Um analista júnior escreveu a seguinte consulta:

```sql
SELECT 
  categoria,
  produto,
  COUNT(*) AS total_vendas
FROM vendas
GROUP BY categoria;
```

a) Por que esta consulta está errada?
b) Como corrigir esta consulta se queremos saber o total de vendas por categoria e produto?

---

## Exercício 7: WHERE com GROUP BY

A tabela `vendas` tem uma coluna `status` que pode ser 'concluída', 'cancelada' ou 'pendente'.

Escreva uma consulta que mostre o faturamento por categoria, considerando apenas vendas com status 'concluída'.

---

## Exercício 8: HAVING básico

Escreva uma consulta que mostre apenas as categorias que tiveram mais de 50 vendas.

---

## Exercício 9: HAVING com diferentes condições

Escreva consultas separadas que mostrem:
a) Categorias com faturamento total superior a R$ 10.000;
b) Categorias onde o ticket médio é superior a R$ 100;
c) Categorias que tiveram pelo menos 20 vendas E faturamento superior a R$ 5.000.

---

## Exercício 10: WHERE vs HAVING

Explique a diferença entre estas duas consultas e quando usar cada uma:

```sql
-- Consulta A
SELECT categoria, COUNT(*) AS total
FROM vendas
WHERE valor > 100
GROUP BY categoria;

-- Consulta B
SELECT categoria, COUNT(*) AS total
FROM vendas
GROUP BY categoria
HAVING COUNT(*) > 100;
```

---

## Exercício 11: WHERE e HAVING juntos

A tabela `vendas` tem uma coluna `forma_pagamento` (cartão, dinheiro, pix).

Escreva uma consulta que:
1. Considere apenas vendas pagas com cartão;
2. Agrupe por categoria;
3. Mostre apenas categorias com mais de 30 vendas com cartão;
4. Ordene pelo total de vendas (do maior para o menor).

---

## Exercício 12: ORDER BY com GROUP BY

Crie um ranking das 5 categorias que mais faturam, mostrando:
- Nome da categoria;
- Total de vendas;
- Faturamento total;
- Ticket médio.

Ordene pelo faturamento do maior para o menor e limite a 5 resultados.

---

## Exercício 13: CASE WHEN para categorização

Categorize as vendas por faixa de valor:
- 'Baixo': até R$ 50
- 'Médio': de R$ 50,01 até R$ 200
- 'Alto': acima de R$ 200

Mostre quantas vendas existem em cada faixa e o faturamento total de cada uma.

---

## Exercício 14: Agrupamento por data com múltiplas granularidades

A tabela `vendas` tem a coluna `data_venda`.

Crie consultas separadas que mostrem o total de vendas e faturamento:
a) Por ano;
b) Por mês;
c) Por dia da semana (domingo = 0, segunda = 1, etc.).

---

## Exercício 15: COUNT DISTINCT com GROUP BY

A tabela `vendas` tem uma coluna `cliente_id`.

Para cada categoria, mostre:
- Total de vendas;
- Número de clientes únicos que compraram;
- Ticket médio.

---

## Exercício 16: Identificando erro - HAVING no WHERE

Um analista tentou escrever:

```sql
SELECT categoria, SUM(valor) AS faturamento
FROM vendas
WHERE SUM(valor) > 1000
GROUP BY categoria;
```

a) Por que esta consulta está errada?
b) Qual é a forma correta?

---

## Exercício 17: Análise de performance de vendedores

A tabela `vendas` tem uma coluna `vendedor_id`.

Crie um relatório que mostre, para cada vendedor:
- Total de vendas realizadas;
- Faturamento total;
- Ticket médio;
- Maior venda realizada.

Mostre apenas vendedores que:
- Realizaram mais de 10 vendas;
- E tiveram faturamento total acima de R$ 5.000.

Ordene pelo faturamento do maior para o menor.

---

## Exercício 18: Análise sazonal

Crie um relatório que analise as vendas por trimestre de 2024:
- Trimestre (Q1, Q2, Q3, Q4);
- Total de vendas;
- Faturamento;
- Ticket médio;
- Crescimento em relação ao trimestre anterior (apenas conceitual para Q2, Q3, Q4).

---

## Exercício 19: Uso de posição numérica (com cautela)

Reescreva a consulta abaixo usando posições numéricas no `GROUP BY` e `ORDER BY`:

```sql
SELECT 
  categoria,
  regiao,
  COUNT(*) AS total_vendas,
  SUM(valor) AS faturamento
FROM vendas
GROUP BY categoria, regiao
ORDER BY faturamento DESC, total_vendas DESC;
```

Depois, explique por que isso pode ser problemático.

---

## Exercício 20: Dashboard executivo completo

Você precisa criar um relatório executivo completo de vendas. A tabela `vendas` tem as colunas:
- `id`, `data_venda`, `produto`, `categoria`, `valor`, `quantidade`, `cliente_id`, `vendedor_id`, `regiao`, `forma_pagamento`

Crie uma consulta que mostre, para cada categoria:
1. Total de vendas;
2. Faturamento total;
3. Quantidade total de itens vendidos;
4. Ticket médio;
5. Número de clientes únicos;
6. Número de vendedores ativos;
7. Número de regiões onde a categoria foi vendida;
8. Percentual do faturamento total (em relação a todas as categorias).

Mostre apenas categorias com faturamento acima de R$ 1.000.
Ordene pelo faturamento do maior para o menor.

---

## Exercício Desafio: Análise RFM simplificada

RFM (Recência, Frequência, Valor Monetário) é uma técnica de segmentação de clientes. Usando a tabela `vendas` com as colunas `cliente_id`, `data_venda`, `valor`:

Crie um relatório por cliente que mostre:
1. ID do cliente;
2. Data da última compra (Recência);
3. Total de compras realizadas (Frequência);
4. Valor total gasto (Valor Monetário);
5. Ticket médio.

Mostre apenas clientes que:
- Fizeram pelo menos 3 compras;
- Gastaram pelo menos R$ 500 no total;
- Fizeram a última compra após 01/01/2024.

Ordene por valor total gasto (do maior para o menor).

---

## Exercício Extra: Pivotando dados com CASE WHEN

Crie um relatório que mostre, para cada categoria, quantas vendas foram feitas em cada forma de pagamento, transformando as formas de pagamento em colunas separadas.

Formas de pagamento: 'cartão', 'dinheiro', 'pix'.

O resultado deve ter colunas:
- categoria
- vendas_cartao
- vendas_dinheiro
- vendas_pix
- total_vendas