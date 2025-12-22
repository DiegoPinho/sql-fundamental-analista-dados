# Exercícios - Capítulo 04: Restrições (Constraints)

## Exercício 1: Aplicando NOT NULL

Crie uma tabela `usuarios` para armazenar informações de usuários de um sistema. A tabela deve ter:
- id (auto-incrementado, chave primária)
- nome (obrigatório, até 150 caracteres)
- email (obrigatório, até 255 caracteres)
- telefone (opcional, até 15 caracteres)
- data_cadastro (obrigatório)

Garanta que os campos obrigatórios não aceitem valores nulos.

---

## Exercício 2: Testando a restrição NOT NULL

Considerando a tabela criada no exercício anterior, o que acontecerá ao executar o comando abaixo? Por quê?

```sql
INSERT INTO usuarios (nome, telefone, data_cadastro)
VALUES ('João Silva', '11987654321', '2023-12-01');
```

---

## Exercício 3: Restrição UNIQUE

Modifique a tabela `usuarios` do Exercício 1 para garantir que:
- O email seja único (não pode haver dois usuários com o mesmo email)
- O telefone seja único (quando informado)

---

## Exercício 4: Violação de UNIQUE

Considerando a tabela do Exercício 3, analise os comandos abaixo e responda quantos registros serão inseridos com sucesso:

```sql
INSERT INTO usuarios (nome, email, data_cadastro)
VALUES ('Maria Costa', 'maria@email.com', '2023-12-01');

INSERT INTO usuarios (nome, email, data_cadastro)
VALUES ('Ana Santos', 'ana@email.com', '2023-12-02');

INSERT INTO usuarios (nome, email, data_cadastro)
VALUES ('Pedro Lima', 'maria@email.com', '2023-12-03');
```

---

## Exercício 5: PRIMARY KEY composta

Crie uma tabela `matriculas` para registrar matrículas de alunos em cursos. A tabela deve ter:
- aluno_id (inteiro, obrigatório)
- curso_id (inteiro, obrigatório)
- data_matricula (obrigatório)
- status (até 50 caracteres, obrigatório)

A combinação de `aluno_id` e `curso_id` deve ser única (um aluno não pode se matricular duas vezes no mesmo curso).

---

## Exercício 6: Restrição CHECK com valores numéricos

Crie uma tabela `produtos` com as seguintes especificações:
- id (auto-incrementado, chave primária)
- nome (obrigatório, até 200 caracteres)
- preco (obrigatório, deve ser maior que 0)
- estoque (obrigatório, deve ser maior ou igual a 0)
- desconto_percentual (opcional, deve estar entre 0 e 100 quando informado)

---

## Exercício 7: Testando CHECK constraints

Usando a tabela `produtos` do exercício anterior, quais dos comandos abaixo resultarão em erro? Explique por quê.

```sql
-- Comando A
INSERT INTO produtos (nome, preco, estoque)
VALUES ('Notebook', 2500.00, 10);

-- Comando B
INSERT INTO produtos (nome, preco, estoque)
VALUES ('Mouse', -50.00, 20);

-- Comando C
INSERT INTO produtos (nome, preco, estoque, desconto_percentual)
VALUES ('Teclado', 150.00, -5, 10);

-- Comando D
INSERT INTO produtos (nome, preco, estoque, desconto_percentual)
VALUES ('Monitor', 800.00, 15, 150);
```

---

## Exercício 8: Restrição DEFAULT

Crie uma tabela `pedidos` com:
- id (auto-incrementado, chave primária)
- cliente (obrigatório, até 150 caracteres)
- data_pedido (obrigatório)
- status (até 50 caracteres, valor padrão: "Pendente")
- valor_total (obrigatório, deve ser maior que 0)
- observacoes (opcional, texto longo, valor padrão: "Sem observações")

---

## Exercício 9: Testando DEFAULT

Usando a tabela `pedidos` do exercício anterior, insira um pedido sem especificar os campos `status` e `observacoes`. Depois, mostre o comando SELECT para visualizar o resultado.

---

## Exercício 10: Nomeando constraints

Recrie a tabela `usuarios` com nomes personalizados para todas as restrições:
- Chave primária: pk_usuarios_id
- UNIQUE do email: uq_usuarios_email
- UNIQUE do telefone: uq_usuarios_telefone

---

## Exercício 11: CHECK com datas

Crie uma tabela `eventos_futuros` que só aceite eventos com datas futuras:
- id (auto-incrementado, chave primária)
- nome (obrigatório, até 255 caracteres, único)
- data_evento (obrigatório, deve ser maior que a data atual)
- local (obrigatório, até 200 caracteres)
- capacidade (obrigatório, deve ser maior que 0)

---

## Exercício 12: Identificando erros em constraints

A tabela abaixo contém erros na definição das constraints. Identifique todos os erros e corrija-os:

```sql
CREATE TABLE funcionarios (
  id SERIAL,
  cpf VARCHAR(11) NOT NULL,
  nome VARCHAR(150),
  salario DECIMAL(10, 2) CHECK (salario > 0),
  data_admissao DATE NOT NULL,
  ativo BOOLEAN DEFAULT true,
  
  PRIMARY KEY (id, cpf),
  UNIQUE (cpf)
);
```

---

## Exercício 13: UNIQUE composto

Crie uma tabela `avaliacoes` para armazenar avaliações de produtos por usuários:
- id (auto-incrementado, chave primária)
- usuario_id (obrigatório)
- produto_id (obrigatório)
- nota (obrigatória, deve estar entre 1 e 5)
- comentario (opcional, texto longo)
- data_avaliacao (obrigatória)

Garanta que um usuário não possa avaliar o mesmo produto mais de uma vez.

---

## Exercício 14: Combinando múltiplas constraints

Crie uma tabela `contas_bancarias` com todas as proteções necessárias:
- numero_conta (texto de 10 caracteres, chave primária)
- titular (obrigatório, até 200 caracteres)
- saldo (obrigatório, valor padrão 0.00, deve ser >= 0)
- tipo_conta (obrigatório, valores permitidos: 'Corrente' ou 'Poupança')
- ativa (valor padrão true)
- data_abertura (obrigatória)

---

## Exercício 15: Questão conceitual

Explique a diferença entre as restrições UNIQUE e PRIMARY KEY. Uma tabela pode ter múltiplas colunas com cada uma dessas restrições?

---

## Exercício 16: Cenário prático completo

Você foi contratado para criar o banco de dados de uma biblioteca. Crie a tabela `livros` com as seguintes regras de negócio:

1. Cada livro tem um código único gerado automaticamente
2. ISBN deve ser único e obrigatório (13 caracteres)
3. Título é obrigatório (até 255 caracteres)
4. Autor é obrigatório (até 150 caracteres)
5. Ano de publicação é obrigatório e deve estar entre 1000 e o ano atual
6. Número de páginas deve ser maior que 0
7. Quantidade em estoque deve ser >= 0, com valor padrão 1
8. Disponível para empréstimo é um campo booleano, padrão true
9. A combinação de título e autor deve ser única

---

## Exercício 17: Depuração de constraints

Um desenvolvedor tentou criar a seguinte tabela, mas recebeu diversos erros ao tentar inserir dados. Analise a estrutura e os comandos INSERT, identifique os problemas e sugira correções tanto na estrutura da tabela quanto nos comandos de inserção:

```sql
CREATE TABLE cursos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100),
  duracao_horas INT CHECK (duracao_horas > 0),
  preco DECIMAL(10, 2),
  instrutor VARCHAR(150),
  ativo BOOLEAN
);

-- Tentativas de inserção:
INSERT INTO cursos (duracao_horas, preco, instrutor, ativo)
VALUES (40, 500.00, 'João Silva', true);

INSERT INTO cursos (nome, duracao_horas, preco, instrutor, ativo)
VALUES ('SQL Avançado', -20, 800.00, 'Maria Santos', true);

INSERT INTO cursos (nome, duracao_horas, instrutor, ativo)
VALUES ('Python Básico', 30, 'Pedro Costa', true);
```

---

## Exercício 18: Questão conceitual sobre boas práticas

Por que é importante usar constraints (restrições) em vez de validar dados apenas no código da aplicação? Cite pelo menos três motivos.

---

## Exercício 19: Desafio - Sistema de reservas

Crie uma tabela `reservas_hotel` para um sistema de gerenciamento de hotel com as seguintes regras:

1. Código da reserva: gerado automaticamente
2. Número do quarto: obrigatório, deve estar entre 101 e 999
3. Nome do hóspede: obrigatório, até 200 caracteres
4. CPF do hóspede: obrigatório, único, 11 caracteres
5. Data de check-in: obrigatória, deve ser hoje ou no futuro
6. Data de check-out: obrigatória, deve ser posterior à data de check-in
7. Número de hóspedes: obrigatório, deve estar entre 1 e 4
8. Valor diária: obrigatório, deve ser maior que 0
9. Status: valores permitidos: 'Confirmada', 'Cancelada', 'Concluída', padrão 'Confirmada'
10. Observações: opcional, valor padrão 'Sem observações'