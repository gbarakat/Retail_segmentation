# Retail segmentation
Customer segmentation project using SQL.


## Background

Retailer Company is focussed on understanding more about its Multi-channel shopper. Retailer company has a huge Online and Brick and Mortar stores presence all across the globe. The company is keen, more than ever, to understand the value of shoppers who shop across channels.

The purpose of the project is to give a quantitative understanding of some of the key behavioural differences we see with shoppers On Line and In-Store. This will provide more understanding of the barriers and opportunities within Online


## Objectives
1.	Who are the shoppers using online?
2.	What are the differences in behaviour of online (1), in-store (0) & multi-channel shoppers?
3.	The value of the different shopper groups and we start to understand the value opportunity of focusing on the different shopper groups


## Project Implementation
### Deep Look into the used data set:
The used datasets consists of two tables; (1) Customers, And (2) Transcations. 
   <img width="675" alt="image" src="https://user-images.githubusercontent.com/49054741/152719909-79e82f0c-7215-4d7e-a499-e81c15699dd3.png">


### PHASE-1: Multi-channel Segmentation!
#### Step 1 - Build a single view! at a customer level - every row will be unique for each customer.  First convert the txn into customer level. Then join the new txn table with the customer table (becuase now both are at the same customer level i.e. unique rows for every cust)
 ```sql
 Create Table txn_online as
Select * from txn where Home_Shopping_Flg = 1;


Create Table txn_instore as
Select * from txn where Home_Shopping_Flg = 0;

Create table SV as
Select c.*, coalesce(i.visits,0) as instore_visits,
coalesce(i.Home_Shopping_Flg,0) as instore_Home_Shopping_Flg,
coalesce(i.tot_spend,0) as instore_Spends,
coalesce(o.visits,0) as online_visits,
coalesce(o.Home_Shopping_Flg,0) as online_Home_Shopping_Flg,
coalesce(o.tot_spend,0) as online_Spends
from Cust c
Left Join txn_instore i
on i.household_id = c.household_id
Left Join txn_online o
on o.household_id = c.household_id;

 ```
 <img width="1063" alt="image" src="https://user-images.githubusercontent.com/49054741/152720291-d05073e2-4126-4b27-861c-669616e8b74a.png">

 #### Step 2 - Multichannel Segementation
 
 ##### Segmentation definition
 Classifying the customers according to there favorite shopping channel into 4 channels: Multichannel, Istore only, Online only and No shopping. This classification is built based on the total number of online and instores visits 
 ```sql
Create Table SV2 as
Select *,
(Case when instore_visits > 0 AND online_visits > 0 Then 'Multichannel'
	  when instore_visits > 0 AND online_visits = 0 Then 'InstoreOnly'
      when instore_visits = 0 AND online_visits > 0 Then 'OnlineOnly'
      when instore_visits = 0 AND online_visits = 0 Then 'NoShopping'
      END) as channel_seg
 from SV;
```

##### Segmentation profiling
After classifying the customers into well defined segments, it would be useful to do some sort of exploration based on the peferred purchase format and loyality degree. 
```sql
Select channel_seg,
 round(avg(instore_visits)) as avg_instore_visits,
 round(AVG(instore_Spends)) as avg_instore_Spends,
 round(avg(online_visits)) as avg_online_visits,
 round(AVG(online_Spends)) as avg_online_Spends
from SV2
Group by channel_seg;
```

<img width="667" alt="image" src="https://user-images.githubusercontent.com/49054741/152721262-f27e2b78-506b-4a22-8de8-5a08a20102f6.png">

**Key Insight 1** Multi Channel customers are the highest in both average online and instore spending.

**Recommendation 1:** Company Should build strategy to help customers migrate to multi shopping.

```sql
Select channel_seg,count(1) as Count_shoppers,
concat(cast(round(100*Sum((case when loyalty = 'Very Frequent Shoppers' then 1 else 0 end))/count(1))as char), "%") as Sum_VeryFrequentShoppes,
concat(cast(round(100*Sum((case when loyalty = 'Occasional Shoppers' then 1 else 0 end))/count(1))as char), "%") as Sum_OccasionalShoppes,
concat(cast(round(100*Sum((case when loyalty = 'Lapsing Shoppers' then 1 else 0 end))/count(1))as char), "%") as Sum_LapsingShoppes,
concat(cast(round(100*Sum((case when loyalty = 'No longer Shopping' then 1 else 0 end))/count(1))as char), "%") as Sum_NoLongerShoppes
from sv2
group by channel_seg;
```
<img width="1070" alt="image" src="https://user-images.githubusercontent.com/49054741/164071622-5359607d-d5d3-40bf-abed-613eeac2c89b.png">

**key Insights 2** nearly 80% of the Multi channel Shoppers are very Frequent Shoppers, for instore only shoppers almost 40% (2000+ Customers) are Occasional Shoppers.

**Recommendation 2:** The Company should build strategy to migrate Instore Occasional Shoppers to Online, That way even if we are able to migrate 10% of them to Very Frequent Shoppers We will get additional 200 Customers in multichannel. There by growing by 50%.

```sql
select distinct lifestyle from sv2;
select channel_seg, count(1) as Count_Shoppers,
					concat(cast(round(100*sum(case when lifestyle = 'Very Affluent Customers' then 1 else 0 end)/count(1)) as char), "%") as Count_VeryAffluent,
					concat(cast(round(100*sum(case when lifestyle = 'Middle Class' then 1 else 0 end)/count(1)) as char), "%") as Count_MiddleClass,
                    concat(cast(round(100*sum(case when lifestyle = 'Low Affluent Customers' then 1 else 0 end)/count(1)) as char), "%") as Count_LowAffluent
from sv2 group by channel_seg;
```
<img width="1070" alt="image" src="https://user-images.githubusercontent.com/49054741/164071862-b36f4e7c-ac02-4602-a381-61dc508c0fa7.png">

*** Key Insight 4 - Only one-fourth of Multichannel shoppers are Low Affluent. Where as instore we have more then one third. 
*** Recommendation 4 - So when we are thinking of migrating instore shoppers to multichannel it might be prudent to start with affluent shoppers.
