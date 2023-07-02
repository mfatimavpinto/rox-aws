# Rox-aws
Ingestão de dados com MySQL Workbench + RS AWS


🚀 Começando
O intuito do projeto é uma ingestão de dados em alguma nuvem pública, por motivos utilizei a AWS 

📋 Pré-requisitos
Instalar o MySQL Workbench 8.0.33 no site oficial -> https://dev.mysql.com/downloads/workbench/
Para AWS -> https://aws.amazon.com/pt/ utilizei o RS 

⚙️ Modelo relacional 
![modelo](https://github.com/mfatimavpinto/rox-aws/assets/102325898/6dc86d82-0e55-4dc6-9453-d00127d14eb6)

🔩 Etapa 1 
Importar os seguintes arquivos dentro do bd
Sales.SpecialOfferProduct.csv
Production.Product.csv
Sales.SalesOrderHeader.csv
Sales.Customer.csv
Person.Person.csv
Sales.SalesOrderDetail.csv

 
⌨️ Ficando assim no banco
Person(sales)
Production.Product
Sales.Customer
SalesOrderDetail(Sales)
SalesOrderHeader(Sales)
SpecialOfferProduct(Sales)
(algumas dificuldades da máquina para importar, ficando assim na última tentativa)

Dar exemplos
📦 Implantação
No banco MySQL Workbench fiz a conexão utilizando o endpoint da AWS RSD

🛠️ Resultado
![image](https://github.com/mfatimavpinto/rox-aws/assets/102325898/c1bac28c-3487-4794-9692-1b5827443f05)
![image](https://github.com/mfatimavpinto/rox-aws/assets/102325898/f9ef6a49-c5b3-44e8-bc0f-8ce987e9734f)
![image](https://github.com/mfatimavpinto/rox-aws/assets/102325898/3518ea0b-3b71-4cad-87b2-63fb74aca935)
bucket ![image](https://github.com/mfatimavpinto/rox-aws/assets/102325898/64c963e8-1ae9-4a77-8c62-c34f027cf781)


SQL -> Análise de dados

Com base na solução implantada responda aos seguintes questionamentos:

1. Escreva uma query que retorna a quantidade de linhas na tabela Sales.SalesOrderDetail pelo campo SalesOrderID, desde que tenham pelo menos três linhas de detalhes

````
SELECT SalesOrderID as id, 
COUNT(*) AS qtd 
FROM Sales.SalesOrderDetail as sod
GROUP BY SalesOrderID
HAVING qtd >= 3
````

2. Escreva uma query que ligue as tabelas Sales.SalesOrderDetail, Sales.SpecialOfferProduct e Production.Product e retorne os 3 produtos (Name) mais vendidos (pela soma de OrderQty), agrupados pelo número de dias para manufatura (DaysToManufacture).

```
SELECT * FROM(
  SELECT p.DaysToManufacture AS dtm,
         ROW_NUMBER() OVER(PARTITION BY p.DaysToManufacture ORDER BY sum(sod.OrderQty) DESC) as pos,
         p.Name as name,
         sum(sod.OrderQty) AS qtd
  FROM Sales.SpecialOfferProduct AS sop 
  INNER JOIN Production.Products AS p ON sop.ProductID = p.ProductID
  INNER JOIN Sales.SalesOrderDetail AS sod ON sop.SpecialOfferID = sod.SalesOrderDetailID
  GROUP BY name
  ) as by_pos
WHERE pos <= 3
```

3. Escreva uma query ligando as tabelas Person.Person, Sales.Customer e Sales.SalesOrderHeader de forma a obter uma lista de nomes de clientes e uma contagem de pedidos efetuados.

```
SELECT c.CustomerID as id, 
       CONCAT(p.FirstName, ' ', p.LastName) as name, 
       COUNT(*) AS qtd 
FROM Sales.SalesOrderHeader as soh
INNER JOIN	Sales.Customer as c ON soh.CustomerID = c.CustomerID
INNER JOIN Person.Person as p ON c.PersonID = p.BusinessEntityID 
GROUP BY c.PersonID
ORDER BY qtd DESC
```

4. Escreva uma query usando as tabelas Sales.SalesOrderHeader, Sales.SalesOrderDetail e Production.Product, de forma a obter a soma total de produtos (OrderQty) por ProductID e OrderDate.

```
SELECT sod.ProductID as id, 
       p.Name as name,
       sum(OrderQty) OVER(PARTITION BY sod.ProductID) AS qtd_id,
       soh.OrderDate,  
       sum(OrderQty) OVER(PARTITION BY soh.OrderDate) AS qtd_OrderDate
FROM Sales.SalesOrderDetail AS sod
INNER JOIN Sales.SalesOrderHeader as soh ON sod.SalesOrderID  = soh.SalesOrderID 
INNER JOIN Production.Products AS p ON sod.ProductID = p.ProductID 
GROUP BY sod.ProductID, soh.OrderDate
ORDER BY soh.OrderDate
```

5. Escreva uma query mostrando os campos SalesOrderID, OrderDate e TotalDue da tabela Sales.SalesOrderHeader. Obtenha apenas as linhas onde a ordem tenha sido feita durante o mês de setembro/2011 e o total devido esteja acima de 1.000. Ordene pelo total devido decrescente.

```
SELECT SalesOrderID, DATE(OrderDate), TotalDue 
FROM SalesOrderHeader AS soh 
WHERE DATE(OrderDate) BETWEEN DATE('2011-09-01') AND DATE('2011-09-30') AND TotalDue > 1.000
ORDER BY TotalDue DESC
Por favor, leia o COLABORACAO.md para obter detalhes sobre o nosso código de conduta e o processo para nos enviar pedidos de solicitação.

✒️ Autores
Rox

Um desenvolvedor - Trabalho Inicial - umdesenvolvedor
Fattima Viana


🎁 Expressões de gratidão
Agradeço a Rox.
