# SQL_Queries

## Revenue Over Time

###Objective:Find the 3-month rolling average of total revenue from purchases given a table with users, their purchase amount, and date purchased.

SELECT X.MONTH, AVG(X.monthly_revenue)
       OVER(ORDER BY X.MONTH ROWS BETWEEN 2 PRECEDING AND CURRENT ROW)
       from
(
SELECT to_char(created_at::date, 'YYYY-MM') AS MONTH,
          sum(purchase_amt) AS monthly_revenue
   FROM amazon_purchases
   WHERE purchase_amt>0
   GROUP BY to_char(created_at::date, 'YYYY-MM')
   ORDER BY to_char(created_at::date, 'YYYY-MM')
) X


Find employees from Arizona, California, and Hawaii while making sure to output all employees from each city. 
Output column headers should be Arizona, California, and Hawaii.

with arizona as(
select first_name as aname, ROW_NUMBER () OVER (ORDER BY first_name) as arn
from employee
where city='Arizona'),

california as (
select first_name as cname, ROW_NUMBER () OVER (ORDER BY first_name) as crn
from employee
where city='California'),

hawaii as (
select first_name as hname, ROW_NUMBER () OVER (ORDER BY first_name) as hrn
from employee
where city='Hawaii')

SELECT aname AS arizona,
       cname AS california,
       hname AS hawaii
from hawaii
full outer join california on hrn=crn
full outer join arizona on crn =arn

Views Per Keyword
Create a report showing how many views each keyword has. Output the keyword and the total views, and order records with highest view count first.

with table_1 as
(SELECT post_id, BTRIM(post_keywords, '[]') key_words FROM facebook_posts),

table_2 as 
(select a.post_id, regexp_split_to_table(key_words, ',') word, viewer_id
from table_1 a
left join facebook_post_views b on a.post_id=b.post_id)

select word, count(viewer_id) from table_2
group by word
order by count(viewer_id) desc


## Premium vs Freemium
Purpose: Find the total number of downloads for paying and non-paying users by date.

with table_1 as(
select date, paying_customer, downloads from ms_user_dimension mud
left join ms_acc_dimension mad on mud.acc_id=mad.acc_id
left join ms_download_facts mdf on mud.user_id=mdf.user_id),

table_2 as(
select date, sum(CASE
    WHEN paying_customer='yes' THEN downloads end) paying,
    sum(CASE
    WHEN paying_customer='no' THEN downloads end) not_paying
    from table_1
    group by date
    order by date)

select date,not_paying,paying  from table_2
where not_paying>paying

