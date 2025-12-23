# Exercícios - Capítulo 11: Relacionamentos entre Tabelas

## Exercício 1: Criando chave primária

Você está criando um sistema de biblioteca. Crie uma tabela `livros` com as seguintes colunas:
- `id` (chave primária, autoincremental)
- `titulo` (obrigatório, até 200 caracteres)
- `isbn` (obrigatório, único, 17 caracteres)
- `ano_publicacao` (inteiro)
- `preco` (decimal com 2 casas)

---

## Exercício 2: Criando chave estrangeira básica

Agora crie uma tabela `autores` e modifique a tabela `livros` para incluir um relacionamento com a tabela de autores. Cada livro deve ter um autor associado.

Tabela `autores`:
- `id` (chave primária, autoincremental)
- `nome` (obrigatório, até 100 caracteres)
- `nacionalidade` (até 50 caracteres)

Tabela `livros` deve ter:
- Todas as colunas do exercício anterior
- `autor_id` (chave estrangeira referenciando `autores`)

---

## Exercício 3: Testando integridade referencial

Usando as tabelas criadas no exercício anterior:

a) Insira um autor chamado "Machado de Assis", nacionalidade "Brasileira";
b) Insira um livro "Dom Casmurro" para este autor;
c) Tente inserir um livro com `autor_id = 999` (que não existe);
d) O que acontece? Por quê?

---

## Exercício 4: Chave estrangeira com nome personalizado

Recrie a tabela `livros` usando uma constraint nomeada para a chave estrangeira. Use o nome `fk_livro_autor`.

---

## Exercício 5: ON DELETE CASCADE

Recrie a tabela `livros` com a seguinte regra: quando um autor for deletado, todos os seus livros também devem ser deletados automaticamente.

Depois:
a) Insira um autor e dois livros para ele;
b) Delete o autor;
c) Verifique o que aconteceu com os livros.

---

## Exercício 6: ON DELETE SET NULL

Recrie a tabela `livros` com uma regra diferente: quando um autor for deletado, o `autor_id` dos seus livros deve ser definido como `NULL` (mantendo os livros, mas sem autor).

Teste da mesma forma que o exercício anterior.

---

## Exercício 7: ON DELETE RESTRICT (comportamento padrão)

Recrie a tabela `livros` sem especificar ON DELETE (ou explicitamente com `ON DELETE RESTRICT`).

Tente deletar um autor que tem livros associados. O que acontece?

---

## Exercício 8: Identificando tipo de relacionamento - 1:N

No sistema de biblioteca, qual é o tipo de relacionamento entre `autores` e `livros` na nossa modelagem atual?

a) Um-para-um (1:1)
b) Um-para-muitos (1:N)
c) Muitos-para-muitos (N:M)

Justifique sua resposta.

---

## Exercício 9: Relacionamento 1:1 - Dados sensíveis

Crie uma tabela `autores_dados_privados` para armazenar informações confidenciais dos autores separadamente:
- `id` (chave primária)
- `autor_id` (chave estrangeira ÚNICA para autores)
- `cpf` (obrigatório, único)
- `data_nascimento` (obrigatório)
- `conta_bancaria` (texto)

Se um autor for deletado, seus dados privados também devem ser deletados.

---

## Exercício 10: Testando relacionamento 1:1

Usando a tabela criada no exercício anterior:

a) Insira um autor e seus dados privados;
b) Tente inserir um segundo registro de dados privados para o mesmo autor;
c) O que acontece? Por quê?

---

## Exercício 11: Relacionamento N:M - Criando tabela de junção

Na realidade, um livro pode ter múltiplos autores e um autor pode escrever múltiplos livros. Crie uma nova modelagem para suportar isso:

1. Remova a coluna `autor_id` da tabela `livros`;
2. Crie uma tabela de junção `livros_autores` que relacione livros e autores;
3. Garanta que não haja duplicação (mesmo autor associado duas vezes ao mesmo livro).

---

## Exercício 12: Populando relacionamento N:M

Usando a estrutura do exercício anterior:

a) Insira 2 autores: "Neil Gaiman" e "Terry Pratchett";
b) Insira o livro "Good Omens" (escrito por ambos);
c) Associe os dois autores a este livro;
d) Consulte quais autores escreveram "Good Omens".

---

## Exercício 13: Tabela de junção com informações adicionais

Recrie a tabela `livros_autores` incluindo informações sobre a participação de cada autor:
- `ordem_autoria` (inteiro) - ordem do autor na publicação (1º autor, 2º autor, etc.)
- `papel` (texto) - "autor principal", "coautor", "colaborador", etc.

---

## Exercício 14: Sistema de e-commerce - Modelagem completa

Você está criando um sistema de e-commerce. Crie as seguintes tabelas com seus relacionamentos:

1. `clientes`: id, nome, email (único), telefone
2. `produtos`: id, nome, preco, estoque
3. `pedidos`: id, cliente_id, data_pedido, status, valor_total
4. `itens_pedido`: relaciona pedidos e produtos (tabela de junção)
   - Deve incluir: quantidade, preco_unitario (preço no momento da compra)

Defina as chaves estrangeiras apropriadas e o comportamento ON DELETE.

---

## Exercício 15: Populando o e-commerce

Usando a estrutura do exercício anterior, crie um cenário completo:

a) Insira 2 clientes: João e Maria;
b) Insira 3 produtos: Notebook (R$ 3000), Mouse (R$ 50), Teclado (R$ 150);
c) Crie um pedido para João com Notebook e Mouse;
d) Crie um pedido para Maria com Teclado e Mouse;
e) Consulte todos os itens do pedido de João.

---

## Exercício 16: Identificando erros - Deleção protegida

Usando o sistema de e-commerce do exercício anterior:

a) Tente deletar o cliente João (que tem pedidos);
b) Tente deletar o produto Mouse (que está em pedidos);
c) O que acontece em cada caso? Por quê?

---

## Exercício 17: Diferença entre CASCADE e RESTRICT

Explique a diferença entre `ON DELETE CASCADE` e `ON DELETE RESTRICT` e dê um exemplo de quando usar cada um.

---

## Exercício 18: Sistema de blog - Relacionamentos múltiplos

Crie um sistema de blog com a seguinte estrutura:

1. `usuarios`: id, nome, email, senha
2. `posts`: id, titulo, conteudo, autor_id, data_publicacao
3. `comentarios`: id, post_id, autor_id, conteudo, data_comentario
4. `categorias`: id, nome
5. `posts_categorias`: relaciona posts e categorias (N:M)

Defina todos os relacionamentos e constraints apropriadas.

---

## Exercício 19: Analisando modelagem - Problemas

Analise a seguinte modelagem e identifique os problemas:

```sql
CREATE TABLE funcionarios (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100),
  departamento_nome VARCHAR(100),
  salario DECIMAL(10, 2)
);

CREATE TABLE projetos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(200),
  funcionario_responsavel_nome VARCHAR(100),
  funcionario_responsavel_email VARCHAR(100)
);
```

a) Quais problemas você identifica?
b) Como melhorar esta modelagem?

---

## Exercício 20: Questão conceitual - Escolhendo relacionamentos

Para cada cenário abaixo, identifique o tipo de relacionamento mais apropriado (1:1, 1:N ou N:M):

a) Países e capitais
b) Estudantes e cursos universitários
c) Pessoas e CPF
d) Músicas e playlists do Spotify
e) Empresas e CEOs
f) Produtos e fornecedores
g) Funcionários e crachás de identificação
h) Hashtags e posts no Instagram

---

## Exercício Desafio: Sistema universitário completo

Projete um banco de dados completo para um sistema universitário com as seguintes regras de negócio:

1. Estudantes se matriculam em cursos
2. Cursos têm múltiplas disciplinas
3. Disciplinas são ministradas por professores
4. Um professor pode ministrar várias disciplinas
5. Uma disciplina pode ter vários professores (diferentes turmas)
6. Estudantes recebem notas nas disciplinas em que se matriculam
7. Cada departamento tem vários professores
8. Cada curso pertence a um departamento

Crie todas as tabelas com relacionamentos apropriados, incluindo:
- Chaves primárias e estrangeiras
- Constraints de integridade
- Comportamentos ON DELETE adequados