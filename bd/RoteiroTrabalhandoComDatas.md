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
  
 

 ##modelo
```sql

```
