|criar o banco de dados

```sql
-- Remove o banco se ele já existir (para testes)
DROP DATABASE IF EXISTS loja;
CREATE DATABASE loja;
USE loja;

-- 1. Tabela de Clientes
CREATE TABLE clientes (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL
);

-- 2. Tabela de Produtos
CREATE TABLE produtos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(255) NOT NULL,
    preco DECIMAL(10, 2) NOT NULL,
    estoque INT NOT NULL DEFAULT 0
);

-- 3. Tabela de Pedidos (cabeçalho)
CREATE TABLE pedidos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    cliente_id INT,
    data_pedido DATETIME DEFAULT CURRENT_TIMESTAMP,
    status ENUM('Pendente', 'Pago', 'Enviado', 'Cancelado') NOT NULL DEFAULT 'Pendente',
    FOREIGN KEY (cliente_id) REFERENCES clientes(id)
);

-- 4. Tabela de Itens do Pedido (detalhes)
CREATE TABLE pedido_itens (
    id INT AUTO_INCREMENT PRIMARY KEY,
    pedido_id INT,
    produto_id INT,
    quantidade INT NOT NULL,
    preco_unitario DECIMAL(10, 2) NOT NULL, -- "Congela" o preço no momento da venda
    FOREIGN KEY (pedido_id) REFERENCES pedidos(id),
    FOREIGN KEY (produto_id) REFERENCES produtos(id)
);

-- 5. Tabela de Log para o Trigger de Auditoria
CREATE TABLE log_auditoria_preco (
    id INT AUTO_INCREMENT PRIMARY KEY,
    produto_id INT,
    preco_antigo DECIMAL(10, 2),
    preco_novo DECIMAL(10, 2),
    usuario_modificador VARCHAR(100),
    data_modificacao DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (produto_id) REFERENCES produtos(id)
);
```

Agora, vamos implementar as regras usando Triggers e Procedures.

Importante: Precisamos mudar o delimitador padrão (;) para que o MySQL entenda onde o código do trigger/procedure começa e termina.
Regra: Ninguém pode alterar o preço de um produto sem que isso seja registrado na tabela log_auditoria_preco.

Implementação: Usamos um trigger BEFORE UPDATE (antes de atualizar) na tabela produtos. Ele compara o preço antigo (OLD.preco) com o preço novo (NEW.preco). Se forem diferentes, ele insere um registro no log.

```sql
DELIMITER $$

CREATE TRIGGER trg_log_mudanca_preco
BEFORE UPDATE ON produtos
FOR EACH ROW
BEGIN
    -- Só registra no log se o preço realmente mudou
    IF OLD.preco <> NEW.preco THEN
        INSERT INTO log_auditoria_preco (produto_id, preco_antigo, preco_novo, usuario_modificador)
        VALUES (OLD.id, OLD.preco, NEW.preco, USER());
    END IF;
END$$

```

Contexto 2: Trigger de Baixa de Estoque (AFTER INSERT)
Regra: Quando um item é vendido (inserido em pedido_itens), o estoque principal em produtos deve ser atualizado.

Implementação: Usamos um trigger AFTER INSERT (depois de inserir) na tabela pedido_itens. Ele pega a quantidade recém-inserida (NEW.quantidade) e subtrai do estoque do produto correspondente.

```sql
DELIMITER $$

CREATE TRIGGER trg_baixar_estoque_venda
AFTER INSERT ON pedido_itens
FOR EACH ROW
BEGIN
    UPDATE produtos
    SET estoque = estoque - NEW.quantidade
    WHERE id = NEW.produto_id;
END$$

```

Novo Trigger: AFTER UPDATE em pedido_itens
Contexto: O cliente mudou a quantidade de um item no pedido.

Lógica: Precisamos calcular a diferença do que ele tinha (OLD.quantidade) para o que ele tem agora (NEW.quantidade).

Se OLD=2 e NEW=5, a diferença é +3. Devemos baixar 3 do estoque.

Se OLD=5 e NEW=1, a diferença é -4. Devemos devolver 4 ao estoque.

A fórmula para o ajuste é: (OLD.quantidade - NEW.quantidade).


```sql
DELIMITER $$
CREATE TRIGGER trg_atualizar_estoque_update
AFTER UPDATE ON pedido_itens
FOR EACH ROW
BEGIN
    DECLARE v_diferenca INT;
    
    -- Calcula a diferença. 
    -- Se OLD=2 e NEW=5, v_diferenca = 2 - 5 = -3 (Baixar 3)
    -- Se OLD=5 e NEW=2, v_diferenca = 5 - 2 = +3 (Devolver 3)
    SET v_diferenca = OLD.quantidade - NEW.quantidade;
    
    -- Atualiza o estoque somando a diferença
    -- (somar -3 é o mesmo que subtrair 3)
    UPDATE produtos
    SET estoque = estoque + v_diferenca
    WHERE id = NEW.produto_id; -- Pode ser NEW.produto_id ou OLD.produto_id
END$$
```
*** desafio ***
Novo Trigger: AFTER DELETE em pedido_itens
Contexto: O cliente cancelou um item (ou o pedido inteiro) e a linha foi apagada de pedido_itens.

Lógica: Precisamos devolver a quantidade (OLD.quantidade) do item excluído de volta ao estoque.

Implementação:

```sql
DELIMITER $$
CREATE TRIGGER trg_retornar_estoque_delete
AFTER DELETE ON pedido_itens
FOR EACH ROW
BEGIN
    -- Simplesmente devolve o que foi comprado
    UPDATE produtos
    SET estoque = estoque + OLD.quantidade
    WHERE id = OLD.produto_id;
END$$
```
