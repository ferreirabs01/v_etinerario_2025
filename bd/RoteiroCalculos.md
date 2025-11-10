> Para verificarmos qual a data corrente


```sql
select CURDATE()
```
Diferença de Dias (DATEDIFF):
```sql
SELECT DATEDIFF('2025-12-25', CURDATE()) AS dias_para_o_natal;
```

 Para calcular idade:
```sql
select a.nome, a.nome, a.email, a.data_nascimento,
TIMESTAMPDIFF(YEAR, a.data_nascimento, CURDATE()) AS idade
from alunos a
```

 ##modelo
```sql

```
