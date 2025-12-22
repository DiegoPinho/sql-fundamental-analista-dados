# Exercícios - Capítulo 03: Inserções (INSERT)

## Exercício 1: Primeira inserção

```sql
INSERT INTO produtos (id, nome, preco, estoque, descricao)
VALUES (1, 'Notebook', 3500.00, 15, 'Notebook para uso profissional');
```

---

## Exercício 2: Ordem diferente das colunas

```sql
INSERT INTO produtos (nome, descricao, preco, id, estoque)
VALUES ('Mouse', 'Mouse sem fio', 45.90, 2, 50);
```

---

## Exercício 3: Identificando o erro de formato de data

O comando resultará em erro porque o formato da data `'25-12-2023'` não é compatível com o tipo `DATE` do PostgreSQL, que espera o formato `YYYY-MM-DD`.

Comando corrigido:
```sql
INSERT INTO vendas (id, data_venda, produto, quantidade)
VALUES (1, '2023-12-25', 'Teclado', 3);
```

---

## Exercício 4: Erro de número de colunas

O erro está no número de valores fornecidos (5) que não corresponde ao número de colunas especificadas (3). Existem duas formas de corrigir:

**Opção 1 - Ajustar as colunas:**
```sql
INSERT INTO funcionarios (id, nome, cargo, salario)
VALUES (1, 'Maria Silva', 'Analista de Dados', 5500.00);
```

**Opção 2 - Remover o valor extra (se não houver coluna para ele):**
```sql
INSERT INTO funcionarios (id, nome, cargo)
VALUES (1, 'Maria Silva', 'Analista de Dados');
```
Nota: Neste caso, a coluna `salario` ficará NULL.

---

## Exercício 5: Múltiplas inserções

```sql
INSERT INTO clientes (id, nome, email, telefone, cidade)
VALUES
  (1, 'João Santos', 'joao@email.com', '11987654321', 'São Paulo'),
  (2, 'Ana Costa', 'ana@email.com', '21976543210', 'Rio de Janeiro'),
  (3, 'Carlos Pereira', 'carlos@email.com', '31965432109', 'Belo Horizonte');
```

---

## Exercício 6: Comportamento atômico

**Nenhum registro será inserido.** 

Explicação: O comando `INSERT` é atômico. Como a segunda linha contém uma data inválida (`'2023-32-15'` - não existe dia 32), toda a operação falhará e nenhuma das três linhas será inserida, mesmo que as outras duas estejam corretas.

---

## Exercício 7: Usando SERIAL

```sql
CREATE TABLE tarefas (
  id SERIAL,
  titulo VARCHAR(200),
  status VARCHAR(50),
  data_criacao DATE
);

INSERT INTO tarefas (titulo, status, data_criacao)
VALUES
  ('Estudar SQL', 'Pendente', '2023-12-01'),
  ('Fazer exercícios', 'Em andamento', '2023-12-02');
```

---

## Exercício 8: RETURNING clause

```sql
INSERT INTO livros (titulo, autor, ano_publicacao)
VALUES ('Banco de Dados Relacionais', 'Carlos Silva', 2023)
RETURNING id;
```

O banco retornará algo como:
```
 id
---- 
  1
(1 row)
```

---

## Exercício 9: Inserção completa de dados

```sql
CREATE TABLE eventos (
  id SERIAL,
  nome_evento VARCHAR(255),
  data_evento DATE,
  local VARCHAR(200),
  capacidade INT,
  confirmado BOOLEAN
);

INSERT INTO eventos (nome_evento, data_evento, local, capacidade, confirmado)
VALUES
  ('Workshop de SQL', '2024-01-15', 'Auditório Principal', 100, true),
  ('Palestra sobre Data Science', '2024-01-20', 'Sala 201', 50, true),
  ('Meetup de Tecnologia', '2024-02-05', 'Espaço Coworking', 80, false),
  ('Hackathon 2024', '2024-02-28', 'Centro de Convenções', 200, true);
```

---

## Exercício 10: Corrigindo múltiplos erros

Erros identificados:
1. Primeira linha: 4 valores para 3 colunas (falta `ativo` na lista de colunas)
2. Primeira linha: formato de data incorreto (`'15-01-2024'` deve ser `'2024-01-15'`)
3. Terceira linha: faltam parênteses ao redor dos valores
4. Falta ponto e vírgula após o último valor

Versão corrigida:
```sql
CREATE TABLE cursos (
  id SERIAL,
  nome VARCHAR(150),
  duracao_horas INT,
  data_inicio DATE,
  ativo BOOLEAN
);

INSERT INTO cursos (nome, duracao_horas, data_inicio, ativo)
VALUES
  ('SQL Básico', 40, '2024-01-15', true),
  ('Python Avançado', 60, '2024-01-20', false),
  ('JavaScript Intermediário', 50, '2024-02-01', true);
```

---

## Exercício 11: Desafio - Sistema de biblioteca

```sql
-- 1. criando a tabela
CREATE TABLE emprestimos (
  id SERIAL,
  livro VARCHAR(200),
  usuario VARCHAR(150),
  data_emprestimo DATE,
  data_devolucao DATE,
  devolvido BOOLEAN
);

-- 2 e 3. inserindo 5 empréstimos e retornando os ids
INSERT INTO emprestimos (livro, usuario, data_emprestimo, data_devolucao, devolvido)
VALUES
  ('1984', 'João Silva', '2023-12-01', '2023-12-15', false),
  ('Dom Casmurro', 'Maria Santos', '2023-12-03', '2023-12-17', false),
  ('O Cortiço', 'Pedro Costa', '2023-11-20', '2023-12-04', true),
  ('Grande Sertão: Veredas', 'Ana Oliveira', '2023-12-05', '2023-12-19', false),
  ('Memórias Póstumas', 'Carlos Pereira', '2023-11-25', '2023-12-09', true)
RETURNING id;
```

Resultado esperado:
```
 id
----
  1
  2
  3
  4
  5
(5 rows)
```

---

## Exercício 12: Questão conceitual

1. Unicidade garantida: O banco de dados garante automaticamente que cada novo registro terá um id único, eliminando o risco de inserir ids duplicados acidentalmente.
2. Simplicidade: Não é necessário rastrear manualmente qual foi o último id usado ou consultar a tabela antes de cada inserção para determinar o próximo valor disponível.
3. Consistência em operações concorrentes: Em ambientes onde múltiplos usuários ou aplicações inserem dados simultaneamente, o SERIAL garante que não haverá conflitos de ids, pois o banco gerencia a sequência de forma segura.
4. Redução de erros: Elimina erros humanos como esquecer de incrementar o id ou reutilizar um valor já existente.
5. Performance: O banco de dados é otimizado para gerar esses valores sequenciais de forma eficiente, sendo mais rápido do que calcular manualmente o próximo id disponível.
