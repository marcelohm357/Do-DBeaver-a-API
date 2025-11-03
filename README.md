# Do-DBeaver-a-API

Entendendo o Ecossistema de Dados: Banco de Dados, SQL e APIs

Este guia é um roteiro de estudos prático baseado em uma sessão de aprendizado real. O objetivo é entender como as peças de um aplicativo moderno (Banco de Dados, API, Cliente) se conectam e como usar a linguagem SQL para interagir com elas.

🚀 Parte 1: As Peças do Quebra-Cabeça

Para entender como um sistema funciona, precisamos primeiro conhecer as ferramentas. A melhor analogia é a de um Restaurante.

1. O Banco de Dados (ex: MariaDB, MySQL)

Analogia: A Cozinha ou o Estoque.
O que é? É onde todos os dados brutos são armazenados de forma organizada. Suas tabelas (applications, users, all_things) são as "prateleiras" onde os ingredientes (os dados) ficam guardados.

2. O Cliente de Banco de Dados (ex: DBeaver)

Analogia: A Chave Mestra da Cozinha.
O que é? O DBeaver é um software que te dá acesso direto à "Cozinha". Ele não é o banco de dados, mas sim a ferramenta que permite que você (o desenvolvedor/testador) entre no estoque, abra as prateleiras e veja ou mude os ingredientes diretamente.

3. A API (Application Programming Interface)

Analogia: O Garçom ou o Sistema de Pedidos.
O que é? Nenhum cliente pode entrar na cozinha. O cliente faz um pedido ao Garçom (a API). O Garçom vai até a Cozinha (Banco de Dados), pega os ingredientes, monta o prato (formata em JSON) e o entrega ao cliente.
Por que ela existe?
Segurança: Impede que o cliente entre na cozinha e faça bagunça (como um DELETE por acidente).
Regras de Negócio: O Garçom pode ter regras ("Você só pode pedir este prato se for maior de idade"). A API faz o mesmo ("Você só pode ver os dados do device_id = 5 se você for o dono dele").

4. O Cliente de API (ex: Insomnia, Postman)

Analogia: O Cliente no Restaurante.
O que é? O Insomnia é uma ferramenta para fingir ser um aplicativo (um site, um app de celular). Você o usa para fazer os pedidos ao "Garçom" (API) e ver o que ele te traz de volta.

💡 Parte 2: O Fluxo de Dados (A Conexão)

Agora, vamos juntar tudo na ordem correta:
O Cliente (Insomnia) faz um pedido para a API. Ex: "Garçom, me traga o usuário com ID 4!" (GET /api/users/4).
O Garçom (API) recebe o pedido.
O Garçom (API) vai até a Cozinha (Banco de Dados) e traduz o pedido para a linguagem da cozinha (SQL).
A API executa o comando: SELECT * FROM users WHERE id = 4;
A Cozinha (BD) entrega os "ingredientes" (os dados do Fulano de Tal) para o Garçom (API).
O Garçom (API) monta o "prato" (formata os dados em JSON) e entrega para o Cliente (Insomnia).
Seu trabalho é testar os dois lados:
Você usa o Insomnia para ver se o Garçom (API) te entrega o prato certo.
Você usa o DBeaver para ir direto na Cozinha (BD) e confirmar se os ingredientes estão corretos na prateleira.

🛠️ Parte 3: A Linguagem da Cozinha (SQL)

SQL (Structured Query Language) é a linguagem universal que usamos para "conversar" com a cozinha (o Banco de Dados).
Quase tudo que você precisa fazer no dia a dia se resume a quatro operações, conhecidas como CRUD:
Create (Criar) -> INSERT
Read (Ler) -> SELECT
Update (Atualizar) -> UPDATE
Delete (Apagar) -> DELETE

📖 Parte 4: Guia de Comandos SQL (O CRUD na Prática)

Aqui estão os comandos que praticamos, do mais simples ao mais avançado.

1. R - READ (Ler com SELECT)

O comando mais usado. Serve para "ler" ou "selecionar" dados.
A. Ver TUDO de uma tabela:
O asterisco (*) é um coringa para "todas as colunas".

<img width="339" height="128" alt="image" src="https://github.com/user-attachments/assets/9126bb7c-b700-4759-9f55-e2b9c0ee1d96" />



B. Ver colunas ESPECÍFICAS:
É mais rápido e limpo pedir apenas o que você precisa.

SQL
SELECT name, status FROM applications;


C. Filtrar linhas com WHERE:
Usado para encontrar dados específicos.

SQL
SELECT * FROM applications
WHERE status = 'active';


D. Combinar filtros com AND:
Para encontrar dados que atendam a MÚLTIPLAS condições.

SQL
SELECT * FROM applications
WHERE status = 'pending'
  AND owner_id = 4;



2. C - CREATE (Criar com INSERT)

Usado para adicionar uma nova linha de dados em uma tabela.
A Sintaxe:
INSERT INTO nome_da_tabela (coluna1, coluna2) VALUES (valor1, valor2);
A Lição Mais Importante: Respeite os Tipos de Dados!
Nós aprendemos que tentar inserir um texto (como 'off') em uma coluna que espera um número (como tinyint(1)) causa um erro.
❌ Errado: INSERT INTO air_conditioners (power_status) VALUES ('off');
✅ Certo: INSERT INTO air_conditioners (power_status) VALUES (0);
Exemplo Completo:

SQL


INSERT INTO all_things
    (device_id, power_status, operation_mode, fan_speed, setpoint)
VALUES
    (1, 0, 'cool', 0, 23);



3. U - UPDATE (Atualizar com UPDATE)

Usado para modificar dados que já existem.
⚠️ PERIGO! Sempre, SEMPRE use a cláusula WHERE em um UPDATE. Se você esquecer, você atualizará TODAS AS LINHAS da tabela por acidente.
Exemplo: Mudar a temperatura (setpoint) do ar-condicionado que acabamos de criar.

SQL


UPDATE all_things
SET setpoint = 24
WHERE device_id = 1;


SET setpoint = 24: Define o novo valor.
WHERE device_id = 1: Especifica qual linha deve ser alterada.

4. D - DELETE (Apagar com DELETE)

Usado para remover linhas de uma tabela.
⚠️ PERIGO MÁXIMO! Este é o comando mais perigoso. Se você esquecer o WHERE, você APAGARÁ TODOS OS DADOS da tabela.
Exemplo: Apagar o ar-condicionado de teste que criamos.

SQL


DELETE FROM all_things
WHERE device_id = 1;


WHERE device_id = 1: Especifica qual linha deve ser apagada.

🔗 Parte 5: Indo Além do Básico (O Comando JOIN)

O JOIN é o comando mais poderoso do SQL. Ele serve para combinar tabelas.
O Problema: A tabela applications nos deu um owner_id = 4. Esse número não significa nada sozinho.
A Solução: Precisamos "juntar" a tabela applications com a tabela users para descobrir quem é o dono.
A "Ponte" (A Regra da Junção):
ON applications.owner_id = users.id
(Onde o owner_id da aplicação for igual ao id do usuário)
Exemplo Completo:

SQL


SELECT
    applications.name,
    applications.status,
    users.full_name,
    users.email
FROM
    applications
INNER JOIN
    users ON applications.owner_id = users.id;


Este comando nos deu uma tabela nova, combinada, mostrando o nome da aplicação ao lado do nome e email do seu verdadeiro dono.

Conclusão

Completando o ciclo de aprendizado:
Conexão em um banco de dados (DBeaver).
Entender como ele se relaciona com uma API (Insomnia).
Aprender o "CRUD" completo (SELECT, INSERT, UPDATE, DELETE).
Dominar o JOIN para conectar dados de tabelas diferentes.
