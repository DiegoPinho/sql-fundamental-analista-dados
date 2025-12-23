# Exercícios - Capítulo 12: JOINs - Combinando dados de múltiplas tabelas

## Exercício 1: INNER JOIN básico

Você tem duas tabelas em um sistema de biblioteca:

```sql
CREATE TABLE autores (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  nacionalidade VARCHAR(50)
);

CREATE TABLE livros (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(200) NOT NULL,
  autor_id INTEGER REFERENCES autores(id),
  ano_publicacao INTEGER
);

INSERT INTO autores (nome, nacionalidade)
VALUES 
  ('Machado de Assis', 'Brasileira'),
  ('Jorge Amado', 'Brasileira'),
  ('Clarice Lispector', 'Brasileira');

INSERT INTO livros (titulo, autor_id, ano_publicacao)
VALUES 
  ('Dom Casmurro', 1, 1899),
  ('Memórias Póstumas de Brás Cubas', 1, 1881),
  ('Capitães da Areia', 2, 1937),
  ('A Hora da Estrela', 3, 1977);
```

Escreva uma consulta que mostre todos os livros com o nome do autor, usando `INNER JOIN`.

---

## Exercício 2: INNER JOIN com aliases

Reescreva a consulta do exercício anterior usando aliases mais descritivos: `livros` como `liv` e `autores` como `aut`. Selecione apenas o título do livro e nome do autor.

---

## Exercício 3: LEFT JOIN - Incluindo registros sem correspondência

Adicione mais um autor que não escreveu nenhum livro na base de dados:

```sql
INSERT INTO autores (nome, nacionalidade)
VALUES ('José Saramago', 'Portuguesa');
```

Escreva uma consulta que mostre TODOS os autores e quantos livros cada um escreveu, incluindo autores sem livros.

---

## Exercício 4: LEFT JOIN - Encontrando registros órfãos

Usando `LEFT JOIN`, encontre todos os autores que NÃO têm nenhum livro cadastrado.

---

## Exercício 5: Diferença entre INNER JOIN e LEFT JOIN

Adicione um livro sem autor:

```sql
INSERT INTO livros (titulo, autor_id, ano_publicacao)
VALUES ('Livro Misterioso', NULL, 2024);
```

Execute estas duas consultas e explique a diferença nos resultados:

```sql
-- Consulta A (INNER JOIN)
SELECT l.titulo, a.nome AS autor
FROM livros AS l
INNER JOIN autores AS a ON l.autor_id = a.id;

-- Consulta B (LEFT JOIN)
SELECT l.titulo, a.nome AS autor
FROM livros AS l
LEFT JOIN autores AS a ON l.autor_id = a.id;
```

---

## Exercício 6: RIGHT JOIN

Escreva uma consulta usando `RIGHT JOIN` que mostre todos os autores e seus livros, incluindo autores sem livros.

Depois, reescreva a mesma consulta usando `LEFT JOIN`.

---

## Exercício 7: FULL OUTER JOIN

Escreva uma consulta usando `FULL OUTER JOIN` que mostre todos os livros e todos os autores, mesmo aqueles sem correspondência.

---

## Exercício 8: INNER JOIN com condições múltiplas

Escreva uma consulta que mostre livros com seus autores, mas APENAS para livros publicados após 1900 E autores brasileiros. Use condições no `ON`.

---

## Exercício 9: ON vs WHERE - Diferença crucial

Compare estas duas consultas e explique a diferença:

```sql
-- Consulta A: Filtro no ON
SELECT a.nome, l.titulo
FROM autores AS a
LEFT JOIN livros AS l 
  ON a.id = l.autor_id 
  AND l.ano_publicacao > 1900;

-- Consulta B: Filtro no WHERE
SELECT a.nome, l.titulo
FROM autores AS a
LEFT JOIN livros AS l 
  ON a.id = l.autor_id
WHERE l.ano_publicacao > 1900;
```

---

## Exercício 10: JOIN com múltiplas tabelas - Setup

Crie um sistema de e-commerce com três tabelas relacionadas:

```sql
CREATE TABLE clientes (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL
);

CREATE TABLE pedidos (
  id SERIAL PRIMARY KEY,
  cliente_id INTEGER REFERENCES clientes(id),
  data_pedido DATE NOT NULL,
  valor_total DECIMAL(10, 2)
);

CREATE TABLE itens_pedido (
  id SERIAL PRIMARY KEY,
  pedido_id INTEGER REFERENCES pedidos(id),
  produto VARCHAR(100) NOT NULL,
  quantidade INTEGER NOT NULL,
  preco_unitario DECIMAL(10, 2) NOT NULL
);

-- Dados de exemplo
INSERT INTO clientes (nome, email)
VALUES 
  ('João Silva', 'joao@email.com'),
  ('Maria Santos', 'maria@email.com'),
  ('Pedro Costa', 'pedro@email.com');

INSERT INTO pedidos (cliente_id, data_pedido, valor_total)
VALUES 
  (1, '2024-12-01', 1050.00),
  (1, '2024-12-15', 250.00),
  (2, '2024-12-10', 3500.00);

INSERT INTO itens_pedido (pedido_id, produto, quantidade, preco_unitario)
VALUES 
  (1, 'Notebook', 1, 1000.00),
  (1, 'Mouse', 1, 50.00),
  (2, 'Teclado', 1, 150.00),
  (2, 'Mousepad', 2, 50.00),
  (3, 'Monitor', 2, 1500.00),
  (3, 'Webcam', 1, 500.00);
```

Escreva uma consulta que mostre TODOS os itens de pedido com:
- Nome do cliente
- Data do pedido
- Produto
- Quantidade
- Preço unitário

---

## Exercício 11: Agregação com múltiplos JOINs

Usando as tabelas do exercício anterior, crie um relatório que mostre para cada cliente:
- Nome do cliente
- Total de pedidos realizados
- Valor total gasto (soma de todos os pedidos)

---

## Exercício 12: Self JOIN - Hierarquia organizacional

Crie uma tabela de funcionários com hierarquia:

```sql
CREATE TABLE funcionarios (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  cargo VARCHAR(50),
  gerente_id INTEGER REFERENCES funcionarios(id)
);

INSERT INTO funcionarios (nome, cargo, gerente_id)
VALUES 
  ('Ana Silva', 'Diretora', NULL),
  ('Carlos Souza', 'Gerente', 1),
  ('Paula Santos', 'Gerente', 1),
  ('João Costa', 'Analista', 2),
  ('Maria Oliveira', 'Analista', 2),
  ('Pedro Lima', 'Analista', 3);
```

Escreva uma consulta que mostre cada funcionário com o nome do seu gerente.

---

## Exercício 13: Self JOIN - Encontrando colegas

Usando a tabela de funcionários do exercício anterior, encontre todos os pares de funcionários que têm o MESMO gerente (colegas de equipe).

---

## Exercício 14: Self JOIN - Encontrando inconsistências

Na tabela de funcionários, encontre todos os gerentes que não têm nenhum subordinado (gerentes sem equipe).

---

## Exercício 15: CROSS JOIN (Produto Cartesiano)

Um `CROSS JOIN` (ou produto cartesiano) combina CADA registro de uma tabela com CADA registro de outra, sem condição.

Crie tabelas de tamanhos (P, M, G) e cores (Vermelho, Azul, Verde) e gere todas as combinações possíveis de produto.

---

## Exercício 16: Identificando erros - Ambiguidade

Por que esta consulta gera erro?

```sql
SELECT 
  id,
  nome,
  titulo
FROM autores
INNER JOIN livros ON autores.id = livros.autor_id;
```

Como corrigir?

---

## Exercício 17: Sistema de redes sociais - Múltiplos JOINs

Crie um sistema simples de postagens em rede social:

```sql
CREATE TABLE usuarios (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  username VARCHAR(50) UNIQUE NOT NULL
);

CREATE TABLE posts (
  id SERIAL PRIMARY KEY,
  usuario_id INTEGER REFERENCES usuarios(id),
  conteudo TEXT NOT NULL,
  data_publicacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE curtidas (
  id SERIAL PRIMARY KEY,
  post_id INTEGER REFERENCES posts(id),
  usuario_id INTEGER REFERENCES usuarios(id),
  data_curtida TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  UNIQUE(post_id, usuario_id)
);

INSERT INTO usuarios (nome, username)
VALUES 
  ('Alice Silva', 'alice'),
  ('Bob Santos', 'bob'),
  ('Carol Costa', 'carol');

INSERT INTO posts (usuario_id, conteudo)
VALUES 
  (1, 'Meu primeiro post!'),
  (1, 'Aprendendo SQL é incrível!'),
  (2, 'Bom dia, pessoal!');

INSERT INTO curtidas (post_id, usuario_id)
VALUES 
  (1, 2), (1, 3),  -- Post 1 curtido por Bob e Carol
  (2, 2), (2, 3), (2, 1),  -- Post 2 curtido por todos
  (3, 1);  -- Post 3 curtido por Alice
```

Crie um relatório que mostre cada post com:
- Nome do autor
- Conteúdo do post
- Número de curtidas

---

## Exercício 18: Subconsulta vs JOIN

Compare estas duas formas de encontrar autores que têm livros:

```sql
-- Forma A: Usando JOIN
SELECT DISTINCT a.nome
FROM autores AS a
INNER JOIN livros AS l ON a.id = l.autor_id;

-- Forma B: Usando subconsulta (IN)
SELECT nome
FROM autores
WHERE id IN (SELECT autor_id FROM livros WHERE autor_id IS NOT NULL);

-- Forma C: Usando subconsulta (EXISTS)
SELECT nome
FROM autores AS a
WHERE EXISTS (SELECT 1 FROM livros WHERE autor_id = a.id);
```

Qual você usaria e por quê?

---

## Exercício 19: Análise de vendas - JOINs complexos

Usando o sistema de e-commerce dos exercícios 10-11, crie um relatório COMPLETO mostrando:

Para cada cliente:
- Nome do cliente
- Total de pedidos
- Total de itens comprados (soma das quantidades)
- Valor total gasto
- Ticket médio por pedido
- Produto mais caro que já comprou
- Data do primeiro pedido
- Data do último pedido

Inclua clientes sem pedidos.

---

## Exercício 20: Comparação de todos os tipos de JOIN

Dadas estas duas tabelas simples:

```sql
CREATE TABLE times (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(50)
);

CREATE TABLE jogadores (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(50),
  time_id INTEGER REFERENCES times(id)
);

INSERT INTO times (nome)
VALUES ('Time A'), ('Time B'), ('Time C');

INSERT INTO jogadores (nome, time_id)
VALUES 
  ('João', 1),
  ('Maria', 1),
  ('Pedro', 2),
  ('Ana', NULL);  -- Jogador sem time
```

Execute e explique o resultado de cada tipo de JOIN:

a) INNER JOIN
b) LEFT JOIN (times como base)
c) RIGHT JOIN (times como base)
d) FULL OUTER JOIN

---

## Exercício Desafio: Sistema de cursos - JOINs múltiplos e complexos

Crie um sistema completo de cursos online com:
- Instrutores
- Cursos (ministrados por instrutores)
- Módulos (que pertencem a cursos)
- Aulas (que pertencem a módulos)
- Estudantes
- Matrículas (estudantes em cursos)
- Progresso (estudantes que completaram aulas)

```sql
CREATE TABLE instrutores (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  especialidade VARCHAR(100)
);

CREATE TABLE cursos (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(200) NOT NULL,
  instrutor_id INTEGER REFERENCES instrutores(id),
  preco DECIMAL(10, 2)
);

CREATE TABLE modulos (
  id SERIAL PRIMARY KEY,
  curso_id INTEGER REFERENCES cursos(id),
  titulo VARCHAR(200) NOT NULL,
  ordem INTEGER
);

CREATE TABLE aulas (
  id SERIAL PRIMARY KEY,
  modulo_id INTEGER REFERENCES modulos(id),
  titulo VARCHAR(200) NOT NULL,
  duracao_minutos INTEGER,
  ordem INTEGER
);

CREATE TABLE estudantes (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL
);

CREATE TABLE matriculas (
  id SERIAL PRIMARY KEY,
  estudante_id INTEGER REFERENCES estudantes(id),
  curso_id INTEGER REFERENCES cursos(id),
  data_matricula DATE DEFAULT CURRENT_DATE,
  UNIQUE(estudante_id, curso_id)
);

CREATE TABLE progresso (
  id SERIAL PRIMARY KEY,
  estudante_id INTEGER REFERENCES estudantes(id),
  aula_id INTEGER REFERENCES aulas(id),
  data_conclusao TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  UNIQUE(estudante_id, aula_id)
);

-- Dados de exemplo
INSERT INTO instrutores (nome, especialidade)
VALUES 
  ('Prof. Silva', 'Banco de Dados'),
  ('Prof. Santos', 'Programação Web');

INSERT INTO cursos (titulo, instrutor_id, preco)
VALUES 
  ('SQL para Iniciantes', 1, 199.00),
  ('JavaScript Avançado', 2, 299.00);

INSERT INTO modulos (curso_id, titulo, ordem)
VALUES 
  (1, 'Introdução ao SQL', 1),
  (1, 'Consultas Avançadas', 2),
  (2, 'ES6 e além', 1);

INSERT INTO aulas (modulo_id, titulo, duracao_minutos, ordem)
VALUES 
  (1, 'O que é SQL?', 15, 1),
  (1, 'Criando tabelas', 30, 2),
  (2, 'JOINs', 45, 1),
  (3, 'Arrow Functions', 25, 1);

INSERT INTO estudantes (nome, email)
VALUES 
  ('Ana Costa', 'ana@email.com'),
  ('Bruno Lima', 'bruno@email.com');

INSERT INTO matriculas (estudante_id, curso_id)
VALUES 
  (1, 1),  -- Ana em SQL
  (2, 1),  -- Bruno em SQL
  (1, 2);  -- Ana em JavaScript

INSERT INTO progresso (estudante_id, aula_id)
VALUES 
  (1, 1), (1, 2), (1, 3),  -- Ana completou 3 aulas
  (2, 1);  -- Bruno completou 1 aula
```

**Desafio:** Crie um relatório completo de progresso mostrando para cada matrícula:
- Nome do estudante
- Título do curso
- Nome do instrutor
- Total de aulas do curso
- Aulas completadas pelo estudante
- Percentual de conclusão
- Tempo total assistido (soma da duração das aulas completadas)