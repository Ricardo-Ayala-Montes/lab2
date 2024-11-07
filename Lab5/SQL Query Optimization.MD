SQL Query Optimization

1. Orders Query: Retrieve orders with many items and calculate the total price.

´´´
SELECT Orders.OrderID, SUM(OrderDetails.Quantity * OrderDetails.UnitPrice) AS TotalPrice
FROM Orders
JOIN OrderDetails ON Orders.OrderID = OrderDetails.OrderID
WHERE OrderDetails.Quantity > 10
GROUP BY Orders.OrderID;

´´´

a. para obtener el join debe existir llave primaria Orders y llave foranea OrderDetails en el campo OrderID
b. Crear indice
    ALTER TABLE Orders ADD INDEX `i_orderID`(OrderID);
c. Podemos utilizar un limit para no traer tantos resultados





2. NoSQL Query Implementation

´´´
SELECT CustomerName FROM Customers WHERE City = 'London' ORDER BY CustomerName;

´´´

a. Se crea indice fulltext para buscar textos
ALTER TABLE Customers ADD FULLTEXT INDEX `i_cityFullText`(City);
b. Se ejecutan estadisticas
UPDATE STATISTICS Customers;
c. Podemos utilizar un limit para no traer tantos resultados