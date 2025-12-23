# Exercícios - Capítulo 13: Subconsultas - Consultas dentro de consultas

## Exercício 1: Subconsulta básica com IN

```sql
SELECT 
  nome,
  salario
FROM funcionarios
WHERE departamento_id IN (
  SELECT id
  FROM departamentos
  WHERE nome IN ('TI', 'Vendas')
);

-- Resultado:
--  nome  | salario
-- -------+---------
--  Alice | 5000.00
--  Bob   | 4000.00
--  Carol | 5500.00
--  Elena | 4500.00
```

**Explicação:**
- A subconsulta `SELECT id FROM departamentos WHERE nome IN ('TI', 'Vendas')` retorna os IDs dos departamentos TI e Vendas;
- A consulta externa usa `IN` para filtrar funcionários cujo `departamento_id` está nessa lista;
- Esta é a forma mais básica de subconsulta no `WHERE`.

---

## Exercício 2: Subconsulta com NOT IN

```sql
SELECT 
  nome,
  salario
FROM funcionarios
WHERE departamento_id NOT IN (
  SELECT id
  FROM departamentos
  WHERE nome = 'RH'
);

-- Resultado:
--  nome  | salario
-- -------+---------
--  Alice | 5000.00
--  Bob   | 4000.00
--  Carol | 5500.00
--  Elena | 4500.00
```

**Explicação:**
`NOT IN` exclui todos os registros cujo valor está na lista retornada pela subconsulta. David (do RH) não aparece no resultado.

---

## Exercício 3: Subconsulta retornando valor único

```sql
SELECT 
  nome,
  salario
FROM funcionarios
WHERE salario > (
  SELECT AVG(salario)
  FROM funcionarios
);

-- Resultado (assumindo média = 4500.00):
--  nome  | salario
-- -------+---------
--  Alice | 5000.00
--  Carol | 5500.00
```

**Explicação:**
- A subconsulta `SELECT AVG(salario) FROM funcionarios` retorna UM único valor (a média);
- Quando a subconsulta retorna um único valor, podemos usar operadores de comparação (`>`, `<`, `=`, etc.);
- Se a subconsulta retornasse múltiplos valores com `=`, causaria erro.

---

## Exercício 4: Subconsulta com MAX

```sql
SELECT 
  nome,
  salario
FROM funcionarios
WHERE salario = (
  SELECT MAX(salario)
  FROM funcionarios
);

-- Resultado:
--  nome  | salario
-- -------+---------
--  Carol | 5500.00
```

**Explicação:**
- `SELECT MAX(salario)` retorna o maior salário (5500.00);
- A consulta externa compara cada salário com esse valor máximo;
- Se houver empate (dois funcionários com o maior salário), ambos aparecerão.

---

## Exercício 5: Subconsulta no SELECT

```sql
SELECT 
  d.nome AS departamento,
  (
    SELECT COUNT(*)
    FROM funcionarios f
    WHERE f.departamento_id = d.id
  ) AS total_funcionarios
FROM departamentos d
ORDER BY total_funcionarios DESC;

-- Resultado:
--  departamento | total_funcionarios
-- --------------+--------------------
--  TI           |                  2
--  Vendas       |                  2
--  RH           |                  1
--  Marketing    |                  0
```

**Explicação:**
- A subconsulta no `SELECT` é executada uma vez para cada departamento;
- `WHERE f.departamento_id = d.id` conecta a subconsulta à linha atual da consulta externa;
- Esta é uma subconsulta correlacionada (referencia a tabela externa);
- Marketing aparece com 0 funcionários.

---

## Exercício 6: Subconsulta correlacionada

```sql
SELECT 
  f.nome,
  d.nome AS departamento,
  f.salario,
  (
    SELECT AVG(f2.salario)
    FROM funcionarios f2
    WHERE f2.departamento_id = f.departamento_id
  ) AS media_departamento,
  f.salario - (
    SELECT AVG(f2.salario)
    FROM funcionarios f2
    WHERE f2.departamento_id = f.departamento_id
  ) AS diferenca_da_media
FROM funcionarios f
INNER JOIN departamentos d ON f.departamento_id = d.id
ORDER BY departamento, nome;

-- Resultado:
--  nome  | dept.  | salario | media_dept | diferenca
-- -------+--------+---------+------------+-----------
--  David | RH     | 3500.00 |    3500.00 |      0.00
--  Alice | TI     | 5000.00 |    5250.00 |   -250.00
--  Carol | TI     | 5500.00 |    5250.00 |    250.00
--  Bob   | Vendas | 4000.00 |    4250.00 |   -250.00
--  Elena | Vendas | 4500.00 |    4250.00 |    250.00
```

**Explicação:**
- A subconsulta é executada para cada linha de funcionário;
- `WHERE f2.departamento_id = f.departamento_id` faz a correlação;
- Calculamos a diferença entre o salário individual e a média do departamento;
- Valores positivos = acima da média; negativos = abaixo da média.

---

## Exercício 7: EXISTS - Verificando existência

```sql
SELECT 
  d.nome AS departamento
FROM departamentos d
WHERE EXISTS (
  SELECT 1
  FROM funcionarios f
  WHERE f.departamento_id = d.id
);

-- Resultado:
--  departamento
-- --------------
--  TI
--  Vendas
--  RH

-- Marketing não aparece (não tem funcionários)
```

**Explicação:**
- `EXISTS` retorna TRUE se a subconsulta retornar pelo menos uma linha;
- `SELECT 1` é uma convenção - o valor selecionado não importa, apenas se há linhas;
- Mais eficiente que `IN` porque para assim que encontra a primeira correspondência;
- Esta é uma subconsulta correlacionada (usa `d.id` da consulta externa).

---

## Exercício 8: NOT EXISTS - Encontrando ausências

```sql
SELECT 
  d.nome AS departamento
FROM departamentos d
WHERE NOT EXISTS (
  SELECT 1
  FROM funcionarios f
  WHERE f.departamento_id = d.id
);

-- Resultado:
--  departamento
-- --------------
--  Marketing
```

**Explicação:**
`NOT EXISTS` retorna TRUE quando a subconsulta NÃO retorna nenhuma linha. Muito útil para encontrar registros "órfãos" ou ausências.

---

## Exercício 9: Subconsulta no FROM (tabela derivada)

```sql
SELECT 
  departamento,
  salario_medio
FROM (
  SELECT 
    d.nome AS departamento,
    AVG(f.salario) AS salario_medio
  FROM departamentos d
  INNER JOIN funcionarios f 
    ON d.id = f.departamento_id
  GROUP BY d.id, d.nome
) AS medias_por_depto
WHERE salario_medio > 4000
ORDER BY salario_medio DESC;

-- Resultado:
--  departamento | salario_medio
-- --------------+---------------
--  TI           |       5250.00
--  Vendas       |       4250.00
```

**Explicação:**
- A subconsulta no `FROM` cria uma "tabela temporária" chamada `medias_por_depto`;
- Primeiro calculamos a média por departamento;
- Depois filtramos apenas médias > 4000;
- O alias `medias_por_depto` é OBRIGATÓRIO no PostgreSQL;
- Alternativa: poderíamos usar `HAVING` diretamente no GROUP BY.

---

## Exercício 10: Comparando subconsulta vs JOIN

```sql
-- Versão 1: Usando subconsulta com IN
SELECT 
  nome,
  salario
FROM funcionarios
WHERE departamento_id IN (
  SELECT id
  FROM departamentos
  WHERE nome LIKE 'T%' OR nome LIKE 'V%'
);

-- Versão 2: Usando INNER JOIN
SELECT 
  f.nome,
  f.salario
FROM funcionarios f
INNER JOIN departamentos d 
  ON f.departamento_id = d.id
WHERE d.nome LIKE 'T%' OR d.nome LIKE 'V%';

-- Ambas retornam:
--  nome  | salario
-- -------+---------
--  Alice | 5000.00
--  Bob   | 4000.00
--  Carol | 5500.00
--  Elena | 4500.00

-- Qual é melhor?
-- - JOIN: Geralmente mais eficiente, especialmente se precisar de colunas de ambas
-- - Subconsulta: Mais legível se só precisar filtrar
```

**Explicação:**
Muitas consultas podem ser escritas tanto com subconsultas quanto com JOINs. Neste caso, ambas têm performance similar, mas JOIN pode ser mais eficiente.

---

## Exercício 11: Subconsultas aninhadas

```sql
SELECT 
  nome,
  salario
FROM funcionarios
WHERE salario > (
  SELECT AVG(salario)
  FROM funcionarios
  WHERE departamento_id = (
    SELECT id
    FROM departamentos
    WHERE nome = 'TI'
  )
);

-- Resultado:
--  nome  | salario
-- -------+---------
--  Carol | 5500.00
--  Alice | 5000.00 (se média TI for menor que 5000)
```

**Explicação:**
- Subconsulta mais interna: Encontra o ID do departamento de TI;
- Subconsulta intermediária: Calcula a média salarial desse departamento;
- Consulta externa: Compara cada salário com essa média;
- Subconsultas aninhadas são executadas de dentro para fora.

---

## Exercício 12: ALL e ANY com subconsultas

```sql
-- a) Mais caro que TODOS (> ALL = maior que o máximo)
SELECT 
  nome,
  preco
FROM produtos
WHERE preco > ALL (
  SELECT preco
  FROM produtos
  WHERE preco < 200
);

-- Resultado:
--    nome    | preco
-- -----------+--------
--  Notebook  | 3000.00
--  Monitor   | 1200.00
-- (Mais caros que o máximo dos produtos < 200, que é 150.00)

-- b) Mais caro que ALGUM (> ANY = maior que o mínimo)
SELECT 
  nome,
  preco
FROM produtos
WHERE preco > ANY (
  SELECT preco
  FROM produtos
  WHERE preco < 200
);

-- Resultado:
--    nome    | preco
-- -----------+--------
--  Notebook  | 3000.00
--  Teclado   | 150.00
--  Monitor   | 1200.00
-- (Mais caros que o mínimo dos produtos < 200, que é 50.00)

-- Equivalências úteis:
-- > ALL = maior que o MAX
-- < ALL = menor que o MIN
-- > ANY = maior que o MIN
-- < ANY = menor que o MAX
```

**Explicação:**
- `ALL`: Condição deve ser verdadeira para TODOS os valores da subconsulta;
- `ANY`: Condição deve ser verdadeira para PELO MENOS UM valor;
- `> ALL` é equivalente a `> MAX(...)`;
- `> ANY` é equivalente a `> MIN(...)`.

---

## Exercício 13: Subconsulta para ranking

```sql
SELECT 
  nome,
  salario,
  (
    SELECT COUNT(*) + 1
    FROM funcionarios f2
    WHERE f2.salario > f1.salario
  ) AS ranking
FROM funcionarios f1
ORDER BY ranking;

-- Resultado:
--  nome  | salario | ranking
-- -------+---------+---------
--  Carol | 5500.00 |       1
--  Alice | 5000.00 |       2
--  Elena | 4500.00 |       3
--  Bob   | 4000.00 |       4
--  David | 3500.00 |       5
```

**Explicação:**
- Para cada funcionário, conta quantos têm salário maior;
- Se ninguém tem salário maior, COUNT = 0, então ranking = 1;
- Se um tem salário maior, COUNT = 1, então ranking = 2;
- Esta é uma subconsulta correlacionada;
- Em SQL moderno, `RANK()` window function seria mais eficiente.

---

## Exercício 14: Subconsulta para encontrar duplicatas

```sql
SELECT 
  produto,
  data_venda,
  COUNT(*) AS vendas_duplicadas
FROM vendas
WHERE (produto, data_venda) IN (
  SELECT produto, data_venda
  FROM vendas
  GROUP BY produto, data_venda
  HAVING COUNT(*) > 1
)
GROUP BY produto, data_venda;

-- Resultado:
--  produto  | data_venda | vendas_duplicadas
-- ----------+------------+-------------------
--  Notebook | 2024-12-01 |                 2

-- Alternativa mais simples (sem subconsulta):
SELECT 
  produto,
  data_venda,
  COUNT(*) AS vendas_duplicadas
FROM vendas
GROUP BY produto, data_venda
HAVING COUNT(*) > 1;
```

**Explicação:**
- A subconsulta encontra combinações (produto, data) que aparecem mais de uma vez;
- `(produto, data_venda) IN (...)` compara múltiplas colunas simultaneamente;
- Neste caso, a versão sem subconsulta (só com `HAVING`) é mais simples e eficiente.

---

## Exercício 15: EXISTS vs IN - Performance
s
```sql
-- Todas retornam o mesmo resultado:
--  nome
-- --------
--  TI
--  Vendas
--  RH

-- ANÁLISE DE PERFORMANCE E LEGIBILIDADE:

-- Forma A (IN):
-- ✓ Legível e intuitiva
-- ✗ Pode ser lento com subconsultas grandes
-- ✗ Problema com NULL: IN (NULL) sempre retorna falso
-- Uso: Quando a subconsulta retorna poucos valores conhecidos

SELECT nome FROM departamentos
WHERE id IN (SELECT departamento_id FROM funcionarios);

-- Forma B (EXISTS):
-- ✓ MELHOR PERFORMANCE para verificar existência
-- ✓ Para assim que encontra primeira correspondência
-- ✓ Lida bem com NULL
-- ✓ Otimizador pode criar melhores planos de execução
-- ✗ Sintaxe menos intuitiva para iniciantes
-- Uso: RECOMENDADO para verificar existência em tabelas grandes

SELECT nome FROM departamentos d
WHERE EXISTS (
  SELECT 1 FROM funcionarios f 
  WHERE f.departamento_id = d.id
);

-- Forma C (JOIN + DISTINCT):
-- ✓ Útil se precisar de dados de ambas as tabelas
-- ✗ DISTINCT pode ser custoso
-- ✗ Gera duplicatas que precisam ser removidas
-- Uso: Quando precisa de colunas de ambas as tabelas

SELECT DISTINCT d.nome
FROM departamentos d
INNER JOIN funcionarios f ON d.id = f.departamento_id;

-- RECOMENDAÇÃO:
-- Para apenas verificar existência: Use EXISTS (Forma B)
-- Para buscar dados de ambas tabelas: Use JOIN sem DISTINCT
-- Para lista pequena conhecida: Use IN
```

**Explicação:**
`EXISTS` é geralmente a melhor escolha para verificar existência. É otimizado para parar na primeira correspondência, enquanto `IN` precisa processar todos os valores.

---

## Exercício 16: Subconsulta com UPDATE

```sql
-- Primeiro, ver a média atual
SELECT AVG(salario) FROM funcionarios;
-- Resultado: 4500.00

-- Atualizar salários abaixo da média
UPDATE funcionarios
SET salario = salario * 1.10
WHERE salario < (
  SELECT AVG(salario)
  FROM funcionarios
);

-- Verificar resultado
SELECT nome, salario FROM funcionarios ORDER BY salario;

-- Resultado após o UPDATE:
--  nome  | salario
-- -------+---------
--  David | 3850.00  (era 3500.00, +10%)
--  Bob   | 4400.00  (era 4000.00, +10%)
--  Elena | 4500.00  (não mudou, estava na média)
--  Alice | 5000.00  (não mudou, acima da média)
--  Carol | 5500.00  (não mudou, acima da média)
```

**Explicação:**
- Subconsultas podem ser usadas em `UPDATE` e `DELETE`;
- A subconsulta calcula a média ANTES do UPDATE começar;
- Apenas registros que atendem à condição são atualizados;
- CUIDADO: Sempre teste com SELECT antes de fazer UPDATE!

---

## Exercício 17: Subconsulta com DELETE

```sql
-- Primeiro, identificar quais seriam deletados (sempre teste antes!)
SELECT 
  f.nome,
  d.nome AS departamento
FROM funcionarios f
INNER JOIN departamentos d ON f.departamento_id = d.id
WHERE f.departamento_id IN (
  SELECT departamento_id
  FROM funcionarios
  GROUP BY departamento_id
  HAVING COUNT(*) = 1
);

-- Resultado (departamentos com 1 funcionário):
--  nome  | departamento
-- -------+--------------
--  David | RH

-- Agora, deletar
DELETE FROM funcionarios
WHERE departamento_id IN (
  SELECT departamento_id
  FROM funcionarios
  GROUP BY departamento_id
  HAVING COUNT(*) = 1
);

-- David seria removido (único do RH)
```

**Explicação:**
- A subconsulta encontra departamentos com apenas 1 funcionário;
- `DELETE` remove funcionários desses departamentos;
- **SEMPRE** teste com SELECT antes de DELETE!
- Use transações (`BEGIN; ... ROLLBACK;`) para testar com segurança.

---

## Exercício 18: Subconsulta em INSERT

```sql
INSERT INTO historico_salarios_altos 
  (funcionario_nome, salario)
SELECT 
  nome,
  salario
FROM funcionarios
WHERE salario > (
  SELECT AVG(salario)
  FROM funcionarios
);

-- Verificar inserção
SELECT * FROM historico_salarios_altos;

-- Resultado:
--  id | funcionario_nome | salario | data_registro
-- ----+------------------+---------+---------------
--   1 | Alice            | 5000.00 | 2024-12-23
--   2 | Carol            | 5500.00 | 2024-12-23
```

**Explicação:**
- `INSERT ... SELECT` permite inserir resultados de uma consulta;
- A subconsulta filtra apenas salários acima da média;
- Muito útil para criar tabelas de histórico, backup ou análise;
- A data é preenchida automaticamente pelo DEFAULT.

---

## Exercício 19: Subconsulta complexa - Segundo maior salário

```sql
-- Método 1: Usando subconsulta com MAX
SELECT MAX(salario) AS segundo_maior_salario
FROM funcionarios
WHERE salario < (
  SELECT MAX(salario)
  FROM funcionarios
);

-- Resultado:
--  segundo_maior_salario
-- -----------------------
--                5000.00

-- Método 2: Usando subconsulta correlacionada (mais complexo)
SELECT DISTINCT salario AS segundo_maior_salario
FROM funcionarios f1
WHERE 2 = (
  SELECT COUNT(DISTINCT f2.salario)
  FROM funcionarios f2
  WHERE f2.salario >= f1.salario
);

-- Ambos retornam 5000.00 (Carol tem 5500, Alice tem 5000)
```

**Explicação:**
- Método 1: Encontra o maior salário que é menor que o máximo geral;
- Método 2: Conta quantos salários são maiores ou iguais ao atual (ranking);
- Se a contagem é 2, é o segundo maior;
- Método 1 é mais simples e eficiente.

---

## Exercício 20: Questão conceitual - Quando usar subconsulta?

```sql
-- a) Listar produtos e suas categorias
-- RESPOSTA: JOIN
-- Justificativa: Precisamos de dados de AMBAS as tabelas lado a lado

SELECT p.nome, c.nome AS categoria
FROM produtos p
INNER JOIN categorias c ON p.categoria_id = c.id;

-- b) Verificar se existem pedidos sem cliente
-- RESPOSTA: Subconsulta com EXISTS ou LEFT JOIN
-- Justificativa: Estamos VERIFICANDO EXISTÊNCIA

SELECT COUNT(*) FROM pedidos
WHERE NOT EXISTS (
  SELECT 1 FROM clientes WHERE clientes.id = pedidos.cliente_id
);
-- Ou: LEFT JOIN + WHERE clientes.id IS NULL

-- c) Calcular o total de vendas por vendedor
-- RESPOSTA: JOIN + GROUP BY
-- Justificativa: Agregação sobre dados relacionados

SELECT 
  v.nome,
  SUM(p.valor) AS total_vendas
FROM vendedores v
INNER JOIN pedidos p ON v.id = p.vendedor_id
GROUP BY v.id, v.nome;

-- d) Encontrar clientes que nunca fizeram pedidos
-- RESPOSTA: Subconsulta com NOT EXISTS (mais eficiente)
-- Justificativa: Verificar AUSÊNCIA de relacionamento

SELECT nome FROM clientes c
WHERE NOT EXISTS (
  SELECT 1 FROM pedidos WHERE cliente_id = c.id
);
-- Alternativa: LEFT JOIN + WHERE pedidos.id IS NULL

-- e) Mostrar para cada produto quantas vezes foi vendido
-- RESPOSTA: Subconsulta no SELECT ou JOIN + GROUP BY
-- Justificativa: Cálculo por linha da tabela principal

-- Opção 1: Subconsulta (se quiser todos os produtos, mesmo sem vendas)
SELECT 
  nome,
  (SELECT COUNT(*) FROM vendas WHERE produto_id = p.id) AS total_vendas
FROM produtos p;

-- Opção 2: JOIN (se só quiser produtos vendidos)
SELECT 
  p.nome,
  COUNT(v.id) AS total_vendas
FROM produtos p
LEFT JOIN vendas v ON p.id = v.produto_id
GROUP BY p.id, p.nome;

-- RESUMO DAS DIRETRIZES:
-- Use SUBCONSULTA quando:
-- - Verificar existência/ausência (EXISTS/NOT EXISTS)
-- - Comparar com valores agregados (média, máximo, etc.)
-- - Adicionar coluna calculada por linha
-- - Lógica em múltiplas etapas

-- Use JOIN quando:
-- - Precisa de colunas de múltiplas tabelas
-- - Agregação sobre dados relacionados
-- - Performance é crítica e tabelas são grandes
-- - Lógica é simples e linear
```

**Explicação:**
Não há resposta absoluta - depende do contexto. Considere performance, legibilidade e manutenibilidade ao escolher.

---

## Exercício Desafio: Sistema de recomendações

```sql
-- Solução completa usando múltiplas subconsultas

SELECT DISTINCT
  c.nome AS cliente,
  l.titulo AS livro_recomendado,
  l.genero,
  l.preco,
  CASE 
    -- Regra 1: Mesmo gênero de livros bem avaliados
    WHEN l.genero IN (
      SELECT DISTINCT l2.genero
      FROM compras co
      INNER JOIN livros l2 ON co.livro_id = l2.id
      WHERE co.cliente_id = c.id 
        AND co.avaliacao >= 4
    ) THEN 'Mesmo gênero que você gostou'
    
    -- Regra 2: Comprado por vizinhos da cidade
    WHEN l.id IN (
      SELECT DISTINCT co2.livro_id
      FROM compras co2
      INNER JOIN clientes c2 ON co2.cliente_id = c2.id
      WHERE c2.cidade = c.cidade 
        AND c2.id != c.id
    ) THEN 'Popular na sua cidade'
    
    ELSE 'Recomendação geral'
  END AS motivo_recomendacao
FROM clientes c
CROSS JOIN livros l
WHERE 
  -- Regra 0: Não recomendar livros já comprados
  l.id NOT IN (
    SELECT livro_id
    FROM compras
    WHERE cliente_id = c.id
  )
  AND (
    -- Mesmos gêneros que o cliente gostou (4-5 estrelas)
    l.genero IN (
      SELECT DISTINCT l2.genero
      FROM compras co
      INNER JOIN livros l2 ON co.livro_id = l2.id
      WHERE co.cliente_id = c.id 
        AND co.avaliacao >= 4
    )
    OR
    -- Livros comprados por outros da mesma cidade
    l.id IN (
      SELECT DISTINCT co2.livro_id
      FROM compras co2
      INNER JOIN clientes c2 ON co2.cliente_id = c2.id
      WHERE c2.cidade = c.cidade 
        AND c2.id != c.id
    )
  )
ORDER BY c.nome, l.preco DESC;

-- Resultado esperado:
--  cliente |    livro_recomendado   |  genero  | preco | motivo_recomendacao
-- ---------+------------------------+----------+-------+-------------------------
--  Ana     | Neuromancer            | Ficção   | 38.00 | Mesmo gênero que você gostou
--  Ana     | Cosmos                 | Ciência  | 50.00 | Popular na sua cidade
--  Bruno   | Neuromancer            | Ficção   | 38.00 | Mesmo gênero que você gostou
--  Bruno   | Sapiens                | História | 45.00 | Recomendação geral
--  Carla   | Sapiens... (já comprou, não aparece)
--  Carla   | Cosmos... (já comprou, não aparece)
--  Diego   | (nenhuma recomendação - não comprou nada ainda)

-- ANÁLISE DA CONSULTA:

-- 1. Estrutura principal:
--    - CROSS JOIN: Gera todas as combinações cliente-livro possíveis
--    - WHERE: Filtra combinações relevantes

-- 2. Subconsultas utilizadas:
--    a) NOT IN: Exclui livros já comprados
--    b) IN (gêneros): Encontra gêneros que o cliente gostou
--    c) IN (livros): Encontra livros populares na cidade
--    d) CASE: Determina o motivo da recomendação

-- 3. Técnicas avançadas:
--    - Subconsultas correlacionadas (referenciam c.id, c.cidade)
--    - Subconsultas aninhadas (dentro do CASE)
--    - DISTINCT: Evita duplicatas
--    - Múltiplas condições com OR

-- 4. Performance:
--    - Em produção, considere criar uma view materializada
--    - Índices em (cliente_id, livro_id, avaliacao) melhorariam performance
--    - Para milhões de registros, técnicas de machine learning seriam melhores

```

**Explicação detalhada:**

Este exercício demonstra o uso avançado de subconsultas em um cenário real:

1. **Múltiplas subconsultas correlacionadas:**
   - Cada subconsulta referencia dados da consulta externa
   - Executadas para cada combinação cliente-livro

2. **Subconsultas em diferentes cláusulas:**
   - `WHERE` com `NOT IN`: Filtrar livros já comprados
   - `WHERE` com `IN`: Encontrar padrões de gênero e localização
   - `CASE` com subconsultas: Determinar motivo da recomendação

3. **Lógica de negócio complexa:**
   - Exclusão (livros comprados)
   - Similaridade (mesmo gênero de livros bem avaliados)
   - Colaboração (livros populares entre vizinhos)

4. **Alternativa com CTEs:**
   - A versão com CTEs (Common Table Expressions) é mais legível
   - Separa a lógica em passos nomeados
   - Facilita manutenção e debugging
   - Pode ter melhor performance (subconsultas calculadas uma vez)

5. **Aplicações no mundo real:**
   - E-commerce (Amazon, Netflix)
   - Redes sociais (amigos em comum, posts relacionados)
   - Sistemas de música (Spotify, Apple Music)
   - Análise de padrões de comportamento

Este exercício mostra como subconsultas podem ser combinadas para criar sistemas sofisticados de recomendação e análise de dados!