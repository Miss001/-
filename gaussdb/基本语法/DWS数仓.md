## 创建外表
```
CREATE FOREIGN TABLE product_info_ext
(
product_price integer not null,
product_id char(30) not null,
product_time date,
product_level char(10),
product_name varchar(200),
product_type1 varchar(20),
product_type2 char(10),
product_monthly_sales_cnt integer,
product_comment_time date,
product_comment_num integer,
product_comment_content varchar(200) 
) 
SERVER gsmpp_server 
OPTIONS(
LOCATION 'obs://dws-obs04/input_data/',
FORMAT 'CSV' ,
DELIMITER ',',
ENCODING 'utf8',
HEADER 'false',
ACCESS_KEY '8TKDLGRBY4JASQUAVV1L',
SECRET_ACCESS_KEY 'hIGsavExi1zR89gmb8xMYJfZeQsWAWkUh9D3PzgN',
FILL_MISSING_FIELDS 'true',
IGNORE_EXTRA_DATA 'true'
)
READ ONLY 
LOG INTO product_info_err 
PER NODE REJECT LIMIT 'unlimited';
```
## 创建普通表
```
CREATE TABLE product_info
(
product_price integer not null,
product_id char(30) not null,
product_time date ,
product_level char(10) ,
product_name varchar(200) ,
product_type1 varchar(20) ,
product_type2 char(10) ,
product_monthly_sales_cnt integer ,
product_comment_time date ,
product_comment_num integer ,
product_comment_content varchar(200) 
) 
WITH (
orientation = column,
compression=middle
) 
DISTRIBUTE BY hash (product_id);
```
