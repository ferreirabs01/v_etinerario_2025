> Para verificarmos qual a data corrente


```sql
select CURDATE()
```
 Para calcular idade:
```sql
select a.nome, a.nome, a.email, a.data_nascimento,
TIMESTAMPDIFF(YEAR, a.data_nascimento, CURDATE()) AS idade
from alunos a
```
 
