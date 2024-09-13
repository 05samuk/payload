# Oque eu fiz?

1. #### Geração de Payloads em JSON
Utilizando o ChatGPT, foram gerados cinco tipos de payloads distintos em JSON. Esses payloads foram baseados nas tabelas previamente criadas no BigQuery, chamadas squadcalouros.squadcalouros.campaigns e squadcalouros.squadcalouros.purchases. Os campos utilizados para gerar os payloads são descritos abaixo:

## Campos de campaigns:

**campaign_id**

**type_campaign**

**days_valid**

**data_campaign**

**channel**

**return_status**

**return_date**

**client_id**

## Campos de purchases:

**purchase_id**

**product_name**

**product_id**

**amount**

**price**

**discount_applied**

**payment_method**

**purchase_datetime**

**purchase_location**

## 2. Geração de Views no Vertex AI
Após a criação dos payloads, eles foram introduzidos na plataforma Vertex AI da GCP, onde foram processados para gerar views SQL. Cada payload foi transformado em comandos SQL que descreviam como as tabelas campaigns e purchases deveriam ser combinadas e estruturadas nas views.

## 3. Visualização e Análise no BigQuery
Os comandos SQL gerados no Vertex AI foram transferidos para o BigQuery para criação e visualização das views. O foco foi testar a integridade das consultas, verificar o correto relacionamento entre as tabelas e identificar possíveis erros de sintaxe ou lógica.

## Resultados (propostas, oque foi atendido)

O resultado do payload gerado e escolhido foi esse:

```json
{
  "view_name": "channel_purchase_summary",
  "source_table": "campaigns_purchases",
  "fields": [
    {"name": "channel", "type": "string"},
    {"name": "total_amount", "type": "decimal", "aggregation": "SUM"},
    {"name": "total_discount", "type": "decimal", "aggregation": "SUM", "field": "discount_applied"}
  ],
  "group_by": ["channel"],
  "order_by": [
    {"field": "total_amount", "direction": "DESC"}
  ]
} 
```

Em seguida, inseri o payload no Vertex AI, solicitando que ele gerasse o código correspondente com base nas informações fornecidas. O sistema retornou o seguinte código:

```sql

CREATE VIEW `squadcalouros.squadcalouros.campaign_purchase_summary` AS
SELECT 
    id_campaign,
    type_campaign,
    channel,
    COUNT(purchase_id) AS purchase_id,
    SUM(price) AS price
FROM 
    `squadcalouros.squadcalouros.combined_campaigns_purchase`
GROUP BY 
    id_campaign, type_campaign, channel;
  
```

Para a visualização da view, utilizei o seguinte comando:

```sql
 SELECT * FROM `squadcalouros.squadcalouros.campaign_purchase_summary`
    LIMIT 10;
```
Gerando esse view 

<img src= "images/Captura de tela 2024-09-13 165800.png">


# Dúvidas 

Nessa atividade tive dúvidas apenas em detalhes pequenos de sintaxe, na hora de passar o código sql, porque no gcp é diferente do que eu estou acostumado como por exemplo a criação de nomes para as tabelas. De resto foi tranquilo.