# Cluster Analysis for Rock Typing

**Description:** K-means clustering is used to discover primary rock types in a deep well drilled in the Williston Basin. At first, the entire column of rock is viewed, 
then we focus in on the primary oil target in the basin, the Bakken Petroleum System.

### Data Context: 
The data we are working with here was scraped from a webpage and stored in a SQLite3 database, which I cover in [another project](/page_scrapy.md). 
For those unfamiliar with these oil and gas data types, I hope to provide some context. We will be working with 2 tables:

#### Header Table
Contains general information about a well such as the name, the location, and depth. It also contains the UWI (unique well identifier).

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="images/SQL/header_table.PNG?raw=true">

#### Production Table
Wells produce Oil, Water, and Gas over time. This is our time-series data, which is why it is held in a separate table. They typically have higher rates early time and decline throughout their life. This table contains the UWI, the time stamp, the number of days that well actually flowed for that month, and the coinciding volumes for oil/water/gas.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="images/SQL/prod_table.PNG?raw=true"/>

## View Tables in DataBase
<img src="images/SQL/View Tables.PNG?raw=true"/>
<br>

## Select Entire Table
<img src="images/SQL/Select all from table.PNG?raw=true"/>
<br>

## Select First 5 Rows
<img src="images/SQL/Select first 5 rows.PNG?raw=true"/>
<br>

## Select Using Single Condition
<img src="images/SQL/Single Condition.PNG?raw=true"/>
<br>

## Select Using Multiple Conditions
Using the AND logical operator
<img src="images/SQL/Multiple Conditions.PNG?raw=true"/>
<br>

## Select Specific Wells
Using hte IN logical operator
<img src="images/SQL/Specific Wells.PNG?raw=true"/>
<br>

## Join: <br> Select Columns from 2 Tables
<img src="images/SQL/Join Select Specific Columns.PNG?raw=true"/>
<br>

## Group By: <br> What Operators/Companies have Produced the Most Oil to Date?
It appears Continental Resources has produced 400 Million + Barrels of Oil and Drilled just over 1700 Wells!
<img src="images/SQL/Aggregate Operator Oil and Wells.PNG?raw=true"/>
<br>

## Group By: <br> What Wells have Produced the Most Oil to Date? Who do they belong to? 
<img src="images/SQL/Top Producing Wells.PNG?raw=true"/>
<br>

## Group By: <br> What are the top producing wells for a particular operator? 
<img src="images/SQL/Top Wells by Operator.PNG?raw=true"/>
<br>

## Group By: <br> Top Producing Wells with Cumulative Water Filter
Wells that produce less water are more favorable, as the water is costly to dispose of. 
<img src="images/SQL/Top Producing Wells Water Filter.PNG?raw=true"/>
