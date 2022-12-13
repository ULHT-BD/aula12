# aula12
Nesta introduzimos o conceito de transação, propriedades ACID e níveis de isolamento. Aprendemos ainda as cláusulas SQL que nos permitem criar e executar transações bem como configurar diferentes níveis de isolamento.
Bom trabalho!

[0. Requisitos](#0-requisitos)

[1. Transação e Propriedades Acid](#1-transação-e-propriedades-acid)

[2. Anomalias e Níveis de Isolamento](#2-anomalias-e-níveis-de-isolamento)

[3. Transações em SQL](#3-transações-em-sql)

[4. Trabalho de Casa](#4-trabalho-de-casa)

[5. Resoluções](#5-resoluções)

[Bibliografia e Referências](#bibliografia-e-referências)

[Outros](#outros)

## 0. Requisitos
Requisitos: Para esta aula, precisa de ter o ambiente de trabalho configurado ([Docker](https://www.docker.com/products/docker-desktop/) com [base de dados HR](https://github.com/ULHT-BD/aula03/blob/main/docker_db_aula03.zip) e [DBeaver](https://dbeaver.io/download/)). Caso ainda não o tenha feito, veja como fazer seguindo o passo 1 da [aula03](https://github.com/ULHT-BD/aula03/edit/main/README.md#1-prepare-o-seu-ambiente-de-trabalho).

Caso já tenha o docker pode iniciá-lo usando o comando ```docker start mysgbd``` no terminal, ou através do interface gráfico do docker-desktop:
<img width="1305" alt="image" src="https://user-images.githubusercontent.com/32137262/194916340-13af4c85-c282-4d98-a571-9c4f7b468bbb.png">

Deve também ter o cliente DBeaver.

## 1. Transação e Propriedades Acid
Uma transação consiste na execução de um conjunto de instruções que acede e possivelmente altera dados e que deve ser executada de forma atómica. Um exemplo de transação é a transferência de 50€ entre uma conta bancária A e outra conta bancária B uma vez que envolve uma sequência de instruções de leitura e escrita do saldo.

Exemplo:
```
Read(A)
A := A – 50
Write(A)
Read(B)
B := B + 50
Write(B)
```

O fluxo de execução de uma transação pode ser descrito por uma máquina de estados, composta por 5 estados, bem definidos:
<img width="577" alt="image" src="https://user-images.githubusercontent.com/32137262/207198610-ea64864c-bd67-4ccc-93fb-fa6589c16701.png">


Para assegurar a integridade dos dados, a BD deve garantir as propriedades ACID:

* Atomicidade – conjunto é realizado ou nada é realizado

* Consistência – execução de transações em isolamento preserva consistência de dados

* Isolamento –transações devem ignorar existência de transações concorrentes 

* Durabilidade – conclusão da transação implica dados persistentes


## 2. Anomalias e Níveis de Isolamento
A execução concorrente de várias transações pode conduzir a várias anomalias bem conhecidas Dirty Read, Non-Repeatable Read e Phantom Read.

Consoante a aplicação a desenvolver, podemos querer tolerar/permitir a ocorrência de algumas destas anomalias ou assegurar um maior nível de isolamento.

A tabela resume as anomalias e níveis de isolamento:

<img width="552" alt="image" src="https://user-images.githubusercontent.com/32137262/207202337-6c54effa-a76e-4200-9b10-6af2669d2048.png">

Em SQL podemos alterar o nível de isolamento de forma global, durante a sessão ou apenas para a transação, usando:

``` sql
SET [GLOBAL | SESSION] TRANSACTION ISOLATION LEVEL level;
```

Exemplo, para alterar nivel de isolamento da sessão para repeatable read
``` sql
SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;
```

## 3. Transações em SQL
No MySQL o autocommit está ligado por default, o que significa que todas as instruções são executadas como uma transação quando é encontrado o caracter ```;```. Podemos desligar este mecanismo usando:

``` sql
SET autocommit=0;
```

Em SQL podemos as cláusulas ```START TRANSACTION``` inicia uma transação e a cláusula ```COMMIT``` termina e executa a transação.

``` sql
START TRANSACTION;
(sequência de comandos corpo da transação)
COMMIT;
```

Exemplo:

<img width="272" alt="image" src="https://user-images.githubusercontent.com/32137262/207200753-1bfd6f09-55b2-4e78-a012-932c35ed8516.png">

Durante a execução de uma transação, podemos anular a sua execução voltando ao estado anterior usando a cláusula ```ROLLBACK```:

``` sql
START TRANSACTION;
(sequência de comandos corpo da transação)
ROLLBACK;
```

O estado da base de dados é reposto para o ponto anterior à transação.
Podemos inclusivamente definir pontos chave (savepoints) até onde queremos reverter usando a cláusula ```SAVEPOINT nome-savepoint```. Podemos escolher até onde deve ir o rollback usando ```ROLLBACK TO SAVEPOINT nome-savepoint```.

### Exercícios
Para cada uma das alíneas seguintes, escreva a query que permite obter:
1. Uma única lista com os nomes próprios dos trabalhadores que trabalham em departamentos de it e dos apelidos dos trabalhadores de Marketing.
2. A lista de nomes próprios que coexistam no departamentos de IT e de Finance.
3. A lista de nomes próprios apenas de trabalhadores não managers, i.e. excluindo os nomes de managers.


## 4. Trabalho de Casa
(a publicar)

## 5. Resoluções
[Resolução dos exercícios em aula](https://github.com/ULHT-BD/aula11/blob/main/aula11_resolucao.sql)

## Bibliografia e Referências
* [Slides aula](https://github.com/ULHT-BD/aula12/blob/main/Aula12.pdf) 
* [Isolation Levels - Medium]((https://medium.com/analytics-vidhya/understanding-mysql-transaction-isolation-levels-by-example-1d56fce66b3d))


## Outros
Para dúvidas e discussões pode juntar-se ao grupo slack da turma através do [link](
https://join.slack.com/t/ulht-bd/shared_invite/zt-1iyiki38n-ObLCdokAGUG5uLQAaJ1~fA) (atualizado 2022-11-03)
