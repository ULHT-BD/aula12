# aula12
Na aula passada olhámos para junções horizontais e como podemos obter conteúdo obtido a partir de várias tabelas, quando relacionado através de chaves estrangeiras, utilizando a cláusula ```JOIN```.
Nesta aula vemos outra forma de realizar junções como podemos combinar resultados de operações sobre conjuntos e sobre outra junções verticais e como.
Bom trabalho!

[0. Requisitos](#0-requisitos)

[1. Nested Queries](#1-nested-queries)

[2. Operações UNION, INTERSECT e MINUS](#2-operações-union-intersect-e-minus)

[3. Trabalho de Casa](#3-trabalho-de-casa)

[4. Resoluções](#4-resoluções)

[Bibliografia e Referências](#bibliografia-e-referências)

[Outros](#outros)

## 0. Requisitos
Requisitos: Para esta aula, precisa de ter o ambiente de trabalho configurado ([Docker](https://www.docker.com/products/docker-desktop/) com [base de dados HR](https://github.com/ULHT-BD/aula03/blob/main/docker_db_aula03.zip) e [DBeaver](https://dbeaver.io/download/)). Caso ainda não o tenha feito, veja como fazer seguindo o passo 1 da [aula03](https://github.com/ULHT-BD/aula03/edit/main/README.md#1-prepare-o-seu-ambiente-de-trabalho).

Caso já tenha o docker pode iniciá-lo usando o comando ```docker start mysgbd``` no terminal, ou através do interface gráfico do docker-desktop:
<img width="1305" alt="image" src="https://user-images.githubusercontent.com/32137262/194916340-13af4c85-c282-4d98-a571-9c4f7b468bbb.png">

Deve também ter o cliente DBeaver.

## 1. Nested Queries
Nested queries ou subqueries consistem em queries onde um SELECT contém outro SELECT. Podemos ter vários nested queries com vários níveis de SELECT encadeados, por exemplo

``` sql
SELECT * FROM t1 
WHERE t1.id IN (
  SELECT t2.id FROM t2 
  WHERE t2.id IN (
    SELECT t3.id FROM t3
  )
);
```

As vantagens na utilização de nested queries são o melhor isolamento das diferentes partes da query, alternativa mais simples a operações JOIN muito complexas e logo com maior facilidade de leitura. No entanto, estas queries são normalmente muito dificeis de otimizar pelos Sistemas de Gestão de Base de Dados pelo que podem comprometer a performance.

A query interna pode aparecer em várias localizações da query externa, exemplo

``` sql
SELECT 
  nome, 
  (SELECT nome FROM t2 WHERE id = 1) nome_t2 
FROM t1;
```


``` sql
SELECT 
  * 
FROM 
  (SELECT * FROM t1 WHERE id > 10) t2
WHERE id < 20;
```


``` sql
SELECT * FROM t1 
WHERE t1.id IN (
  SELECT t2.id FROM t2);
```

As palavras chave ```ALL```, ```ANY``` ou ```IN``` podem ser usadas para filtrar conjuntos de tuplos com as operações comparação existentes:
|Operador|Descrição|Exemplo|
|--------|---------|-------|
|ALL|Necessita satisfazer a expressão para todos os tuplos resultado da subquery|``` SELECT eid	FROM inscrito WHERE nota > ALL(SELECT nota FROM inscritos WHERE uid = 222); ```|
|ANY|Necessita satisfazer a expressão para pelo menos um tuplo resultado da subquery|``` SELECT nome	FROM estudante	WHERE	eid = ANY(SELECT eid	FROM inscrito	WHERE uid=215);```|
|IN|Equivalente a ‘=ANY()’ (valor de atributo contido na subquery)|```SELECT eid, nome	FROM	estudante,	WHERE eid IN (SELECT MAX(eid)	FROM inscritos);```|


### Exercícios
Considere a Base de Dados hr que usou nas aulas anteriores. Usando nested queries, escreva a query SQL que permite responder cada uma das seguintes questões:

1. Quais os empregados (primeiro nome e último nome) que recebem um salário superior ao empregado cujo id é 163
2. Quais os empregados (primeiro nome, último nome, salário e departamento id) que recebem salário igual ao salário mínimo de algum departamento.
3. Quais os empregados (id, primeiro nome, último nome) dos empregados que ganham acima do salário médio
4. Quais os empregados (primeiro nome, departamento id, job id e nome do departamento) dos empregados que trabalham no departamento Finance.
5. Quais os empregados cujo salário está acima da média mas abaixo de 10000.
6. Quais os empregados que não trabalham nos departamentos dos managers cujo id é entre 100 e 200.
7. Quais os empregados que recebem o segundo salário mais elevado. 
8. Quais os empregados (id primeiro nome e salario) que recebem acima da média e trabalham no mesmo departamento onde algum nome empregado contenha a letra 'J'.
9. Quais os empregados (primeiro nome) que trabalham nos departamentos de United Kingdom.
10. Quais os empregados (primeiro nome, último nome, salário e job id) que recebem acima da média de salários para a sua função.


## 2. Operações UNION, INTERSECT e MINUS
Em SQL podemos efetuar operações entre vários conjuntos. 
![image](https://user-images.githubusercontent.com/32137262/197638351-749da169-af37-4809-b1e3-b0e8f4d3fc2f.png)

Exemplos:
|Operador|Descrição|Exemplo|
|--------|---------|-------|
|UNION|Conjunto de tuplos que estão no primeiro e/ou no segundo conjunto, sem duplicados|Obter diferentes nomes de alunos e nomes de professores: ```SELECT nome FROM alunos UNION SELECT nome FROM professores;```|
|UNION ALL|Conjunto de tuplos que estão no primeiro e/ou no segundo conjunto, incluindo duplicados|Obter nomes de alunos e nomes de professores mantendo repetições entre grupos: ```SELECT nome FROM alunos UNION ALL SELECT nome FROM professores;```|
|INTERSECT|Conjunto de tuplos que existem simultaneamente no primeiro e no segundo conjuntos.|Obter nomes de alunos que também são nomes de professores: ```SELECT nome FROM alunos INTERSECT SELECT nome FROM professores;```|
|EXCEPT (MINUS)|Conjunto de tuplos que estão no primeiro conjunto mas não no segundo conjunto|Obter nomes de alunos que não são nomes de professores: ```SELECT nome FROM alunos EXCEPT SELECT nome FROM professores;```|

### Exercícios
Para cada uma das alíneas seguintes, escreva a query que permite obter:
1. Uma única lista com os nomes próprios dos trabalhadores que trabalham em departamentos de it e dos apelidos dos trabalhadores de Marketing.
2. A lista de nomes próprios que coexistam no departamentos de IT e de Finance.
3. A lista de nomes próprios apenas de trabalhadores não managers, i.e. excluindo os nomes de managers.


## 3. Trabalho de Casa
Nesta aula grande parte dos alunos não conseguiram terminar os exercícios propostos. Assim o trabalho de casa serão os 3 últimos exercícios sobre subqueries (1.8, 1.9 e 1.10):

8. Quais os empregados (id primeiro nome e salario) que recebem acima da média e trabalham no mesmo departamento onde algum nome empregado contenha a letra 'J'.

9. Quais os empregados (primeiro nome) que trabalham nos departamentos de United Kingdom.

10. Quais os empregados (primeiro nome, último nome, salário e job id) que recebem acima da média de salários para a sua função.

Bom trabalho!

NOTA: submeta a sua resposta ao trabalho de casa no moodle contendo as queries que respondem às questões num script sql. O ficheiro de texto com o nome TPC_a11_[N_ALUNO].sql (exemplo: TPC_a11_12345.sql para o aluno número 12345).

## 4. Resoluções
[Resolução dos exercícios em aula](https://github.com/ULHT-BD/aula11/blob/main/aula11_resolucao.sql)

## Bibliografia e Referências
* [Slides aula](https://github.com/ULHT-BD/aula12/blob/main/Aula12.pdf) 
* [Isolation Levels - Medium]((https://medium.com/analytics-vidhya/understanding-mysql-transaction-isolation-levels-by-example-1d56fce66b3d))


## Outros
Para dúvidas e discussões pode juntar-se ao grupo slack da turma através do [link](
https://join.slack.com/t/ulht-bd/shared_invite/zt-1iyiki38n-ObLCdokAGUG5uLQAaJ1~fA) (atualizado 2022-11-03)
