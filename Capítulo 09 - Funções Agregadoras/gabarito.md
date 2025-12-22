# Exercícios - Capítulo 09: Funções Agregadoras

## Exercício 1: COUNT básico
```sql
SELECT COUNT(*) 
FROM produtos;

-- ouu com um alias mais descritivo:
SELECT COUNT(*) AS total_produtos
FROM produtos;
```

---

## Exercício 2: COUNT com coluna específica

```sql
-- a) total de produtos
SELECT COUNT(*) AS total_produtos
FROM produtos;

-- b) produtos com descrição
SELECT COUNT(descricao) AS produtos_com_descricao
FROM produtos;
```

---

## Exercício 3: COUNT DISTINCT

```sql
-- a) total de vendas
SELECT COUNT(*) AS total_vendas
FROM vendas;

-- b) clientes únicos
SELECT COUNT(DISTINCT cliente_id) AS clientes_unicos
FROM vendas;
```

---

## Exercício 4: COUNT com WHERE

```sql
-- a) total de pedidos
SELECT COUNT(*) AS total_pedidos
FROM pedidos;

-- b) pedidos entregues
SELECT COUNT(*) AS pedidos_entregues
FROM pedidos
WHERE status = 'entregue';

-- c) pedidos cancelados
SELECT COUNT(*) AS pedidos_cancelados
FROM pedidos
WHERE status = 'cancelado';

-- d) pedidos ativos (não cancelados)
SELECT COUNT(*) AS pedidos_ativos
FROM pedidos
WHERE status != 'cancelado';

-- Ou usando NOT:
SELECT COUNT(*) AS pedidos_ativos
FROM pedidos
WHERE status NOT IN ('cancelado');
```

---

## Exercício 5: SUM básico

```sql
SELECT SUM(valor_total) AS faturamento_total
FROM vendas;
```

---

## Exercício 6: SUM com WHERE

```sql
-- a) faturamento de março de 2024
SELECT SUM(valor_total) AS faturamento_marco
FROM vendas
WHERE data_venda >= '2024-03-01' 
  AND data_venda < '2024-04-01';

-- ou usando BETWEEN:
SELECT SUM(valor_total) AS faturamento_marco
FROM vendas
WHERE data_venda BETWEEN '2024-03-01' AND '2024-03-31';

-- b) faturamento do primeiro trimestre de 2024
SELECT SUM(valor_total) AS faturamento_q1
FROM vendas
WHERE data_venda >= '2024-01-01' 
  AND data_venda < '2024-04-01';
```

---

## Exercício 7: AVG básico

```sql
SELECT ROUND(AVG(preco), 2) AS preco_medio
FROM produtos;
```

---

## Exercício 8: AVG com NULL

```sql
-- a) explicação:
-- O PostgreSQL ignora valores NULL no cálculo da média.
-- Para o Produto A: (5 + 4 + 5 + 3) / 4 = 4.25 (NULL não é considerado)
-- Para o Produto B: (2 + 3 + 4) / 3 = 3.00 (NULL não é considerado)

-- b) nota média de todos os produtos
SELECT ROUND(AVG(nota), 2) AS nota_media
FROM avaliacoes;
```

---

## Exercício 9: MIN e MAX com números

```sql
SELECT 
  MIN(estoque) AS menor_estoque,
  MAX(estoque) AS maior_estoque,
  MAX(estoque) - MIN(estoque) AS diferenca
FROM produtos;
```

---

## Exercício 10: MIN e MAX com datas

```sql
SELECT 
  MIN(data_pedido) AS primeiro_pedido,
  MAX(data_pedido) AS ultimo_pedido,
  MAX(data_pedido) - MIN(data_pedido) AS dias_decorridos
FROM pedidos;
```

---

## Exercício 11: MIN e MAX com texto

```sql
SELECT 
  MIN(nome) AS primeiro_alfabetico,
  MAX(nome) AS ultimo_alfabetico
FROM clientes;
```

---

## Exercício 12: Múltiplas funções agregadoras - Estatísticas de vendas

```sql
SELECT 
  COUNT(*) AS total_vendas,
  SUM(valor_total) AS faturamento_total,
  ROUND(AVG(valor_total), 2) AS ticket_medio,
  MIN(valor_total) AS menor_venda,
  MAX(valor_total) AS maior_venda,
  SUM(desconto_aplicado) AS total_descontos
FROM vendas;
```

---

## Exercício 13: Estatísticas condicionais

```sql
-- consultas separadas:
-- a)
SELECT COUNT(*) AS vendas_pix
FROM vendas
WHERE forma_pagamento = 'pix';

-- b)
SELECT SUM(valor_total) AS faturamento_pix
FROM vendas
WHERE forma_pagamento = 'pix';

-- c)
SELECT ROUND(AVG(valor_total), 2) AS ticket_medio_pix
FROM vendas
WHERE forma_pagamento = 'pix';

-- d)
SELECT 
  MIN(valor_total) AS menor_venda_pix,
  MAX(valor_total) AS maior_venda_pix
FROM vendas
WHERE forma_pagamento = 'pix';

-- consulta combinada:
SELECT 
  COUNT(*) AS vendas_pix,
  SUM(valor_total) AS faturamento_pix,
  ROUND(AVG(valor_total), 2) AS ticket_medio_pix,
  MIN(valor_total) AS menor_venda_pix,
  MAX(valor_total) AS maior_venda_pix
FROM vendas
WHERE forma_pagamento = 'pix';
```

---

## Exercício 14: Comparação de períodos

```sql
-- março de 2024
SELECT 
  'Março 2024' AS periodo,
  COUNT(*) AS num_vendas,
  SUM(valor_total) AS faturamento,
  ROUND(AVG(valor_total), 2) AS ticket_medio
FROM vendas
WHERE data_venda >= '2024-03-01' 
  AND data_venda < '2024-04-01';

-- abril de 2024
SELECT 
  'Abril 2024' AS periodo,
  COUNT(*) AS num_vendas,
  SUM(valor_total) AS faturamento,
  ROUND(AVG(valor_total), 2) AS ticket_medio
FROM vendas
WHERE data_venda >= '2024-04-01' 
  AND data_venda < '2024-05-01';
```

---

## Exercício 15: Análise de estoque

```sql
SELECT 
  COUNT(*) AS total_produtos,
  SUM(estoque) AS unidades_estoque,
  SUM(estoque * preco) AS valor_total_estoque,
  ROUND(AVG(preco), 2) AS preco_medio,
  MIN(preco) AS preco_minimo,
  MAX(preco) AS preco_maximo,
  COUNT(DISTINCT categoria) AS num_categorias
FROM produtos;
```

---

## Exercício 16: Identificando erros - Mistura de colunas

```sql
-- a) Por que está errada:
-- A consulta mistura uma coluna regular (nome) com uma função agregadora (COUNT).
-- O banco não sabe como combinar múltiplos nomes com um único número.

-- b) O que acontecerá:
-- O PostgreSQL retornará um erro como:
-- ERROR: column "produtos.nome" must appear in the GROUP BY clause or be used in an aggregate function

-- c) Como corrigir:
-- Opção 1: Remover a coluna nome e contar apenas o total
SELECT COUNT(*) AS total_produtos
FROM produtos;

-- Opção 2: Usar GROUP BY para agrupar por nome (próximo capítulo)
SELECT nome, COUNT(*) 
FROM produtos
GROUP BY nome;

-- Opção 3: Se queremos contar vendas por produto, precisaríamos fazer um JOIN
-- com a tabela de vendas e usar GROUP BY (conceitos dos próximos capítulos)
```

---

## Exercício 17: Identificando erros - WHERE com funções agregadoras

```sql
-- a) Por que está errada:
-- WHERE filtra linhas ANTES da agregação acontecer.
-- Funções agregadoras não podem ser usadas em WHERE.

-- b) O erro:
-- ERROR: aggregate functions are not allowed in WHERE

-- c) Forma correta (usando HAVING - próximo capítulo):
SELECT categoria, COUNT(*) AS total
FROM produtos
GROUP BY categoria
HAVING COUNT(*) > 10;
```

---

## Exercício 18: Cuidado com divisão por zero

```sql
-- a) O que pode dar errado:
-- Se o pedido 999 não existir, COUNT(*) retornará 0, causando divisão por zero.

-- b) Como proteger:
-- Podemos usar NULLIF para converter 0 em NULL, evitando a divisão por zero.
-- Ou podemos usar CASE para verificar antes de dividir.

-- c) Consulta mais segura:

-- opção 1: Usando NULLIF
SELECT 
  pedido_id,
  valor_total / NULLIF(COUNT(*), 0) AS valor_medio_produto
FROM itens_pedido
WHERE pedido_id = 999;

-- opção 2: Usando CASE
SELECT 
  pedido_id,
  CASE 
    WHEN COUNT(*) > 0 THEN valor_total / COUNT(*)
    ELSE NULL
  END AS valor_medio_produto
FROM itens_pedido
WHERE pedido_id = 999;

-- opção 3: Verificar se há registros primeiro
SELECT 
  pedido_id,
  valor_total / COUNT(*) AS valor_medio_produto
FROM itens_pedido
WHERE pedido_id = 999
  AND EXISTS (SELECT 1 FROM itens_pedido WHERE pedido_id = 999);
```

---

## Exercício 19: Análise completa - Dashboard de vendas

```sql
SELECT 
  COUNT(*) AS total_vendas,
  SUM(valor_total) AS faturamento_bruto,
  SUM(desconto_aplicado) AS total_descontos,
  SUM(valor_total) - SUM(desconto_aplicado) AS faturamento_liquido,
  SUM(custo_produtos) AS custo_total,
  (SUM(valor_total) - SUM(desconto_aplicado)) - SUM(custo_produtos) AS lucro_bruto,
  ROUND(AVG(valor_total), 2) AS ticket_medio,
  MIN(valor_total) AS menor_venda,
  MAX(valor_total) AS maior_venda,
  COUNT(DISTINCT cliente_id) AS clientes_unicos,
  COUNT(DISTINCT vendedor_id) AS vendedores_ativos
FROM vendas
WHERE data_venda >= '2024-12-01' 
  AND data_venda < '2025-01-01';
```

---

## Exercício 20: Questão conceitual - NULL em agregações

```sql
-- a) COUNT(*) conta todas as linhas, incluindo as com NULL
-- Resultado: 3 (todas as 3 linhas do produto 1)

SELECT COUNT(*) FROM avaliacoes_produtos WHERE produto_id = 1;
-- Resultado: 3

-- b) COUNT(nota) conta apenas linhas onde nota NÃO é NULL
-- Produto 1 tem: 5, 4, NULL
-- Resultado: 2

SELECT COUNT(nota) FROM avaliacoes_produtos WHERE produto_id = 1;
-- Resultado: 2

-- c) COUNT(comentario) conta apenas linhas onde comentario NÃO é NULL
-- Produto 1 tem: "Excelente", NULL, "Sem nota"
-- Resultado: 2

SELECT COUNT(comentario) FROM avaliacoes_produtos WHERE produto_id = 1;
-- Resultado: 2

-- d) AVG(nota) calcula a média apenas dos valores não-NULL
-- Produto 1 tem notas: 5, 4, NULL
-- Média: (5 + 4) / 2 = 4.5
-- Resultado: 4.5

SELECT AVG(nota) FROM avaliacoes_produtos WHERE produto_id = 1;
-- Resultado: 4.5

-- e) Todos os produtos (5 linhas no total)
-- COUNT(*) = 5 (todas as linhas)
-- COUNT(nota) = 3 (5, 4, 3 - ignora os 2 NULL)
-- COUNT(comentario) = 3 ("Excelente", "Sem nota", "Regular" - ignora os 2 NULL)

SELECT COUNT(*), COUNT(nota), COUNT(comentario) 
FROM avaliacoes_produtos;
-- Resultado: 5, 3, 3
```

---

## Exercício Desafio: Análise de performance de vendedores

```sql
SELECT 
  COUNT(*) AS total_vendas,
  SUM(valor_total) AS faturamento_total,
  SUM(custo) AS custo_total,
  ROUND(AVG((valor_total - custo) / NULLIF(valor_total, 0) * 100), 2) AS margem_lucro_media,
  COUNT(*) FILTER (WHERE meta_atingida = TRUE) AS vendas_meta_atingida,
  ROUND(
    COUNT(*) FILTER (WHERE meta_atingida = TRUE)::NUMERIC / 
    NULLIF(COUNT(*), 0) * 100, 
    2
  ) AS percentual_meta,
  COUNT(DISTINCT vendedor_id) AS vendedores_ativos,
  MAX(valor_total) AS melhor_performance,
  MIN(data_venda) AS primeira_venda,
  MAX(data_venda) AS ultima_venda
FROM vendas
WHERE data_venda >= '2024-01-01' 
  AND data_venda < '2025-01-01';
```