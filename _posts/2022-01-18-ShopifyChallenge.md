---
published: true
---
* TOC
{:toc}
# Question 1 

**Problem Statement:** On Shopify, we have exactly 100 sneaker shops, and each of these shops sells only one model of shoe. We want to do some analysis of the average order value (AOV). When we look at orders data over a 30 day window, we naively calculate an AOV of $3145.13. Given that we know these shops are selling sneakers, a relatively affordable item, something seems wrong with our analysis. 

**Data exploration**

We start with basic data exploration.

```python
shopifydf = pd.read_csv("shopifyChal.csv")   # CSV renamed for convenience
shopifydf.head()
```

data-challenge
![1.png]({{site.baseurl}}/images/data-challenge/1.png)


Check for missing values:

```python
nan_rows = shopifydf[shopifydf.isnull().T.any()]
shopifydf.isna().any(axis=None)
```
No missing values were found in this dataset.

## a. Think about what could be going wrong with our calculation. Think about a better way to evaluate this data. 

```python
# examine the summary statistics of order_amount column
shopifydf.order_amount.describe()
```

    Numbers of unique shops: 100

    count      5000.000000
    mean       3145.128000
    std       41282.539349
    min          90.000000
    25%         163.000000
    50%         284.000000
    75%         390.000000
    max      704000.000000
    Name: order_amount, dtype: float64


Given the large standard deviation of $41282.54, it is apparent that the naïvely calculated AOV skewed by outliers. In this case, the outliers come from large orders worth 704000, corresponding from orders of 2000, which are most likely wholesale orders. 

Depending on what we are interested in, a better way to evaluate the dataset could be to look at the **mean product cost**: since one shop sells only one shoe, we take the 
order_amount divided by total_items and then find the mean. 

```python
# examine the summary statistics of order_amount column
shopifydf.order_amount.describe()
```

Alternatively if we are more interested in how much customers spend per order on averge we can look at the **median order value**.

## b. What metric would you report for this dataset?

This really depends on what insights we wish to extract from the dataset. If this is for market research and we want to know how much shoes cost on average, then I would report the **mean product cost**. If we want to know how much customers spend on averge per order, I would report the **median order value** instead of the mean order value as it less prone to be skewed by outliers.

## c. What is its value?

The median order value is **$284.0**.

# Question 2
### a. How many orders were shipped by Speedy Express in total?

```sql
SELECT COUNT(*) FROM Orders 
INNER JOIN Shippers 
ON Orders.ShipperID = Shippers.ShipperID
WHERE ShipperName == "Speedy Express"
```

There were **54** orders shipped by Speedy Express in total.


### b. What is the last name of the employee with the most orders?

```sql
SELECT LastName, COUNT(*) 
FROM Orders 
INNER JOIN Employees ON Orders.EmployeeID = Employees.EmployeeID
GROUP BY Orders.EmployeeID
ORDER BY COUNT(*) DESC
```

The last name of the employee with the most orders is **Peacock**.


### c. What product was ordered most by customers in Germany?

The question is a little ambiguous. If it is asking for the product that was most frequently ordered, it would be **Gorgonzola Telino** (Ordered 5 times).

```sql
SELECT ProductName, COUNT(*) FROM OrderDetails
INNER JOIN Products ON OrderDetails.ProductID = Products.ProductID
INNER JOIN Orders ON OrderDetails.OrderID = Orders.OrderID
INNER JOIN Customers ON Orders.CustomerID = Customers.CustomerID
WHERE Country == "Germany"
GROUP BY ProductName
ORDER BY COUNT(*) DESC
```

If it is asking for the product with the largest quantity purchased by customers in Germany, it would be : **Boston Crab Meat** (160 purchased).

```sql
SELECT ProductName, SUM(Quantity) FROM OrderDetails
INNER JOIN Products ON OrderDetails.ProductID = Products.ProductID
INNER JOIN Orders ON OrderDetails.OrderID = Orders.OrderID
INNER JOIN Customers ON Orders.CustomerID = Customers.CustomerID
WHERE Country == "Germany"
GROUP BY ProductName
ORDER BY SUM(Quantity) DESC
```
