# Exploratory Data Analysis for ~15,000 Oil & Gas Wells in the Williston Basin

**Description:** SQL and Plotly are used to generate some high-level views on oil production. The performance of these wells are governed by two primary sets of factors:

1. Geologic: Factors outside of our control but measurable. Parameters such as pressure, thickness, porosity/permeability, saturations (oil/gas/water), stress, natural fracturing, etc. The primary geologic drivers change as you look around the basin and are very much a topic of interpretation. These variables are best viewed spatially on a map, not in tabular format.

2. Engineering: Factors under our control. These include depth and length of the well, where we place the well in the reservoir, how we stimulate the well (pumping fluid and proppant in to create fractures), and how closely we place multiple wells together (too close and they compete with one another, too far and we leave resource behind). These features are best viewed in tabular format.

- We don't have any traditional geologic varaibles to work with here, nor do we have the information on how the wells were stimulated. We will have to be creative and work with what we have to generate features that approximate these missing features. 

### Data Context: 
The data we are working with here was scraped from a webpage and stored in a SQLite3 database, which I cover in [another project](/pages/page_scrapy.md). 
For those unfamiliar with these oil and gas data types, I hope to provide some context. We will be working with 2 tables:

#### Header Table
Contains general information about a well such as depth and location. It also contains the UWI (unique well identifier). There is a row for every well, leaving us with ~15,000 records. 

#### Production Table (Time-Series)
Wells produce oil, water, and gas over time. This is our time-series data, which is why it is held in a separate table. Each of our ~15,000 wells has multiple records in this table, making is significantly larger at around 1.1 million rows. This table contains the UWI, the time stamp, the number of days that well actually flowed for that month, and the coinciding volumes for oil/water/gas.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="/images/SQL/prod_table.PNG?raw=true">

## What does the basin look like? 
Here we have all 15,000 wells on a map, colored by the depth of the top perforation. The top perf on a well is typically within ~100' of entering the formation, with this in mind we can use this perf depth as a proxy for formation depth. Looking at our map below it appears to be pretty accurate. The Williston basin has one structural feature that stands out from the others, the nesson [anticline](https://www.glossary.oilfield.slb.com/en/Terms/a/anticline.aspx), which I have annotated here below!

<align="center" img src="/images/EDA/Structure Map.PNG?raw=true" width="50%" height="50%">

## What Operator has Produced the Most Oil to Date?

<img src="/images/EDA/Oil and Wells by Operator.PNG?raw=true" width="75%" height="75%">

## What Operator has Produced the Most Oil for their Well Count?
Producing more oil with less wells likely translates into better project level economics (and better investments). 

<img src="/images/EDA/Oil Per Well by Operator.PNG?raw=true" width="75%" height="75%">

## Simple Oil Production Plot
Here we choose 20 wells at random for a given operator, and plot their oil production streams. 

<img src="/images/EDA/Simple Production Plot by Operator.PNG?raw=true" width="75%" height="75%">

## Water / Oil Ratio Plot
Wells that produce less water are more favorable from an economic standpoint, as the water is costly to dispose of. As you can see, over time operators have been producing more and more water!

<img src="/images/EDA/WOR By Vintage.PNG?raw=true" width="75%" height="75%">

## Oil Production Plot: Averaged by ~ Depth/Pressure
Here we are binning by average top perf depth, which is a proxy for depth of the formation. The correlation between depth and pressure is pretty strong in this basin. Higher pressure normally leads to better wells. 

<img src="/images/EDA/Average Stream Plot - Top Perf.PNG?raw=true" width="75%" height="75%">

## What Areas have Produced the Most Oil?
The basin is divided up into 6mi x 6mi squares called townships. Lets see what townships have produced the most oil.

<img src="images/EDA/Code - Cum Oil Per Block Per Well.PNG?raw=true" width="75%" height="75%">
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="/images/EDA/Map - Cum Oil Per Block.PNG?raw=true" width="75%" height="75%">

## What Areas have Produced the Most Oil for their Well Count?
Lets see what townships have produced the most oil for their well count. More oil with less wells is favorable. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="/images/EDA/Map - Cum Oil Per Block Per Well.PNG" width="50%" height="50%">

As you can see, this map looks notably different than the previous, showing that simply the number of wells in a township is a primary driver. Would we want an investment in a township that has produces the most oil, or the township that how produces the most oil per well drilled?





