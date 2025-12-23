# Exercícios - Capítulo 11: Relacionamentos entre Tabelas

## Exercício 1: Criando chave primária
```sql
CREATE TABLE livros (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(200) NOT NULL,
  isbn VARCHAR(17) UNIQUE NOT NULL,
  ano_publicacao INTEGER,
  preco DECIMAL(10, 2)
);
```

**Explicação:**
- `SERIAL PRIMARY KEY` cria uma coluna autoincremental que será a chave primária;
- A chave primária automaticamente aplica `NOT NULL` e `UNIQUE`;
- `UNIQUE` no ISBN garante que não haverá livros duplicados.

---

## Exercício 2: Criando chave estrangeira básica
```sql
-- Criar tabela de autores
CREATE TABLE autores (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  nacionalidade VARCHAR(50)
);

-- Criar tabela de livros com chave estrangeira
CREATE TABLE livros (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(200) NOT NULL,
  isbn VARCHAR(17) UNIQUE NOT NULL,
  ano_publicacao INTEGER,
  preco DECIMAL(10, 2),
  autor_id INTEGER REFERENCES autores(id)
);
```

**Explicação:**
- `REFERENCES autores(id)` cria a chave estrangeira;
- Isso garante que `autor_id` só pode conter valores que existem em `autores.id`;
- O valor pode ser NULL (autor opcional) a menos que adicionemos `NOT NULL`.

---

## Exercício 3: Testando integridade referencial
```sql
-- a) Inserir autor
INSERT INTO autores (nome, nacionalidade)
VALUES ('Machado de Assis', 'Brasileira');
-- Retorna: id = 1

-- b) Inserir livro com autor válido
INSERT INTO livros (titulo, isbn, ano_publicacao, preco, autor_id)
VALUES ('Dom Casmurro', '978-85-359-0277-1', 1899, 29.90, 1);
-- Sucesso!

-- c) Tentar inserir livro com autor inexistente
INSERT INTO livros (titulo, isbn, ano_publicacao, preco, autor_id)
VALUES ('Livro Fantasma', '978-00-000-0000-0', 2024, 19.90, 999);

-- d) O que acontece:
-- ERRO: insert or update on table "livros" violates 
-- foreign key constraint "livros_autor_id_fkey"
-- DETAIL: Key (autor_id)=(999) is not present in table "autores".

-- Por quê:
-- A chave estrangeira impede a inserção de livros com autor_id
-- que não existe na tabela autores, mantendo a integridade referencial.
```

**Explicação:**
O banco de dados protege os dados impedindo a criação de referências inválidas (órfãos). Isso é integridade referencial em ação!

---

## Exercício 4: Chave estrangeira com nome personalizado
```sql
-- Primeiro, se a tabela já existe, precisamos apagá-la
DROP TABLE IF EXISTS livros;

-- Criar tabela com constraint nomeada
CREATE TABLE livros (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(200) NOT NULL,
  isbn VARCHAR(17) UNIQUE NOT NULL,
  ano_publicacao INTEGER,
  preco DECIMAL(10, 2),
  autor_id INTEGER,
  
  CONSTRAINT fk_livro_autor
    FOREIGN KEY (autor_id)
    REFERENCES autores(id)
);
```

**Explicação:**
- `CONSTRAINT fk_livro_autor` dá um nome personalizado à chave estrangeira;
- Isso facilita identificar qual constraint foi violada em mensagens de erro;
- É especialmente útil em bancos de dados grandes com muitos relacionamentos.

---

## Exercício 5: ON DELETE CASCADE
```sql
-- Recriar tabela com ON DELETE CASCADE
DROP TABLE IF EXISTS livros;

CREATE TABLE livros (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(200) NOT NULL,
  isbn VARCHAR(17) UNIQUE NOT NULL,
  ano_publicacao INTEGER,
  preco DECIMAL(10, 2),
  autor_id INTEGER,
  
  CONSTRAINT fk_livro_autor
    FOREIGN KEY (autor_id)
    REFERENCES autores(id)
    ON DELETE CASCADE
);

-- a) Inserir autor e livros
INSERT INTO autores (nome, nacionalidade)
VALUES ('Clarice Lispector', 'Brasileira');
-- Assumindo id = 2

INSERT INTO livros (titulo, isbn, autor_id)
VALUES 
  ('A Hora da Estrela', '978-85-359-0276-4', 2),
  ('A Paixão Segundo G.H.', '978-85-359-0275-7', 2);

-- b) Deletar o autor
DELETE FROM autores WHERE id = 2;

-- c) Verificar os livros
SELECT * FROM livros WHERE autor_id = 2;
-- Resultado: 0 linhas (os livros foram deletados em cascata)
```

**Explicação:**
`ON DELETE CASCADE` propaga a deleção para registros relacionados. Quando o autor é deletado, todos os seus livros são automaticamente removidos.

---

## Exercício 6: ON DELETE SET NULL
```sql
-- Recriar tabela com ON DELETE SET NULL
DROP TABLE IF EXISTS livros;

CREATE TABLE livros (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(200) NOT NULL,
  isbn VARCHAR(17) UNIQUE NOT NULL,
  ano_publicacao INTEGER,
  preco DECIMAL(10, 2),
  autor_id INTEGER,
  
  CONSTRAINT fk_livro_autor
    FOREIGN KEY (autor_id)
    REFERENCES autores(id)
    ON DELETE SET NULL
);

-- Inserir autor e livros
INSERT INTO autores (nome, nacionalidade)
VALUES ('Jorge Amado', 'Brasileira');
-- Assumindo id = 3

INSERT INTO livros (titulo, isbn, autor_id)
VALUES 
  ('Capitães da Areia', '978-85-359-0274-0', 3),
  ('Gabriela, Cravo e Canela', '978-85-359-0273-3', 3);

-- Deletar o autor
DELETE FROM autores WHERE id = 3;

-- Verificar os livros
SELECT titulo, autor_id FROM livros WHERE titulo LIKE '%Capitães%';
-- Resultado: titulo = 'Capitães da Areia', autor_id = NULL
```

**Explicação:**
`ON DELETE SET NULL` mantém os registros relacionados mas remove a referência, definindo a chave estrangeira como `NULL`. Útil quando queremos preservar o histórico.

---

## Exercício 7: ON DELETE RESTRICT (comportamento padrão)
```sql
-- Recriar tabela sem ON DELETE (padrão = RESTRICT)
DROP TABLE IF EXISTS livros;

CREATE TABLE livros (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(200) NOT NULL,
  isbn VARCHAR(17) UNIQUE NOT NULL,
  ano_publicacao INTEGER,
  preco DECIMAL(10, 2),
  autor_id INTEGER,
  
  CONSTRAINT fk_livro_autor
    FOREIGN KEY (autor_id)
    REFERENCES autores(id)
    -- ON DELETE RESTRICT é o padrão, não precisa especificar
);

-- Inserir autor e livro
INSERT INTO autores (nome, nacionalidade)
VALUES ('José Saramago', 'Portuguesa');
-- Assumindo id = 4

INSERT INTO livros (titulo, isbn, autor_id)
VALUES ('Ensaio sobre a Cegueira', '978-85-359-0272-6', 4);

-- Tentar deletar o autor
DELETE FROM autores WHERE id = 4;

-- Resultado:
-- ERRO: update or delete on table "autores" violates 
-- foreign key constraint "fk_livro_autor" on table "livros"
-- DETAIL: Key (id)=(4) is still referenced from table "livros".

-- O que acontece:
-- O banco impede a deleção do autor enquanto existirem livros
-- que o referenciem. Precisaríamos deletar os livros primeiro.
```

**Explicação:**
`RESTRICT` (comportamento padrão) protege contra deleções acidentais que quebrariam a integridade referencial. É a opção mais segura.

---

## Exercício 8: Identificando tipo de relacionamento - 1:N
```
Resposta: b) Um-para-muitos (1:N)

Justificativa:
- Um autor pode escrever MUITOS livros (N)
- Cada livro tem APENAS UM autor (1) (na nossa modelagem atual)

Isso caracteriza o relacionamento 1:N (um-para-muitos).

Exemplos:
- Machado de Assis (1) → Dom Casmurro, Memórias Póstumas, 
  Quincas Borba, etc. (N livros)
- Jorge Amado (1) → Capitães da Areia, Gabriela, 
  Dona Flor, etc. (N livros)

Cada livro aponta para apenas um autor através da chave 
estrangeira autor_id.
```

**Explicação:**
O relacionamento 1:N é o mais comum em bancos de dados. É implementado com uma chave estrangeira na tabela do lado "muitos" (livros) apontando para a tabela do lado "um" (autores).

---

## Exercício 9: Relacionamento 1:1 - Dados sensíveis
```sql
CREATE TABLE autores_dados_privados (
  id SERIAL PRIMARY KEY,
  autor_id INTEGER UNIQUE NOT NULL,
  cpf VARCHAR(14) UNIQUE NOT NULL,
  data_nascimento DATE NOT NULL,
  conta_bancaria TEXT,
  
  CONSTRAINT fk_autor_dados_privados
    FOREIGN KEY (autor_id)
    REFERENCES autores(id)
    ON DELETE CASCADE
);
```

**Explicação:**
- `UNIQUE` em `autor_id` garante o relacionamento 1:1;
- Cada autor pode ter NO MÁXIMO um registro de dados privados;
- `ON DELETE CASCADE` remove os dados privados quando o autor é deletado;
- Esta separação facilita controle de acesso (segurança) e melhora performance (dados raramente acessados).

---

## Exercício 10: Testando relacionamento 1:1
```sql
-- a) Inserir autor e dados privados
INSERT INTO autores (nome, nacionalidade)
VALUES ('Fernando Pessoa', 'Portuguesa');
-- Assumindo id = 5

INSERT INTO autores_dados_privados 
  (autor_id, cpf, data_nascimento, conta_bancaria)
VALUES 
  (5, '123.456.789-00', '1888-06-13', '12345-6');

-- b) Tentar inserir segundo registro para o mesmo autor
INSERT INTO autores_dados_privados 
  (autor_id, cpf, data_nascimento)
VALUES 
  (5, '987.654.321-00', '1888-06-13');

-- c) O que acontece:
-- ERRO: duplicate key value violates unique constraint 
-- "autores_dados_privados_autor_id_key"
-- DETAIL: Key (autor_id)=(5) already exists.

-- Por quê:
-- A constraint UNIQUE em autor_id impede que o mesmo autor 
-- tenha mais de um registro de dados privados, garantindo 
-- o relacionamento 1:1.
```

**Explicação:**
A constraint `UNIQUE` é fundamental para garantir o relacionamento 1:1. Sem ela, teríamos um relacionamento 1:N.

---

## Exercício 11: Relacionamento N:M - Criando tabela de junção
```sql
-- 1. Recriar tabela livros SEM autor_id
DROP TABLE IF EXISTS livros;

CREATE TABLE livros (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(200) NOT NULL,
  isbn VARCHAR(17) UNIQUE NOT NULL,
  ano_publicacao INTEGER,
  preco DECIMAL(10, 2)
);

-- 2. Criar tabela de junção
CREATE TABLE livros_autores (
  id SERIAL PRIMARY KEY,
  livro_id INTEGER NOT NULL,
  autor_id INTEGER NOT NULL,
  
  CONSTRAINT fk_livro
    FOREIGN KEY (livro_id)
    REFERENCES livros(id)
    ON DELETE CASCADE,
    
  CONSTRAINT fk_autor
    FOREIGN KEY (autor_id)
    REFERENCES autores(id)
    ON DELETE CASCADE,
  
  -- 3. Evitar duplicação
  CONSTRAINT unique_livro_autor
    UNIQUE (livro_id, autor_id)
);
```

**Explicação:**
- A tabela de junção estabelece dois relacionamentos 1:N que juntos formam um N:M;
- `UNIQUE (livro_id, autor_id)` garante que não teremos o mesmo autor associado duas vezes ao mesmo livro;
- `ON DELETE CASCADE` em ambas as chaves estrangeiras remove os relacionamentos quando livro ou autor são deletados.

---

## Exercício 12: Populando relacionamento N:M
```sql
-- a) Inserir autores
INSERT INTO autores (nome, nacionalidade)
VALUES 
  ('Neil Gaiman', 'Britânica'),
  ('Terry Pratchett', 'Britânica');
-- Assumindo ids 6 e 7

-- b) Inserir livro
INSERT INTO livros (titulo, isbn, ano_publicacao, preco)
VALUES ('Good Omens', '978-00-060-8853-9', 1990, 45.90);
-- Assumindo id = 1

-- c) Associar os dois autores ao livro
INSERT INTO livros_autores (livro_id, autor_id)
VALUES 
  (1, 6),  -- Neil Gaiman
  (1, 7);  -- Terry Pratchett

-- d) Consultar autores de "Good Omens"
SELECT autor_id 
FROM livros_autores 
WHERE livro_id = 1;

-- Resultado:
-- autor_id
-- --------
--       6
--       7
```

**Explicação:**
O relacionamento N:M permite que um livro tenha múltiplos autores e cada autor tenha múltiplos livros. A tabela de junção armazena todas as combinações possíveis.

---

## Exercício 13: Tabela de junção com informações adicionais
```sql
DROP TABLE IF EXISTS livros_autores;

CREATE TABLE livros_autores (
  id SERIAL PRIMARY KEY,
  livro_id INTEGER NOT NULL,
  autor_id INTEGER NOT NULL,
  ordem_autoria INTEGER DEFAULT 1,
  papel VARCHAR(50) DEFAULT 'autor',
  
  CONSTRAINT fk_livro
    FOREIGN KEY (livro_id)
    REFERENCES livros(id)
    ON DELETE CASCADE,
    
  CONSTRAINT fk_autor
    FOREIGN KEY (autor_id)
    REFERENCES autores(id)
    ON DELETE CASCADE,
    
  CONSTRAINT unique_livro_autor
    UNIQUE (livro_id, autor_id)
);

-- Exemplo de uso:
INSERT INTO livros_autores 
  (livro_id, autor_id, ordem_autoria, papel)
VALUES 
  (1, 6, 1, 'autor principal'),
  (1, 7, 2, 'coautor');
```

**Explicação:**
A tabela de junção é o lugar ideal para armazenar informações sobre o relacionamento em si, não sobre as entidades relacionadas. Aqui armazenamos a ordem e o papel de cada autor em cada livro.

---

## Exercício 14: Sistema de e-commerce - Modelagem completa
```sql
-- 1. Tabela de clientes
CREATE TABLE clientes (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  telefone VARCHAR(20)
);

-- 2. Tabela de produtos
CREATE TABLE produtos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(200) NOT NULL,
  preco DECIMAL(10, 2) NOT NULL,
  estoque INTEGER DEFAULT 0
);

-- 3. Tabela de pedidos (1:N com clientes)
CREATE TABLE pedidos (
  id SERIAL PRIMARY KEY,
  cliente_id INTEGER NOT NULL,
  data_pedido TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  status VARCHAR(20) DEFAULT 'pendente',
  valor_total DECIMAL(10, 2),
  
  CONSTRAINT fk_pedido_cliente
    FOREIGN KEY (cliente_id)
    REFERENCES clientes(id)
    ON DELETE RESTRICT  -- Não permitir deletar cliente com pedidos
);

-- 4. Tabela de junção (N:M entre pedidos e produtos)
CREATE TABLE itens_pedido (
  id SERIAL PRIMARY KEY,
  pedido_id INTEGER NOT NULL,
  produto_id INTEGER NOT NULL,
  quantidade INTEGER NOT NULL,
  preco_unitario DECIMAL(10, 2) NOT NULL,
  
  CONSTRAINT fk_item_pedido
    FOREIGN KEY (pedido_id)
    REFERENCES pedidos(id)
    ON DELETE CASCADE,  -- Deletar itens se pedido for deletado
    
  CONSTRAINT fk_item_produto
    FOREIGN KEY (produto_id)
    REFERENCES produtos(id)
    ON DELETE RESTRICT,  -- Não permitir deletar produto em pedidos
    
  CONSTRAINT unique_pedido_produto
    UNIQUE (pedido_id, produto_id)
);
```

**Explicação:**
- `clientes → pedidos`: 1:N (um cliente pode ter muitos pedidos)
- `pedidos → itens_pedido → produtos`: N:M (um pedido tem muitos produtos, um produto está em muitos pedidos)
- `ON DELETE RESTRICT` para clientes e produtos protege dados históricos
- `ON DELETE CASCADE` para itens remove-os quando o pedido é deletado
- `preco_unitario` na tabela de junção preserva o preço no momento da compra

---

## Exercício 15: Populando o e-commerce
```sql
-- a) Inserir clientes
INSERT INTO clientes (nome, email, telefone)
VALUES 
  ('João Silva', 'joao@email.com', '11-98888-8888'),
  ('Maria Santos', 'maria@email.com', '11-97777-7777');
-- João id=1, Maria id=2

-- b) Inserir produtos
INSERT INTO produtos (nome, preco, estoque)
VALUES 
  ('Notebook', 3000.00, 10),
  ('Mouse', 50.00, 50),
  ('Teclado', 150.00, 30);
-- Notebook id=1, Mouse id=2, Teclado id=3

-- c) Criar pedido para João
INSERT INTO pedidos (cliente_id, valor_total)
VALUES (1, 3050.00);
-- Pedido id=1

-- Adicionar itens ao pedido de João
INSERT INTO itens_pedido 
  (pedido_id, produto_id, quantidade, preco_unitario)
VALUES 
  (1, 1, 1, 3000.00),  -- Notebook
  (1, 2, 1, 50.00);    -- Mouse

-- d) Criar pedido para Maria
INSERT INTO pedidos (cliente_id, valor_total)
VALUES (2, 200.00);
-- Pedido id=2

-- Adicionar itens ao pedido de Maria
INSERT INTO itens_pedido 
  (pedido_id, produto_id, quantidade, preco_unitario)
VALUES 
  (2, 3, 1, 150.00),   -- Teclado
  (2, 2, 1, 50.00);    -- Mouse

-- e) Consultar itens do pedido de João
SELECT produto_id, quantidade, preco_unitario
FROM itens_pedido
WHERE pedido_id = 1;

-- Resultado:
-- produto_id | quantidade | preco_unitario
-- -----------+------------+----------------
--          1 |          1 |        3000.00
--          2 |          1 |          50.00
```

**Explicação:**
Este é um exemplo realista de como relacionamentos N:M funcionam no mundo real. A tabela de junção armazena não apenas o relacionamento, mas também informações específicas da transação (quantidade, preço).

---

## Exercício 16: Identificando erros - Deleção protegida
```sql
-- a) Tentar deletar cliente João (id=1)
DELETE FROM clientes WHERE id = 1;

-- Erro:
-- ERROR: update or delete on table "clientes" violates 
-- foreign key constraint "fk_pedido_cliente" on table "pedidos"
-- DETAIL: Key (id)=(1) is still referenced from table "pedidos".

-- Por quê:
-- A constraint foi criada com ON DELETE RESTRICT, impedindo
-- a deleção de clientes que têm pedidos associados.

-- b) Tentar deletar produto Mouse (id=2)
DELETE FROM produtos WHERE id = 2;

-- Erro:
-- ERROR: update or delete on table "produtos" violates 
-- foreign key constraint "fk_item_produto" on table "itens_pedido"
-- DETAIL: Key (id)=(2) is still referenced from table "itens_pedido".

-- Por quê:
-- A constraint foi criada com ON DELETE RESTRICT, protegendo
-- o histórico de pedidos ao impedir deleção de produtos já vendidos.

-- c) O que acontece:
-- Ambas as deleções são bloqueadas pelo banco de dados, protegendo
-- a integridade dos dados históricos. Isso é intencional e desejável
-- em sistemas de e-commerce, onde o histórico de pedidos deve ser
-- preservado.
```

**Explicação:**
`ON DELETE RESTRICT` é crucial para proteger dados históricos em sistemas transacionais. Imagine o caos se pudéssemos deletar clientes ou produtos que fazem parte de pedidos já realizados!

---

## Exercício 17: Diferença entre CASCADE e RESTRICT
```sql
-- ON DELETE CASCADE:
-- - Deleta automaticamente os registros relacionados
-- - Uso: Quando os dados relacionados não fazem sentido 
--   sem o registro principal

-- Exemplo 1: Pedido e itens do pedido
CREATE TABLE pedidos (
  id SERIAL PRIMARY KEY,
  cliente_id INTEGER
);

CREATE TABLE itens_pedido (
  id SERIAL PRIMARY KEY,
  pedido_id INTEGER,
  
  FOREIGN KEY (pedido_id)
    REFERENCES pedidos(id)
    ON DELETE CASCADE  -- Deletar itens se pedido for deletado
);

-- Justificativa: Itens de pedido não têm sentido sem o pedido.
-- Se o pedido é cancelado/removido, seus itens também devem ser.

-- ON DELETE RESTRICT:
-- - Impede a deleção se houver registros relacionados
-- - Uso: Quando os dados relacionados devem ser preservados
--   ou tratados antes da deleção

-- Exemplo 2: Cliente e pedidos
CREATE TABLE clientes (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100)
);

CREATE TABLE pedidos2 (
  id SERIAL PRIMARY KEY,
  cliente_id INTEGER,
  
  FOREIGN KEY (cliente_id)
    REFERENCES clientes(id)
    ON DELETE RESTRICT  -- NÃO permitir deletar cliente com pedidos
);

-- Justificativa: Histórico de pedidos deve ser preservado.
-- Não podemos simplesmente deletar um cliente e perder todo
-- o histórico de compras.

-- RESUMO:
-- CASCADE: "Se o pai for deletado, delete os filhos também"
-- RESTRICT: "Não permita deletar o pai se houver filhos"
```

**Explicação:**
A escolha entre CASCADE e RESTRICT depende das regras de negócio. CASCADE para dependências fortes, RESTRICT para preservar histórico ou forçar limpeza manual.

---

## Exercício 18: Sistema de blog - Relacionamentos múltiplos
```sql
-- 1. Tabela de usuários
CREATE TABLE usuarios (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  senha VARCHAR(255) NOT NULL
);

-- 2. Tabela de posts (1:N com usuários)
CREATE TABLE posts (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(200) NOT NULL,
  conteudo TEXT NOT NULL,
  autor_id INTEGER NOT NULL,
  data_publicacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT fk_post_autor
    FOREIGN KEY (autor_id)
    REFERENCES usuarios(id)
    ON DELETE RESTRICT  -- Não permitir deletar usuário com posts
);

-- 3. Tabela de comentários (1:N com posts e 1:N com usuários)
CREATE TABLE comentarios (
  id SERIAL PRIMARY KEY,
  post_id INTEGER NOT NULL,
  autor_id INTEGER NOT NULL,
  conteudo TEXT NOT NULL,
  data_comentario TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT fk_comentario_post
    FOREIGN KEY (post_id)
    REFERENCES posts(id)
    ON DELETE CASCADE,  -- Deletar comentários se post for deletado
    
  CONSTRAINT fk_comentario_autor
    FOREIGN KEY (autor_id)
    REFERENCES usuarios(id)
    ON DELETE SET NULL  -- Manter comentário mas sem autor se usuário deletado
);

-- 4. Tabela de categorias
CREATE TABLE categorias (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(50) UNIQUE NOT NULL
);

-- 5. Tabela de junção posts-categorias (N:M)
CREATE TABLE posts_categorias (
  id SERIAL PRIMARY KEY,
  post_id INTEGER NOT NULL,
  categoria_id INTEGER NOT NULL,
  
  CONSTRAINT fk_post_cat
    FOREIGN KEY (post_id)
    REFERENCES posts(id)
    ON DELETE CASCADE,
    
  CONSTRAINT fk_categoria
    FOREIGN KEY (categoria_id)
    REFERENCES categorias(id)
    ON DELETE CASCADE,
    
  CONSTRAINT unique_post_categoria
    UNIQUE (post_id, categoria_id)
);
```

**Explicação:**
Este sistema mostra diferentes tipos de relacionamentos e comportamentos:
- Posts-Usuários: 1:N com RESTRICT (preservar posts de autores)
- Comentários-Posts: 1:N com CASCADE (remover comentários se post deletado)
- Comentários-Usuários: 1:N com SET NULL (manter comentário mesmo sem autor)
- Posts-Categorias: N:M (um post pode ter várias categorias)

---

## Exercício 19: Analisando modelagem - Problemas
```sql
-- a) Problemas identificados:

-- 1. Redundância de dados do departamento
--    - Nome do departamento repetido para cada funcionário
--    - Se o nome do departamento mudar, precisa atualizar 
--      múltiplos registros

-- 2. Dados do funcionário responsável repetidos nos projetos
--    - Nome e email duplicados
--    - Informações podem ficar inconsistentes

-- 3. Falta de integridade referencial
--    - Nada impede inserir nome/email de funcionário inexistente
--    - Nada impede inserir departamento inexistente

-- 4. Dificuldade em fazer consultas
--    - Para buscar projetos de um funcionário, precisa comparar 
--      strings (lento e propenso a erros)

-- b) Modelagem melhorada:

-- Tabela de departamentos
CREATE TABLE departamentos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) UNIQUE NOT NULL
);

-- Tabela de funcionários com chave estrangeira
CREATE TABLE funcionarios (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  departamento_id INTEGER NOT NULL,
  salario DECIMAL(10, 2),
  
  CONSTRAINT fk_funcionario_departamento
    FOREIGN KEY (departamento_id)
    REFERENCES departamentos(id)
    ON DELETE RESTRICT
);

-- Tabela de projetos com chave estrangeira
CREATE TABLE projetos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(200) NOT NULL,
  responsavel_id INTEGER NOT NULL,
  
  CONSTRAINT fk_projeto_responsavel
    FOREIGN KEY (responsavel_id)
    REFERENCES funcionarios(id)
    ON DELETE RESTRICT
);

-- Vantagens da nova modelagem:
-- ✓ Sem redundância de dados
-- ✓ Integridade referencial garantida
-- ✓ Consultas mais eficientes (comparar IDs é mais rápido que strings)
-- ✓ Facilita atualizações (mudar email do funcionário em um lugar só)
-- ✓ Impede dados inconsistentes
```

**Explicação:**
Armazenar dados relacionados como texto em vez de usar chaves estrangeiras leva a redundância, inconsistência e problemas de manutenção. Sempre normalize os dados criando tabelas separadas e relacionamentos apropriados.

---

## Exercício 20: Questão conceitual - Escolhendo relacionamentos
```
a) Países e capitais: 1:1
   - Cada país tem UMA capital
   - Cada capital pertence a UM país
   - Nota: Na prática pode ser 1:N se considerarmos capitais múltiplas 
     (administrativa, legislativa) como na África do Sul

b) Estudantes e cursos universitários: N:M
   - Um estudante se matricula em MUITOS cursos
   - Cada curso tem MUITOS estudantes
   - Requer tabela de junção: matriculas

c) Pessoas e CPF: 1:1
   - Cada pessoa tem UM CPF
   - Cada CPF pertence a UMA pessoa

d) Músicas e playlists: N:M
   - Uma música pode estar em MUITAS playlists
   - Cada playlist contém MUITAS músicas
   - Requer tabela de junção: playlists_musicas

e) Empresas e CEOs: 1:1
   - Cada empresa tem UM CEO (em um momento específico)
   - Cada pessoa é CEO de UMA empresa por vez
   - Nota: Se considerar histórico, seria 1:N

f) Produtos e fornecedores: N:M (geralmente)
   - Um produto pode ser fornecido por MUITOS fornecedores
   - Cada fornecedor fornece MUITOS produtos
   - Requer tabela de junção: produtos_fornecedores

g) Funcionários e crachás: 1:1
   - Cada funcionário tem UM crachá
   - Cada crachá pertence a UM funcionário

h) Hashtags e posts: N:M
   - Um post pode ter MUITAS hashtags
   - Cada hashtag aparece em MUITOS posts
   - Requer tabela de junção: posts_hashtags

DICA: Pergunte-se sempre:
- Quantos do lado A podem estar associados ao lado B?
- Quantos do lado B podem estar associados ao lado A?
```

**Explicação:**
Identificar corretamente o tipo de relacionamento é crucial para uma modelagem eficiente. Na dúvida, pense em exemplos concretos e conte quantos de cada lado podem estar relacionados.

---

## Exercício Desafio: Sistema universitário completo
```sql
-- 1. Tabela de departamentos
CREATE TABLE departamentos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) UNIQUE NOT NULL,
  codigo VARCHAR(10) UNIQUE NOT NULL
);

-- 2. Tabela de cursos (1:N com departamentos)
CREATE TABLE cursos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  codigo VARCHAR(10) UNIQUE NOT NULL,
  departamento_id INTEGER NOT NULL,
  
  CONSTRAINT fk_curso_departamento
    FOREIGN KEY (departamento_id)
    REFERENCES departamentos(id)
    ON DELETE RESTRICT
);

-- 3. Tabela de professores (1:N com departamentos)
CREATE TABLE professores (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  departamento_id INTEGER NOT NULL,
  
  CONSTRAINT fk_professor_departamento
    FOREIGN KEY (departamento_id)
    REFERENCES departamentos(id)
    ON DELETE RESTRICT
);

-- 4. Tabela de disciplinas (1:N com cursos)
CREATE TABLE disciplinas (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  codigo VARCHAR(10) UNIQUE NOT NULL,
  carga_horaria INTEGER NOT NULL,
  curso_id INTEGER NOT NULL,
  
  CONSTRAINT fk_disciplina_curso
    FOREIGN KEY (curso_id)
    REFERENCES cursos(id)
    ON DELETE RESTRICT
);

-- 5. Tabela de estudantes
CREATE TABLE estudantes (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  matricula VARCHAR(20) UNIQUE NOT NULL,
  data_ingresso DATE NOT NULL
);

-- 6. Tabela de turmas (N:M entre disciplinas e professores)
CREATE TABLE turmas (
  id SERIAL PRIMARY KEY,
  disciplina_id INTEGER NOT NULL,
  professor_id INTEGER NOT NULL,
  semestre VARCHAR(6) NOT NULL,  -- Ex: '2024.1'
  horario VARCHAR(50),
  sala VARCHAR(20),
  
  CONSTRAINT fk_turma_disciplina
    FOREIGN KEY (disciplina_id)
    REFERENCES disciplinas(id)
    ON DELETE CASCADE,
    
  CONSTRAINT fk_turma_professor
    FOREIGN KEY (professor_id)
    REFERENCES professores(id)
    ON DELETE RESTRICT,
    
  CONSTRAINT unique_turma
    UNIQUE (disciplina_id, professor_id, semestre)
);

-- 7. Tabela de matrículas (N:M entre estudantes e turmas)
CREATE TABLE matriculas (
  id SERIAL PRIMARY KEY,
  estudante_id INTEGER NOT NULL,
  turma_id INTEGER NOT NULL,
  data_matricula TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  status VARCHAR(20) DEFAULT 'ativa',
  nota_final DECIMAL(4, 2),
  
  CONSTRAINT fk_matricula_estudante
    FOREIGN KEY (estudante_id)
    REFERENCES estudantes(id)
    ON DELETE CASCADE,
    
  CONSTRAINT fk_matricula_turma
    FOREIGN KEY (turma_id)
    REFERENCES turmas(id)
    ON DELETE RESTRICT,
    
  CONSTRAINT unique_estudante_turma
    UNIQUE (estudante_id, turma_id),
    
  CONSTRAINT check_nota
    CHECK (nota_final IS NULL OR (nota_final >= 0 AND nota_final <= 10))
);

-- Explicação dos relacionamentos:

-- 1:N Relacionamentos:
-- - Departamentos → Cursos (um departamento tem muitos cursos)
-- - Departamentos → Professores (um departamento tem muitos professores)
-- - Cursos → Disciplinas (um curso tem muitas disciplinas)

-- N:M Relacionamentos:
-- - Disciplinas ↔ Professores (através de turmas)
--   Uma disciplina pode ter vários professores (turmas diferentes)
--   Um professor pode ministrar várias disciplinas
-- - Estudantes ↔ Turmas (através de matrículas)
--   Um estudante se matricula em várias turmas
--   Uma turma tem vários estudantes

-- ON DELETE escolhidos:
-- - RESTRICT: Para relacionamentos onde a preservação de dados é crucial
--   (cursos, disciplinas, professores em turmas/matrículas ativas)
-- - CASCADE: Para dependências fortes
--   (turmas quando disciplina deletada, matrículas quando estudante deletado)

-- Dados adicionais na tabela de junção:
-- - Turmas: semestre, horário, sala (informações da oferta)
-- - Matrículas: data_matricula, status, nota_final 
--   (informações do relacionamento estudante-turma)
```

**Exemplo de uso:**
```sql
-- Inserir departamento
INSERT INTO departamentos (nome, codigo)
VALUES ('Ciência da Computação', 'CC');

-- Inserir curso
INSERT INTO cursos (nome, codigo, departamento_id)
VALUES ('Bacharelado em Ciência da Computação', 'BCC', 1);

-- Inserir disciplina
INSERT INTO disciplinas (nome, codigo, carga_horaria, curso_id)
VALUES ('Banco de Dados', 'CC101', 60, 1);

-- Inserir professor
INSERT INTO professores (nome, email, departamento_id)
VALUES ('Prof. Silva', 'silva@univ.br', 1);

-- Criar turma
INSERT INTO turmas (disciplina_id, professor_id, semestre, sala)
VALUES (1, 1, '2024.1', 'Lab 101');

-- Inserir estudante
INSERT INTO estudantes (nome, email, matricula, data_ingresso)
VALUES ('João Silva', 'joao@univ.br', '2024001', '2024-03-01');

-- Matricular estudante na turma
INSERT INTO matriculas (estudante_id, turma_id)
VALUES (1, 1);

-- Lançar nota
UPDATE matriculas 
SET nota_final = 8.5 
WHERE estudante_id = 1 AND turma_id = 1;
```

**Explicação:**
Este sistema demonstra como múltiplos tipos de relacionamentos trabalham juntos em um cenário real complexo. Note como:
- Cada relacionamento tem seu propósito específico
- As constraints protegem a integridade dos dados
- A tabela de junção armazena informações contextuais
- Os comportamentos ON DELETE foram escolhidos cuidadosamente baseados nas regras de negócio

Este é um modelo simplificado - sistemas reais teriam mais tabelas (frequência, avaliações parciais, pré-requisitos, etc.), mas ilustra bem os conceitos fundamentais de relacionamentos.