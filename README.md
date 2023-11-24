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


























