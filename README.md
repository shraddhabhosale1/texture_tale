# texture_tale

create database text_tale;

use text_tale;

/* What is the total quantity sold for all products? */

select pd.product_name, sum(s.qty) as sales_count from sales s 
inner join product_details pd on pd.product_id = s.prod_id 
group by product_name order by sales_count desc;


/* What is the total generated revenue for all products? */

select sum(price*qty) as no_discount_revenue from sales;

/* What was the total discount amount for all products */

select sum(price*qty*discount)/100 as total_discount from sales;

/* How many unique transactions were there */

select count(distinct txn_id) as unique_transactions from sales;

/* What are the average unique producs purchased in each transaction */

with cte_trans_discount as
(select txn_id, sum(price*qty*discount)/100 as total_discount from sales 
group by txn_id)
select round(avg(total_discount)) as avg_discount
from cte_trans_discount;

/* What is the average revenue for member transactions and non-member transactions? */

with cte_member_revenue as
(select member, txn_id, sum(price*qty) as revenue
from sales group by member, txn_id)
select member, round(avg(revenue),2) as avg_revenue
from cte_member_revenue
group by member;

/* What are the top 3 products by total revenue before discount? */

select s.prod_id, pd.product_name, sum(s.price*s.qty*s.discount) as revenue 
from sales s inner join product_details pd
on pd.product_id = s.prod_id group by s.prod_id order by revenue desc limit 3;

/* What are the total quantity, revenue and discount for each segment? */

select pd.segment_name, sum(s.qty) as total_qty, sum(s.price*s.qty) as revenue, 
sum(s.price*s.qty*s.discount)/100 as total_discount
from sales s join product_details pd on pd.product_id = s.prod_id group by pd.segment_name;

/* What are the top 5 selling product for each segment? */

select pd.product_id, pd.product_name, pd.segment_id, pd.segment_name, sum(s.qty) as total_qty 
from sales s inner join product_details pd on s.prod_id = pd.product_id 
group by pd.segment_id, pd.segment_name, pd.product_id, pd.product_name
order by total_qty desc limit 5;

/* What are the total quantity, revenue and discount for each category? */

select pd.category_name, sum(s.qty) as total_qty, sum(s.price*s.qty) as total_revenue, sum(s.price*s.qty*s.discount) as total_discount
from sales s inner join product_details pd on pd.product_id = s.prod_id group by pd.category_name;


/* What is the top selling product for each category? */

select pd.category_id, pd.category_name, pd.product_name, sum(s.qty) as total_qty from sales s inner join product_details pd
on pd.product_id = s.prod_id group by pd.category_id, pd.category_name, pd.product_name order by total_qty desc;
