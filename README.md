# **Kimia Farma Business Performance Analysis Dashboard**
Tools :
|📁 GitHub
|📝 SQL Query
|💻 Google BigQuery
|📊 Google Looker Studio 

## **Project Description**
Project-Based Internship is one of Rakamin Academy's programs that supports self-development by elevating one's skills, portfolio, and work experience by providing a virtual internship at well-known companies. From this program, I had the opportunity to be a Big Data Analytics intern at Kimia Farma. Kimia Farma is Indonesia's first pharmaceutical producer and distributor, founded by the Dutch East Indies Government in 1817. 

As a Big Data Analytics Intern, I evaluated Kimia Farma's business performance from 2020 until 2023 using an analysis dashboard. The analysis dashboard was designed using Google Looker Studio and based on the data mart created by processing the given datasets using Google Big Query.


## **01: Data Preparation with Google BigQuery**
These are the data preparation stages that I completed to create the data mart before designing the analysis dashboard. 

1. Download the CSV datasets that were given as analysis material.
**kf_final_transaction.csv**
  - transaction_id: Transaction ID Code
  - product_id: Product ID Code
  - branch_id: Kimia Farma's Branch ID Code
  - customer_name: Customer Name
  - date: Transaction Date
  - price: Product Price
  - discount_percentage: Product's Discount Percentage
  - rating: Rating of the customer's transaction

**kf_product.csv**
  - product_id: Product ID Code
  - product_name: Product Name
  - product_category: Product Category
  - price: Product Price

**kf_inventory.csv**
  - inventory_ID: Product Inventory ID Code
  - branch_id: Kimia Farma's Branch ID Code
  - product_id: Product ID Code
  - product_name: Product Name
  - opname_stock: Product Stock Quantity

**kf_kantor_cabang.csv**
  - branch_id: Kimia Farma's Branch ID Code
  - branch_category: Kimia Farma's Branch Category
  - branch_name: Kimia Farma's Branch Name
  - kota: Kimia Farma's Branch City
  - provinsi: Kimia Farma's Branch Province
  - rating: The rating of Kimia Farma's Branch
  
2. Open Google BigQuery and make a new project called "Rakamin-KF-Analytics"
3. Create a new dataset called "kimia_farma" and import the CSV datasets that were given into it.
4. Create the data mart by combining the necessary information from the CSV datasets.

<details>

<summary>SQL Query</summary>

```sql
CREATE TABLE Data_Kimia_Farma.final_task_datasets AS
SELECT 
  x.transaction_id, 
  x.date, 
  x.branch_id, 
  x.branch_name, 
  x.kota, 
  x.provinsi, 
  x.rating_cabang, 
  x.customer_name, 
  x.product_id, 
  x.product_name, 
  x.actual_price, 
  x.discount_percentage,
  x.gross_profit_percentage, 
  x.nett_sales, 
  (x.actual_price * x.gross_profit_percentage) - (x.actual_price - x.nett_sales) nett_profit,
  x.rating_transaksi
FROM (
SELECT 
  a.transaction_id,
  a.date, 
  a.branch_id,
  b.branch_name, 
  b.kota, 
  b.provinsi, 
  b.rating rating_cabang, 
  a.customer_name, 
  a.product_id, 
  c.product_name, 
  c.price actual_price, 
  a.discount_percentage, 
  case 
  when c.price <= 50000 then 0.10
  when c.price > 100000 and c.price <= 300000 then 0.20
  when c.price > 300000 and c.price <= 500000 then 0.25
  when c.price > 500000 then 0.30 end gross_profit_percentage,
  (c.price-(c.price*a.discount_percentage)) nett_sales,
  a.rating rating_transaksi
FROM `Data_Kimia_Farma.kf_final_transaction` a
LEFT JOIN `Data_Kimia_Farma.kf_kantor_cabang` b
  ON a.branch_id = b.branch_id
LEFT JOIN `Data_Kimia_Farma.kf_product` c
  ON a.product_id = c.product_id

) x

 ```

</details>

## **02: Dashboard Design**
After the data preparation, I designed the dashboard for business performance analysis using Google Looker Studio. Here is the link for the interactive dashboard : [Interactive Dashboard](https://lookerstudio.google.com/reporting/28231ad9-5f55-41d7-8aaa-e620059fc038).

![image](https://github.com/noviamaharaniii/PBI_Kimia-Farma_Big-Data-Analytics_Final-Task/assets/160397160/41cf095d-477f-4e3f-92fe-7a9fd6e5e518)!


**Based on the analysis that was carried out by using the dashboard, these are the key insights that I've concluded:**
1. The province of Jawa Barat (West Java) significantly outperforms other regions in terms of net sales and transaction volume.
2. Kimia Farma has maintained stable quarterly revenue despite economic fluctuations, showcasing resilience and effective market strategies. Revenue has remained relatively consistent across quarters from 2020 to 2023, with each quarter contributing around Rp4.53B to Rp4.58B. A slight upward trend in revenue over the years suggests steady growth.
3. Overall branch satisfaction ratings range from 4.1 to 4.8, indicating good service quality.
4. There is variability in branch performance, with some branches significantly outperforming others in sales and transactions.

**From those conclusions, here are a few recommendations that can be given:**
1. Expand high-performing models by replicating the successful strategies of top-performing branches like those in Jawa Barat (West Java) across other regions. 
2. Conduct research through Customer Feedback to further investigate lower customer satisfaction scores to understand and address any service gaps.
3. Focus on enhancing the performance of lower-ranking branches through targeted support and resources to conduct branch development.
