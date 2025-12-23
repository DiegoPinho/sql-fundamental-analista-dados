# Exercícios - Capítulo 10: GROUP BY e HAVING

## Exercício 1: GROUP BY básico
```sql
SELECT 
  categoria,
  COUNT(*) AS total_vendas
FROM vendas
GROUP BY categoria;
```

---

## Exercício 2: GROUP BY com SUM
```sql
SELECT 
  categoria,
  SUM(valor) AS faturamento_total
FROM vendas
GROUP BY categoria;
```

---

## Exercício 3: Múltiplas funções agregadoras
```sql
SELECT 
  categoria,
  COUNT(*) AS total_vendas,
  SUM(valor) AS faturamento_total,
  ROUND(AVG(valor), 2) AS ticket_medio,
  MIN(valor) AS menor_venda,
  MAX(valor) AS maior_venda
FROM vendas
GROUP BY categoria;
```

---

## Exercício 4: GROUP BY com múltiplas colunas
```sql
SELECT 
  categoria,
  regiao,
  COUNT(*) AS total_vendas,
  SUM(valor) AS faturamento
FROM vendas
GROUP BY categoria, regiao
ORDER BY categoria, regiao;
```

---

## Exercício 5: GROUP BY com função de data
```sql
SELECT 
  DATE_TRUNC('month', data_venda) AS mes,
  COUNT(*) AS total_vendas,
  SUM(valor) AS faturamento
FROM vendas
WHERE data_venda >= '2024-01-01' 
  AND data_venda < '2025-01-01'
GROUP BY DATE_TRUNC('month', data_venda)
ORDER BY mes;
```

---

## Exercício 6: Identificando erro - Coluna não agregada
```sql
-- a) Por que está errada:
-- A coluna 'produto' está no SELECT mas não está no GROUP BY.
-- O banco não sabe qual dos vários produtos de cada categoria deve exibir.
-- Erro: column "vendas.produto" must appear in the GROUP BY clause 
-- or be used in an aggregate function

-- b) Forma correta:
SELECT 
  categoria,
  produto,
  COUNT(*) AS total_vendas
FROM vendas
GROUP BY categoria, produto
ORDER BY categoria, produto;
```

---

## Exercício 7: WHERE com GROUP BY
```sql
SELECT 
  categoria,
  COUNT(*) AS total_vendas,
  SUM(valor) AS faturamento
FROM vendas
WHERE status = 'concluída'
GROUP BY categoria;
```

---

## Exercício 8: HAVING básico
```sql
SELECT 
  categoria,
  COUNT(*) AS total_vendas
FROM vendas
GROUP BY categoria
HAVING COUNT(*) > 50;
```

---

## Exercício 9: HAVING com diferentes condições
```sql
-- a) Faturamento total > 10.000
SELECT 
  categoria,
  SUM(valor) AS faturamento_total
FROM vendas
GROUP BY categoria
HAVING SUM(valor) > 10000;

-- b) Ticket médio > 100
SELECT 
  categoria,
  ROUND(AVG(valor), 2) AS ticket_medio
FROM vendas
GROUP BY categoria
HAVING AVG(valor) > 100;

-- c) Pelo menos 20 vendas E faturamento > 5.000
SELECT 
  categoria,
  COUNT(*) AS total_vendas,
  SUM(valor) AS faturamento_total
FROM vendas
GROUP BY categoria
HAVING COUNT(*) >= 20 
  AND SUM(valor) > 5000;
```

---

## Exercício 10: WHERE vs HAVING
```sql
-- Consulta A: Filtra VENDAS individuais antes de agrupar
-- Conta apenas vendas com valor > 100 por categoria
-- WHERE age em registros individuais ANTES do agrupamento

SELECT categoria, COUNT(*) AS total
FROM vendas
WHERE valor > 100  -- Filtra vendas individuais
GROUP BY categoria;

-- Exemplo de resultado:
-- Eletrônicos | 45  (45 vendas acima de R$ 100)
-- Livros      | 12  (12 vendas acima de R$ 100)

-- Consulta B: Filtra GRUPOS após o agrupamento
-- Mostra apenas categorias que tiveram mais de 100 vendas (independente do valor)
-- HAVING age em grupos DEPOIS do agrupamento

SELECT categoria, COUNT(*) AS total
FROM vendas
GROUP BY categoria
HAVING COUNT(*) > 100;  -- Filtra grupos

-- Exemplo de resultado:
-- Eletrônicos | 350  (categoria teve mais de 100 vendas)
-- (Livros não aparece porque teve apenas 80 vendas no total)

-- RESUMO:
-- WHERE: Filtra linhas antes de agrupar (não pode usar funções agregadoras)
-- HAVING: Filtra grupos depois de agrupar (pode usar funções agregadoras)
```

---

## Exercício 11: WHERE e HAVING juntos
```sql
SELECT 
  categoria,
  COUNT(*) AS vendas_cartao,
  SUM(valor) AS faturamento
FROM vendas
WHERE forma_pagamento = 'cartão'  -- Filtra antes de agrupar
GROUP BY categoria
HAVING COUNT(*) > 30  -- Filtra depois de agrupar
ORDER BY vendas_cartao DESC;
```

---

## Exercício 12: ORDER BY com GROUP BY
```sql
SELECT 
  categoria,
  COUNT(*) AS total_vendas,
  SUM(valor) AS faturamento_total,
  ROUND(AVG(valor), 2) AS ticket_medio
FROM vendas
GROUP BY categoria
ORDER BY faturamento_total DESC
LIMIT 5;
```

---

## Exercício 13: CASE WHEN para categorização
```sql
SELECT 
  CASE 
    WHEN valor <= 50 THEN 'Baixo'
    WHEN valor <= 200 THEN 'Médio'
    ELSE 'Alto'
  END AS faixa_valor,
  COUNT(*) AS total_vendas,
  SUM(valor) AS faturamento_total
FROM vendas
GROUP BY 
  CASE 
    WHEN valor <= 50 THEN 'Baixo'
    WHEN valor <= 200 THEN 'Médio'
    ELSE 'Alto'
  END
ORDER BY 
  CASE faixa_valor
    WHEN 'Baixo' THEN 1
    WHEN 'Médio' THEN 2
    WHEN 'Alto' THEN 3
  END;
```

---

## Exercício 14: Agrupamento por data com múltiplas granularidades
```sql
-- a) Por ano
SELECT 
  DATE_TRUNC('year', data_venda) AS ano,
  COUNT(*) AS total_vendas,
  SUM(valor) AS faturamento
FROM vendas
GROUP BY DATE_TRUNC('year', data_venda)
ORDER BY ano;

-- Ou usando EXTRACT:
SELECT 
  EXTRACT(YEAR FROM data_venda) AS ano,
  COUNT(*) AS total_vendas,
  SUM(valor) AS faturamento
FROM vendas
GROUP BY EXTRACT(YEAR FROM data_venda)
ORDER BY ano;

-- b) Por mês (mostrando ano e mês)
SELECT 
  DATE_TRUNC('month', data_venda) AS mes,
  COUNT(*) AS total_vendas,
  SUM(valor) AS faturamento
FROM vendas
GROUP BY DATE_TRUNC('month', data_venda)
ORDER BY mes;

-- c) Por dia da semana
SELECT 
  EXTRACT(DOW FROM data_venda) AS dia_semana,  -- 0=domingo, 1=segunda, etc.
  CASE EXTRACT(DOW FROM data_venda)
    WHEN 0 THEN 'Domingo'
    WHEN 1 THEN 'Segunda'
    WHEN 2 THEN 'Terça'
    WHEN 3 THEN 'Quarta'
    WHEN 4 THEN 'Quinta'
    WHEN 5 THEN 'Sexta'
    WHEN 6 THEN 'Sábado'
  END AS nome_dia,
  COUNT(*) AS total_vendas,
  SUM(valor) AS faturamento
FROM vendas
GROUP BY EXTRACT(DOW FROM data_venda)
ORDER BY dia_semana;
```

---

## Exercício 15: COUNT DISTINCT com GROUP BY
```sql
SELECT 
  categoria,
  COUNT(*) AS total_vendas,
  COUNT(DISTINCT cliente_id) AS clientes_unicos,
  ROUND(AVG(valor), 2) AS ticket_medio
FROM vendas
GROUP BY categoria
ORDER BY clientes_unicos DESC;
```

---

## Exercício 16: Identificando erro - HAVING no WHERE
```sql
-- a) Por que está errada:
-- WHERE não pode usar funções agregadoras!
-- WHERE filtra linhas ANTES do agrupamento, quando SUM ainda não foi calculado
-- Erro: aggregate functions are not allowed in WHERE

-- b) Forma correta - usar HAVING:
SELECT 
  categoria, 
  SUM(valor) AS faturamento
FROM vendas
GROUP BY categoria
HAVING SUM(valor) > 1000;
```

---

## Exercício 17: Análise de performance de vendedores
```sql
SELECT 
  vendedor_id,
  COUNT(*) AS total_vendas,
  SUM(valor) AS faturamento_total,
  ROUND(AVG(valor), 2) AS ticket_medio,
  MAX(valor) AS maior_venda
FROM vendas
GROUP BY vendedor_id
HAVING COUNT(*) > 10 
  AND SUM(valor) > 5000
ORDER BY faturamento_total DESC;
```

---

## Exercício 18: Análise sazonal
```sql
SELECT 
  CASE 
    WHEN EXTRACT(MONTH FROM data_venda) BETWEEN 1 AND 3 THEN 'Q1'
    WHEN EXTRACT(MONTH FROM data_venda) BETWEEN 4 AND 6 THEN 'Q2'
    WHEN EXTRACT(MONTH FROM data_venda) BETWEEN 7 AND 9 THEN 'Q3'
    WHEN EXTRACT(MONTH FROM data_venda) BETWEEN 10 AND 12 THEN 'Q4'
  END AS trimestre,
  COUNT(*) AS total_vendas,
  SUM(valor) AS faturamento,
  ROUND(AVG(valor), 2) AS ticket_medio
FROM vendas
WHERE data_venda >= '2024-01-01' 
  AND data_venda < '2025-01-01'
GROUP BY 
  CASE 
    WHEN EXTRACT(MONTH FROM data_venda) BETWEEN 1 AND 3 THEN 'Q1'
    WHEN EXTRACT(MONTH FROM data_venda) BETWEEN 4 AND 6 THEN 'Q2'
    WHEN EXTRACT(MONTH FROM data_venda) BETWEEN 7 AND 9 THEN 'Q3'
    WHEN EXTRACT(MONTH FROM data_venda) BETWEEN 10 AND 12 THEN 'Q4'
  END
ORDER BY trimestre;
```

---

## Exercício 19: Uso de posição numérica (com cautela)
```sql
-- Usando posições numéricas:
SELECT 
  categoria,           -- posição 1
  regiao,             -- posição 2
  COUNT(*) AS total_vendas,     -- posição 3
  SUM(valor) AS faturamento     -- posição 4
FROM vendas
GROUP BY 1, 2  -- agrupa por colunas 1 e 2
ORDER BY 4 DESC, 3 DESC;  -- ordena por colunas 4 e 3

-- Problemas com esta abordagem:
-- 1. Menos legível - não fica claro quais colunas estão sendo usadas
-- 2. Frágil - se alguém adicionar uma coluna no SELECT, os números mudam
-- 3. Dificulta manutenção - precisa contar as posições
-- 4. Não é autoexplicativo - novos desenvolvedores terão dificuldade

-- RECOMENDAÇÃO: Use nomes de colunas, não posições numéricas!
```

---

## Exercício 20: Dashboard executivo completo
```sql
-- Solução em duas partes:
-- Parte 1: Cálculo do faturamento total (será usado no percentual)
-- Parte 2: Consulta principal

SELECT 
  categoria,
  COUNT(*) AS total_vendas,
  SUM(valor) AS faturamento_total,
  SUM(quantidade) AS quantidade_total,
  ROUND(AVG(valor), 2) AS ticket_medio,
  COUNT(DISTINCT cliente_id) AS clientes_unicos,
  COUNT(DISTINCT vendedor_id) AS vendedores_ativos,
  COUNT(DISTINCT regiao) AS regioes_ativas,
  ROUND(
    SUM(valor) * 100.0 / (SELECT SUM(valor) FROM vendas), 
    2
  ) AS percentual_faturamento
FROM vendas
GROUP BY categoria
HAVING SUM(valor) > 1000
ORDER BY faturamento_total DESC;
```

---

## Exercício Desafio: Análise RFM simplificada
```sql
SELECT 
  cliente_id,
  MAX(data_venda) AS ultima_compra,  -- Recência
  COUNT(*) AS total_compras,         -- Frequência
  SUM(valor) AS valor_total_gasto,   -- Valor Monetário
  ROUND(AVG(valor), 2) AS ticket_medio
FROM vendas
GROUP BY cliente_id
HAVING COUNT(*) >= 3                    -- Pelo menos 3 compras
  AND SUM(valor) >= 500                 -- Gastou pelo menos R$ 500
  AND MAX(data_venda) >= '2024-01-01'   -- Última compra após 01/01/2024
ORDER BY valor_total_gasto DESC;
```
---

## Exercício Extra: Pivotando dados com CASE WHEN
```sql
SELECT 
  categoria,
  SUM(CASE WHEN forma_pagamento = 'cartão' THEN 1 ELSE 0 END) AS vendas_cartao,
  SUM(CASE WHEN forma_pagamento = 'dinheiro' THEN 1 ELSE 0 END) AS vendas_dinheiro,
  SUM(CASE WHEN forma_pagamento = 'pix' THEN 1 ELSE 0 END) AS vendas_pix,
  COUNT(*) AS total_vendas
FROM vendas
GROUP BY categoria
ORDER BY total_vendas DESC;
```