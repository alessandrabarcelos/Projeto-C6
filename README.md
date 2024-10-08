# Banco de Dados para Estratégias do Processo Seletivo no C6 Bank

**Nome**: Alessandra de Fátima Barcelos  
**E-mail**: alessandrabarceloseng@gmail.com  
**Celular**: (37) 9 8851-4361  

## Objetivo
Criar um banco de dados de contatos de gestores do C6 Bank para enviar mensagens estratégicas e gerenciar atividades em busca de oportunidades de emprego.

## Ferramentas Utilizadas
- **Linguagem de Programação**: SQL
- **Sistemas de Banco de Dados**: BigQuery e MySQL
- **Apresentação e Visualização de Dados**: Power BI
- **Armazenamento e Documentação**: Google Docs

## Coleta de Dados
- **Pesquisas Online**: LinkedIn, site do C6 Bank, Serasa e Google.
- **Networking**: Conexão com pessoas do setor para obter referências.

### Acesso ao Armazenamento de Dados
[Planilha Google](https://docs.google.com/spreadsheets/d/1_uBVZ5ppmRjEEwmW21UumuR-9ftvD03J2cG4NwHKjfc/edit?usp=sharing)

## Tratamento de Dados Sensíveis

Neste projeto, implementamos uma camada de segurança para proteger informações sensíveis, como e-mails dos usuários, utilizando criptografia SHA-256. 

## Código SQL Utilizado
### 1. Implementação da Criptografia
```sql


-- Atualiza o campo 'email' de todos os usuários na tabela, aplicando SHA-256
UPDATE usuarios
SET email = SHA2(CONCAT(LOWER(last), LOWER(first), '@c6bank.com.br'), 256);

-- Caso esteja inserindo um novo usuário com e-mail criptografado
-- Exemplo para João Silva, onde 'first' é 'j' e 'last' é 'silva'
INSERT INTO usuarios (first, last, email)
VALUES ('j', 'silva', SHA2(CONCAT('silva', 'j', '@c6bank.com.br'), 256));

```
### 2. Conversão de Tabelas Google Sheets para BigQuery
```sql
CREATE OR REPLACE TABLE `processo-seletivo-c6.DadosC6.Formacaonovatabela` AS
SELECT * FROM `processo-seletivo-c6.DadosC6.Formacao`;

CREATE OR REPLACE TABLE `processo-seletivo-c6.DadosC6.Interacoes_e_contatosonovatabela` AS
SELECT * FROM `processo-seletivo-c6.DadosC6.Interacoes_e_contatos`;

CREATE OR REPLACE TABLE `processo-seletivo-c6.DadosC6.Perfil_do_Gestornovatabela` AS
SELECT * FROM `processo-seletivo-c6.DadosC6.Perfil_do_Gestor`;

```
### 3.Consulta Principal para Contato e Verificação de Atividades
```sql
SELECT
  p.First_Name,
  p.Last_Name,
  p.Cargo,
  f.Formacao,
  p.LinkedIn,
  p.E_mail,
  p.Numero_de_Celular,
  i.Status_convite_ln,
  i.Obteve_contato,
  i.Resposta_Recebida,
  i.Proxima_Acao,
  i.Comentarios
FROM `processo-seletivo-c6.DadosC6.Perfil_do_Gestornovatabela` AS p
JOIN `processo-seletivo-c6.DadosC6.Formacaonovatabela` AS f
  ON p.Gestor_ID = f.Gestor_ID
JOIN `processo-seletivo-c6.DadosC6.Interacoes_e_contatosonovatabela` AS i
  ON p.Gestor_ID = i.Gestor_ID;

```
### 4.Contagem de Gestores por Cargo
```sql
SELECT
  UPPER(p.Cargo) AS Cargo_Uppercase,
  ROUND(COUNT(f.Formacao_ID), 0) AS Total_Gestores_por_cargo
FROM `processo-seletivo-c6.DadosC6.Formacaonovatabela` AS f
JOIN `processo-seletivo-c6.DadosC6.Perfil_do_Gestornovatabela` AS p
  ON f.Gestor_ID = p.Gestor_ID
GROUP BY Cargo_Uppercase
ORDER BY Total_Gestores_por_cargo ASC;

```
### 5. Atualização de Comentários Após Contato com Gestores
```sql
UPDATE `processo-seletivo-c6.DadosC6.Interacoes_e_contatosonovatabela`
SET Comentarios = 'Aguardando resposta'
WHERE UPPER(Obteve_contato) = UPPER('TRUE');

```
### 6. Criação da Tabela para Planejamento e Verificação de Atividades
```sql
CREATE TABLE `processo-seletivo-c6.DadosC6.Atividade`
  (Atividades_ID INT64,
   Interacao_ID  INT64,
   Atividade STRING,
   Data_da_acao DATE);
   
INSERT INTO `processo-seletivo-c6.DadosC6.Atividade`
  (Atividades_ID, Interacao_ID, Atividade, Data_da_acao)
VALUES (8, 8, 'Realização de projeto', DATE '2024-10-01');

```
### 7. Inserção de Coluna de Controle e Deleção de Tabelas
```sql
ALTER TABLE `processo-seletivo-c6.DadosC6.Atividade` ADD COLUMN controle STRING;

DELETE FROM `processo-seletivo-c6.DadosC6.Atividade`
WHERE controle = 'Contratada';

DROP TABLE `processo-seletivo-c6.DadosC6.Formacaonovatabela`;
DROP TABLE `processo-seletivo-c6.DadosC6.Interacoes_e_contatosonovatabela`;
DROP TABLE `processo-seletivo-c6.DadosC6.Perfil_do_Gestornovatabela`;
DROP TABLE `processo-seletivo-c6.DadosC6.Atividade`;
DROP TABLE `processo-seletivo-c6.DadosC6.Banco_talentos`;

```
## Apresentação de Dados no Power BI:

A seguir, foram criadas as seguintes visualizações no Power BI.

## Ferramentas Utilizadas:

- **Gráfico de Barras**: Comparando as áreas de formação dos gestores com as competências.
- **Gráfico de Linha**: Analisando o tempo médio de promoção dos gestores no C6 Bank.
- **Gráfico de Radar**: Comparando as competências exigidas pelos gestores com minhas próprias habilidades (SQL, Power BI, Python).

## Considerações Finais: 

- Com os dados filtrados, ficou mais fácil organizar os contatos e otimizar a abordagem. As mensagens devem ser enviadas preferencialmente no período da manhã, utilizando canais como LinkedIn. Caso não haja resposta, o contato pode ser tentado por e-mail corporativo ou telefone.
- Além disso, observei que uma engenheira de alimentos faz parte da equipe, e verifiquei suas especializações, o que pode servir como referência para futuras interações.
