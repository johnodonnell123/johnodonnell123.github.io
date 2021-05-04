# SQL in Jupyter for Data Retrieval and Analysis

**Description:** Here I highlight some useful examples of SQL for general data querying and high level analysis. To run the queries in Jupyter I use ipython-sql, which allows for a more simple and intuitive syntax. 

### Data Context: 
The data we are working with here was scraped from a webpage and stored in a SQLite3 database, which I cover in [another project](https://johnodonnell123.github.io/pages/page_scrapy.html). 
For those unfamiliar with these oil and gas data types, I hope to provide some context. We will be working with 2 tables:

#### Header Table
Contains general information about a well such as the name, the location, and depth. It also contains the UWI (unique well identifier).

<p align='center'>
  <img src="/images/SQL/header_table.PNG?raw=true" height='60%' width='60%'>
</p>

#### Production Table
Wells produce Oil, Water, and Gas over time. This is our time-series data, which is why it is held in a separate table. They typically have higher rates early time and decline throughout their life. This table contains the UWI, the time stamp, the number of days that well actually flowed for that month, and the coinciding volumes for oil/water/gas.

<p align='center'>
  <img src="/images/SQL/prod_table.PNG?raw=true" height='45%' width='45%'>
</p>

## View Tables in DataBase
```python
%%sql 

SELECT name 
FROM sqlite_master 
WHERE type ='table'
```
<img src="/images/SQL/View Tables2.png?raw=true" height='50%' width='50%'>
<br>

## Select First 5 Rows of a table
Showing shape of table (rows, columns) and a sample
```python
%%sql 

SELECT * 
FROM header_table 
LIMIT 5
```
<img src="/images/SQL/Select first 5 rows2.png?raw=true" height='60%' width='60%'>
<br>

## Select Using Conditions
Showing shape of table and a random sample
```python 
%%sql 

SELECT * 
FROM prod_table 
WHERE Days > 20 AND Water < 100
```
<img src="/images/SQL/Multiple Conditions2.png?raw=true" height='50%' width='50%'>
<br>

## Select Specific Wells
Using the logical IN operator
```python
%%sql 

SELECT 
  p.UWI, p.Days, p.Oil 
FROM prod_table 
WHERE p.UWI IN (33061042810000,33061005070000)
```
<img src="/images/SQL/Specific Wells2.png?raw=true" height='30%' width='30%'>
<br>

## Select Columns from 2 Tables
Showing shape of table and a random sample
```python
%%sql 

SELECT 
  p.UWI, p.Days, p.Oil, h.Current_Operator 
FROM prod_table p 
JOIN header_table h 
  USING(UWI)
```
<img src="/images/SQL/Join Select Specific Columns2.png?raw=true" height='60%' width='60%'>
<br>

## What Operators/Companies have Produced the Most Oil to Date?
It appears Continental Resources has produced > 400 million barrels of oil and drilled just over 1700 wells!
```python
%%sql 

SELECT 
  p.UWI, COUNT(DISTINCT p.UWI) AS 'Wells', SUM(p.Oil) AS 'Cumulative_Oil', h.Current_Operator
FROM prod_table p 
JOIN header_table h 
  USING(UWI) 
GROUP BY Current_Operator
ORDER BY Cumulative_Oil DESC
LIMIT 5
```

<img src="/images/SQL/Aggregate Operator Oil and Wells2.png?raw=true" height='45%' width='45%'>
<br>

## Check for duplicate well names
Well names <em>should</em> be unique, however we know for sure UWI's are unique
```python
%%sql 

SELECT 
    p.UWI, h.Current_Operator, h.Well_Name, COUNT(DISTINCT p.UWI) AS 'UWIs'
FROM prod_table p 
JOIN header_table h 
    USING(UWI) 
GROUP BY Well_Name
    HAVING UWIs > 1
```
Surprisingly only one repeat!

<img src="/images/SQL/Check Duplicates.PNG?raw=true" height='45%' width='45%'>
<br>


## What Wells have Produced the Most Oil to Date? Who do they belong to? 
```python
%%sql 

SELECT 
  p.UWI, sum(p.Oil) as 'Cumulative_Oil', h.Well_Name, h.Current_Operator
FROM prod_table p 
JOIN header_table h 
  USING(UWI)  
GROUP BY h.Well_Name 
ORDER BY Cumulative_Oil desc
LIMIT 5
```
<img src="/images/SQL/Top Producing Wells2.png?raw=true" height='60%' width='60%'>
<br>

## What Wells for a Particular Operator have Produced the Most Oil to Date? 
```python
%%sql

SELECT 
  p.UWI, h.Current_Operator, sum(p.Oil) as 'Cumulative_Oil', h.Well_Name 
FROM prod_table p 
JOIN header_table h
  USING(UWI)  
WHERE Current_Operator = 'MARATHON OIL COMPANY'
GROUP BY h.Well_Name
ORDER BY Cumulative_Oil DESC
LIMIT 5
```
<img src="/images/SQL/Top Wells by Operator2.png?raw=true" height='60%' width='60%'>
<br>

## Top Producing Wells with Cumulative Water Filter
Wells that produce less water are more favorable, as the water is costly to dispose of. 
```python
%%sql

SELECT 
  p.UWI, h.Current_Operator, sum(p.Oil) as 'Cumulative_Oil',sum(p.Water) as 'Cumulative_Water', h.Well_Name 
FROM prod_table p 
JOIN header_table h 
  USING(UWI)  
GROUP BY Well_Name
  HAVING Cumulative_Water < 100000
ORDER BY Cumulative_Oil DESC
LIMIT 5
```
<img src="/images/SQL/Top Producing Wells Water Filter2.png?raw=true" height='75%' width='75%'>

