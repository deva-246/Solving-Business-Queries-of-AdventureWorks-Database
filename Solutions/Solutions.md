

1. Show the first name and the email address of customer with CompanyName 'Bike World' 

	SELECT 
	
	d.Name,b.FirstName,c.EmailAddress
	
	FROM sales.customer a
	
	join person.person b on a.PersonID = b.BusinessEntityID
	
	JOIN [Person].[EmailAddress] c ON b.BusinessEntityID = c.BusinessEntityID
	
	JOIN [Sales].[Store] d ON c.EmailAddressID = d.BusinessEntityID 
	
	where d.Name = 'Bike World'
 

2. Show the CompanyName for all customers with an address in City 'Dallas'. 

SELECT 
    
    s.Name, p.FirstName, p.LastName, a.City, a.AddressLine1

FROM 
    
    [Sales].[Customer] c

JOIN
 
 [Sales].[Store] s ON c.StoreID = s.BusinessEntityID

JOIN 
     
    [Person].[Person] p ON c.CustomerID = p.BusinessEntityID

JOIN 
    
    [Person].[Address] a ON a.AddressID = a.AddressID

WHERE 
   
    a.City = 'Dallas';

 

3. How many items with ListPrice more than $1000 have been sold? 

SELECT
    
    COUNT(*) AS 'Number Of Items Sold with List Price > $1000'

FROM
    
    Sales.SalesOrderDetail a

JOIN
    
    Production.Product b ON a.ProductID = b.ProductID

WHERE b.ListPrice > 1000;

 

4. Give the CompanyName of those customers with orders over $100000. Include the subtotal plus tax plus freight. 

SELECT
 
  c.Name, a.SubTotal + a.TaxAmt + a.Freight as Total
FROM
  sales.SalesOrderHeader a

JOIN
    
    sales.Customer b
    
    ON a.CustomerID = b.CustomerID

JOIN
     
     sales.store c
	 
  on b.StoreID = c.BusinessEntityID

WHERE a.SubTotal + a.TaxAmt + a.Freight> 100000;
 

5. Find the number of left racing socks ('Racing Socks, L') ordered by CompanyName 'Riding Cycles' 
SELECT a.Name,sum(b.OrderQty) as 'Ordered Quantity'
FROM Production.Product a JOIN Sales.SalesOrderDetail b ON a.ProductID = b.ProductID
  JOIN Sales.SalesOrderHeader ON b.SalesOrderID = SalesOrderHeader.SalesOrderID
  JOIN Sales.Customer c ON SalesOrderHeader.CustomerID = c.CustomerID
  JOIN sales.Store d ON d.BusinessEntityID = c.StoreID
WHERE a.Name = 'Racing Socks, L' AND d.Name = 'Riding Cycles'
group by a.Name
 

6. "Single Item Order" is a customer order where only one item is ordered. - Show the SalesOrderID and the UnitPrice for every Single Item Order. 
SELECT a.SalesOrderID,a.UnitPrice
FROM Sales.SalesOrderDetail a 
JOIN Sales.SalesOrderHeader b  ON a.SalesOrderID = b.SalesOrderID
GROUP BY  a.SalesOrderID,a.UnitPrice
HAVING SUM(a.OrderQty) = 1;
 

7. List the product name and the CompanyName for all Customers who ordered ProductModel 'Racing Socks'. 
 SELECT x.Name as 'Product Name', d.Name as 'Company Name'
FROM 
Production.Product x
  JOIN Sales.SalesOrderDetail a ON x.ProductID = a.ProductID
  JOIN Sales.SalesOrderHeader b ON a.SalesOrderID = b.SalesOrderID
  JOIN Sales.Customer c ON b.CustomerID = c.CustomerID
  JOIN Sales.store d ON d.BusinessEntityID = c.StoreID
WHERE x.ProductModelID = 
  (SELECT ProductModelID
  FROM Production.ProductModel
  WHERE Name = 'Racing Socks')

 

8. Show the product description for culture 'fr' for product with ProductID 736. · 
SELECT c.Description,CultureID,ProductID
FROM Production.Product  a
JOIN Production.ProductModelProductDescriptionCulture  b ON a.ProductModelID = b.ProductModelID
JOIN Production.ProductDescription  c ON b.ProductDescriptionID = c.ProductDescriptionID
WHERE a.ProductID = 736 AND b.CultureID = 'fr';
 

9. Use the SubTotal value in SaleOrderHeader to list orders from the largest to the smallest. 
select SubTotal as 'SubTotal values' from [Sales].[SalesOrderHeader] 
order by SubTotal DESC;

 
10. For each order show the CompanyName and the SubTotal and the total weight of theorder.
SELECT
    y.Name AS 'Company Name',
    a.SubTotal,
    SUM(p.Weight * o.OrderQty) AS 'TotalWeight of the order'
FROM

    [Sales].[SalesOrderHeader] a
join
sales.Customer x on a.CustomerID = x.CustomerID
join
sales.store y on x.StoreID = y.BusinessEntityID
JOIN
    [Sales].[SalesOrderDetail] o ON a.SalesOrderID = o.SalesOrderID
JOIN

    [Production].[Product] p ON o.ProductID = p.ProductID
GROUP BY
    y.Name, a.SubTotal, a.SalesOrderID
ORDER BY a.SubTotal DESC;

 

11. How many products in ProductCategory 'Cranksets' have been sold to an address in 'London'? 
SELECT
    COUNT(*) AS NumberOfProductsSold
FROM
    [Sales].[SalesOrderDetail] sod
    JOIN [Sales].[SalesOrderHeader] soh ON sod.SalesOrderID = soh.SalesOrderID
    JOIN [Person].[Address] a ON soh.BillToAddressID = a.AddressID
    JOIN [Person].[StateProvince] sp ON a.StateProvinceID = sp.StateProvinceID
    JOIN [Person].[CountryRegion] cr ON sp.CountryRegionCode = cr.CountryRegionCode
    JOIN [Production].[Product] p ON sod.ProductID = p.ProductID
    JOIN [Production].[ProductSubCategory] pc ON p.ProductSubcategoryID = pc.ProductSubcategoryID
WHERE
    pc.Name = 'Cranksets'
    AND a.City = 'London';

 

12. For every customer with a 'Main Office' in Dallas show AddressLine1 of the 'Main Office' and AddressLine1 of the 'Shipping' address. if there is no shipping address leave it blank. Use one row per customer.
SELECT
  x.FirstName,
  MAX(CASE WHEN e.Name= 'Main Office' THEN f.AddressLine1 ELSE '' END) AS 'Main Office Address',
  MAX(CASE WHEN e.Name = 'Shipping' THEN f.AddressLine1 ELSE '' END) AS 'Shipping Address'
from 
sales.customer a 
join
sales.SalesOrderHeader  b on a.CustomerID = b.CustomerID
join
person.Address c on b.BillToAddressID = c.AddressID
join
person.BusinessEntityAddress d on c.AddressID = d.AddressID
join
person.AddressType e on d.AddressTypeID = e.AddressTypeID
join
person.Address f on f.AddressID = b.ShipToAddressID
join
person.Person x on a.PersonID = x.BusinessEntityID
group by x.FirstName;

 

13. For each order show the SalesOrderID and SubTotal calculated three ways: 
A) From the SalesOrderHeader B) Sum of OrderQty*UnitPrice C) Sum of OrderQty*ListPrice 
SELECT b.SalesOrderID, a.SubTotal,
  SUM(b.OrderQty * b.UnitPrice) as 'B',
  SUM(b.OrderQty * c.ListPrice) as 'C'
FROM sales.SalesOrderHeader a JOIN sales.SalesOrderDetail b ON a.SalesOrderID = b.SalesOrderID
  JOIN production.Product c ON b.ProductID = c.ProductID
GROUP BY b.SalesOrderID, a.SubTotal
 ·
14. Show how many orders are in the following ranges (in $): RANGE Num Orders Total Value 99 100- 999 1000-9999 10000.
WITH details AS (
  SELECT a.salesorderid, SUM(a.orderqty * a.unitprice) order_total
  FROM sales.SalesOrderDetail a
  GROUP BY salesorderid
), rangedetails AS (
  SELECT salesorderid, order_total, CASE
    WHEN order_total BETWEEN 0 AND 99 THEN '0-99'
    WHEN order_total BETWEEN 100 AND 999 THEN '100-999'
    WHEN order_total BETWEEN 1000 AND 9999 THEN '1000-9999'
    WHEN order_total >= 10000 THEN '10000-'
    ELSE 'Unidentifiable'
    END AS rng
  FROM details
)
SELECT rng 'RANGE', COUNT(rng) 'Count of Orders', SUM(order_total) 'Total Expense'
FROM rangedetails
GROUP BY rng;
 
15. Identify the three most important cities. Show the breakdown of top level product category against city.
select sum(a.OrderQty) as 'Count of products',b.Name,f.city
from [Sales].[SalesOrderDetail] a
join [Production].[Product] b on a.ProductID =b.ProductID
join [Production].[ProductSubCategory] c on b.ProductSubcategoryID = c.ProductSubcategoryID
join [Production].[ProductSubCategory] d on d.ProductCategoryID = c.ProductCategoryID
join [Sales].[SalesOrderHeader] e on a.SalesOrderID = e.SalesOrderID
join Person.Address f on e.ShipToAddressID = f.AddressID
group by f.city, a.OrderQty, b.Name
order by a.OrderQty DESC
 

