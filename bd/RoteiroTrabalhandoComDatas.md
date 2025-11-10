> Para verificarmos qual a data corrente


```sql
select CURDATE()
```
Diferença de Dias (DATEDIFF) por exemplo de hoje faltam quantos dias para o natal?:
```sql
SELECT DATEDIFF('2025-12-25', CURDATE()) AS dias_para_o_natal;
```
Vamos descobrir de hoje mais 6 meses (por exemplo) qual dia seria:

```sql
SELECT
  CURDATE() AS hoje,
  DATE_ADD(CURDATE(), INTERVAL 6 MONTH) AS daqui_6_meses;

```

e para adicionar 10 dias a data de hoje 
```sql

SELECT
  CURDATE() AS hoje,
  DATE_ADD(CURDATE(), INTERVAL 10 day) AS daqui_10_dias;

```
adicionando 2 anos:
```sql
SELECT
  CURDATE() AS hoje,
  DATE_ADD(CURDATE(), INTERVAL 2 year) AS daqui_2_anos;
```
formatando a data :
```sql
SELECT a.nome,a.email, a.data_nascimento, 
  DATE_FORMAT(a.data_nascimento, '%e de %M de %Y') AS data_formatada
from alunos a;
```

apos utilizar a formatacao em ingles podemos setar para pt-br:
```sql
SET lc_time_names = 'pt_BR';
```

volte e execute o comando anterior.

agora vamos exibir o dia da semana (segunda, terca, quarta ...)
```sql
SELECT DAYNAME(CURDATE()) AS dia_de_hoje;
```
vamos fazer agora com o campo data_nascimento:
```sql
SELECT a.nome,a.email, a.data_nascimento, 
  DAYNAME(a.data_nascimento) AS dia_semana
from alunos a;
```

 Para calcular idade:
```sql
select a.nome, a.nome, a.email, a.data_nascimento,
TIMESTAMPDIFF(YEAR, a.data_nascimento, CURDATE()) AS idade
from alunos a
```
resolucao da atividade de fixacao trabalhando com datas classroom:
```sql
SET lc_time_names = 'pt_BR';
```
```sql
SELECT
  nome,
  -- 1. Calcula a idade exata
  TIMESTAMPDIFF(YEAR, data_nascimento, CURDATE()) AS idade,

  -- 2. Formata a data por extenso
  DATE_FORMAT(data_nascimento, '%e de %M de %Y') AS aniversario_formatado,

  -- 3. Extrai o dia da semana
  DAYNAME(data_nascimento) AS dia_que_nasceu
FROM
  alunos;
```

# 🇫 O que é uma Function (Função Armazenada)?

Uma **Function** (ou Função Armazenada), em um banco de dados, é um bloco de código reutilizável que executa uma tarefa específica e, o mais importante, **sempre retorna um único valor**.

Pense nela como uma "calculadora" especializada que você cria e salva.

1.  Você **envia** dados para ela (através de "parâmetros", como uma `data_nascimento`).
2.  Ela **processa** esses dados (como a `fn_calcular_idade` fez o cálculo).
3.  Ela **devolve** um único resultado (como a `idade` 25).

---

## 📈 Qual a sua Usabilidade?

O propósito (usabilidade) de uma Function é resolver problemas de **repetição**, **complexidade** e **manutenção** no seu código SQL.

### 1. Reusabilidade (Não se Repita)
Este é o motivo principal. Em vez de escrever o mesmo cálculo complexo (como `TIMESTAMPDIFF(...)`) em 10 queries diferentes, você o escreve **uma única vez** dentro da `FUNCTION`. Depois, você apenas "chama" a função pelo nome em todos os 10 lugares.

### 2. Manutenção Centralizada
Se a regra de negócio mudar, você só precisa alterar o código em **um lugar**: dentro da Function. Todos os 10 lugares que a utilizam são atualizados automaticamente.

> **Exemplo:** Se a sua função `fn_calcular_idade` precisasse ser ajustada por causa de um fuso horário, você a alteraria uma vez, e todas as queries que a usam estariam corretas.

### 3. Abstração e Simplicidade
Ela "esconde" a complexidade. Seu comando `SELECT` fica muito mais limpo e fácil de ler para quem não precisa saber *como* o cálculo é feito.

* **Complexo:**
    ```sql
    SELECT nome, TIMESTAMPDIFF(YEAR, data_nascimento, CURDATE()) AS idade FROM ...
    ```
* **Simples (com Function):**
    ```sql
    SELECT nome, fn_calcular_idade(data_nascimento) AS idade FROM ...
    ```

### 4. Flexibilidade de Uso
Como ela retorna um valor, você pode usá-la em várias partes de uma query, não apenas no `SELECT`:

* **No `SELECT`:** Para exibir o valor calculado.
    ```sql
    SELECT fn_calcular_idade(data_nascimento) ...
    ```
* **No `WHERE`:** Para filtrar resultados.
    ```sql
    WHERE fn_calcular_idade(data_nascimento) > 18
    ```
* **No `ORDER BY`:** Para ordenar os resultados.
    ```sql
    ORDER BY fn_calcular_idade(data_nascimento) DESC
    ```

---

**Em resumo:** você cria uma **Function** sempre que tem um cálculo que precisa fazer repetidamente e quer poder usá-lo de forma flexível dentro dos seus comandos SQL.


  agora vamos criar uma function para calcular idade:

  ```sql
DELIMITER //

CREATE FUNCTION fn_calcular_idade(
    p_data_nascimento DATE  -- Recebe a data de nascimento
)
RETURNS INT -- Informa que ela VAI RETORNAR um número inteiro
READS SQL DATA -- Informa ao MySQL que a função apenas lê dados (boa prática)
BEGIN
    -- Retorna diretamente o resultado do cálculo
    RETURN TIMESTAMPDIFF(YEAR, p_data_nascimento, CURDATE());
END//

DELIMITER ;

```

utilizando a function:

```sql
SELECT
  nome,
  email,
  data_nascimento,
  -- A MÁGICA ACONTECE AQUI!
  fn_calcular_idade(data_nascimento) AS idade
FROM
  alunos;

```
 

 ##modelo
```sql

```
