# Exercícios - Capítulo 08: Cláusulas para Buscas

## Exercício 1: WHERE com operadores de comparação

a) Preço maior que 200:
```sql
SELECT nome, preco 
FROM produtos 
WHERE preco > 200;
```

Resultado:
```
   nome   | preco   
----------+---------
 Notebook | 3500.00
 Monitor  |  800.00
 Webcam   |  250.00
```

b) Estoque menor ou igual a 30:
```sql
SELECT nome, estoque 
FROM produtos 
WHERE estoque <= 30;
```

Resultado:
```
  nome   | estoque 
---------+---------
 Notebook|      15
 Monitor |      20
 Webcam  |      30
```

c) Produtos que NÃO sejam Eletrônicos:
```sql
SELECT nome, categoria 
FROM produtos 
WHERE categoria != 'Eletrônicos';
```

Ou:
```sql
SELECT nome, categoria 
FROM produtos 
WHERE categoria <> 'Eletrônicos';
```

Resultado:
```
  nome   |  categoria  
---------+-------------
 Mouse   | Periféricos
 Teclado | Periféricos
 Webcam  | Periféricos
```

d) Preço entre 100 e 300:
```sql
SELECT nome, preco 
FROM produtos 
WHERE preco >= 100 AND preco <= 300;
```

Resultado:
```
  nome   | preco  
---------+--------
 Teclado | 120.00
 Webcam  | 250.00
```

---

## Exercício 2: Operador AND

a) Periféricos com preço < 100:
```sql
SELECT nome, categoria, preco 
FROM produtos 
WHERE categoria = 'Periféricos' 
  AND preco < 100;
```

Resultado:
```
  nome  |  categoria  | preco 
--------+-------------+-------
 Mouse  | Periféricos | 45.90
```

b) Estoque > 20 e preço < 1000:
```sql
SELECT nome, estoque, preco 
FROM produtos 
WHERE estoque > 20 
  AND preco < 1000;
```

Resultado:
```
  nome   | estoque | preco  
---------+---------+--------
 Mouse   |     100 |  45.90
 Teclado |      50 | 120.00
 Webcam  |      30 | 250.00
```

c) Eletrônicos com estoque > 10:
```sql
SELECT nome, categoria, estoque 
FROM produtos 
WHERE categoria = 'Eletrônicos' 
  AND estoque > 10;
```

Resultado:
```
   nome   |  categoria  | estoque 
----------+-------------+---------
 Notebook | Eletrônicos |      15
 Monitor  | Eletrônicos |      20
```

---

## Exercício 3: Operador OR

a) Preço < 50 OU > 500:
```sql
SELECT nome, preco 
FROM produtos 
WHERE preco < 50 OR preco > 500;
```

Resultado:
```
   nome   | preco   
----------+---------
 Notebook | 3500.00
 Mouse    |   45.90
 Monitor  |  800.00
```

b) Eletrônicos OU estoque < 20:
```sql
SELECT nome, categoria, estoque 
FROM produtos 
WHERE categoria = 'Eletrônicos' 
   OR estoque < 20;
```

Resultado:
```
   nome   |  categoria  | estoque 
----------+-------------+---------
 Notebook | Eletrônicos |      15
 Monitor  | Eletrônicos |      20
```

c) Mouse OU Teclado:
```sql
SELECT nome 
FROM produtos 
WHERE nome = 'Mouse' OR nome = 'Teclado';
```

Resultado:
```
  nome   
---------
 Mouse
 Teclado
```

---

## Exercício 4: Combinando AND e OR

```sql
SELECT nome, cargo, departamento, salario 
FROM funcionarios 
WHERE (departamento = 'TI' AND salario > 5000) 
   OR cargo = 'Gerente';
```

Resultado:
```
     nome      |   cargo    | departamento | salario 
---------------+------------+--------------+---------
 Bruno Costa   | Gerente    | Vendas       | 8000.00
 Carla Souza   | Analista   | TI           | 5500.00
 Eva Santos    | Gerente    | TI           | 9000.00
```

**Explicação**: 
- Carla Souza: atende à primeira condição (TI com salário > 5000)
- Bruno Costa e Eva Santos: atendem à segunda condição (são Gerentes)
- Os parênteses são importantes para agrupar corretamente a lógica

---

## Exercício 5: Operador NOT

a) Que NÃO são Analistas:
```sql
SELECT nome, cargo 
FROM funcionarios 
WHERE NOT cargo = 'Analista';
```

Ou:
```sql
SELECT nome, cargo 
FROM funcionarios 
WHERE cargo != 'Analista';
```

Resultado:
```
     nome      |   cargo    
---------------+------------
 Bruno Costa   | Gerente
 Diego Lima    | Assistente
 Eva Santos    | Gerente
```

b) NÃO TI e NÃO Assistentes:
```sql
SELECT nome, cargo, departamento 
FROM funcionarios 
WHERE NOT (departamento = 'TI' OR cargo = 'Assistente');
```

Resultado:
```
     nome     |  cargo  | departamento 
--------------+---------+--------------
 Bruno Costa  | Gerente | Vendas
```

c) Salário NÃO entre 4000 e 6000:
```sql
SELECT nome, salario 
FROM funcionarios 
WHERE NOT (salario >= 4000 AND salario <= 6000);
```

Resultado:
```
     nome      | salario 
---------------+---------
 Bruno Costa   | 8000.00
 Diego Lima    | 3000.00
 Eva Santos    | 9000.00
```

---

## Exercício 6: BETWEEN

a) Quantidade entre 3 e 8:
```sql
SELECT produto, quantidade 
FROM vendas 
WHERE quantidade BETWEEN 3 AND 8;
```

Resultado:
```
 produto | quantidade 
---------+------------
 Teclado |          5
 Monitor |          3
 Webcam  |          8
```

b) Preço entre 100 e 500:
```sql
SELECT produto, preco_unitario 
FROM vendas 
WHERE preco_unitario BETWEEN 100 AND 500;
```

Resultado:
```
 produto | preco_unitario 
---------+----------------
 Teclado |         120.00
 Webcam  |         250.00
```

c) Vendas entre 05/11 e 15/11:
```sql
SELECT produto, data_venda 
FROM vendas 
WHERE data_venda BETWEEN '2023-11-05' AND '2023-11-15';
```

Resultado:
```
 produto | data_venda 
---------+------------
 Mouse   | 2023-11-05
 Teclado | 2023-11-10
 Monitor | 2023-11-15
```

d) Quantidade NÃO entre 5 e 10:
```sql
SELECT produto, quantidade 
FROM vendas 
WHERE quantidade NOT BETWEEN 5 AND 10;
```

Resultado:
```
  produto  | quantidade 
-----------+------------
 Notebook  |          2
 Monitor   |          3
```

---

## Exercício 7: Operador IN

a) Estados SP, RJ ou MG:
```sql
SELECT nome, estado 
FROM clientes 
WHERE estado IN ('SP', 'RJ', 'MG');
```

Resultado:
```
     nome      | estado 
---------------+--------
 João Silva    | SP
 Maria Santos  | RJ
 Pedro Costa   | MG
```

b) Status Ativo ou Pendente:
```sql
SELECT nome, status 
FROM clientes 
WHERE status IN ('Ativo', 'Pendente');
```

Resultado:
```
     nome      |  status  
---------------+----------
 João Silva    | Ativo
 Pedro Costa   | Ativo
 Ana Oliveira  | Ativo
 Carlos Lima   | Pendente
```

c) NÃO SP, RJ ou MG:
```sql
SELECT nome, estado 
FROM clientes 
WHERE estado NOT IN ('SP', 'RJ', 'MG');
```

Resultado:
```
     nome      | estado 
---------------+--------
 Ana Oliveira  | PR
 Carlos Lima   | BA
```

d) Nomes específicos:
```sql
SELECT nome 
FROM clientes 
WHERE nome IN ('João Silva', 'Ana Oliveira', 'Carlos Lima');
```

Resultado:
```
     nome      
---------------
 João Silva
 Ana Oliveira
 Carlos Lima
```

---

## Exercício 8: Operador LIKE com %

a) Título contém 'SQL':
```sql
SELECT titulo 
FROM livros 
WHERE titulo LIKE '%SQL%';
```

Resultado:
```
       titulo        
---------------------
 Aprendendo SQL
 SQL Avançado
 SQL para Analistas
```

b) Título começa com 'SQL':
```sql
SELECT titulo 
FROM livros 
WHERE titulo LIKE 'SQL%';
```

Resultado:
```
      titulo      
------------------
 SQL Avançado
 SQL para Analistas
```

c) Título termina com 'Python':
```sql
SELECT titulo 
FROM livros 
WHERE titulo LIKE '%Python';
```

Resultado:
```
        titulo         
-----------------------
 Introdução ao Python
```

d) Autor contém 'Silva':
```sql
SELECT autor 
FROM livros 
WHERE autor LIKE '%Silva%';
```

Resultado:
```
    autor    
-------------
 João Silva
```

e) Editora contém 'Tech':
```sql
SELECT titulo, editora 
FROM livros 
WHERE editora LIKE '%Tech%';
```

Resultado:
```
       titulo        |  editora  
---------------------+-----------
 Aprendendo SQL      | TechBooks
 SQL para Analistas  | TechBooks
```

---

## Exercício 9: Operador LIKE com _ (underscore)

a) A-00 + 1 dígito:
```sql
SELECT codigo, descricao 
FROM codigos 
WHERE codigo LIKE 'A-00_';
```

Resultado:
```
 codigo |        descricao        
--------+-------------------------
 A-001  | Produto tipo A, série 1
 A-002  | Produto tipo A, série 2
```

b) Qualquer letra + -00 + 1 dígito:
```sql
SELECT codigo, descricao 
FROM codigos 
WHERE codigo LIKE '_-00_';
```

Resultado:
```
 codigo |        descricao        
--------+-------------------------
 A-001  | Produto tipo A, série 1
 A-002  | Produto tipo A, série 2
 B-001  | Produto tipo B, série 1
```

c) Exatamente 5 caracteres:
```sql
SELECT codigo 
FROM codigos 
WHERE codigo LIKE '_____';
```

Resultado:
```
 codigo 
--------
 A-001
 A-002
 B-001
```

---

## Exercício 10: ILIKE (case-insensitive)

a) Usando LIKE com LOWER:
```sql
SELECT remetente, assunto 
FROM emails 
WHERE LOWER(assunto) LIKE '%urgente%';
```

b) Usando ILIKE:
```sql
SELECT remetente, assunto 
FROM emails 
WHERE assunto ILIKE '%urgente%';
```

Ambas retornam:
```
     remetente      |            assunto             
--------------------+--------------------------------
 joao@empresa.com   | URGENTE: Reunião amanhã
 pedro@empresa.com  | urgente: Aprovação necessária
 carlos@empresa.com | Urgente - Atualização de sistema
```

c) **Diferença**:
- `LIKE com LOWER()`: Converte o texto para minúsculas antes de comparar. Funciona em qualquer banco de dados.
- `ILIKE`: Faz comparação case-insensitive nativamente. Mais direto, mas específico do PostgreSQL (não funciona em todos os SGBDs).

Ambos produzem o mesmo resultado, mas `ILIKE` é mais limpo e legível quando disponível.

---

## Exercício 11: IS NULL e IS NOT NULL

a) Sem responsável:
```sql
SELECT titulo, responsavel 
FROM tarefas 
WHERE responsavel IS NULL;
```

Resultado:
```
  titulo  | responsavel 
----------+-------------
 Tarefa 2 | 
 Tarefa 5 | 
```

b) Já concluídas:
```sql
SELECT titulo, data_conclusao 
FROM tarefas 
WHERE data_conclusao IS NOT NULL;
```

Resultado:
```
  titulo  | data_conclusao 
----------+----------------
 Tarefa 1 | 2023-11-15
 Tarefa 4 | 2023-11-20
```

c) Sem observações:
```sql
SELECT titulo, observacoes 
FROM tarefas 
WHERE observacoes IS NULL;
```

Resultado:
```
  titulo  | observacoes 
----------+-------------
 Tarefa 2 | 
 Tarefa 4 | 
```

d) Tem responsável MAS não concluída:
```sql
SELECT titulo, responsavel, data_conclusao 
FROM tarefas 
WHERE responsavel IS NOT NULL 
  AND data_conclusao IS NULL;
```

Resultado:
```
  titulo  | responsavel | data_conclusao 
----------+-------------+----------------
 Tarefa 3 | Maria       | 
```

---

## Exercício 12: LIMIT básico

a) 3 mais vendidos:
```sql
SELECT nome, vendas_totais 
FROM produtos_ranking 
ORDER BY vendas_totais DESC 
LIMIT 3;
```

Resultado:
```
   nome    | vendas_totais 
-----------+---------------
 Produto D |          3200
 Produto F |          2800
 Produto B |          2300
```

b) 2 com melhor avaliação:
```sql
SELECT nome, avaliacao 
FROM produtos_ranking 
ORDER BY avaliacao DESC 
LIMIT 2;
```

Resultado:
```
   nome    | avaliacao 
-----------+-----------
 Produto C |      4.90
 Produto A |      4.80
```

c) 4 primeiros alfabeticamente:
```sql
SELECT nome 
FROM produtos_ranking 
ORDER BY nome ASC 
LIMIT 4;
```

Resultado:
```
   nome    
-----------
 Produto A
 Produto B
 Produto C
 Produto D
```

d) Menos vendido (apenas 1):
```sql
SELECT nome, vendas_totais 
FROM produtos_ranking 
ORDER BY vendas_totais ASC 
LIMIT 1;
```

Resultado:
```
   nome    | vendas_totais 
-----------+---------------
 Produto C |           890
```

---

## Exercício 13: LIMIT com OFFSET (Paginação)

a) Página 1:
```sql
SELECT nome, vendas_totais 
FROM produtos_ranking 
ORDER BY vendas_totais DESC 
LIMIT 2 OFFSET 0;
```

Ou simplesmente:
```sql
SELECT nome, vendas_totais 
FROM produtos_ranking 
ORDER BY vendas_totais DESC 
LIMIT 2;
```

Resultado:
```
   nome    | vendas_totais 
-----------+---------------
 Produto D |          3200
 Produto F |          2800
```

b) Página 2:
```sql
SELECT nome, vendas_totais 
FROM produtos_ranking 
ORDER BY vendas_totais DESC 
LIMIT 2 OFFSET 2;
```

Resultado:
```
   nome    | vendas_totais 
-----------+---------------
 Produto B |          2300
 Produto A |          1500
```

c) Página 3:
```sql
SELECT nome, vendas_totais 
FROM produtos_ranking 
ORDER BY vendas_totais DESC 
LIMIT 2 OFFSET 4;
```

Resultado:
```
   nome    | vendas_totais 
-----------+---------------
 Produto E |          1100
 Produto C |           890
```

**Fórmula geral para paginação:**
- `LIMIT tamanho_pagina OFFSET (numero_pagina - 1) * tamanho_pagina`
- Página 1: OFFSET 0
- Página 2: OFFSET 2
- Página 3: OFFSET 4

---

## Exercício 14: Combinação complexa

```sql
SELECT cliente, produto, valor, status, data_pedido 
FROM pedidos_completos 
WHERE (cliente = 'Empresa A' OR cliente = 'Empresa B')
  AND status IN ('Entregue', 'Pendente')
  AND valor > 50
  AND observacoes IS NULL
ORDER BY data_pedido DESC 
LIMIT 3;
```

Ou usando IN para simplificar:
```sql
SELECT cliente, produto, valor, status, data_pedido 
FROM pedidos_completos 
WHERE cliente IN ('Empresa A', 'Empresa B')
  AND status IN ('Entregue', 'Pendente')
  AND valor > 50
  AND observacoes IS NULL
ORDER BY data_pedido DESC 
LIMIT 3;
```

Resultado:
```
 cliente   | produto | valor  |  status  | data_pedido 
-----------+---------+--------+----------+-------------
 Empresa B | Webcam  | 250.00 | Entregue | 2023-11-12
(1 row)
```

**Explicação**: Apenas a Webcam da Empresa B atende a todos os critérios.

---

## Exercício 15: Busca de padrões complexos

a) Email corporativo:
```sql
SELECT username, email 
FROM usuarios_sistema 
WHERE email LIKE '%@empresa.com';
```

Resultado:
```
  username   |           email            
-------------+----------------------------
 joao.silva  | joao.silva@empresa.com
 pedro.costa | pedro.costa@empresa.com
 carlos.lima | carlos.lima@empresa.com
```

b) Username com ponto:
```sql
SELECT username 
FROM usuarios_sistema 
WHERE username LIKE '%.%';
```

Resultado:
```
  username   
-------------
 joao.silva
 pedro.costa
 carlos.lima
```

c) Desenvolvedores com telefone:
```sql
SELECT username, cargo, telefone 
FROM usuarios_sistema 
WHERE cargo LIKE '%Desenvolvedor%' 
  AND telefone IS NOT NULL;
```

Resultado:
```
   username    |        cargo         |    telefone     
---------------+----------------------+-----------------
 joao.silva    | Desenvolvedor Senior | 11-98765-4321
 ana_oliveira  | Desenvolvedor Pleno  | 11-97654-3210
```

d) Cargo termina com Senior OU Pleno:
```sql
SELECT username, cargo 
FROM usuarios_sistema 
WHERE cargo LIKE '%Senior' OR cargo LIKE '%Pleno';
```

Resultado:
```
   username   |        cargo         
--------------+----------------------
 joao.silva   | Desenvolvedor Senior
 ana_oliveira | Desenvolvedor Pleno
```

e) Top 2 com email corporativo:
```sql
SELECT username, email 
FROM usuarios_sistema 
WHERE email LIKE '%@empresa.com' 
ORDER BY username ASC 
LIMIT 2;
```

Resultado:
```
  username   |          email          
-------------+-------------------------
 carlos.lima | carlos.lima@empresa.com
 joao.silva  | joao.silva@empresa.com
```

---

## Exercício 16: Questão conceitual - Ordem das cláusulas

**Problema**: A ordem das cláusulas está incorreta. LIMIT aparece antes de FROM.

**Ordem correta das cláusulas em SQL:**
1. SELECT
2. FROM
3. WHERE
4. ORDER BY
5. LIMIT

**Consulta corrigida:**
```sql
SELECT nome, preco 
FROM produtos 
WHERE preco > 100 
ORDER BY preco DESC 
LIMIT 5;
```

**Regra mnemônica**: "**S**elect **F**rom **W**here **O**rder **L**imit" (SFWOL)

---

## Exercício 17: Desafio final - Análise de vendas

a) Top 3 clientes por número de transações:
```sql
SELECT cliente, COUNT(*) as total_compras 
FROM transacoes_ecommerce 
GROUP BY cliente 
ORDER BY total_compras DESC 
LIMIT 3;
```

Nota: Esta usa COUNT que ainda não vimos. Alternativa sem agregação:
```sql
SELECT DISTINCT cliente 
FROM transacoes_ecommerce 
ORDER BY cliente;
-- Depois contar manualmente ou visualmente
```

Resposta esperada: João Silva (3), Maria Santos (2), outros (1 cada)

b) Avaliação ruim (1-3) com comentários:
```sql
SELECT produto, avaliacao, comentario 
FROM transacoes_ecommerce 
WHERE avaliacao IN (1, 2, 3) 
  AND comentario IS NOT NULL;
```

Resultado:
```
     produto     | avaliacao |    comentario    
-----------------+-----------+------------------
 Notebook Asus   |         3 | Veio com defeito
```

c) Eletrônicos/Periféricos, valor > 100, entregues após 05/11:
```sql
SELECT produto, categoria, valor, data_compra 
FROM transacoes_ecommerce 
WHERE categoria IN ('Eletrônicos', 'Periféricos')
  AND valor > 100
  AND status = 'Entregue'
  AND data_compra > '2023-11-05'
ORDER BY data_compra;
```

Resultado:
```
      produto      |  categoria  | valor   | data_compra 
-------------------+-------------+---------+-------------
 Webcam HD         | Periféricos |  250.00 | 2023-11-10
 Notebook Asus     | Eletrônicos | 4200.00 | 2023-11-12
```

d) Não avaliados, excluindo cancelados e pendentes:
```sql
SELECT produto, status, avaliacao 
FROM transacoes_ecommerce 
WHERE avaliacao IS NULL 
  AND status NOT IN ('Cancelado', 'Pendente');
```

Resultado:
```
      produto      |  status  | avaliacao 
-------------------+----------+-----------
 Teclado Mecânico  | Entregue | 
 Mousepad          | Entregue | 
```

e) Top 5 por valor total, exceto Acessórios:
```sql
SELECT 
  produto, 
  valor, 
  quantidade,
  (valor * quantidade) AS valor_total
FROM transacoes_ecommerce 
WHERE categoria != 'Acessórios'
ORDER BY (valor * quantidade) DESC 
LIMIT 5;
```

Resultado:
```
      produto      | valor   | quantidade | valor_total 
-------------------+---------+------------+-------------
 Notebook Asus     | 4200.00 |          1 |     4200.00
 Notebook Dell     | 3500.00 |          1 |     3500.00
 Monitor LG        |  800.00 |          1 |      800.00
 Teclado Mecânico  |  350.00 |          1 |      350.00
 Webcam HD         |  250.00 |          1 |      250.00
```

---

## Exercício 18: Armadilhas comuns

**Consulta A** - Erro: comparação com NULL usando =
```sql
-- ❌ Errado
WHERE preco = NULL

-- ✅ Correto
WHERE preco IS NULL
```

**Consulta B** - Erro: case-sensitive
```sql
-- ❌ Pode não encontrar 'São Paulo'
WHERE cidade = 'são paulo'

-- ✅ Correto (opção 1)
WHERE LOWER(cidade) = 'são paulo'

-- ✅ Correto (opção 2, PostgreSQL)
WHERE cidade ILIKE 'são paulo'
```

**Consulta C** - Erro: falta AND no BETWEEN
```sql
-- ❌ Errado
WHERE valor BETWEEN 100 200

-- ✅ Correto
WHERE valor BETWEEN 100 AND 200
```

**Consulta D** - Erro: falta parênteses no IN
```sql
-- ❌ Errado
WHERE departamento IN 'TI', 'Vendas', 'RH'

-- ✅ Correto
WHERE departamento IN ('TI', 'Vendas', 'RH')
```

---

## Exercício 19: Boas práticas

**Versão melhorada:**
```sql
SELECT 
  nome,
  categoria,
  preco,
  estoque
FROM produtos 
WHERE (categoria IN ('Eletrônicos', 'Periféricos'))
  AND (preco BETWEEN 100 AND 1000)
  AND (estoque >= 10)
ORDER BY preco ASC 
LIMIT 5;
```

**Melhorias aplicadas:**

1. **Formatação**: Quebras de linha para melhor legibilidade
2. **SELECT específico**: Em vez de *, selecionamos apenas as colunas necessárias
3. **IN em vez de múltiplos OR**: Mais limpo e eficiente
4. **BETWEEN**: Mais legível que duas comparações
5. **Parênteses**: Deixam a lógica clara
6. **ASC explícito**: Documenta a intenção
7. **Padrão SQL**: Maiúsculas para palavras-chave

**Observação**: A consulta original tinha ambiguidade lógica devido à falta de parênteses com AND/OR misturados.