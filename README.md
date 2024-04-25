# Customer-Behaviour-Analysis-MySQL-Workbench-

This project and the data used was part of a case study that focused on examining patterns, trends, and factors influencing customer spending in order to gain insights into their preferences, purchasing habits, and potential areas for improvement in menu offerings or marketing strategies in a dining establishment.

## Table of Contents

- [Project Overview](#project-overview)
- [Data Source](#data-source)
- [Problem Statement](#problem-statement)
- [Tools](#tools)
- [Skills Applied](#skills-applied)
- [Questions Explored](questions-explored)
- [Some interesting queries used](#some-interesting-queries-used)
- [Insights and Recommendations](#insights-and-recommendations)
- [Referreces](#referreces)

# Project Overview

Danny, a diner owner seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a little restaurant that sells his 3 favourite foods: sushi, curry and ramen. Danny’s Diner is in need of assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from their few months of operation but have no idea how to use their data to help them run the business.

# Data Source

The data used for this project and case study can be found [Here](https://8weeksqlchallenge.com/case-study-1/)

# Problem Statement

Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favorite. Having this deeper connection with his customers will help him deliver a better and more personalized experience, especially for his loyal customers.

Danny plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.

Danny has provided a sample of his overall customer data due to privacy issues - but he hopes that these examples are enough to write fully functioning SQL queries to help him answer his questions.

# Tools 

MySQL Workbench was used for this project and analysis.


# Skills Applied

-  Window Functions.
- CTEs.
- Aggregations.
- JOINs.
- Write scripts to generate basic reports that can be used and run every period.

# Questions Explored :

1. What is the total amount each customer spent at the restaurant?
2. How many days has each customer visited the restaurant?
3. What was the first item from the menu purchased by each customer?
4. What is the most purchased item on the menu and how many times was it purchased by all customers?
5. Which item was the most popular for each customer?
6. Which item was purchased first by the customer after they became a member?
7. Which item was purchased just before the customer became a member?
8. What is the total items and amount spent for each member before they became a member?
9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

# Some interesting queries used

- - Which item was purchased just before the customer became a member-- ?
 ```SQL
WITH Last_Purchase_Before_Membership AS (
	SELECT s.customer_id, MAX(s.order_date) AS Last_Purchase_Date
		FROM sales s
		JOIN members mb
		ON s.customer_id = mb.customer_id
		WHERE s.order_date < mb.join_date
		GROUP BY s.customer_id
)
SELECT lpbm.customer_id, m.product_name
FROM Last_Purchase_Before_Membership lpbm
JOIN sales s
ON s.customer_id = lpbm.customer_id
AND lpbm.Last_Purchase_Date = s.order_date
JOIN menu m
ON s.product_id = m.product_id;
```

- - If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have ?
```SQL
SELECT s.customer_id, SUM(
	CASE
		WHEN m.product_name = 'sushi' THEN m.price*20
        ELSE m.price*10 END) AS Total_Points
	
FROM sales s
JOIN menu m
ON s.product_id = m.product_id
GROUP BY s.customer_id;
```

- - Which item was the most popular for each customer ?
```SQL
WITH customer_popularity AS (
	SELECT s.customer_id, m.product_name, 
    COUNT(*) AS Purchase_Count,
		DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY COUNT(*) DESC) AS `RANK`
		FROM sales s
		JOIN menu m 
        ON s.product_id = m.product_id
		GROUP BY s.customer_id, m.product_name
)
SELECT cp.customer_id, cp.product_name, cp.Purchase_Count
FROM customer_popularity cp
WHERE cp.RANK = 1;
```

- - What is the most purchased item on the menu and how many times was it purchased by all customers ?
```SQL
SELECT m.product_name, COUNT(*) AS `Total Purchased`
FROM sales s
JOIN menu m 
ON s.product_id = m.product_id
GROUP BY m.product_name
ORDER BY `Total Purchased` DESC
LIMIT 2;
```

# Insights and Recommendations

- Customer B is the most frequent visitor with 6 visits in Jan 2021.
- Danny’s Diner’s most popular item is ramen, followed by curry and sushi.
- Customer A loves ramen, Customer C loves only ramen whereas Customer B seems to enjoy sushi, curry and ramen equally.
- The last item ordered by Customers A and B before they became members are sushi and curry - Does it mean both of these items are the deciding factors ? 
  
It would be advised that Danny's Diner focuses more attention on Ramen as it is the most popular item sought for, also, it can be observed that Customer A and B loves Ramen, whilst Customer C is undecided on a favourite between the items sold at the Diner.

# Referreces
[SQL Tutorial for Business Analysts](https://www.coursera.org/learn/sql-for-data-science)
