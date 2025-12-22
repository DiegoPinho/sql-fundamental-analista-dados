# Exercícios - Capítulo 04: Restrições (Constraints)

## Exercício 1: Aplicando NOT NULL

```sql
CREATE TABLE usuarios (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  email VARCHAR(255) NOT NULL,
  telefone VARCHAR(15),
  data_cadastro DATE NOT NULL
);
```

---

## Exercício 2: Testando a restrição NOT NULL

O comando resultará em **erro** porque a coluna `email` tem a restrição `NOT NULL` e não foi fornecido um valor para ela na inserção.

Mensagem de erro esperada:

```
ERROR: null value in column "email" violates not-null constraint
```

Para corrigir, é necessário incluir um valor para o email:

```sql
INSERT INTO usuarios (nome, email, telefone, data_cadastro)
VALUES ('João Silva', 'joao@email.com', '11987654321', '2023-12-01');
```

---

## Exercício 3: Restrição UNIQUE

```sql
CREATE TABLE usuarios (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  telefone VARCHAR(15) UNIQUE,
  data_cadastro DATE NOT NULL
);
```

---

## Exercício 4: Violação de UNIQUE

**Apenas 2 registros** serão inseridos com sucesso (Maria Costa e Ana Santos).

O terceiro INSERT falhará porque tenta inserir o email `'maria@email.com'` que já existe no primeiro registro, violando a restrição `UNIQUE` da coluna `email`.

Mensagem de erro esperada:

```
ERROR: duplicate key value violates unique constraint "usuarios_email_key"
DETAIL: Key (email)=(maria@email.com) already exists.
```

---

## Exercício 5: PRIMARY KEY composta

```sql
CREATE TABLE matriculas (
  aluno_id INT NOT NULL,
  curso_id INT NOT NULL,
  data_matricula DATE NOT NULL,
  status VARCHAR(50) NOT NULL,
  PRIMARY KEY (aluno_id, curso_id)
);
```

---

## Exercício 6: Restrição CHECK com valores numéricos

```sql
CREATE TABLE produtos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(200) NOT NULL,
  preco DECIMAL(10, 2) NOT NULL CHECK (preco > 0),
  estoque INT NOT NULL CHECK (estoque >= 0),
  desconto_percentual DECIMAL(5, 2) CHECK (desconto_percentual >= 0 AND desconto_percentual <= 100)
);
```

---

## Exercício 7: Testando CHECK constraints

- Comando A: Sucesso - todos os valores atendem às restrições;
- Comando B: Erro - `preco` é -50.00, viola `CHECK (preco > 0)`;
- Comando C: Erro - `estoque` é -5, viola `CHECK (estoque >= 0)`;
- Comando D: Erro - `desconto_percentual` é 150, viola `CHECK (desconto_percentual >= 0 AND desconto_percentual <= 100)`.

---

## Exercício 8: Restrição DEFAULT

```sql
CREATE TABLE pedidos (
  id SERIAL PRIMARY KEY,
  cliente VARCHAR(150) NOT NULL,
  data_pedido DATE NOT NULL,
  status VARCHAR(50) DEFAULT 'Pendente',
  valor_total DECIMAL(10, 2) NOT NULL CHECK (valor_total > 0),
  observacoes TEXT DEFAULT 'Sem observações'
);
```

---

## Exercício 9: Testando DEFAULT

```sql
-- inserindo o pedido
INSERT INTO 
  pedidos (cliente, data_pedido, valor_total)
VALUES ('Empresa ABC', '2023-12-15', 5000.00);

-- visualizando
SELECT * FROM pedidos;
```

Resultado esperado:
```
 id |   cliente    | data_pedido |  status  | valor_total |   observacoes   
----+--------------+-------------+----------+-------------+-----------------
  1 | Empresa ABC  | 2023-12-15  | Pendente |     5000.00 | Sem observações
(1 row)
```

---

## Exercício 10: Nomeando constraints

```sql
CREATE TABLE usuarios (
  id SERIAL,
  nome VARCHAR(150) NOT NULL,
  email VARCHAR(255) NOT NULL,
  telefone VARCHAR(15),
  data_cadastro DATE NOT NULL,
  
  CONSTRAINT pk_usuarios_id PRIMARY KEY (id),
  CONSTRAINT uq_usuarios_email UNIQUE (email),
  CONSTRAINT uq_usuarios_telefone UNIQUE (telefone)
);
```

---

## Exercício 11: CHECK com datas

```sql
CREATE TABLE eventos_futuros (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(255) UNIQUE NOT NULL,
  data_evento DATE NOT NULL CHECK (data_evento > CURRENT_DATE),
  local VARCHAR(200) NOT NULL,
  capacidade INT NOT NULL CHECK (capacidade > 0)
);
```

---

## Exercício 12: Identificando erros em constraints

Erros identificados:

1. A PRIMARY KEY está definida como composta (id, cpf), mas o id sozinho já é suficiente e mais adequado
2. Ter UNIQUE(cpf) e CPF na PRIMARY KEY é redundante
3. A coluna `nome` deveria ser NOT NULL (um funcionário deve ter nome)
4. A coluna `salario` deveria ser NOT NULL (todo funcionário tem salário)

Versão corrigida:

```sql
CREATE TABLE funcionarios (
  id SERIAL PRIMARY KEY,
  cpf VARCHAR(11) UNIQUE NOT NULL,
  nome VARCHAR(150) NOT NULL,
  salario DECIMAL(10, 2) NOT NULL CHECK (salario > 0),
  data_admissao DATE NOT NULL,
  ativo BOOLEAN DEFAULT true
);
```

---

## Exercício 13: UNIQUE composto

```sql
CREATE TABLE avaliacoes (
  id SERIAL PRIMARY KEY,
  usuario_id INT NOT NULL,
  produto_id INT NOT NULL,
  nota INT NOT NULL CHECK (nota >= 1 AND nota <= 5),
  comentario TEXT,
  data_avaliacao DATE NOT NULL,
  UNIQUE (usuario_id, produto_id)
);
```

Ou com nome personalizado:

```sql
CREATE TABLE avaliacoes (
  id SERIAL PRIMARY KEY,
  usuario_id INT NOT NULL,
  produto_id INT NOT NULL,
  nota INT NOT NULL CHECK (nota >= 1 AND nota <= 5),
  comentario TEXT,
  data_avaliacao DATE NOT NULL,
  CONSTRAINT uq_usuario_produto UNIQUE (usuario_id, produto_id)
);
```

---

## Exercício 14: Combinando múltiplas constraints

```sql
CREATE TABLE contas_bancarias (
  numero_conta VARCHAR(10) PRIMARY KEY,
  titular VARCHAR(200) NOT NULL,
  saldo DECIMAL(15, 2) NOT NULL DEFAULT 0.00 CHECK (saldo >= 0),
  tipo_conta VARCHAR(20) NOT NULL CHECK (tipo_conta IN ('Corrente', 'Poupança')),
  ativa BOOLEAN DEFAULT true,
  data_abertura DATE NOT NULL
);
```

---

## Exercício 15: Questão conceitual

**Diferenças:**

1. **PRIMARY KEY**:
   - Identifica unicamente cada registro na tabela;
   - Não permite valores `NULL`;
   - Uma tabela pode ter **apenas UMA** chave primária (mas pode ser composta por múltiplas colunas);
   - Automaticamente cria um índice para melhor performance.

2. **UNIQUE**:
   - Garante que os valores sejam únicos na coluna;
   - **Permite valores NULL** (e pode ter múltiplos NULLs, dependendo do SGBD);
   - Uma tabela pode ter **várias** constraints UNIQUE em diferentes colunas;
   - Também cria um índice automaticamente.

**Resumo:**
- Uma tabela pode ter apenas 1 `PRIMARY KEY`, mas múltiplas constraints `UNIQUE`;
- PRIMARY KEY = UNIQUE + NOT NULL;
- Use PRIMARY KEY para o identificador principal da tabela;
- Use UNIQUE para outras colunas que precisam ter valores exclusivos (como email, CPF, etc.).s

---

## Exercício 16: Cenário prático completo

```sql
CREATE TABLE livros (
  codigo SERIAL PRIMARY KEY,
  isbn VARCHAR(13) UNIQUE NOT NULL,
  titulo VARCHAR(255) NOT NULL,
  autor VARCHAR(150) NOT NULL,
  ano_publicacao INT NOT NULL CHECK (ano_publicacao >= 1000 AND ano_publicacao <= EXTRACT(YEAR FROM CURRENT_DATE)),
  numero_paginas INT NOT NULL CHECK (numero_paginas > 0),
  quantidade_estoque INT NOT NULL DEFAULT 1 CHECK (quantidade_estoque >= 0),
  disponivel_emprestimo BOOLEAN DEFAULT true,
  UNIQUE (titulo, autor)
);
```

Ou com nomes personalizados para as constraints:

```sql
CREATE TABLE livros (
  codigo SERIAL,
  isbn VARCHAR(13) NOT NULL,
  titulo VARCHAR(255) NOT NULL,
  autor VARCHAR(150) NOT NULL,
  ano_publicacao INT NOT NULL,
  numero_paginas INT NOT NULL,
  quantidade_estoque INT NOT NULL DEFAULT 1,
  disponivel_emprestimo BOOLEAN DEFAULT true,
  
  CONSTRAINT pk_livros PRIMARY KEY (codigo),
  CONSTRAINT uq_isbn UNIQUE (isbn),
  CONSTRAINT uq_titulo_autor UNIQUE (titulo, autor),
  CONSTRAINT chk_ano_publicacao CHECK (ano_publicacao >= 1000 AND ano_publicacao <= EXTRACT(YEAR FROM CURRENT_DATE)),
  CONSTRAINT chk_paginas CHECK (numero_paginas > 0),
  CONSTRAINT chk_estoque CHECK (quantidade_estoque >= 0)
);
```

---

## Exercício 17: Depuração de constraints

**Problemas identificados na estrutura:**

1. A coluna `nome` não tem restrição NOT NULL (um curso deve ter nome)
2. A coluna `preco` não tem restrição CHECK para garantir valores positivos
3. A coluna `instrutor` não tem NOT NULL (todo curso precisa de instrutor)
4. Não há valor DEFAULT para colunas que podem ser omitidas

**Problemas nas inserções:**

- Primeiro INSERT: falta o nome do curso
- Segundo INSERT: duracao_horas é negativa (-20), viola o CHECK
- Terceiro INSERT: falta o preço

**Estrutura corrigida:**

```sql
CREATE TABLE cursos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  duracao_horas INT NOT NULL CHECK (duracao_horas > 0),
  preco DECIMAL(10, 2) NOT NULL CHECK (preco >= 0),
  instrutor VARCHAR(150) NOT NULL,
  ativo BOOLEAN DEFAULT true
);
```

**Inserções corrigidas:**
```sql
-- correção do primeiro INSERT
INSERT INTO cursos (nome, duracao_horas, preco, instrutor, ativo)
VALUES ('Introdução ao SQL', 40, 500.00, 'João Silva', true);

-- correção do segundo INSERT
INSERT INTO cursos (nome, duracao_horas, preco, instrutor, ativo)
VALUES ('SQL Avançado', 20, 800.00, 'Maria Santos', true);

-- correção do terceiro INSERT
INSERT INTO cursos (nome, duracao_horas, preco, instrutor, ativo)
VALUES ('Python Básico', 30, 600.00, 'Pedro Costa', true);

-- osu aproveitando o DEFAULT para 'ativo':
INSERT INTO cursos (nome, duracao_horas, preco, instrutor)
VALUES ('Python Básico', 30, 600.00, 'Pedro Costa');
```

---

## Exercício 18: Questão conceitual sobre boas práticas

1. Integridade dos dados garantida no nível do banco: As constraints garantem que os dados estejam corretos independentemente de qual aplicação ou ferramenta acesse o banco de dados. Se você validar apenas no código da aplicação, dados inconsistentes podem ser inseridos por outras aplicações, scripts SQL diretos, ou ferramentas de administração.

2. Performance: O banco de dados é otimizado para validar constraints de forma muito eficiente. Validações no nível do banco são geralmente mais rápidas do que validações feitas em camadas de aplicação.

3. Centralização das regras de negócio: As constraints definem as regras de negócio diretamente na estrutura do banco de dados, servindo como documentação viva e garantindo que todos os desenvolvedores e sistemas que acessam o banco sigam as mesmas regras.

4. Prevenção de bugs: Constraints evitam que bugs na aplicação resultem em dados inconsistentes no banco. São uma camada adicional de proteção contra erros de programação.

5. Atomicidade e consistência: As constraints são verificadas automaticamente durante transações, garantindo que os dados permaneçam consistentes mesmo em operações complexas que envolvem múltiplas tabelas.

6. Facilita manutenção: Quando as regras de validação estão no banco de dados, não é necessário atualizar múltiplas aplicações se uma regra mudar - basta atualizar a constraint.

---

## Exercício 19: Desafio - Sistema de reservas

```sql
CREATE TABLE reservas_hotel (
  codigo SERIAL PRIMARY KEY,
  numero_quarto INT NOT NULL CHECK (numero_quarto BETWEEN 101 AND 999),
  nome_hospede VARCHAR(200) NOT NULL,
  cpf_hospede VARCHAR(11) UNIQUE NOT NULL,
  data_checkin DATE NOT NULL CHECK (data_checkin >= CURRENT_DATE),
  data_checkout DATE NOT NULL,
  numero_hospedes INT NOT NULL CHECK (numero_hospedes BETWEEN 1 AND 4),
  valor_diaria DECIMAL(10, 2) NOT NULL CHECK (valor_diaria > 0),
  status VARCHAR(20) DEFAULT 'Confirmada' CHECK (status IN ('Confirmada', 'Cancelada', 'Concluída')),
  observacoes TEXT DEFAULT 'Sem observações',
  CHECK (data_checkout > data_checkin)
);
```

Nota importante: A constraint `CHECK (data_checkout > data_checkin)` é definida no nível da tabela (não da coluna) porque compara duas colunas diferentes.