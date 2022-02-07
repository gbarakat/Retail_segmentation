# Retail_segmentation
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
    The used datasets consists of two tables; (1) Customers, And (2) Transcations. <img width="675" alt="image" src="https://user-images.githubusercontent.com/49054741/152719872-31924a87-847b-4ace-8143-73d6957784f6.png">

### PHASE-1: Multi-channel Segmentation!
#### Step 1 - Build a single view! t a customer level - every row will be unique for each customer.  First convert the txn into customer level. Then join the new txn table with the customer table (becuase now both are at the same customer level i.e. unique rows for every cust)
 ```sql
 Select * from cust
 ```
