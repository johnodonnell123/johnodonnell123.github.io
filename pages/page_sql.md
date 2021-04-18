# SQL in Jupyter for Data Retrieval and Analysis

**Description:** Here I highlight some useful examples of SQL for general data querying and high level analysis. Some queries are converted into Pandas DataFrames allowing for further analysis, while others are left alone and are meant for summary purposes. To run the queries in Jupyter I use ipython-sql, which allows for a more simple and intuitive syntax. 

### Data Context: 
The data we are working with here was scraped from a webpage and stored in a SQLite3 database, which I cover in [another project](https://johnodonnell123.github.io/pages/page_scrapy.html). 
For those unfamiliar with these oil and gas data types, I hope to provide some context. We will be working with 2 tables:

#### Header Table
Contains general information about a well such as the name, the location, and depth. It also contains the UWI (unique well identifier).

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="/images/SQL/header_table.PNG?raw=true">

#### Production Table
Wells produce Oil, Water, and Gas over time. This is our time-series data, which is why it is held in a separate table. They typically have higher rates early time and decline throughout their life. This table contains the UWI, the time stamp, the number of days that well actually flowed for that month, and the coinciding volumes for oil/water/gas.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;/<img src="/images/SQL/prod_table.PNG?raw=true"/>

## View Tables in DataBase
```javascript
query = %sql SELECT name FROM sqlite_master WHERE type ='table'
query
```
<img src="/images/SQL/View Tables2.png?raw=true"/>
<br>

## Select Entire Table
```javascript  
query = %sql SELECT * FROM prod_table

df = query.DataFrame()
print(df.shape)
df.iloc[:,:6].sample(5)
```
<img src="/images/SQL/Select all from table2.png?raw=true"/>
<br>

## Select First 5 Rows
```javascript
query = %sql SELECT * FROM header_table LIMIT 5

df = query.DataFrame()
print(df.shape)
df.iloc[:,:6].sample(5)
df.columns
```
<img src="/images/SQL/Select first 5 rows2.png?raw=true"/>
<br>

## Select Using Single Condition
```javascript
query = %sql SELECT * FROM prod_table WHERE Days > 20 

df = query.DataFrame()
print(df.shape)
df.sample(5)
```
<img src="/images/SQL/Single Condition2.png?raw=true"/>
<br>

## Select Using Multiple Conditions
Using the logical AND operator
```javascript 
query = %sql SELECT * FROM prod_table WHERE Days > 20 and Water < 100

df = query.DataFrame()
print(df.shape)
df.sample(5)
```
<img src="/images/SQL/Multiple Conditions2.png?raw=true"/>
<br>

## Select Specific Wells
Using the logical IN operator
```javascript
query = %sql SELECT UWI, Days, Oil FROM prod_table WHERE UWI IN (33061042810000,33061005070000)

df = query.DataFrame()
print(df.shape)
df.sample(5)
```
<img src="/images/SQL/Specific Wells2.png?raw=true"/>
<br>

## Join: <br> Select Columns from 2 Tables
```javascript
query = %sql SELECT p.UWI,p.Days,p.Oil,h.Current_Operator FROM prod_table p JOIN header_table h ON p.UWI = h.UWI

df = query.DataFrame()
print(df.shape)
df.sample(5)
```
<img src="/images/SQL/Join Select Specific Columns2.png?raw=true"/>
<br>

## Group By: <br> What Operators/Companies have Produced the Most Oil to Date?
It appears Continental Resources has produced > 400 Million Barrels of Oil and Drilled just over 1700 Wells!
```javascript
%%sql 

SELECT p.UWI, COUNT(DISTINCT p.UWI) AS 'Wells', SUM(p.Oil) AS 'Cumulative_Oil', h.Current_Operator
FROM prod_table p 
JOIN header_table h 
ON p.UWI = h.UWI 
GROUP BY Current_Operator
ORDER BY Cumulative_Oil desc
LIMIT 5
```

<img src="/images/SQL/Aggregate Operator Oil and Wells2.png?raw=true"/>
<br>

## Group By: <br> What Wells have Produced the Most Oil to Date? Who do they belong to? 
```javascript
%%sql 

SELECT p.UWI, sum(p.Oil) as 'Cumulative_Oil',h.Well_Name ,h.Current_Operator
FROM prod_table p 
JOIN header_table h 
ON p.UWI = h.UWI 
GROUP BY Well_Name 
ORDER BY Cumulative_Oil desc
LIMIT 5
```
<img src="/images/SQL/Top Producing Wells2.png?raw=true"/>
<br>

## Group By: <br> What are the top producing wells for a particular operator? 
```javascript
%%sql

SELECT p.UWI,h.Current_Operator, sum(p.Oil) as 'Cumulative_Oil',h.Well_Name 
FROM prod_table p 
JOIN header_table h ON p.UWI = h.UWI 
GROUP BY Well_Name
HAVING Current_Operator = 'MARATHON OIL COMPANY'
ORDER BY Cumulative_Oil desc
LIMIT 5
```
<img src="/images/SQL/Top Wells by Operator2.png?raw=true"/>
<br>

## Group By: <br> Top Producing Wells with Cumulative Water Filter
Wells that produce less water are more favorable, as the water is costly to dispose of. 
```javascript
%%sql

SELECT p.UWI,h.Current_Operator, sum(p.Oil) as 'Cumulative_Oil',sum(p.Water) as 'Cumulative_Water',h.Well_Name 
FROM prod_table p 
JOIN header_table h ON p.UWI = h.UWI 
GROUP BY Well_Name
HAVING Cumulative_Water < 100000
ORDER BY Cumulative_Oil desc
LIMIT 5
```
<img src="/images/SQL/Top Producing Wells Water Filter2.png?raw=true"/>

