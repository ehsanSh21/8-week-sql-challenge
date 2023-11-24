# 8-week-sql-challenge
Case Study #1 - Danny's Diner

### What is the 8 Week SQL Challenge?
   The 8-week SQL challenge is a program that helps improve SQL skills through real-world problem-solving. It offers weekly challenges to enhance query writing abilities and provides a supportive community. It's an ideal opportunity to strengthen SQL skills in a structured and engaging manner.

   You can read more abou it here: https://8weeksqlchallenge.com/getting-started/

### Project Overview:
Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen.

Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers. 

More information about this case study: https://8weeksqlchallenge.com/case-study-1/


### Entity Relationship Diagram:

![alt text](https://github.com/ehsanSh21/8-week-sql-challenge/blob/main/Danny's%20Diner.svg)

### Case Study Questions

1. What is the total amount each customer spent at the restaurant?

```sql
SELECT
      dannys_diner.sales.customer_id,sum(dannys_diner.menu.price) as total_amount
FROM dannys_diner.sales
join dannys_diner.menu
on dannys_diner.sales.product_id=dannys_diner.menu.product_id
group by dannys_diner.sales.customer_id;
```

2. How many days has each customer visited the restaurant?

```sql
SELECT
      dannys_diner.sales.customer_id,count(distinct dannys_diner.sales.order_date) as customer_total_days
FROM dannys_diner.sales
group by dannys_diner.sales.customer_id;
```

3. What was the first item from the menu purchased by each customer?

```sql
select
customer_id,
STRING_AGG(distinct sub.product_name, ', ') AS aggregated_product_names
from
(SELECT
      customer_id,order_date,product_name,
    RANK () OVER (
      partition by customer_id
        ORDER BY order_date asc
    ) rank
FROM dannys_diner.sales
inner join dannys_diner.menu
on dannys_diner.sales.product_id=dannys_diner.menu.product_id
order by dannys_diner.sales.customer_id,dannys_diner.sales.order_date) as sub
where sub.rank=1
group by customer_id;
```

4. What is the most purchased item on the menu and how many times was it purchased by all customers?

```sql
SELECT
      product_name,count(*) as purchased_times
FROM dannys_diner.sales
inner join dannys_diner.menu
on dannys_diner.sales.product_id=dannys_diner.menu.product_id
group by dannys_diner.sales.product_id,dannys_diner.menu.product_name
order by purchased_times desc
limit 1;
```

5. Which item was the most popular for each customer?

```sql
select
sub2.customer_id,
STRING_AGG(sub2.product_name, ', ') AS popular_item
from
(select
sub.*,
    RANK () OVER (
      partition by sub.customer_id
        ORDER BY sub.count desc
    ) as rank
from
(SELECT
  customer_id,dannys_diner.menu.product_name,count(*)
FROM dannys_diner.sales
inner join dannys_diner.menu
on dannys_diner.sales.product_id=dannys_diner.menu.product_id
group by customer_id,dannys_diner.sales.product_id,dannys_diner.menu.product_name
order by customer_id,dannys_diner.sales.product_id) as sub
) as sub2
where sub2.rank=1
group by sub2.customer_id;
```

6. Which item was purchased first by the customer after they became a member?

```sql
select
sub.customer_id,
sub.order_date,
dannys_diner.menu.product_name
from
(SELECT
  dannys_diner.sales.customer_id,
  dannys_diner.sales.order_date,
  dannys_diner.sales.product_id,
      RANK () OVER (
      partition by dannys_diner.sales.customer_id
        ORDER BY dannys_diner.sales.order_date asc
    ) as rank
FROM dannys_diner.sales
inner join dannys_diner.members
on dannys_diner.sales.customer_id=dannys_diner.members.customer_id
and dannys_diner.sales.order_date>=dannys_diner.members.join_date
order by dannys_diner.sales.customer_id,dannys_diner.sales.order_date asc
) as sub
inner join dannys_diner.menu
on sub.product_id=dannys_diner.menu.product_id
where sub.rank=1;
```

7. Which item was purchased just before the customer became a member?

```sql
select
sub.customer_id,
sub.order_date,
-- dannys_diner.menu.product_name,
STRING_AGG(distinct dannys_diner.menu.product_name, ', ') AS aggregated_product_names
from
(SELECT
  dannys_diner.sales.customer_id,
  dannys_diner.sales.order_date,
  dannys_diner.sales.product_id
  ,
    RANK () OVER (
    partition by dannys_diner.sales.customer_id
        ORDER BY dannys_diner.sales.order_date desc
    ) as rank
FROM dannys_diner.sales
inner join dannys_diner.members
on dannys_diner.sales.customer_id=dannys_diner.members.customer_id
and dannys_diner.sales.order_date<dannys_diner.members.join_date
order by dannys_diner.sales.customer_id,dannys_diner.sales.order_date desc
) as sub
inner join dannys_diner.menu
on sub.product_id=dannys_diner.menu.product_id
where sub.rank=1
group by sub.customer_id,sub.order_date
;
```
Result:

| customer_id | order_date               | aggregated_product_names |
| ----------- | ------------------------ | ------------------------ |
| A           | 2021-01-01T00:00:00.000Z | curry, sushi             |
| B           | 2021-01-04T00:00:00.000Z | sushi                    |


8. What is the total items and amount spent for each member before they became a member?

```sql
SELECT
dannys_diner.sales.customer_id,
count(dannys_diner.menu.product_id) as total_items,
sum(dannys_diner.menu.price) as total_price
FROM dannys_diner.sales
inner join dannys_diner.members
on dannys_diner.sales.customer_id=dannys_diner.members.customer_id
and dannys_diner.sales.order_date<dannys_diner.members.join_date
inner join dannys_diner.menu
on dannys_diner.sales.product_id=dannys_diner.menu.product_id
group by dannys_diner.sales.customer_id
;
```













