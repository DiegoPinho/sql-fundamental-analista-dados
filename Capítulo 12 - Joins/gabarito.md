# Exercícios - Capítulo 12: JOINs - Combinando dados de múltiplas tabelas

## Exercício 1: INNER JOIN básico
```sql
SELECT 
  l.titulo,
  l.ano_publicacao,
  a.nome AS autor,
  a.nacionalidade
FROM livros AS l
INNER JOIN autores AS a 
  ON l.autor_id = a.id
ORDER BY l.ano_publicacao;

-- Resultado:
--            titulo             | ano | autor              | nac.
-- ------------------------------+-----+--------------------+------
--  Memórias Póstumas...         | 1881| Machado de Assis   | Bras.
--  Dom Casmurro                 | 1899| Machado de Assis   | Bras.
--  Capitães da Areia            | 1937| Jorge Amado        | Bras.
--  A Hora da Estrela            | 1977| Clarice Lispector  | Bras.
```

**Explicação:**
- `INNER JOIN` combina apenas registros que têm correspondência em ambas as tabelas;
- `ON l.autor_id = a.id` especifica a condição de junção (relacionamento);
- Aliases (`l` e `a`) tornam a consulta mais legível;
- `ORDER BY` organiza cronologicamente.

---

## Exercício 2: INNER JOIN com aliases
```sql
SELECT 
  liv.titulo,
  aut.nome AS autor
FROM livros AS liv
INNER JOIN autores AS aut 
  ON liv.autor_id = aut.id;

-- Forma alternativa (sem AS):
SELECT 
  liv.titulo,
  aut.nome AS autor
FROM livros liv
INNER JOIN autores aut 
  ON liv.autor_id = aut.id;
```

**Explicação:**
A palavra `AS` é opcional ao definir aliases. Ambas as formas funcionam igualmente, mas usar `AS` pode tornar o código mais explícito e legível.

---

## Exercício 3: LEFT JOIN - Incluindo registros sem correspondência
```sql
SELECT 
  a.nome AS autor,
  a.nacionalidade,
  COUNT(l.id) AS total_livros
FROM autores AS a
LEFT JOIN livros AS l 
  ON a.id = l.autor_id
GROUP BY a.id, a.nome, a.nacionalidade
ORDER BY total_livros DESC;

-- Resultado:
--       autor          | nacionalidade | total_livros
-- ---------------------+---------------+--------------
--  Machado de Assis    | Brasileira    |            2
--  Jorge Amado         | Brasileira    |            1
--  Clarice Lispector   | Brasileira    |            1
--  José Saramago       | Portuguesa    |            0
```

**Explicação:**
- `LEFT JOIN` inclui TODOS os autores, mesmo sem livros;
- `COUNT(l.id)` conta livros (retorna 0 para autores sem livros);
- Se usássemos `INNER JOIN`, José Saramago não apareceria;
- `GROUP BY` precisa incluir todas as colunas não agregadas.

---

## Exercício 4: LEFT JOIN - Encontrando registros órfãos
```sql
SELECT 
  a.nome AS autor,
  a.nacionalidade
FROM autores AS a
LEFT JOIN livros AS l 
  ON a.id = l.autor_id
WHERE l.id IS NULL;

-- Resultado:
--      autor      | nacionalidade
-- ----------------+---------------
--  José Saramago  | Portuguesa
```

**Explicação:**
- `LEFT JOIN` traz todos os autores;
- `WHERE l.id IS NULL` filtra apenas os que não têm correspondência em livros;
- Esta técnica é muito útil para encontrar registros "órfãos" ou inconsistências.

---

## Exercício 5: Diferença entre INNER JOIN e LEFT JOIN
```sql
-- Consulta A (INNER JOIN) - Resultado: 4 livros
--            titulo             |       autor
-- ------------------------------+--------------------
--  Dom Casmurro                 | Machado de Assis
--  Memórias Póstumas...         | Machado de Assis
--  Capitães da Areia            | Jorge Amado
--  A Hora da Estrela            | Clarice Lispector

-- "Livro Misterioso" NÃO aparece pois não tem autor

-- Consulta B (LEFT JOIN) - Resultado: 5 livros
--            titulo             |       autor
-- ------------------------------+--------------------
--  Dom Casmurro                 | Machado de Assis
--  Memórias Póstumas...         | Machado de Assis
--  Capitães da Areia            | Jorge Amado
--  A Hora da Estrela            | Clarice Lispector
--  Livro Misterioso             | NULL

-- "Livro Misterioso" aparece com autor = NULL

-- Explicação da diferença:
-- INNER JOIN: Retorna apenas registros com correspondência em AMBAS as tabelas
-- LEFT JOIN: Retorna TODOS os registros da tabela à esquerda (livros),
--            mesmo sem correspondência na tabela à direita (autores)
```

**Explicação:**
Esta é uma diferença fundamental entre os dois JOINs. Use `INNER JOIN` quando quiser apenas dados completos. Use `LEFT JOIN` quando precisar ver tudo da tabela principal, mesmo registros incompletos.

---

## Exercício 6: RIGHT JOIN
```sql
-- Usando RIGHT JOIN (todos os autores)
SELECT 
  a.nome AS autor,
  l.titulo
FROM livros AS l
RIGHT JOIN autores AS a 
  ON l.autor_id = a.id
ORDER BY a.nome;

-- Reescrita com LEFT JOIN (equivalente)
SELECT 
  a.nome AS autor,
  l.titulo
FROM autores AS a
LEFT JOIN livros AS l 
  ON a.id = l.autor_id
ORDER BY a.nome;

-- Ambas retornam:
--       autor          |          titulo
-- ---------------------+------------------------------
--  Clarice Lispector   | A Hora da Estrela
--  Jorge Amado         | Capitães da Areia
--  José Saramago       | NULL
--  Machado de Assis    | Dom Casmurro
--  Machado de Assis    | Memórias Póstumas...
```

**Explicação:**
- `RIGHT JOIN` retorna todos os registros da tabela à DIREITA (autores);
- Qualquer `RIGHT JOIN` pode ser reescrito como `LEFT JOIN` invertendo a ordem;
- A maioria dos desenvolvedores prefere usar apenas `LEFT JOIN` por consistência.

---

## Exercício 7: FULL OUTER JOIN
```sql
SELECT 
  a.nome AS autor,
  l.titulo,
  l.ano_publicacao
FROM autores AS a
FULL OUTER JOIN livros AS l 
  ON a.id = l.autor_id
ORDER BY a.nome, l.ano_publicacao;

-- Resultado mostrará:
-- - José Saramago (autor sem livros) → titulo = NULL
-- - Livro Misterioso (livro sem autor) → autor = NULL
-- - Todos os outros com correspondências normais
```

**Explicação:**
`FULL OUTER JOIN` combina `LEFT JOIN` e `RIGHT JOIN`. É útil para análises de completude, auditorias ou identificar inconsistências em ambos os lados.

---

## Exercício 8: INNER JOIN com condições múltiplas
```sql
SELECT 
  l.titulo,
  l.ano_publicacao,
  a.nome AS autor,
  a.nacionalidade
FROM livros AS l
INNER JOIN autores AS a 
  ON l.autor_id = a.id 
  AND l.ano_publicacao > 1900
  AND a.nacionalidade = 'Brasileira'
ORDER BY l.ano_publicacao;

-- Resultado:
--       titulo        | ano  |       autor        | nac.
-- --------------------+------+--------------------+------
--  Capitães da Areia  | 1937 | Jorge Amado        | Bras.
--  A Hora da Estrela  | 1977 | Clarice Lispector  | Bras.
```

**Explicação:**
Condições adicionais podem ser incluídas no `ON` com `AND`/`OR`. Para `INNER JOIN`, adicionar filtros no `ON` ou `WHERE` geralmente produz o mesmo resultado.

---

## Exercício 9: ON vs WHERE - Diferença crucial
```sql
-- Consulta A: Filtro no ON
-- Resultado: TODOS os autores aparecem
-- Livros publicados antes de 1900 não são unidos, mas autores aparecem
--       nome          |       titulo
-- --------------------+---------------------
--  Machado de Assis   | NULL (seus livros são pré-1900)
--  Jorge Amado        | Capitães da Areia
--  Clarice Lispector  | A Hora da Estrela
--  José Saramago      | NULL (não tem livros)

-- Consulta B: Filtro no WHERE
-- Resultado: Apenas autores com livros pós-1900
-- Autores sem livros pós-1900 são REMOVIDOS do resultado
--       nome          |       titulo
-- --------------------+---------------------
--  Jorge Amado        | Capitães da Areia
--  Clarice Lispector  | A Hora da Estrela

-- EXPLICAÇÃO DA DIFERENÇA CRUCIAL:
-- ON: Afeta quais registros são UNIDOS (mas LEFT JOIN mantém todos à esquerda)
-- WHERE: Filtra o resultado FINAL (pode remover registros da tabela à esquerda)

-- Para LEFT JOIN:
-- - Condições de filtro da tabela à DIREITA → use ON
-- - Condições de filtro da tabela à ESQUERDA → use WHERE
-- - Condições de filtro de AMBAS → depende do comportamento desejado
```

**Explicação:**
Esta é uma diferença CRÍTICA para `LEFT JOIN`. Com `INNER JOIN` o resultado é geralmente o mesmo, mas com `LEFT JOIN` a diferença é significativa. Entender isso evita muitos bugs!

---

## Exercício 10: JOIN com múltiplas tabelas - Setup
```sql
SELECT 
  c.nome AS cliente,
  p.data_pedido,
  i.produto,
  i.quantidade,
  i.preco_unitario,
  (i.quantidade * i.preco_unitario) AS subtotal
FROM itens_pedido AS i
INNER JOIN pedidos AS p 
  ON i.pedido_id = p.id
INNER JOIN clientes AS c 
  ON p.cliente_id = c.id
ORDER BY p.data_pedido, c.nome;

-- Resultado:
--    cliente    | data_pedido |  produto  | qtd | preco_un | subtotal
-- --------------+-------------+-----------+-----+----------+----------
--  João Silva   | 2024-12-01  | Notebook  |  1  | 1000.00  | 1000.00
--  João Silva   | 2024-12-01  | Mouse     |  1  |   50.00  |   50.00
--  Maria Santos | 2024-12-10  | Monitor   |  2  | 1500.00  | 3000.00
--  Maria Santos | 2024-12-10  | Webcam    |  1  |  500.00  |  500.00
--  João Silva   | 2024-12-15  | Teclado   |  1  |  150.00  |  150.00
--  João Silva   | 2024-12-15  | Mousepad  |  2  |   50.00  |  100.00
```

**Explicação:**
Para unir três tabelas:
1. Começamos com a tabela central (`itens_pedido`);
2. Fazemos JOIN com `pedidos` usando `pedido_id`;
3. Fazemos JOIN com `clientes` usando `cliente_id`;
4. Cada JOIN adiciona mais informações ao resultado.

---

## Exercício 11: Agregação com múltiplos JOINs
```sql
SELECT 
  c.nome AS cliente,
  COUNT(DISTINCT p.id) AS total_pedidos,
  SUM(p.valor_total) AS valor_total_gasto
FROM clientes AS c
LEFT JOIN pedidos AS p 
  ON c.id = p.cliente_id
GROUP BY c.id, c.nome
ORDER BY valor_total_gasto DESC;

-- Resultado:
--    cliente     | total_pedidos | valor_total_gasto
-- ---------------+---------------+-------------------
--  Maria Santos  |             1 |           3500.00
--  João Silva    |             2 |           1300.00
--  Pedro Costa   |             0 |               NULL
```

**Explicação:**
- `LEFT JOIN` garante que Pedro Costa apareça mesmo sem pedidos;
- `COUNT(DISTINCT p.id)` conta pedidos únicos;
- `SUM(p.valor_total)` soma o valor total;
- Pedro tem 0 pedidos e valor NULL (não 0 - importante!).

---

## Exercício 12: Self JOIN - Hierarquia organizacional
```sql
SELECT 
  f.nome AS funcionario,
  f.cargo,
  g.nome AS gerente,
  g.cargo AS cargo_gerente
FROM funcionarios AS f
LEFT JOIN funcionarios AS g 
  ON f.gerente_id = g.id
ORDER BY g.nome, f.nome;

-- Resultado:
--   funcionario    |  cargo   |    gerente    | cargo_gerente
-- -----------------+----------+---------------+---------------
--  Ana Silva       | Diretora | NULL          | NULL
--  Carlos Souza    | Gerente  | Ana Silva     | Diretora
--  Paula Santos    | Gerente  | Ana Silva     | Diretora
--  João Costa      | Analista | Carlos Souza  | Gerente
--  Maria Oliveira  | Analista | Carlos Souza  | Gerente
--  Pedro Lima      | Analista | Paula Santos  | Gerente
```

**Explicação:**
- Self JOIN une a tabela com ela mesma;
- Usamos aliases diferentes (`f` para funcionário, `g` para gerente);
- `LEFT JOIN` garante que Ana (sem gerente) apareça;
- Esta técnica é comum para estruturas hierárquicas.

---

## Exercício 13: Self JOIN - Encontrando colegas
```sql
SELECT DISTINCT
  f1.nome AS funcionario1,
  f2.nome AS funcionario2,
  g.nome AS gerente_comum
FROM funcionarios AS f1
INNER JOIN funcionarios AS f2 
  ON f1.gerente_id = f2.gerente_id 
  AND f1.id < f2.id  -- Evita duplicação e auto-comparação
INNER JOIN funcionarios AS g 
  ON f1.gerente_id = g.id
ORDER BY gerente_comum, funcionario1;

-- Resultado:
--   funcionario1    |   funcionario2   | gerente_comum
-- ------------------+------------------+---------------
--  Carlos Souza     | Paula Santos     | Ana Silva
--  João Costa       | Maria Oliveira   | Carlos Souza
```

**Explicação:**
- `f1.id < f2.id` garante que cada par apareça apenas uma vez;
- Sem essa condição, teríamos "João-Maria" E "Maria-João" (duplicado);
- Também evita comparar pessoa consigo mesma;
- Este padrão é útil para encontrar relacionamentos "pares".

---

## Exercício 14: Self JOIN - Encontrando inconsistências

```sql
SELECT 
  f.nome AS gerente_sem_equipe,
  f.cargo
FROM funcionarios AS f
LEFT JOIN funcionarios AS subordinados 
  ON f.id = subordinados.gerente_id
WHERE subordinados.id IS NULL
  AND f.cargo LIKE '%Gerente%';

-- Resultado:
-- (Neste exemplo, todos os gerentes têm subordinados, 
--  então o resultado seria vazio)

-- Se inserirmos um gerente sem equipe:
INSERT INTO funcionarios (nome, cargo, gerente_id)
VALUES ('Roberto Alves', 'Gerente', 1);

-- Agora a consulta retornaria:
--  gerente_sem_equipe |  cargo
-- --------------------+---------
--  Roberto Alves      | Gerente
```

**Explicação:**
- `LEFT JOIN` com a mesma tabela (subordinados);
- `WHERE subordinados.id IS NULL` encontra gerentes sem subordinados;
- Filtramos apenas cargos de gerente;
- Útil para encontrar inconsistências organizacionais.

---

## Exercício 15: CROSS JOIN (Produto Cartesiano)

```sql
CREATE TABLE tamanhos (
  id SERIAL PRIMARY KEY,
  tamanho VARCHAR(10)
);

CREATE TABLE cores (
  id SERIAL PRIMARY KEY,
  cor VARCHAR(20)
);

INSERT INTO tamanhos (tamanho)
VALUES ('P'), ('M'), ('G');

INSERT INTO cores (cor)
VALUES ('Vermelho'), ('Azul'), ('Verde');

-- CROSS JOIN: Todas as combinações
SELECT 
  t.tamanho,
  c.cor,
  CONCAT(t.tamanho, ' - ', c.cor) AS variacao
FROM tamanhos AS t
CROSS JOIN cores AS c
ORDER BY t.tamanho, c.cor;

-- Resultado: 3 tamanhos × 3 cores = 9 combinações
--  tamanho |   cor    |  variacao
-- ---------+----------+-------------
--  P       | Azul     | P - Azul
--  P       | Verde    | P - Verde
--  P       | Vermelho | P - Vermelho
--  M       | Azul     | M - Azul
--  M       | Verde    | M - Verde
--  M       | Vermelho | M - Vermelho
--  G       | Azul     | G - Azul
--  G       | Verde    | G - Verde
--  G       | Vermelho | G - Vermelho
```

**Explicação:**
- `CROSS JOIN` não precisa de condição `ON`;
- Gera todas as combinações possíveis;
- Útil para criar tabelas de variações de produtos, combinações, etc.;
- **CUIDADO:** Com tabelas grandes, pode gerar milhões de linhas!

---

## Exercício 16: Identificando erros - Ambiguidade

```sql
-- ERRO: column reference "id" is ambiguous
-- Ambas as tabelas têm uma coluna chamada "id"

-- Correção 1: Especificar a tabela
SELECT 
  autores.id AS autor_id,
  autores.nome,
  livros.id AS livro_id,
  livros.titulo
FROM autores
INNER JOIN livros ON autores.id = livros.autor_id;

-- Correção 2: Usar aliases (melhor prática)
SELECT 
  a.id AS autor_id,
  a.nome,
  l.id AS livro_id,
  l.titulo
FROM autores AS a
INNER JOIN livros AS l 
  ON a.id = l.autor_id;
```

**Explicação:**
Sempre especifique a tabela (ou alias) para colunas no SELECT, mesmo que não haja ambiguidade aparente. Isso evita erros quando a estrutura das tabelas muda.

---

## Exercício 17: Sistema de redes sociais - Múltiplos JOINs

```sql
SELECT 
  u.nome AS autor,
  u.username,
  p.conteudo,
  p.data_publicacao,
  COUNT(c.id) AS total_curtidas
FROM posts AS p
INNER JOIN usuarios AS u 
  ON p.usuario_id = u.id
LEFT JOIN curtidas AS c 
  ON p.id = c.post_id
GROUP BY p.id, u.nome, u.username, p.conteudo, p.data_publicacao
ORDER BY total_curtidas DESC;

-- Resultado:
--     autor      | username |        conteudo           | data      | curtidas
-- ---------------+----------+---------------------------+-----------+----------
--  Alice Silva   | alice    | Aprendendo SQL...         | 2024...   |        3
--  Alice Silva   | alice    | Meu primeiro post!        | 2024...   |        2
--  Bob Santos    | bob      | Bom dia, pessoal!         | 2024...   |        1
```

**Explicação:**
- `INNER JOIN` com `usuarios` para pegar nome do autor;
- `LEFT JOIN` com `curtidas` para contar curtidas (pode ser 0);
- `COUNT(c.id)` conta curtidas (não `COUNT(*)`);
- Se usássemos `INNER JOIN` nas curtidas, posts sem curtidas não apareceriam.

---

## Exercício 18: Subconsulta vs JOIN
```sql
-- Todas as três formas retornam o mesmo resultado:
--       nome
-- -------------------
--  Machado de Assis
--  Jorge Amado
--  Clarice Lispector

-- Comparação:

-- Forma A (JOIN + DISTINCT):
-- ✓ Mais legível para juntar dados
-- ✓ Útil quando precisa de dados das duas tabelas
-- ✗ DISTINCT pode ser menos eficiente
-- ✗ Se autor tem muitos livros, gera linhas duplicadas que precisam ser eliminadas

SELECT DISTINCT a.nome
FROM autores AS a
INNER JOIN livros AS l ON a.id = l.autor_id;

-- Forma B (IN):
-- ✓ Simples e legível
-- ✓ Bom para listas pequenas/médias
-- ✗ Pode ser lento com muitos resultados na subconsulta
-- ✗ Não lida bem com NULL

SELECT nome
FROM autores
WHERE id IN (SELECT autor_id FROM livros WHERE autor_id IS NOT NULL);

-- Forma C (EXISTS):
-- ✓ Geralmente mais eficiente (para verificar existência)
-- ✓ Para assim que encontra a primeira correspondência
-- ✓ Lida bem com NULL
-- ✓ Mais eficiente para tabelas grandes
-- ✗ Menos intuitivo para iniciantes

SELECT nome
FROM autores AS a
WHERE EXISTS (SELECT 1 FROM livros WHERE autor_id = a.id);

-- RECOMENDAÇÃO:
-- - Se precisa de dados de ambas as tabelas: use JOIN
-- - Se só verifica existência: use EXISTS
-- - Se tem lista pequena e conhecida: use IN
```

**Explicação:**
Não há resposta "certa" absoluta. A escolha depende do contexto, performance necessária e legibilidade desejada. Para este caso específico, `EXISTS` é geralmente a melhor opção de performance.

---

## Exercício 19: Análise de vendas - JOINs complexos

```sql
SELECT 
  c.nome AS cliente,
  COUNT(DISTINCT p.id) AS total_pedidos,
  COALESCE(SUM(i.quantidade), 0) AS total_itens,
  COALESCE(SUM(p.valor_total), 0) AS valor_total_gasto,
  ROUND(AVG(p.valor_total), 2) AS ticket_medio,
  MAX(i.preco_unitario) AS produto_mais_caro,
  MIN(p.data_pedido) AS primeiro_pedido,
  MAX(p.data_pedido) AS ultimo_pedido
FROM clientes AS c
LEFT JOIN pedidos AS p 
  ON c.id = p.cliente_id
LEFT JOIN itens_pedido AS i 
  ON p.id = i.pedido_id
GROUP BY c.id, c.nome
ORDER BY valor_total_gasto DESC;

-- Resultado:
--    cliente    | pedidos | itens | gasto  | ticket_m | mais_caro | 1º      | último
-- --------------+---------+-------+--------+----------+-----------+---------+---------
--  Maria Santos |    1    |   3   | 3500.00|  3500.00 |  1500.00  |2024-12-10|2024-12-10
--  João Silva   |    2    |   4   | 1300.00|   650.00 |  1000.00  |2024-12-01|2024-12-15
--  Pedro Costa  |    0    |   0   |    0   |     NULL |     NULL  |   NULL   |   NULL
```

**Explicação:**
- Múltiplos `LEFT JOIN` garantem que todos os clientes apareçam;
- `COALESCE` converte NULL em 0 para totais;
- `COUNT(DISTINCT p.id)` conta pedidos únicos;
- `SUM(i.quantidade)` soma itens através dos JOINs;
- `AVG`, `MAX`, `MIN` calculam estatísticas agregadas;
- Esta é uma consulta típica de relatórios gerenciais!

---

## Exercício 20: Comparação de todos os tipos de JOIN

```sql
-- a) INNER JOIN - Apenas com correspondência em AMBAS
SELECT t.nome AS time, j.nome AS jogador
FROM times AS t
INNER JOIN jogadores AS j ON t.id = j.time_id;

-- Resultado:
--  time   | jogador
-- --------+---------
--  Time A | João
--  Time A | Maria
--  Time B | Pedro
-- Não aparecem: Time C (sem jogadores) e Ana (sem time)

-- b) LEFT JOIN - TODOS os times
SELECT t.nome AS time, j.nome AS jogador
FROM times AS t
LEFT JOIN jogadores AS j ON t.id = j.time_id;

-- Resultado:
--  time   | jogador
-- --------+---------
--  Time A | João
--  Time A | Maria
--  Time B | Pedro
--  Time C | NULL
-- Time C aparece sem jogadores. Ana (sem time) não aparece.

-- c) RIGHT JOIN - TODOS os jogadores
SELECT t.nome AS time, j.nome AS jogador
FROM times AS t
RIGHT JOIN jogadores AS j ON t.id = j.time_id;

-- Resultado:
--  time   | jogador
-- --------+---------
--  Time A | João
--  Time A | Maria
--  Time B | Pedro
--  NULL   | Ana
-- Ana aparece sem time. Time C (sem jogadores) não aparece.

-- d) FULL OUTER JOIN - TODOS de ambas as tabelas
SELECT t.nome AS time, j.nome AS jogador
FROM times AS t
FULL OUTER JOIN jogadores AS j ON t.id = j.time_id;

-- Resultado:
--  time   | jogador
-- --------+---------
--  Time A | João
--  Time A | Maria
--  Time B | Pedro
--  Time C | NULL
--  NULL   | Ana
-- TODOS aparecem: Time C sem jogadores E Ana sem time.

-- RESUMO VISUAL:
-- INNER: Só a interseção (têm correspondência)
-- LEFT: Todos à esquerda + correspondências
-- RIGHT: Todos à direita + correspondências
-- FULL: Todos de ambos os lados
```

**Explicação:**
Esta comparação lado a lado mostra claramente como cada tipo de JOIN se comporta. Use este exemplo como referência mental ao escolher qual JOIN usar em suas consultas.

---

## Exercício Desafio: Sistema de cursos - JOINs múltiplos e complexos

```sql
SELECT 
  e.nome AS estudante,
  c.titulo AS curso,
  i.nome AS instrutor,
  COUNT(DISTINCT a.id) AS total_aulas,
  COUNT(DISTINCT p.aula_id) AS aulas_completadas,
  ROUND(
    (COUNT(DISTINCT p.aula_id)::NUMERIC / 
     NULLIF(COUNT(DISTINCT a.id), 0) * 100), 
    2
  ) AS percentual_conclusao,
  COALESCE(SUM(DISTINCT aulas_completas.duracao_minutos), 0) AS minutos_assistidos
FROM matriculas AS m
INNER JOIN estudantes AS e 
  ON m.estudante_id = e.id
INNER JOIN cursos AS c 
  ON m.curso_id = c.id
INNER JOIN instrutores AS i 
  ON c.instrutor_id = i.id
INNER JOIN modulos AS mod 
  ON c.id = mod.curso_id
INNER JOIN aulas AS a 
  ON mod.id = a.modulo_id
LEFT JOIN progresso AS p 
  ON p.estudante_id = e.id 
  AND p.aula_id = a.id
LEFT JOIN aulas AS aulas_completas 
  ON p.aula_id = aulas_completas.id
GROUP BY 
  m.id, 
  e.nome, 
  c.titulo, 
  i.nome
ORDER BY 
  e.nome, 
  c.titulo;

-- Resultado esperado:
--  estudante | curso             | instrutor      | total | completas | % | minutos
-- -----------+-------------------+----------------+-------+-----------+---+---------
--  Ana Costa | JavaScript Av...  | Prof. Santos   |   1   |     0     | 0 |    0
--  Ana Costa | SQL para Inic...  | Prof. Silva    |   3   |     3     |100|   90
--  Bruno Lima| SQL para Inic...  | Prof. Silva    |   3   |     1     | 33|   15
```

**Explicação detalhada:**
Esta consulta é um exemplo completo de JOINs complexos no mundo real:

1. **Estrutura dos JOINs:**
   - Começamos com `matriculas` (relaciona estudantes e cursos)
   - JOIN com `estudantes` → nome do aluno
   - JOIN com `cursos` → título do curso
   - JOIN com `instrutores` → nome do professor
   - JOIN com `modulos` → módulos do curso
   - JOIN com `aulas` → todas as aulas do curso
   - LEFT JOIN com `progresso` → aulas que o aluno completou
   - LEFT JOIN com `aulas` novamente (como `aulas_completas`) → duração das aulas completadas

2. **Agregações:**
   - `COUNT(DISTINCT a.id)`: Total de aulas do curso
   - `COUNT(DISTINCT p.aula_id)`: Aulas completadas pelo aluno
   - `ROUND(...)`: Calcula percentual de conclusão
   - `SUM(DISTINCT aulas_completas.duracao_minutos)`: Tempo assistido

3. **Técnicas importantes:**
   - `::NUMERIC`: Converte para decimal (evita divisão inteira)
   - `NULLIF(..., 0)`: Previne divisão por zero
   - `COALESCE(..., 0)`: Converte NULL em 0
   - `LEFT JOIN` para progresso: Permite estudantes sem aulas completadas
   - `DISTINCT` em agregações: Evita contagem duplicada

4. **Aplicação real:**
   Este tipo de consulta é usado em plataformas educacionais para:
   - Dashboards de progresso
   - Relatórios de engajamento
   - Gamificação (badges, conquistas)
   - Análise de efetividade de cursos

Esta consulta demonstra como JOINs múltiplos permitem navegar por relacionamentos complexos e extrair insights valiosos de estruturas de dados elaboradas!