# Aggregating Oil Production Streams at a DSU (Project) Level to Assess Development Schemes and Economics with Pandas GroupBy

**Description:** One of the primary concerns of operators developing acreage for oil and gas is how best to spend capital getting the resource out of the ground. Common questions
arise such as "how many wells should we drill?" and "how much fluid should we pump in each well?". This is a hot topic of debate in the industry, many operators right next to each other in the same basin are developing their assets very differently. In this project, I step through how to use Python and the Pandas GroupBy function to aggregate the production streams for all wells in a DSU, then visualize the results of different development strategies in an informative manner. Different development schemes are compared to one another to determine the DSUs with the most productive capital. 

## Problem:
Land in the Williston basin is divided up into Townships, Ranges, and Sections. A township is 6mi x 6mi, and a section is 1mi x 1 mi. Traditionally, a DSU (Drill Spacing Unit) is two sections, forming a 2mi x 1mi stretch of land (shown in red). Operators will drill a number of wells horizontally in the longer direction (shown in blue).

<p align = 'center'>
  <img src="/images/GroupBy/DSU Explained.PNG?raw=true" height = "50%" width = "50%">
</p>

The question becomes: to most efficiently recover the oil that is in the ground inside that DSU, how many wells do we drill and how to we stimulate/frac them? Here are a handful
possible scenarios to help further represent the problem. Blue sticks represent horizontal wells, and the yellow areas represent the area that is being drained. Here we are assuming that this area is primarily a function of the amount of fluid pumped into the well (the frac). This is a very simplistic view yet it sheds light on the complexity of the problem. 
<br>

<p align = 'center'>
  <img src="/images/GroupBy/DSU Cartoon.PNG?raw=true" height = "50%" width = "50%">
</p>

## Data: 
This data set comes from Enverus, and can be organized into two tables:

#### Header Table
Contains general information about a well such as the depth, location, and the stimulation of the well. It also contains spacing information (how close the next well is). We have ~ 15,000 rows indexed by UWI (Unique Well Identifier), each row representing one well. The stimulation of the well can be simplified into two primary components, the fluid pumped and the proppant (sand) pumped. 

#### Production Table (Time-Series)
Wells produce oil, water, and gas over time. This is our time-series data, which is why it is held in a separate table. Each of our wells has an entry for every month it produced, making is significantly larger at around 1.1 million rows. This table contains the UWI, the time stamp, the number of days that well actually flowed for that month, and the coinciding volumes for oil/water/gas.

<p align="center">
  <img src="/images/SQL/prod_table.PNG?raw=true">
</p>

## Grouping the Wells by DSU:
To apply an aggregation function to wells in a DSU they all need to have some sort of flag to denote which DSU they belong to. This is a project of its own in terms of complexity and length, I will only cover the workflow at a high level here. In short, we want to give a unique name to all wells that share the same township, range, and two section numbers. 
- For each well we have a surface hole and bottom hole latitude and longitude (a point at each end of our well)
- The `utm` package in Python can be used to convert lat/long into xy coordinates (meters)
- For each well two new XYs are calculated for distances that are 25% and 75% of the distance between our two points (yielding point in each section)
- These XYs are converted back to Latitude and Longitude, and finally converted to Township, Range, and Section
- All wells that share the exact same township and range are grouped together, within these groupings all wells that have two section numbers in common are given a unique name. 

<p align = 'center'>
  <img src="/images/GroupBy/Calculated Coordinates.PNG?raw=true" height = "50%" width = "50%">
</p>

#### Our headers DataFrame now contains a DSU tag 
<p align = 'center'>
  <img src="/images/GroupBy/DSU name preview.PNG?raw=true" height = "50%" width = "50%">
</p>

## Calculating Production
Since we have calendar date producing months for each well, we could aggregate production for the wells in the DSU on their producing month #1, #2 etc. However looking at our production table we see that for each month there is a number representing the reported producing days that ranges from 0 to 31. If we chose to just use the calendar months our production streams would have influence from their production schedules that will add noise to our data. For one well, month 3 might represent 90 days of production and for another it might represent 7. A better method would be to use the cumulative reported producing days, and represent each month as 30.4 days. The challenge here is that we don't have a data point for exactly every 30.4 days, our solution will be to linearly interpolate betweeen the two bounding points. 

The code below calculates the cumulative and monthly streams for each well in our dataset for months 1 - 60.

``` javascript
# For months 1 through 60
for m in range(1,60,1):
    # For each well in our headers DataFrame
    for uwi in df_headers.index.tolist():
        # Calculate an array of cumulative producing days
        cum_days = df_production.loc[uwi,'Prod_Days'].cumsum()
        # Check if we have enough days to calculate the production for the current month
        if m * 30.4 < max(cum_days):
            # For each stream, calculcate cumulative and monthly volumes
            for stream in ['Oil_Nrm','Water_Nrm','Gas_Nrm','Fluid_Nrm']:
                rate_prod_list = df_production.loc[uwi,stream].values
                cum_prod_list = df_production.loc[uwi,stream].cumsum().values
                # Use np.interp to calculcate the correcly value for our current month
                df_headers.loc[uwi,f'{m}m_{stream}_Cum'] = np.interp( m * 30.4 , cum_days , cum_prod_list) 
                df_headers.loc[uwi,f'{m}m_{stream}_Rate'] = np.interp( m * 30.4 , cum_days , rate_prod_list) 
```

## GroupBy
Now that production is represeted by comparable monthly values, we can start to aggregate. This is accomplished with Pandas GroupBy function, which aggregates data when given a shared value. For example, all of the wells in a DSU will share the same DSU name and have their own values for production month 1, 2, etc. When we GroupBy DSU name these monthly production values are summed and we now have monthly production values for the DSU. 



















