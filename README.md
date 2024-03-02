# **Kimia Farma Business Performance 2020 - 2023 Dashboard Analysis**
Tools :
💻 Google BigQuery
📝 SQL Query
📁 GitHub
📊 Google Looker Studio 

## **Project Description**
Project-Based Internship is one of Rakamin Academy's programs that helps people increase their skills, portfolio, and work experience by joining a virtual internship with a wide variety choice of well-known companies. From this program, I joined the Big Data Analytics internship at Kimia Farma. Kimia Farma is the first pharmaceutical producer and distributor in Indonesia that was founded by the Dutch East Indies Government in 1817. As a Big Data Analytics Intern, my final task is to evaluate Kimia Farma business performance from 2020 until 2023 by making an analysis dashboard for a more deeper understanding of data and it's analysis.

## **01: Data Preparation with Google BigQuery**
Here are the steps that I've done to prepare the data and make data mart before designing the dashboard and making the analysis:
1. Download the csv datasets that were given as an analysis materials.
   **kf_final_transaction.csv**
- transaction_id: kode id transaksi
- product_id : kode produk obat
- branch_id: kode id cabang Kimia Farma
- customer_name: nama customer yang melakukan transaksi
- date: tanggal transaksi dilakukan
- price: harga obat
- discount_percentage: Persentase diskon yang diberikan pada obat
- rating: penilaian konsumen terhadap transaksi yang dilakukan

**kf_product.csv**
- product_id: kode produk obat
- product_name: nama produk obat
- product_category: kategori produk obat
- price: harga obat

**kf_inventory.csv**
- inventory_ID: kode inventory produk obat
- branch_id: kode id cabang Kimia Farma
- product_id: kode id produk obat
- product_name: nama produk obat
- opname_stock: jumlah stok produk obat

**kf_kantor_cabang.csv**
- branch_id: kode id cabang Kimia Farma
- branch_category: kategori cabang Kimia Farma
- branch_name: nama kantor cabang Kimia Farma
- kota: kota cabang Kimia Farma
- provinsi: provinsi cabang Kimia Farma
- rating: penilaian konsumen terhadap cabang Kimia Farma
  
2. Open Google BigQuery and make a new project called "Rakamin-KF-Analytics"
3. Create new datasets called "kimia_farma" and import the csv datasets that were given into it.
4. Create the data marts by combining the necessary information from the csv datasets.

<details>

<summary>SQL Query</summary>

```sql
-- create base table --
CREATE TABLE Kimia_Farma.kf_analisa AS
SELECT 
  ft.transaction_id, 
  ft.rating AS rating_transaksi,
  ft.date,
  ft.customer_name,
  pd.product_id,
  pd.product_name,
  pd.product_category,
  pd.price AS actual_price,
  ft.discount_percentage,
  pd.price - (pd.price * ft.discount_percentage) AS nett_sales,
  (CASE 
  WHEN pd.price <= 50000 THEN 0.10 
  WHEN pd.price BETWEEN 50000 AND 100000 THEN 0.15
  WHEN pd.price BETWEEN 100000 AND 300000 THEN 0.20  
  WHEN pd.price BETWEEN 300000 AND 500000 THEN 0.25 
  ELSE 0.30 
  END) AS persentase_gross_laba,
  ft.branch_id,
  kc.branch_category,
  kc.branch_name, 
  kc.kota, 
  kc.provinsi,
  kc.rating AS rating_cabang
FROM Kimia_Farma.kf_final_transaction AS ft
JOIN Kimia_Farma.kf_kantor_cabang AS kc
  on ft.branch_id = kc.branch_id 
JOIN Kimia_Farma.kf_product AS pd
  on pd.product_id = ft.product_id ;

-- Create Aggregate Table 1 : Sales --
CREATE TABLE Kimia_Farma.kf_sales AS
SELECT 
  transaction_id,
  date,
  rating_transaksi,
  product_id,
  product_name,
  product_category,
  nett_sales,
  nett_sales - (nett_sales * persentase_gross_laba) AS nett_profit,
  branch_id,
  branch_category,
  kota,
  provinsi,
  rating_cabang
  FROM Kimia_Farma.kf_analisa ;

-- Create Aggregate Table 2 : Branch Performance --
CREATE TABLE Kimia_Farma.kf_branch AS
SELECT 
  branch_id,
  branch_category,
  kota,
  provinsi,
  date,
  SUM(nett_sales) OVER (ORDER BY branch_id) AS total_nett_sales,
  SUM(nett_profit) OVER (ORDER BY branch_id) AS total_nett_profit
  FROM Kimia_Farma.kf_sales ;

-- Create Aggregate Table 3 : Rating --
CREATE TABLE Kimia_Farma.kf_rating AS
SELECT 
  branch_id,
  branch_category,
  rating_cabang,
  ROUND(avg(rating_transaksi)) AS avg_rating_transaksi
FROM Kimia_Farma.kf_sales
  GROUP BY branch_id, branch_category, rating_cabang
  ORDER BY avg_rating_transaksi ASC ;
 ```

</details>

## **02: Dashboard Analysis Design**
After the data preparation, I designed the dashboard for business performance analysis by using Google Looker Studio. Here is the link for the interactive dashboard :

