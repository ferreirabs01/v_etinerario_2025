Roteiro SQL completo

-----

## Roteiro de Banco de Dados para a `escola`

### 1\. Criando o Banco de Dados

```sql
CREATE DATABASE escola;
```

### 2\. Selecionando o Banco de Dados

```sql
USE escola;
```

### 3\. Criando as Tabelas

  * **Tabela `alunos` (sem UNIQUE):**

    ```sql
    CREATE TABLE alunos (
        id_aluno INT PRIMARY KEY AUTO_INCREMENT,
        nome VARCHAR(100) NOT NULL,
        email VARCHAR(100),
        data_nascimento DATE
    );
    ```

  * **Tabela `cursos`:**

    ```sql
    CREATE TABLE cursos (
        id_curso INT PRIMARY KEY AUTO_INCREMENT,
        nome_curso VARCHAR(100) NOT NULL,
        descricao TEXT,
        carga_horaria INT
    );
    ```

  * **Tabela `matriculas` (com chaves estrangeiras - FKs):**

    ```sql
    CREATE TABLE matriculas (
        id_matricula INT PRIMARY KEY AUTO_INCREMENT,
        id_aluno INT,
        id_curso INT,
        data_matricula TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
        FOREIGN KEY (id_aluno) REFERENCES alunos(id_aluno),
        FOREIGN KEY (id_curso) REFERENCES cursos(id_curso)
    );
    ```

      * **`FOREIGN KEY (id_aluno) REFERENCES alunos(id_aluno)`:** Cria uma chave estrangeira na coluna `id_aluno` da tabela `matriculas` que faz referência à chave primária `id_aluno` da tabela `alunos`.
      * **`FOREIGN KEY (id_curso) REFERENCES cursos(id_curso)`:** Cria uma chave estrangeira na coluna `id_curso` da tabela `matriculas` que faz referência à chave primária `id_curso` da tabela `cursos`.

### 4\. Inserindo Dados de Exemplo

  * **Inserindo dados na tabela `alunos`:**

    ```sql
    INSERT INTO alunos (nome, email, data_nascimento) VALUES
    ('João da Silva', 'joao.silva@email.com', '2000-05-15'),
    ('Maria Souza', 'maria.souza@email.com', '1999-11-20'),
    ('Pedro Santos', 'pedro.santos@email.com', '2001-08-01');
    ```

  * **Inserindo dados na tabela `cursos`:**

    ```sql
    INSERT INTO cursos (nome_curso, descricao, carga_horaria) VALUES
    ('Banco de Dados com SQL', 'Curso completo de banco de dados relacional.', 40),
    ('Programação Web', 'Aprenda a criar sites e aplicações web.', 60),
    ('Introdução à Ciência de Dados', 'Primeiros passos no mundo da análise de dados.', 30);
    ```

  * **Inserindo dados na tabela `matriculas`:**

    ```sql
    INSERT INTO matriculas (id_aluno, id_curso) VALUES
    (1, 1), -- João se matricula no curso de SQL
    (1, 2), -- João se matricula no curso de Programação Web
    (2, 1); -- Maria se matricula no curso de SQL
    ```

### 5\. Consultando Dados com JOIN

Para ver as informações de matrícula de forma mais completa, você pode usar o comando `JOIN`.

  * **Consultar matrículas mostrando os nomes dos alunos e dos cursos:**
    ```sql
    SELECT 
        a.nome AS nome_aluno, 
        c.nome_curso, 
        m.data_matricula
    FROM matriculas m
    JOIN alunos a ON m.id_aluno = a.id_aluno
    JOIN cursos c ON m.id_curso = c.id_curso;
    ```
-----

## Passo 6. Adicionar o Campo `end_aluno` na Tabela `alunos`

Para adicionar a nova coluna `end_aluno` (endereço do aluno) à sua tabela `alunos`, use o comando `ALTER TABLE`.

### Comando para Adicionar a Coluna

```sql
ALTER TABLE alunos ADD COLUMN end_aluno VARCHAR(255);
```

### Explicação

  * **`ALTER TABLE alunos`**: Informa ao MySQL que você deseja fazer uma alteração na estrutura da tabela `alunos`.
  * **`ADD COLUMN end_aluno VARCHAR(255)`**: Adiciona uma nova coluna chamada **`end_aluno`** com o tipo de dado **`VARCHAR(255)`**, que pode armazenar textos de até 255 caracteres.

Depois de executar este comando, a tabela `alunos` terá a nova coluna, mas ela estará vazia para todos os registros existentes. Você pode usar o comando `UPDATE` para preencher os endereços, como no exemplo abaixo:

```sql
UPDATE alunos
SET end_aluno = 'Rua das Flores, 123'
WHERE id_aluno = 1;
```

-----

## Passo 7. Alterar o nome da coluna `end_aluno` para `endereco`

Para renomear a coluna que você acabou de criar, use o comando `ALTER TABLE` novamente, mas desta vez com a cláusula `CHANGE COLUMN`.

### Comando para Renomear a Coluna

```sql
ALTER TABLE alunos CHANGE COLUMN end_aluno endereco VARCHAR(255);
```

### Explicação

  * **`ALTER TABLE alunos`**: Seleciona a tabela `alunos` para a alteração.
  * **`CHANGE COLUMN end_aluno endereco VARCHAR(255)`**: Este é o ponto principal do comando.
      * `CHANGE COLUMN`: A palavra-chave que indica que você vai renomear uma coluna.
      * `end_aluno`: O nome **antigo** da coluna.
      * `endereco`: O **novo** nome que você deseja dar à coluna.
      * `VARCHAR(255)`: Você precisa especificar novamente o tipo de dado da coluna.

Ao executar este comando, todos os dados que estavam na coluna `end_aluno` serão mantidos e transferidos para a nova coluna `endereco`.

Para fazer o backup do seu banco de dados `escola` usando o `mysqldump`, você precisará executar um comando no seu terminal (linha de comando), e não diretamente no console do MySQL.

-----

### Passo 8. Gerar o Backup com `mysqldump`

O `mysqldump` é um utilitário de linha de comando que exporta o conteúdo do seu banco de dados em um arquivo SQL. É a maneira mais comum de fazer backups lógicos no MySQL e MariaDB.

### Comando para o Backup Completo

Siga a sintaxe abaixo. Lembre-se de substituir `[usuário]` pelo seu nome de usuário do banco de dados (ex: `root`) e `[senha]` pela sua senha.

```sh
mysqldump -u [usuário] -p[senha] escola > backup_escola.sql
```

### Explicação Detalhada do Comando

  * **`mysqldump`**: O nome do programa que você está executando.
  * **`-u [usuário]`**: A flag para informar o nome de usuário do MySQL.
  * **`-p[senha]`**: A flag para a senha. **Atenção:** não há espaço entre `-p` e a senha. Se você não colocar a senha aqui, o sistema irá pedi-la logo após você executar o comando.
  * **`escola`**: O nome do banco de dados que você quer fazer o backup.
  * **`>`**: O operador de redirecionamento. Ele envia a saída do `mysqldump` (que é o roteiro SQL) para um arquivo.
  * **`backup_escola.sql`**: O nome do arquivo que será criado com o seu backup.

Ao executar este comando, um arquivo chamado **`backup_escola.sql`** será gerado no mesmo diretório em que você está no terminal. Este arquivo conterá todos os comandos SQL necessários para recriar o banco de dados `escola`, incluindo a estrutura das tabelas (`CREATE TABLE`) e todos os dados (`INSERT INTO`).

-----

## Passo 9. Demonstração de `DELETE` com Chaves Estrangeiras

Este passo irá demonstrar como o banco de dados protege a integridade dos seus dados, impedindo que você apague registros que estão sendo referenciados por outras tabelas.

### 1\. Tentativa de `DELETE` na Tabela `alunos` (Onde Haverá Erro)

Vamos tentar apagar todos os registros da tabela `alunos`. Isso vai gerar um erro, pois os alunos estão vinculados a matrículas na tabela `matriculas`.

  * **Comando:**
    ```sql
    DELETE FROM alunos;
    ```
  * **Resultado esperado:**
    O banco de dados irá retornar um erro, geralmente com uma mensagem como:
    ```
    ERROR 1451 (23000): Cannot delete or update a parent row: a foreign key constraint fails (`escola`.`matriculas`, CONSTRAINT `matriculas_ibfk_1` FOREIGN KEY (`id_aluno`) REFERENCES `alunos` (`id_aluno`))
    ```
  * **Explicação:**
    O erro ocorre porque a tabela `alunos` é a **tabela-mãe** (`parent`) e a tabela `matriculas` é a **tabela-filha** (`child`). A restrição de **`FOREIGN KEY`** impede que você apague um aluno se houver alguma matrícula vinculada a ele, garantindo que não haja "matrículas órfãs" (registros sem um aluno correspondente).

-----

### 2\. `DELETE` na Tabela `matriculas` (Onde Haverá Sucesso)

Para poder apagar os alunos, você deve primeiro apagar as matrículas, pois a tabela `matriculas` é a tabela-filha.

  * **Comando:**
    ```sql
    DELETE FROM matriculas;
    ```
  * **Resultado esperado:**
    O comando será executado com sucesso e todos os registros da tabela `matriculas` serão apagados. A tabela-filha não possui restrições de chave estrangeira que a impeçam de ser esvaziada.

-----

### 3\. `DELETE` na Tabela `alunos` Novamente (Agora com Sucesso)

Com a tabela `matriculas` vazia, a restrição de chave estrangeira não se aplica mais. Agora você pode apagar os registros da tabela `alunos` sem problemas.

  * **Comando:**
    ```sql
    DELETE FROM alunos;
    ```
  * **Resultado esperado:**
    O comando será executado com sucesso, e todos os registros da tabela `alunos` serão apagados.

-----

## Passo 10. Apagar o Banco de Dados

Este é o comando para apagar completamente o banco de dados `escola` e todas as tabelas e dados que ele contém.

### Comando para Apagar o Banco de Dados

```sql
DROP DATABASE escola;
```

  * **Atenção:** Este comando é irreversível. Certifique-se de que você realmente deseja apagar o banco de dados.

-----

## Passo 11. Restaurar o Backup

Depois de apagar o banco de dados, você pode restaurá-lo a partir do arquivo de backup (`backup_escola.sql`) que você gerou no Passo 8. Este comando deve ser executado no seu terminal (linha de comando), não no console do MySQL.

### Comando para Restaurar o Backup

antes de restaurar o bkp lembre-se de criar a database na linha de comando (pela IDE - exemplo HeidiSQL - se houver o create database o proprio script ja o faz)


```sh
create database escola;
```

Siga a sintaxe abaixo. Lembre-se de substituir `[usuário]` pelo seu nome de usuário do banco de dados e `[senha]` pela sua senha.

```sh
mysql -u [usuário] -p[senha] [nomeBanco] < backup_escola.sql
```

### Explicação Detalhada do Comando

  * **`mysql`**: O cliente de linha de comando do MySQL, que será usado para se conectar ao servidor de banco de dados.
  * **`-u [usuário]`**: A flag para informar o nome de usuário.
  * **`-p[senha]`**: A flag para a senha (sem espaço entre `-p` e a senha).
  * **`<`**: O operador de redirecionamento. Ele informa ao `mysql` para ler os comandos SQL do arquivo em vez do terminal.
  * **`backup_escola.sql`**: O nome do arquivo que contém o seu backup.

Ao executar este comando, o MySQL lerá todos os comandos do arquivo `backup_escola.sql` (incluindo `CREATE DATABASE`, `CREATE TABLE`, `INSERT INTO`, etc.) e recriará o banco de dados `escola` exatamente como ele estava no momento do backup.


<div align="center" style="background-color:#90EE90; color:#006400; padding:10px; border-radius:5px;">
  **Roteiro criado por Bruno Ferreira | Atualizado em 29-setembro-2025
</div>
