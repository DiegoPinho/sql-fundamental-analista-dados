# Exercícios - Capítulo 07: Operações de Busca (SELECT)

## Exercício 1: SELECT básico

a) Todos os dados:
```sql
SELECT * FROM funcionarios;
```

Resultado:
```
 id |     nome      |    cargo    | salario | departamento 
----+---------------+-------------+---------+--------------
  1 | Ana Silva     | Analista    | 5000.00 | TI
  2 | Bruno Costa   | Gerente     | 8000.00 | Vendas
  3 | Carla Souza   | Analista    | 5500.00 | TI
  4 | Diego Lima    | Assistente  | 3000.00 | RH
```

b) Apenas nomes:
```sql
SELECT nome FROM funcionarios;
```

Resultado:
```
     nome      
---------------
 Ana Silva
 Bruno Costa
 Carla Souza
 Diego Lima
```

c) Nome e salário:
```sql
SELECT nome, salario FROM funcionarios;
```

Resultado:
```
     nome      | salario 
---------------+---------
 Ana Silva     | 5000.00
 Bruno Costa   | 8000.00
 Carla Souza   | 5500.00
 Diego Lima    | 3000.00
```

---

## Exercício 2: Ordem das colunas no SELECT

a) salario, nome, cargo:
```sql
SELECT salario, nome, cargo 
FROM funcionarios;
```

Resultado:
```
 salario |     nome      |    cargo    
---------+---------------+-------------
 5000.00 | Ana Silva     | Analista
 8000.00 | Bruno Costa   | Gerente
 5500.00 | Carla Souza   | Analista
 3000.00 | Diego Lima    | Assistente
```

b) cargo, departamento, nome:
```sql
SELECT cargo, departamento, nome 
FROM funcionarios;
```

Resultado:
```
    cargo    | departamento |     nome      
-------------+--------------+---------------
 Analista    | TI           | Ana Silva
 Gerente     | Vendas       | Bruno Costa
 Analista    | TI           | Carla Souza
 Assistente  | RH           | Diego Lima
```

**Observação**: A ordem das colunas no resultado corresponde exatamente à ordem especificada no SELECT.

---

## Exercício 3: Usando AS para renomear colunas

```sql
SELECT 
  nm AS "Nome do Produto",
  prc AS "Preço (R$)",
  qtd AS "Quantidade em Estoque"
FROM produtos;
```

Resultado:
```
 Nome do Produto | Preço (R$) | Quantidade em Estoque 
-----------------+------------+-----------------------
 Notebook        |    3500.00 |                    15
 Mouse           |      45.90 |                   100
 Teclado         |     120.00 |                    50
```

**Importante**: As aspas duplas são obrigatórias quando o alias contém espaços ou caracteres especiais.

---

## Exercício 4: SELECT DISTINCT básico

a) Todos os produtos com repetições:
```sql
SELECT produto FROM vendas;
```

Resultado:
```
 produto  
----------
 Notebook
 Mouse
 Notebook
 Teclado
 Mouse
```

b) Produtos sem repetições:
```sql
SELECT DISTINCT produto FROM vendas;
```

Resultado:
```
 produto  
----------
 Notebook
 Mouse
 Teclado
```

c) Vendedores sem repetições:
```sql
SELECT DISTINCT vendedor FROM vendas;
```

Resultado:
```
 vendedor 
----------
 João
 Maria
 Pedro
```

---

## Exercício 5: DISTINCT com múltiplas colunas

**Consulta A** - Produtos únicos:
```sql
SELECT DISTINCT produto FROM vendas;
```

Resultado:
```
 produto  
----------
 Notebook
 Mouse
 Teclado
```
Retorna cada produto apenas uma vez, independentemente de quem vendeu.

**Consulta B** - Combinações únicas de produto + vendedor:
```sql
SELECT DISTINCT produto, vendedor FROM vendas;
```

Resultado:
```
 produto  | vendedor 
----------+----------
 Notebook | João
 Mouse    | Maria
 Teclado  | Pedro
```

**Diferença crucial**: 
- Consulta A: considera apenas a coluna `produto` para eliminar duplicatas
- Consulta B: considera a **combinação** de `produto` E `vendedor`. Mesmo que "Notebook" apareça duas vezes, se foi vendido pelo mesmo vendedor (João), aparece apenas uma vez.

---

## Exercício 6: ORDER BY crescente e decrescente

a) Nota crescente (menor para maior):
```sql
SELECT nome, nota FROM alunos ORDER BY nota ASC;
```

Resultado:
```
 nome  | nota 
-------+------
 Diego | 6.50
 Bruno | 7.00
 Alice | 8.50
 Eva   | 8.80
 Carla | 9.20
```

b) Nota decrescente (maior para menor):
```sql
SELECT nome, nota FROM alunos ORDER BY nota DESC;
```

Resultado:
```
 nome  | nota 
-------+------
 Carla | 9.20
 Eva   | 8.80
 Alice | 8.50
 Bruno | 7.00
 Diego | 6.50
```

c) Nome em ordem alfabética:
```sql
SELECT nome FROM alunos ORDER BY nome;
```
Ou:
```sql
SELECT nome FROM alunos ORDER BY nome ASC;
```

Resultado:
```
 nome  
-------
 Alice
 Bruno
 Carla
 Diego
 Eva
```

d) Idade decrescente:
```sql
SELECT nome, idade FROM alunos ORDER BY idade DESC;
```

Resultado:
```
 nome  | idade 
-------+-------
 Bruno |    22
 Diego |    21
 Alice |    20
 Eva   |    20
 Carla |    19
```

---

## Exercício 7: ORDER BY com múltiplas colunas

```sql
SELECT nome, idade, nota 
FROM alunos 
ORDER BY idade DESC, nota DESC;
```

Resultado:
```
 nome  | idade | nota 
-------+-------+------
 Bruno |    22 | 7.00
 Diego |    21 | 6.50
 Eva   |    20 | 8.80
 Alice |    20 | 8.50
 Carla |    19 | 9.20
```

**Explicação**: 
- Bruno (22 anos) aparece primeiro
- Diego (21 anos) em segundo
- Eva e Alice têm 20 anos, mas Eva tem nota maior (8.80 vs 8.50), então Eva aparece primeiro
- Carla (19 anos) por último

**Ordem importa**: Se invertêssemos para `ORDER BY nota DESC, idade DESC`, o resultado seria diferente - primeiro ordenaria por nota, depois por idade.

---

## Exercício 8: Combinando WHERE, DISTINCT e ORDER BY

```sql
SELECT DISTINCT cliente 
FROM pedidos 
WHERE status = 'Concluído'
ORDER BY cliente ASC;
```

Resultado:
```
 cliente   
-----------
 Empresa A
 Empresa C
```

**Explicação**:
- WHERE filtra apenas pedidos concluídos
- DISTINCT elimina repetições (Empresa A aparece 2 vezes nos concluídos)
- ORDER BY ordena alfabeticamente

---

## Exercício 9: Ordenação com valores NULL

a) Ordem crescente (NULL por último, padrão):
```sql
SELECT titulo, data_conclusao 
FROM tarefas 
ORDER BY data_conclusao ASC;
```

Resultado:
```
 titulo  | data_conclusao 
---------+----------------
 Tarefa C| 2023-11-15
 Tarefa A| 2023-12-01
 Tarefa E| 2023-12-10
 Tarefa B| NULL
 Tarefa D| NULL
```

b) Ordem decrescente (NULL por primeiro, padrão):
```sql
SELECT titulo, data_conclusao 
FROM tarefas 
ORDER BY data_conclusao DESC;
```

Resultado:
```
 titulo  | data_conclusao 
---------+----------------
 Tarefa B| NULL
 Tarefa D| NULL
 Tarefa E| 2023-12-10
 Tarefa A| 2023-12-01
 Tarefa C| 2023-11-15
```

c) Crescente com NULL primeiro:
```sql
SELECT titulo, data_conclusao 
FROM tarefas 
ORDER BY data_conclusao ASC NULLS FIRST;
```

Resultado:
```
 titulo  | data_conclusao 
---------+----------------
 Tarefa B| NULL
 Tarefa D| NULL
 Tarefa C| 2023-11-15
 Tarefa A| 2023-12-01
 Tarefa E| 2023-12-10
```

---

## Exercício 10: NULLS FIRST e NULLS LAST

a) Prioridade crescente, NULL no final:
```sql
SELECT titulo, prioridade 
FROM tarefas 
ORDER BY prioridade ASC NULLS LAST;
```

b) Prioridade decrescente, NULL no final:
```sql
SELECT titulo, prioridade 
FROM tarefas 
ORDER BY prioridade DESC NULLS LAST;
```

**Uso prático**: 
- `NULLS FIRST`: Útil quando NULL representa "prioridade máxima" ou "urgente"
- `NULLS LAST`: Útil quando NULL representa "sem prioridade" ou "não definido"

---

## Exercício 11: SELECT com cálculos

```sql
SELECT 
  nome,
  (preco_venda - preco_custo) AS "Lucro por Unidade",
  (preco_venda * estoque) AS "Valor Total"
FROM produtos_loja
ORDER BY (preco_venda - preco_custo) DESC;
```

Resultado:
```
   nome   | Lucro por Unidade | Valor Total 
----------+-------------------+-------------
 Notebook |           1000.00 |    35000.00
 Teclado  |             60.00 |     6000.00
 Mouse    |             25.90 |     4590.00
```

---

## Exercício 12: Questão conceitual - SELECT *

**Problemas do SELECT *:**

1. Performance: 
   - Retorna todas as colunas, mesmo as não necessárias
   - Transfere mais dados pela rede
   - Usa mais memória no servidor e na aplicação
   - Exemplo: Se você só precisa de nome e email, mas a tabela tem 50 colunas incluindo campos BLOB/TEXT grandes

2. Manutenibilidade:
   - Se alguém adicionar novas colunas à tabela, o SELECT * retornará automaticamente essas colunas
   - Pode quebrar aplicações que esperam um número específico de colunas
   - Dificulta entender exatamente quais dados estão sendo usados

3. Segurança:
   - Pode expor dados sensíveis não intencionalmente
   - Exemplo: Adiciona-se uma coluna `senha_hash` ou `cpf` e o SELECT * começa a retorná-la

4. Legibilidade:
   - Código menos autodocumentado
   - Difícil saber quais colunas realmente importam para a query sem olhar a estrutura da tabela

5. Índices:
   - Impede o uso de "covering indexes" (índices que contêm todas as colunas necessárias)
   - Força o banco a acessar a tabela mesmo quando um índice poderia ser suficiente

**Boa prática**: 
```sql
-- ❌ Evite
SELECT * FROM usuarios;

-- ✅ Prefira
SELECT id, nome, email FROM usuarios;
```

---

## Exercício 13: Consulta complexa combinada

```sql
SELECT DISTINCT 
  titulo AS "Título do Livro",
  autor AS "Nome do Autor"
FROM livros
WHERE genero = 'Ficção' 
  AND preco > 30.00
ORDER BY titulo ASC;
```

Resultado:
```
 Título do Livro | Nome do Autor 
-----------------+---------------
 1984            | George Orwell
 Fundação        | Isaac Asimov
```

**Explicação**:
- WHERE filtra apenas Ficção com preço > 30
- DISTINCT elimina a duplicata do livro 1984
- AS renomeia as colunas
- ORDER BY ordena alfabeticamente

---

## Exercício 14: Desafio - Análise de vendas

Solução com conhecimento atual (sem agregação):

```sql
SELECT DISTINCT 
  categoria,
  (quantidade * preco_unitario) AS "Total por Transação"
FROM transacoes
WHERE (quantidade * preco_unitario) > 500.00
ORDER BY (quantidade * preco_unitario) DESC;
```

Resultado:
```
  categoria   | Total por Transação 
--------------+---------------------
 Eletrônicos  |             7000.00
 Eletrônicos  |             3500.00
 Eletrônicos  |             1600.00
```

**Nota**: Esta solução mostra cada transação individualmente. Para somar os valores por categoria (o que a pergunta realmente pede), precisaríamos do `GROUP BY` que será visto em capítulos futuros.

Solução completa (prévia do que virá):

```sql
SELECT 
  categoria,
  SUM(quantidade * preco_unitario) AS "Total por Categoria"
FROM transacoes
GROUP BY categoria
HAVING SUM(quantidade * preco_unitario) > 500.00
ORDER BY SUM(quantidade * preco_unitario) DESC;
```

---

## Exercício 15: SELECT com filtros em texto

a) Cidades únicas:
```sql
SELECT DISTINCT cidade 
FROM clientes;
```

Resultado:
```
     cidade      
-----------------
 São Paulo
 Rio de Janeiro
 Belo Horizonte
```

b) Estados únicos ordenados:
```sql
SELECT DISTINCT estado 
FROM clientes
ORDER BY estado ASC;
```

Resultado:
```
 estado 
--------
 MG
 RJ
 SP
```

c) Clientes de São Paulo ordenados:
```sql
SELECT nome 
FROM clientes
WHERE cidade = 'São Paulo'
ORDER BY nome ASC;
```

Resultado:
```
     nome      
---------------
 Ana Silva
 Carla Santos
 Eva Lima
```

---

## Exercício 16: Entendendo a ordem de execução

**Ordem lógica de processamento:**

1. FROM: Identifica a tabela `produtos` de onde os dados serão buscados

2. WHERE: Aplica o filtro `preco > 100.00`, eliminando registros que não atendem a condição

3. SELECT: Seleciona as colunas `categoria` e `preco` dos registros filtrados

4. DISTINCT: Remove linhas duplicadas do resultado

5. ORDER BY: Ordena o resultado final por `categoria` em ordem crescente

**Diagrama do fluxo:**
```
produtos (tabela completa)
    ↓
WHERE (filtrar preco > 100)
    ↓
SELECT (selecionar categoria, preco)
    ↓
DISTINCT (remover duplicatas)
    ↓
ORDER BY (ordenar por categoria)
    ↓
Resultado final
```

**Implicação prática**: 
- Você não pode usar aliases criados no SELECT dentro do WHERE
- Mas pode usar aliases no ORDER BY (porque ORDER BY é processado depois)

```sql
-- ❌ Isto não funciona
SELECT preco * 2 AS preco_dobro
FROM produtos
WHERE preco_dobro > 100;

-- ✅ Isto funciona
SELECT preco * 2 AS preco_dobro
FROM produtos
WHERE preco > 50
ORDER BY preco_dobro;
```

---

## Exercício 17: Prática final integrada

a) Categorias vendidas:
```sql
SELECT DISTINCT categoria 
FROM pedidos_ecommerce;
```

Resultado:
```
  categoria   
--------------
 Eletrônicos
 Periféricos
```

b) Clientes com pedidos entregues:
```sql
SELECT DISTINCT cliente 
FROM pedidos_ecommerce
WHERE status = 'Entregue'
ORDER BY cliente ASC;
```

Resultado:
```
  cliente   
------------
 Empresa A
 Empresa B
 Empresa D
```

c) Pedidos entregues com valor final:
```sql
SELECT 
  produto,
  valor AS "Valor Original",
  (valor - desconto) AS "Valor Final"
FROM pedidos_ecommerce
WHERE status = 'Entregue'
ORDER BY (valor - desconto) DESC;
```

Resultado:
```
 produto  | Valor Original | Valor Final 
----------+----------------+-------------
 Notebook |        3500.00 |     3350.00
 Notebook |        3500.00 |     3300.00
 Teclado  |         120.00 |      110.00
 Mouse    |          45.90 |       45.90
 Mouse    |          45.90 |       45.90
```

d) Produtos cancelados:
```sql
SELECT DISTINCT produto 
FROM pedidos_ecommerce
WHERE status = 'Cancelado';
```

Resultado:
```
 produto 
---------
 Monitor
```