# Exercícios - Capítulo 16: Expressões e Operadores Avançados

## Exercício 1: CASE WHEN - Classificação e categorização

```sql
-- a) Classificação por preço
SELECT 
  nome,
  preco,
  CASE
    WHEN preco < 100 THEN 'Barato'
    WHEN preco >= 100 AND preco <= 1000 THEN 'Médio'
    ELSE 'Caro'
  END AS faixa_preco
FROM produtos
ORDER BY preco;

-- Resultado:
--       nome        |  preco  | faixa_preco
-- ------------------+---------+-------------
--  Mouse            |   45.00 | Barato
--  Headset          |  250.00 | Médio
--  Webcam           |  350.00 | Médio
--  Teclado Mecânico |  450.00 | Médio
--  Mesa             |  800.00 | Médio
--  Cadeira Gamer    | 1200.00 | Caro
--  Monitor 27"      | 1500.00 | Caro
--  Notebook         | 3500.00 | Caro

-- b) Classificação de estoque
SELECT 
  nome,
  estoque,
  CASE
    WHEN estoque IS NULL THEN 'Indefinido'
    WHEN estoque = 0 THEN 'Esgotado'
    WHEN estoque BETWEEN 1 AND 10 THEN 'Baixo'
    WHEN estoque BETWEEN 11 AND 50 THEN 'Médio'
    ELSE 'Alto'
  END AS nivel_estoque
FROM produtos
ORDER BY 
  CASE 
    WHEN estoque IS NULL THEN 999
    ELSE estoque 
  END;

-- Resultado:
--       nome        | estoque | nivel_estoque
-- ------------------+---------+---------------
--  Headset          |       0 | Esgotado
--  Mesa             |       3 | Baixo
--  Notebook         |       5 | Baixo
--  Cadeira Gamer    |       8 | Baixo
--  Monitor 27"      |      12 | Médio
--  Teclado Mecânico |      25 | Médio
--  Mouse            |     150 | Alto
--  Webcam           |    NULL | Indefinido

-- c) Status de venda combinado
SELECT 
  nome,
  preco,
  estoque,
  CASE
    WHEN estoque IS NULL THEN 'Verificar Estoque'
    WHEN estoque = 0 THEN 'Indisponível'
    WHEN preco < 100 AND estoque > 50 THEN 'Promoção'
    WHEN estoque <= 5 THEN 'Atenção - Estoque Crítico'
    WHEN estoque <= 10 THEN 'Atenção - Estoque Baixo'
    ELSE 'Normal'
  END AS status_venda
FROM produtos
ORDER BY 
  CASE
    WHEN estoque IS NULL THEN 0
    ELSE estoque
  END;

-- Resultado:
--       nome        |  preco  | estoque |       status_venda
-- ------------------+---------+---------+---------------------------
--  Headset          |  250.00 |       0 | Indisponível
--  Mesa             |  800.00 |       3 | Atenção - Estoque Crítico
--  Notebook         | 3500.00 |       5 | Atenção - Estoque Crítico
--  Cadeira Gamer    | 1200.00 |       8 | Atenção - Estoque Baixo
--  Monitor 27"      | 1500.00 |      12 | Normal
--  Teclado Mecânico |  450.00 |      25 | Normal
--  Mouse            |   45.00 |     150 | Promoção
--  Webcam           |  350.00 |    NULL | Verificar Estoque

-- BÔNUS: Relatório agregado por faixa de preço
SELECT 
  CASE
    WHEN preco < 100 THEN 'Barato'
    WHEN preco >= 100 AND preco <= 1000 THEN 'Médio'
    ELSE 'Caro'
  END AS faixa_preco,
  COUNT(*) AS qtd_produtos,
  SUM(estoque) AS estoque_total,
  ROUND(AVG(preco), 2) AS preco_medio
FROM produtos
GROUP BY faixa_preco
ORDER BY preco_medio;

-- Resultado:
--  faixa_preco | qtd_produtos | estoque_total | preco_medio
-- -------------+--------------+---------------+-------------
--  Barato      |            1 |           150 |       45.00
--  Médio       |            4 |              |      462.50
--  Caro        |            3 |            25 |     2066.67
```

**Explicação:**
- `CASE WHEN` permite criar lógica condicional complexa;
- `BETWEEN` simplifica condições de intervalo;
- Pode combinar múltiplas condições com `AND`/`OR`;
- Sempre trate NULL explicitamente para evitar comportamentos inesperados;
- Útil em ORDER BY para customizar ordenação.

---

## Exercício 2: COALESCE - Tratamento de valores NULL

```sql
-- a) Salário total tratando NULL como 0
SELECT 
  nome,
  salario,
  bonus,
  comissao,
  salario + 
    COALESCE(bonus, 0) + 
    COALESCE(comissao, 0) AS salario_total
FROM funcionarios
ORDER BY salario_total DESC;

-- Resultado:
--     nome      | salario |  bonus  | comissao | salario_total
-- --------------+---------+---------+----------+---------------
--  Carla Souza  | 6000.00 | 1500.00 |     NULL |       7500.00
--  Elena Rocha  | 5500.00 |  800.00 |   600.00 |       6900.00
--  Ana Silva    | 5000.00 | 1000.00 |   500.00 |       6500.00
--  Bruno Costa  | 4500.00 |    NULL |   800.00 |       5300.00
--  Diego Lima   | 3500.00 |    NULL |     NULL |       3500.00

-- Sem COALESCE, qualquer NULL tornaria o total NULL:
SELECT 
  nome,
  salario + bonus + comissao AS total_sem_coalesce
FROM funcionarios;

-- Resultado (veja os NULLs):
--     nome      | total_sem_coalesce
-- --------------+--------------------
--  Ana Silva    |            6500.00
--  Bruno Costa  |               NULL  <- NULL!
--  Carla Souza  |               NULL  <- NULL!
--  Diego Lima   |               NULL  <- NULL!
--  Elena Rocha  |            6900.00

-- b) Departamento com valor padrão
SELECT 
  nome,
  departamento AS depto_original,
  COALESCE(departamento, 'Não Atribuído') AS depto_tratado
FROM funcionarios;

-- Resultado:
--     nome      | depto_original |   depto_tratado
-- --------------+----------------+------------------
--  Ana Silva    | Vendas         | Vendas
--  Bruno Costa  | Vendas         | Vendas
--  Carla Souza  | TI             | TI
--  Diego Lima   | NULL           | Não Atribuído
--  Elena Rocha  | Marketing      | Marketing

-- c) Remuneração garantida (primeiro valor disponível)
SELECT 
  nome,
  comissao,
  bonus,
  COALESCE(comissao, bonus, 500) AS remuneracao_garantida,
  CASE
    WHEN comissao IS NOT NULL THEN 'Comissão'
    WHEN bonus IS NOT NULL THEN 'Bônus'
    ELSE 'Mínimo Garantido'
  END AS origem
FROM funcionarios;

-- Resultado:
--     nome      | comissao |  bonus  | remuneracao_garantida |      origem
-- --------------+----------+---------+-----------------------+------------------
--  Ana Silva    |   500.00 | 1000.00 |                500.00 | Comissão
--  Bruno Costa  |   800.00 |    NULL |                800.00 | Comissão
--  Carla Souza  |     NULL | 1500.00 |               1500.00 | Bônus
--  Diego Lima   |     NULL |    NULL |                500.00 | Mínimo Garantido
--  Elena Rocha  |   600.00 |  800.00 |                600.00 | Comissão

-- BÔNUS: Múltiplos COALESCEs aninhados
SELECT 
  nome,
  salario,
  COALESCE(
    departamento,
    'Depto ' || SUBSTRING(nome FROM 1 FOR 1)  -- Usa primeira letra do nome
  ) AS depto_criativo,
  COALESCE(bonus, comissao, salario * 0.1) AS beneficio
FROM funcionarios;

-- Resultado:
--     nome      | salario | depto_criativo | beneficio
-- --------------+---------+----------------+-----------
--  Ana Silva    | 5000.00 | Vendas         |   1000.00
--  Bruno Costa  | 4500.00 | Vendas         |    800.00
--  Carla Souza  | 6000.00 | TI             |   1500.00
--  Diego Lima   | 3500.00 | Depto D        |    350.00  <- Calculado!
--  Elena Rocha  | 5500.00 | Marketing      |    800.00
```

**Explicação:**
- `COALESCE()` retorna o PRIMEIRO valor não-NULL da lista;
- Essencial para cálculos que podem conter NULL;
- Aceita quantos argumentos forem necessários;
- Muito usado para valores padrão em relatórios;
- Evita o comportamento de NULL em operações aritméticas (NULL + X = NULL).

---

## Exercício 3: NULLIF - Evitando divisão por zero

```sql
-- a) Percentual de atingimento (evitar divisão por zero na meta)
SELECT 
  vendedor,
  meta_mensal,
  vendas_realizadas,
  -- Sem NULLIF (causaria erro se meta fosse 0):
  -- vendas_realizadas / meta_mensal * 100 AS percentual
  
  -- Com NULLIF (transforma 0 em NULL, evitando erro):
  ROUND(
    vendas_realizadas / NULLIF(meta_mensal, 0) * 100, 
    2
  ) AS percentual_atingimento
FROM vendas_vendedor
ORDER BY percentual_atingimento DESC NULLS LAST;

-- Resultado:
--  vendedor | meta_mensal | vendas_realizadas | percentual_atingimento
-- ----------+-------------+-------------------+------------------------
--  Carlos   |    50000.00 |          72000.00 |                 144.00
--  João     |    50000.00 |          65000.00 |                 130.00
--  Pedro    |    50000.00 |          50000.00 |                 100.00
--  Maria    |    50000.00 |          48000.00 |                  96.00
--  Ana      |    50000.00 |              0.00 |                   0.00

-- b) Ticket médio evitando divisão por zero
SELECT 
  vendedor,
  vendas_realizadas,
  qtd_clientes,
  ROUND(
    vendas_realizadas / NULLIF(qtd_clientes, 0),
    2
  ) AS ticket_medio
FROM vendas_vendedor
ORDER BY ticket_medio DESC NULLS LAST;

-- Resultado:
--  vendedor | vendas_realizadas | qtd_clientes | ticket_medio
-- ----------+-------------------+--------------+--------------
--  João     |          65000.00 |           25 |      2600.00
--  Maria    |          48000.00 |           18 |      2666.67
--  Carlos   |          72000.00 |           30 |      2400.00
--  Pedro    |          50000.00 |            0 |         NULL  <- Evitou erro!
--  Ana      |              0.00 |            0 |         NULL  <- Evitou erro!

-- c) Classificação combinada
SELECT 
  vendedor,
  vendas_realizadas,
  qtd_clientes,
  ROUND(
    vendas_realizadas / NULLIF(meta_mensal, 0) * 100,
    2
  ) AS perc_meta,
  ROUND(
    vendas_realizadas / NULLIF(qtd_clientes, 0),
    2
  ) AS ticket_medio,
  CASE
    -- Sem vendas ou clientes
    WHEN qtd_clientes = 0 OR vendas_realizadas = 0 
      THEN 'Crítico'
    -- Bateu meta e tem bom ticket médio
    WHEN vendas_realizadas >= meta_mensal 
      AND vendas_realizadas / NULLIF(qtd_clientes, 0) >= 2500 
      THEN 'Excelente'
    -- Bateu meta
    WHEN vendas_realizadas >= meta_mensal 
      THEN 'Bom'
    -- Próximo da meta (>= 90%)
    WHEN vendas_realizadas >= meta_mensal * 0.9 
      THEN 'Regular'
    ELSE 'Atenção Necessária'
  END AS classificacao
FROM vendas_vendedor
ORDER BY vendas_realizadas DESC;

-- Resultado:
--  vendedor | vendas_realizadas | qtd_clientes | perc_meta | ticket_medio | classificacao
-- ----------+-------------------+--------------+-----------+--------------+---------------
--  Carlos   |          72000.00 |           30 |    144.00 |      2400.00 | Bom
--  João     |          65000.00 |           25 |    130.00 |      2600.00 | Excelente
--  Pedro    |          50000.00 |            0 |    100.00 |         NULL | Crítico
--  Maria    |          48000.00 |           18 |     96.00 |      2666.67 | Regular
--  Ana      |              0.00 |            0 |      0.00 |         NULL | Crítico

-- COMPARAÇÃO: Com e sem NULLIF
SELECT 
  vendedor,
  qtd_clientes,
  -- SEM NULLIF (retorna NULL ou erro):
  vendas_realizadas / qtd_clientes AS sem_nullif,
  
  -- COM NULLIF (retorna NULL de forma controlada):
  vendas_realizadas / NULLIF(qtd_clientes, 0) AS com_nullif,
  
  -- COM COALESCE + NULLIF (substitui NULL por valor padrão):
  COALESCE(
    vendas_realizadas / NULLIF(qtd_clientes, 0),
    0
  ) AS com_ambos
FROM vendas_vendedor;

-- Resultado mostra o controle sobre NULLs:
--  vendedor | qtd_clientes | sem_nullif | com_nullif | com_ambos
-- ----------+--------------+------------+------------+-----------
--  João     |           25 |    2600.00 |    2600.00 |   2600.00
--  Maria    |           18 |    2666.67 |    2666.67 |   2666.67
--  Pedro    |            0 | ERRO/NULL  |       NULL |      0.00
--  Ana      |            0 | ERRO/NULL  |       NULL |      0.00
--  Carlos   |           30 |    2400.00 |    2400.00 |   2400.00
```

**Explicação:**
- `NULLIF(valor, comparacao)` retorna NULL se `valor = comparacao`;
- Uso clássico: `NULLIF(divisor, 0)` para evitar divisão por zero;
- Retorna NULL ao invés de erro, permitindo continuar a consulta;
- Pode combinar com COALESCE para substituir NULL por valor padrão;
- `ORDER BY ... NULLS LAST` coloca NULLs no final da ordenação.

---

## Exercício 4: Operador módulo (%) - Distribuição e padrões

```sql
-- a) Distribuir entre 3 equipes
SELECT 
  id,
  cliente,
  valor,
  id % 3 AS resto,
  CASE
    WHEN id % 3 = 0 THEN 'Equipe A'
    WHEN id % 3 = 1 THEN 'Equipe B'
    ELSE 'Equipe C'
  END AS equipe_responsavel
FROM pedidos
ORDER BY id;

-- Resultado:
--  id |  cliente   | valor  | resto | equipe_responsavel
-- ----+------------+--------+-------+--------------------
--   1 | Cliente A  | 150.00 |     1 | Equipe B
--   2 | Cliente B  | 200.00 |     2 | Equipe C
--   3 | Cliente C  | 180.00 |     0 | Equipe A
--   4 | Cliente D  | 300.00 |     1 | Equipe B
--   5 | Cliente E  | 250.00 |     2 | Equipe C
--   6 | Cliente F  | 175.00 |     0 | Equipe A
--   7 | Cliente G  | 220.00 |     1 | Equipe B
--   8 | Cliente H  | 190.00 |     2 | Equipe C
--   9 | Cliente I  | 280.00 |     0 | Equipe A
--  10 | Cliente J  | 160.00 |     1 | Equipe B

-- Distribuição balanceada:
SELECT 
  CASE
    WHEN id % 3 = 0 THEN 'Equipe A'
    WHEN id % 3 = 1 THEN 'Equipe B'
    ELSE 'Equipe C'
  END AS equipe,
  COUNT(*) AS qtd_pedidos,
  SUM(valor) AS valor_total
FROM pedidos
GROUP BY equipe
ORDER BY equipe;

-- Resultado:
--    equipe   | qtd_pedidos | valor_total
-- ------------+-------------+-------------
--  Equipe A   |           3 |      635.00
--  Equipe B   |           4 |      830.00
--  Equipe C   |           3 |      640.00

-- b) Pedidos pares e ímpares
SELECT 
  id,
  cliente,
  CASE
    WHEN id % 2 = 0 THEN 'Par'
    ELSE 'Ímpar'
  END AS paridade,
  -- Uso alternativo: verificar se último dígito é 0 ou 5
  CASE
    WHEN id % 10 IN (0, 5) THEN 'Termina em 0 ou 5'
    ELSE 'Outro'
  END AS final_id
FROM pedidos
ORDER BY id;

-- Resultado:
--  id |  cliente   | paridade | final_id
-- ----+------------+----------+--------------------
--   1 | Cliente A  | Ímpar    | Outro
--   2 | Cliente B  | Par      | Outro
--   3 | Cliente C  | Ímpar    | Outro
--   4 | Cliente D  | Par      | Outro
--   5 | Cliente E  | Ímpar    | Termina em 0 ou 5
--   6 | Cliente F  | Par      | Outro
--   7 | Cliente G  | Ímpar    | Outro
--   8 | Cliente H  | Par      | Outro
--   9 | Cliente I  | Ímpar    | Outro
--  10 | Cliente J  | Par      | Termina em 0 ou 5

-- c) Grupos de 5 (lotes)
SELECT 
  id,
  cliente,
  id / 5 AS lote_numero,  -- Divisão inteira
  id % 5 AS posicao_no_lote,
  CONCAT('Lote ', (id / 5) + 1) AS lote_nome
FROM pedidos
ORDER BY id;

-- Resultado:
--  id |  cliente   | lote_numero | posicao_no_lote | lote_nome
-- ----+------------+-------------+-----------------+-----------
--   1 | Cliente A  |           0 |               1 | Lote 1
--   2 | Cliente B  |           0 |               2 | Lote 1
--   3 | Cliente C  |           0 |               3 | Lote 1
--   4 | Cliente D  |           0 |               4 | Lote 1
--   5 | Cliente E  |           1 |               0 | Lote 2
--   6 | Cliente F  |           1 |               1 | Lote 2
--   7 | Cliente G  |           1 |               2 | Lote 2
--   8 | Cliente H  |           1 |               3 | Lote 2
--   9 | Cliente I  |           1 |               4 | Lote 2
--  10 | Cliente J  |           2 |               0 | Lote 3

-- Resumo por lote:
SELECT 
  CONCAT('Lote ', (id / 5) + 1) AS lote,
  COUNT(*) AS qtd_pedidos,
  SUM(valor) AS valor_total,
  MIN(id) AS primeiro_id,
  MAX(id) AS ultimo_id
FROM pedidos
GROUP BY id / 5
ORDER BY id / 5;

-- Resultado:
--  lote  | qtd_pedidos | valor_total | primeiro_id | ultimo_id
-- -------+-------------+-------------+-------------+-----------
--  Lote 1|           4 |      830.00 |           1 |         4
--  Lote 2|           5 |     1115.00 |           5 |         9
--  Lote 3|           1 |      160.00 |          10 |        10
```

**Explicação:**
- Operador `%` (módulo) retorna o resto da divisão;
- Útil para distribuição cíclica (round-robin);
- `id % 2` identifica pares (0) e ímpares (1);
- `id % N` distribui entre N grupos (0 a N-1);
- Divisão inteira (`/`) agrupa em blocos sequenciais;
- Combinação de `/` e `%` cria sistemas de loteamento.

---

## Exercício 5: Funções matemáticas - Cálculos complexos

```sql
-- a) Juros compostos usando POWER
SELECT 
  cliente,
  valor_inicial,
  taxa_anual,
  anos,
  ROUND(
    valor_inicial * POWER(1 + (taxa_anual / 100), anos),
    2
  ) AS valor_futuro,
  ROUND(
    valor_inicial * POWER(1 + (taxa_anual / 100), anos) - valor_inicial,
    2
  ) AS ganho_total
FROM investimentos
ORDER BY ganho_total DESC;

-- Resultado:
--  cliente | valor_inicial | taxa_anual | anos | valor_futuro | ganho_total
-- ---------+---------------+------------+------+--------------+-------------
--  Bruno   |      25000.00 |       7.20 |   10 |     50115.69 |    25115.69
--  Diego   |      15000.00 |       9.00 |    7 |     27422.48 |    12422.48
--  Carla   |      50000.00 |       6.80 |    3 |     60820.98 |    10820.98
--  Elena   |      30000.00 |       7.50 |    4 |     39573.56 |     9573.56
--  Ana     |      10000.00 |       8.50 |    5 |     15036.52 |     5036.52

-- b) Diferentes tipos de arredondamento
SELECT 
  cliente,
  valor_inicial * POWER(1 + (taxa_anual / 100), anos) AS valor_exato,
  
  -- ROUND: arredonda para o mais próximo
  ROUND(
    valor_inicial * POWER(1 + (taxa_anual / 100), anos),
    2
  ) AS arredondado,
  
  -- CEIL: sempre arredonda para cima
  CEIL(
    valor_inicial * POWER(1 + (taxa_anual / 100), anos)
  ) AS para_cima,
  
  -- FLOOR: sempre arredonda para baixo
  FLOOR(
    valor_inicial * POWER(1 + (taxa_anual / 100), anos)
  ) AS para_baixo
FROM investimentos
WHERE cliente = 'Ana';

-- Resultado:
--  cliente |    valor_exato     | arredondado | para_cima | para_baixo
-- ---------+--------------------+-------------+-----------+------------
--  Ana     | 15036.518809062501 |    15036.52 |     15037 |      15036

-- c) Ganho percentual e análise de risco
SELECT 
  cliente,
  taxa_anual,
  anos,
  valor_inicial,
  
  -- Ganho percentual total
  ROUND(
    (POWER(1 + (taxa_anual / 100), anos) - 1) * 100,
    2
  ) AS ganho_percentual_total,
  
  -- Raiz quadrada da taxa (indicador de volatilidade/risco)
  ROUND(SQRT(taxa_anual), 2) AS indice_risco,
  
  -- Classificação de risco
  CASE
    WHEN SQRT(taxa_anual) < 2.5 THEN 'Baixo Risco'
    WHEN SQRT(taxa_anual) < 3.0 THEN 'Risco Moderado'
    ELSE 'Alto Risco'
  END AS classificacao_risco
FROM investimentos
ORDER BY indice_risco DESC;

-- Resultado:
--  cliente | taxa_anual | anos | valor_inicial | ganho_percentual_total | indice_risco | classificacao_risco
-- ---------+------------+------+---------------+------------------------+--------------+---------------------
--  Diego   |       9.00 |    7 |      15000.00 |                  82.82 |         3.00 | Alto Risco
--  Ana     |       8.50 |    5 |      10000.00 |                  50.37 |         2.92 | Risco Moderado
--  Elena   |       7.50 |    4 |      30000.00 |                  31.91 |         2.74 | Risco Moderado
--  Bruno   |       7.20 |   10 |      25000.00 |                 100.46 |         2.68 | Risco Moderado
--  Carla   |       6.80 |    3 |      50000.00 |                  21.64 |         2.61 | Risco Moderado

-- d) Diferenças absolutas entre investimentos
SELECT 
  i1.cliente AS cliente_1,
  i2.cliente AS cliente_2,
  i1.valor_inicial AS valor_1,
  i2.valor_inicial AS valor_2,
  
  -- Diferença absoluta (sempre positiva)
  ABS(i1.valor_inicial - i2.valor_inicial) AS diferenca_valores,
  
  -- Diferença de taxas
  ABS(i1.taxa_anual - i2.taxa_anual) AS diferenca_taxas,
  
  -- Quem tem maior investimento inicial
  CASE
    WHEN i1.valor_inicial > i2.valor_inicial 
      THEN i1.cliente || ' tem mais'
    WHEN i1.valor_inicial < i2.valor_inicial 
      THEN i2.cliente || ' tem mais'
    ELSE 'Valores iguais'
  END AS comparacao
FROM investimentos i1
CROSS JOIN investimentos i2
WHERE i1.id < i2.id  -- Evita duplicatas
ORDER BY diferenca_valores DESC
LIMIT 5;

-- Resultado:
--  cliente_1 | cliente_2 | valor_1  | valor_2  | diferenca_valores | diferenca_taxas | comparacao
-- -----------+-----------+----------+----------+-------------------+-----------------+----------------
--  Ana       | Carla     | 10000.00 | 50000.00 |          40000.00 |            1.70 | Carla tem mais
--  Bruno     | Carla     | 25000.00 | 50000.00 |          25000.00 |            0.40 | Carla tem mais
--  Diego     | Carla     | 15000.00 | 50000.00 |          35000.00 |            2.20 | Carla tem mais
--  Ana       | Elena     | 10000.00 | 30000.00 |          20000.00 |            1.00 | Elena tem mais
--  Bruno     | Elena     | 25000.00 | 30000.00 |           5000.00 |            0.30 | Elena tem mais

-- BÔNUS: Comparação de estratégias (conservadora vs agressiva)
SELECT 
  cliente,
  valor_inicial,
  taxa_anual,
  anos,
  
  -- Estratégia conservadora (50% da taxa)
  ROUND(
    valor_inicial * POWER(1 + (taxa_anual * 0.5 / 100), anos),
    2
  ) AS cenario_conservador,
  
  -- Estratégia normal
  ROUND(
    valor_inicial * POWER(1 + (taxa_anual / 100), anos),
    2
  ) AS cenario_normal,
  
  -- Estratégia agressiva (150% da taxa)
  ROUND(
    valor_inicial * POWER(1 + (taxa_anual * 1.5 / 100), anos),
    2
  ) AS cenario_agressivo,
  
  -- Diferença entre agressivo e conservador
  ROUND(
    ABS(
      valor_inicial * POWER(1 + (taxa_anual * 1.5 / 100), anos) -
      valor_inicial * POWER(1 + (taxa_anual * 0.5 / 100), anos)
    ),
    2
  ) AS amplitude
FROM investimentos
ORDER BY amplitude DESC;

-- Resultado mostra o impacto da taxa:
--  cliente | valor_inicial | taxa_anual | anos | cenario_conservador | cenario_normal | cenario_agressivo | amplitude
-- ---------+---------------+------------+------+---------------------+----------------+-------------------+-----------
--  Bruno   |      25000.00 |       7.20 |   10 |            34532.23 |       50115.69 |          69683.85 |  35151.62
--  Diego   |      15000.00 |       9.00 |    7 |            19856.00 |       27422.48 |          36458.13 |  16602.13
--  Carla   |      50000.00 |       6.80 |    3 |            55170.88 |       60820.98 |          66853.92 |  11683.04
--  Elena   |      30000.00 |       7.50 |    4 |            33872.86 |       39573.56 |          45946.67 |  12073.81
--  Ana     |      10000.00 |       8.50 |    5 |            12247.72 |       15036.52 |          18165.29 |   5917.57
```

**Explicação:**

**Funções matemáticas utilizadas:**

1. **POWER(base, expoente):**
   - Calcula potências (base^expoente)
   - Essencial para juros compostos
   - Alternativa: operador `^` no PostgreSQL

2. **ROUND(numero, decimais):**
   - Arredonda para o número de casas decimais especificado
   - Usa arredondamento "bancário" (0.5 → para o par mais próximo)

3. **CEIL(numero):**
   - Arredonda SEMPRE para cima
   - Retorna o menor inteiro >= número

4. **FLOOR(numero):**
   - Arredonda SEMPRE para baixo
   - Retorna o maior inteiro <= número

5. **SQRT(numero):**
   - Calcula raiz quadrada
   - Útil para cálculos estatísticos e análise de risco

6. **ABS(numero):**
   - Retorna valor absoluto (sempre positivo)
   - Útil para calcular distâncias e diferenças

**Aplicações práticas:**
- Cálculos financeiros (juros compostos, investimentos)
- Análise de risco e volatilidade
- Comparações entre valores (diferenças absolutas)
- Simulações de cenários
- Arredondamentos estratégicos (para cima em orçamentos, para baixo em descontos)

**Dicas:**
- Use ROUND para apresentação de valores
- Use CEIL para orçamentos (arredondar para cima evita déficit)
- Use FLOOR para descontos (arredondar para baixo protege margem)
- Combine funções para análises complexas
- POWER é fundamental para crescimento exponencial
