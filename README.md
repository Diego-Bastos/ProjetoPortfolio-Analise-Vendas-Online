# Análise de Vendas Online vs. Outros Canais — Lift & Cia

## Visão Geral do Projeto
Este projeto tem como objetivo avaliar o desempenho dos canais de vendas da empresa **Lift & Cia** utilizando o banco de dados **ContosoRetailDW**, respondendo à solicitação do gestor: **"Estamos pensando em investir mais no canal digital. Quero ver como as vendas online se comparam aos outros canais em faturamento, ticket médio e comportamento de compra. Me
ajuda a construir um argumento para a diretoria?"**.

A solução foi desenvolvida combinando extração e agregação de dados via **SQL**, modelagem relacional de dados com tabela calendário personalizada, fórmulas em **DAX** para inteligência de tempo e a construção de um **Dashboard Executivo interativo no Power BI**.

---

## 🖼️ Preview do Dashboard

![Dashboard Preview](IMGDashboardPortfolio.png)

---

## Estrutura e Tratamento dos Dados (SQL)
A consulta SQL foi elaborada para consolidar as transações do canal online (`FactOnlineSales`) e dos demais canais físicos/tradicionais (`FactSales` integrando com `DimChannel`).

### Métrica de Consolidação:
* **`DateKey`**: Chave temporal para relacionamento com a tabela fato/calendário.
* **`TOTAL_TRANSACOES`**: Contagem total do número de registros (`COUNT(*)`).
* **`QTDE_DE_VENDAS`**: Soma da quantidade total de itens vendidos (`SUM(SalesQuantity)`).
* **`TOTAL_FATURAMENTO`**: Soma do valor total faturado (`SUM(SalesAmount)`).
* **`TICKET_MEDIO`**: Média do valor faturado por transação (`AVG(SalesAmount)`).

```sql
USE ContosoRetailDW;

SELECT 
    DateKey
    ,COUNT(*) AS TOTAL_TRANSACOES
    ,SUM(SalesQuantity) AS QTDE_DE_VENDAS
    ,SUM(SalesAmount) AS TOTAL_FATURAMENTO
    ,AVG(SalesAmount) AS TICKET_MEDIO
    ,'ONLINE' AS CANAL_DE_VENDA
FROM FactOnlineSales AS ONLINE
GROUP BY DateKey

UNION ALL

SELECT 
    F.DateKey
    ,COUNT(*) AS TOTAL_TRANSACOES
    ,SUM(F.SalesQuantity) AS QTDE_DE_VENDAS
    ,SUM(F.SalesAmount) AS TOTAL_FATURAMENTO
    ,AVG(F.SalesAmount) AS TICKET_MEDIO
    ,C.ChannelName AS CANAL_DE_VENDA
FROM FactSales AS F
INNER JOIN DimChannel AS C ON C.ChannelKey = F.ChannelKey
GROUP BY F.DateKey, C.ChannelName
ORDER BY CANAL_DE_VENDA;

## Principais Insights e Descobertas

1. **Volume de Transações vs. Ticket Médio:**
   * O canal **ONLINE** é o líder disparado em volume de pedidos, registrando **5.198,93 Mil transações** e **35,66% do Faturamento** (R$ 1,81 Bi). 
   * No entanto, o **Ticket Médio do canal ONLINE é de apenas R$ 407,02**, sendo muito menor do que os canais mais tradicionais:
     * **Catalog:** R$ 5.528,92
     * **Reseller:** R$ 4.203,73
     * **Store:** R$ 3.193,77

2. **Evolução Temporal e Retração YoY:**
   * No acumulado *Year-to-Date*, a empresa apresenta uma queda de **-7,3% no Faturamento do ano de 2009** (-R$ 363 Milhões em relação ao ano anterior 2008).
   * O gráfico de ticket médio por canal mostra que a aceleração do volume digital não foi suficiente para sustentar o crescimento de faturamento da empresa devido ao baixo ticket médio.

3. ** Faturamento Ano Anterior x Ano Atual:**
  * O gráfico mostra que a queda de faturamento ano após ano por conta do baixo ticket médio das vendas online. O aumento das vendas no canal digital "roubou" volume de outros canais e com um ticket médio infinitamente menor. 

---

## Conclusão Estratégica

### **Pergunta Central:** *Devemos ou não investir mais no canal digital?*

**Resposta:** **SIM, a empresa deve continuar investindo no digital levando em consideração o crescimento no volume de vendas, porém é necessário alterar a estratégia comercial e de marketing para o canal colocando em destaque produtos com um preço maior com o objetivo de aumentar o ticket médio do canal. Consequentemente fazendo o faturamento voltar a crescer.**

---

## Tecnologias e Ferramentas Utilizadas

* **SQL:** Agregação complexa, união de fatos com `UNION ALL` e junção com dimensão via `INNER JOIN`.
* **Power BI:** Modelagem dimensional (`dim_calendario`), criação de medidas DAX para Inteligência de Tempo e visualização de dados executivo.
