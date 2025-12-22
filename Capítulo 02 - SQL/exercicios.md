# Exercícios - Capítulo 02: SQL

## Exercício 1: Criando sua primeira tabela

Crie uma tabela chamada `produtos` para armazenar informações sobre produtos de uma loja. A tabela deve ter as seguintes colunas:
- `id`: identificador único do produto (número inteiro)
- `nome`: nome do produto (texto curto, máximo 100 caracteres)
- `preco`: preço do produto (número decimal)
- `estoque`: quantidade em estoque (número inteiro)
- `descricao`: descrição detalhada do produto (texto longo)

---

## Exercício 2: Escolhendo tipos de dados corretos

Para cada coluna abaixo, escolha o tipo de dado mais apropriado dentre as opções: `INT`, `VARCHAR`, `TEXT`, `DATE`, `DECIMAL`, `BOOLEAN`.

a) `email` - endereço de email do cliente  
b) `data_nascimento` - data de nascimento  
c) `ativo` - indica se o registro está ativo ou não  
d) `salario` - salário de um funcionário  
e) `comentarios` - comentários longos sobre um pedido  
f) `quantidade` - quantidade de itens  

---

## Exercício 3: Identificando erros

O comando SQL abaixo contém erros. Identifique-os e corrija:

```sql
CREATE TABLE clientes (
  id INTEGER
  nome VARCHAR(200)
  telefone VARCHAR(15),
  endereco TEXT
  ativo BOOLEAN
)
```

---

## Exercício 4: Tabela de funcionários

Crie uma tabela chamada `funcionarios` que armazene as seguintes informações:
- Código do funcionário (número)
- Nome completo (até 150 caracteres)
- Data de admissão
- Cargo (até 80 caracteres)
- Salário (valor com centavos)
- Observações (texto longo)

---

## Exercício 5: Verificando a estrutura

Após criar a tabela `reunioes` conforme mostrado no capítulo, qual comando SQL você usaria para verificar se a tabela foi criada corretamente e visualizar sua estrutura (mesmo que ainda esteja vazia)?

---

## Exercício 6: Tabela de vendas

Uma empresa precisa registrar suas vendas. Crie uma tabela chamada `vendas` com as seguintes informações:
- Número da venda (inteiro)
- Data da venda
- Nome do vendedor (até 100 caracteres)
- Nome do cliente (até 100 caracteres)
- Valor total (valor monetário)
- Forma de pagamento (até 50 caracteres)
- Observações (texto longo, opcional)

---

## Exercício 7: Análise de tipos de dados

Por que é importante escolher o tipo de dado correto para cada coluna? Cite pelo menos três motivos.

---

## Exercício 8: Tabela de livros

Você está criando um sistema para uma biblioteca. Crie uma tabela chamada `livros` que contenha:
- Código do livro
- Título do livro
- Autor
- ISBN (código de 13 caracteres)
- Data de publicação
- Número de páginas
- Disponível para empréstimo (sim/não)
- Sinopse

---

## Exercício 9: Corrigindo a sintaxe

Corrija todos os erros no comando SQL abaixo:

```sql
create table pedidos
  id_pedido INT,
  data_pedido DATE
  cliente VARCHAR(100),
  total DECIMAL(10 2),
  status VARCHAR(20)
```

---

## Exercício 10: Projeto prático

Imagine que você precisa criar um sistema para gerenciar eventos. Crie uma tabela chamada `eventos` que armazene:
- Identificador único do evento
- Nome do evento
- Data do evento
- Horário de início (você pode usar VARCHAR por enquanto)
- Local (até 200 caracteres)
- Capacidade máxima de participantes
- Descrição do evento
- Evento confirmado (sim/não)

Após criar a tabela, escreva o comando para verificar se ela foi criada corretamente.