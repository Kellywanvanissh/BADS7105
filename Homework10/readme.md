## Homework 10

From SQL syntax as below, we group and flag customer movement into different types - New, Reactive, Repeat, Churn

```SQL
select A.cust_code, a.new_date, 'repeat' as status, count(*) as BUFFER
from 
(SELECT CUST_CODE,PARSE_DATE('%m/%d/%Y',  CAST(concat(Substring(cast(shop_date as String),5,2),'/','01','/',left(cast(SHOP_DATE as String),4)) as String)) as new_date
FROM `enhanced-oxygen-308203.Supermarket_Data.Supermarket`) as A
left join 
(SELECT CUST_CODE, PARSE_DATE('%m/%d/%Y',  CAST(concat(Substring(cast(shop_date as String),5,2),'/','01','/',left(cast(SHOP_DATE as String),4)) as String)) as new_date
FROM `enhanced-oxygen-308203.Supermarket_Data.Supermarket`)as B 
on A.CUST_CODE = B.CUST_CODE AND DATE_ADD(b.new_date, INTERVAL 1 MONTH) = a.new_date
GROUP BY 1 , 2, 3
union all 
select A.cust_code, a.new_date, 'new' as status, 1 as BUFFER
from 
(select cust_code, min(PARSE_DATE('%m/%d/%Y',  CAST(concat(Substring(cast(shop_date as String),5,2),'/','01','/',left(cast(SHOP_DATE as String),4)) as String))) as new_date
FROM `enhanced-oxygen-308203.Supermarket_Data.Supermarket` GROUP BY 1) as A
union all 
select A.cust_code, a.new_date, 'reactivate' as status, count(*) as BUFFER
from 
(SELECT CUST_CODE,PARSE_DATE('%m/%d/%Y',  CAST(concat(Substring(cast(shop_date as String),5,2),'/','01','/',left(cast(SHOP_DATE as String),4)) as String)) as new_date
FROM `enhanced-oxygen-308203.Supermarket_Data.Supermarket`) as A
left join 
(SELECT CUST_CODE, PARSE_DATE('%m/%d/%Y',  CAST(concat(Substring(cast(shop_date as String),5,2),'/','01','/',left(cast(SHOP_DATE as String),4)) as String)) as new_date
FROM `enhanced-oxygen-308203.Supermarket_Data.Supermarket`)as B 
on A.CUST_CODE = B.CUST_CODE AND DATE_ADD(b.new_date, INTERVAL 1 MONTH) != a.new_date AND DATE_ADD(b.new_date, INTERVAL 1 MONTH) < a.new_date 
where b.new_date is not null
GROUP BY 1 , 2, 3
union all 
select A.cust_code, DATE_ADD(a.new_date, INTERVAL 1 MONTH) as new_date, 'churn' as status, 1 as BUFFER
from 
(select cust_code, max(PARSE_DATE('%m/%d/%Y',  CAST(concat(Substring(cast(shop_date as String),5,2),'/','01','/',left(cast(SHOP_DATE as String),4)) as String))) as new_date
FROM `enhanced-oxygen-308203.Supermarket_Data.Supermarket` GROUP BY 1) as A
![image](https://user-images.githubusercontent.com/77537240/121733156-54ac4c00-cb1d-11eb-9823-b43344abb35d.png)

```


then we export the data out as CSV and plot graph to see the movement 

![image](https://user-images.githubusercontent.com/77537240/121732808-dfd91200-cb1c-11eb-9822-cc6bc56206d9.png)

Stack bar chart is difficult to compare - so below is seperated chart for New and Churn

![image](https://user-images.githubusercontent.com/77537240/121732768-d18af600-cb1c-11eb-924a-f6b4ee3a25f3.png)

![image](https://user-images.githubusercontent.com/77537240/121732790-d8196d80-cb1c-11eb-9229-a2201f84b585.png)
