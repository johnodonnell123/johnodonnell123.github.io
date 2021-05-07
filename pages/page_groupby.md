# Aggregating Oil Production Streams at a DSU (Project) Level to Visualize the Results of Development Strategy in Python

**Description:** One of the primary concerns of operators developing acreage for oil and gas is how best to spend capital getting the resource out of the ground. Gor a given area, operators need to decide how many wells to drill, and how to stimulate/frac them. This is a hot topic of debate in the industry, many operators right next to each other in the same basin are developing their assets very differently, some spending twice as much as their neighbor. In this project, I step through how to use Python and the Pandas GroupBy function to aggregate the production streams for all wells in a DSU, then visualize the results of different development strategies in an informative manner. Displaying production for individual well has been covered in [another project of mine](https://johnodonnell123.github.io/pages/page_EDA.html), and while viewing data at that level can be useful it’s also important to view the results of multiple wells together. Different development schemes are plotted adjacent to one another allowing the interpreter to spot the DSUs with the most productive capital. 

## Problem:
Land in the Williston basin of North Dakota is divided up Townships, Ranges, and Sections. A township is 6mi x 6mi, and a section is 1mi x 1 mi. Traditionally, a DSU (Drill Spacing Unit) is two sections, forming a 2mi x 1mi stretch of land (shown in red). Operators will drill some number of wells horizontally in the longer direction (shown in blue).

<p align = 'center'>
  <img src="/images/GroupBy/DSU Explained.PNG?raw=true" height = "50%" width = "50%">
</p>

The question becomes: to most efficiently recover the oil that is in the ground inside that DSU, how many wells do we drill and how do we stimulate/frac them? Below are a handful of possible scenarios to help further represent the problem. Blue sticks represent horizontal wells, and the yellow areas represent the area that is being drained. Here we are assuming that this area is *primarily* a function of the amount of fluid pumped into the well (the frac). This is a **very** simplistic view, yet it sheds light on the complexity of the problem. 
<br>

<p align = 'center'>
  <img src="/images/GroupBy/DSU Cartoon2.PNG?raw=true" height = "60%" width = "60%">
</p>

As you might have concluded, the issue of capital efficiency becomes one of balancing the cost of wells and the costs of stimulation. That being said, we don't fully understand the relationship between the stimulation and the drainage area, as this relationship is likely non-linear and differs across geologic areas. This is why being able to clearly understand the results of previous development patterns is *essential*. Think about what a wells production profile might look like for the middle scenario vs. that of a well in the third scenario.

## Data: 
This data set comes from Enverus, and can be organized into two tables:

#### Header Table
Contains general information about a well such as the depth, location, and the stimulation of the well. It also contains spacing information (how close the next well is). We have ~ 15,000 rows indexed by UWI (Unique Well Identifier), each row representing one well. 
- The stimulation of the well can be simplified into two primary components, the fluid pumped, and the proppant (sand) pumped. It can be thought of in this context: the fluid creates the fractures (surface area) that was the yellow box in the diagram above, and the sand/proppant keeps those fractures open as pressure declines (maintaining some level of drainage area over time).

#### Production Table (Time-Series)
Wells produce oil, water, and gas over time. This is our time-series data, which is why it is held in a separate table. Each of our wells has an entry for every month it produced, making is significantly larger at around 1.1 million rows. This table contains the UWI, the time stamp, the number of days that well actually flowed for that month, and the coinciding volumes for oil/water/gas.

<p align="center">
  <img src="/images/SQL/prod_table.PNG?raw=true" height = "40%" width = "40%">
</p>

## Grouping the Wells by DSU:
To apply an aggregation function to wells in a DSU they all need to have some sort of flag to denote which DSU they belong to. This is a project of its own in terms of complexity and length, I will only cover the workflow at a high level here. In short, we want to give a unique name to all wells that share the same township, range, and two section numbers. This is the logic behind the program. 
- For each well we have a surface hole and bottom hole latitude and longitude (shown in yellow)
- The `utm` package in Python can be used to convert lat/long into xy coordinates (meters)
- For each well, two new XYs are calculated for distances that are 25% and 75% of the distance between our two points (yielding points in each section, shown in green)
- These XYs are converted back to Latitude and Longitude, and finally converted to Township, Range, and Section
- All wells whose center (green) points share the same township and range are grouped together, within these groupings all wells that have two points that have section numbers in common are given a unique name. 
- Their DSU name is a combination of one of the well names as and the two section numbers

<p align = 'center'>
  <img src="/images/GroupBy/Calculated Coordinates.PNG?raw=true" height = "50%" width = "50%">
</p>

### Our headers DataFrame now contains a DSU tag
<p align = 'center'> 
  <img src="/images/GroupBy/DSU name preview.PNG?raw=true" height = "50%" width = "50%">
</p>

## Calculating Production
Since we have calendar date producing months for each well, we could aggregate production for the wells in the DSU on their producing month #1, #2 etc. However, looking at our production table we see that for each month there is a number representing the reported producing days that ranges from 0 to 31. 

<p align="center">
  <img src="/images/SQL/prod_table.PNG?raw=true" height = "40%" width = "40%">
</p>

If we chose to use calendar months to aggregate, our production streams would have influence from their production management, adding noise. For example: at calendar month 3 one well might have 90 producing days, and another well may only have 13. A better method would be to use the cumulative reported producing days and represent each month as 30.4 days. The challenge here is that we do not have a data point for exactly every 30.4 days, our solution will be to linearly interpolate between the two bounding points. The `np.interp` function allows us to do this, we pass two lists of values (days and production) and specify a point at which to interpolate (every 30.4 days). 

The code below calculates the cumulative and monthly streams for each well in our dataset for months 1 - 60.

``` python
# For months 1 through 60
for m in range(1,60,1):
    # For each well in our headers DataFrame
    for uwi in df_headers.index.tolist():
        # Calculate an array of cumulative producing days for this well
        cum_days = df_production.loc[uwi,'Prod_Days'].cumsum()
        # Check if this well has enough total cumulative producing days to calculate the production for the current month
        if m * 30.4 < max(cum_days):
            # For each stream, calculcate cumulative and monthly volumes
            for stream in ['Oil_Nrm','Water_Nrm','Gas_Nrm','Fluid_Nrm']:
                rate_prod_list = df_production.loc[uwi,stream].values
                cum_prod_list = df_production.loc[uwi,stream].cumsum().values
                # Use np.interp to calculcate the correct value for our current month (month number * 30.4)
                df_headers.loc[uwi,f'{m}m_{stream}_Cum'] = np.interp( m * 30.4 , cum_days , cum_prod_list) 
                df_headers.loc[uwi,f'{m}m_{stream}_Rate'] = np.interp( m * 30.4 , cum_days , rate_prod_list) 
```

This yields monthly production values for all the fluid streams (cumulative and monthly) like so:

<p align="center">
  <img src="/images/GroupBy/Production Computed.PNG?raw=true" height = "85%" width = "85%">
</p>

## GroupBy
Now that production is represented by comparable monthly values, we can start to aggregate. This is accomplished with Pandas GroupBy function, which aggregates data when given a shared value. All wells in a DSU will share the same DSU name and have their own values for our calculated production month 1, 2, etc. When we GroupBy DSU name these monthly production values are aggregated and we can sum them for monthly production values for the DSU. First, we specify the name of the value we want to group by, then grab the column of values to aggregate and provide an aggregation method. 

```python
# Create a new DataFrame
dsu_df = pd.DataFrame()
  
# Calculate DSU level information

# Get the DSU operator by selecting the operator name from one of the wells
dsu_df['Operator'] = df_headers.groupby('DSU_NAME')['OPERATOR'].first()

# Get the number of wells in the DSU by counting the number of well names 
dsu_df['Wells'] = df_headers.groupby('DSU_NAME')['Well_Name'].count()
```

**The resulting DataFrame is indexed by our DSU names, and the columns represent the aggregated metrics we just calculated**
<p align="center">
  <img src="/images/GroupBy/dsu_df wells.PNG?raw=true" height = "45%" width = "45%">
</p>

## Calculating a scalar for partially developed DSU's
Some DSUs are not fully developed, and may only be partially filled in. These DSUs can either be dropped or scaled to represent full development. The logic behind the calculation of the scalar is shown here below. The scaler can be applied to production, well count, stimulation volumes, and costs to normalize DSU's for width. It can also be used to simply remove all partially developed DSU's (they will have a scalar number not ≈ 1. This is a matter of opinion, as partially developed DSU's will have a larger influence from half-bounded well performance (wells that are spaced wider *on average*).

<p align="center">
  <img src="/images/GroupBy/Partial Development.PNG?raw=true" height = "80%" width = "80%">
</p>

Assuming we want to scale our variables, it can be done as shown here below. 

```python
# Calculate scalar to normalize partially developed DSUs to a 5280
dsu_df['Wells_Scaled'] = 5280 / df_headers[df_headers['Bounded'] == 2].groupby('DSU_NAME')['Average_Neighbor_Distance'].mean()
dsu_df['Scalar'] = dsu_df['Wells_Scaled'] / dsu_df['Wells']

# Scale total DSU fluid and proppant 
dsu_df['Fluid_MMBBLS_Scaled'] = (df_headers.groupby('DSU_NAME')['TOTAL_FLUID_BBL'].sum()/1000000) * dsu_df['Scalar']
dsu_df['Proppant_MMLBS_Scaled'] = (df_headers.groupby('DSU_NAME')['TOTAL_PROP_LBS'].sum()/1000000) * dsu_df['Scalar']

# Calculate and Scale production
for m in range(1,60,1):
    for stream in ['Oil','Gas','Water','Fluid']:
        dsu_df[f'{m}m_{stream}_Cum_Scaled'] = (df_headers.groupby('DSU_NAME')[f'{m}m_{stream}_Nrm_Cum'].sum()) * dsu_df['Scalar']
        dsu_df[f'{m}m_{stream}_Rate_Scaled'] = (df_headers.groupby('DSU_NAME')[f'{m}m_{stream}_Nrm_Rate'].sum()) * dsu_df['Scalar']
        
```
**This yields the DataFrame seen below. Now we can begin visualizing the results.**
<p align="center">
  <img src="/images/GroupBy/dsu_df production.PNG?raw=true">
</p>

# Visualizing DSU Production:

## Cumulative Oil vs Producing Months
Here I have selected a random area of the basin and plotted the production for some DSUs. The plotting function can be found in the notebook on my GitHub repository.
```python
DSU_STREAM_PLOT(dataframe = dsu_df,  
            material = 'Oil', 
            cumulative = 1, 
            line_width = 2
            )
```
<p align="center">
  <img src="/images/GroupBy/dsu_cum_time2.PNG?raw=true" height = "75%" width = "75%">
</p>

## Cumulative Oil vs Monthly Oil Rate
This plot allows us to see how the production is declining in these different DSUs. A common technique to forecast the EUR (estimated ultimate recovery) is to extrapolate a line out from these trends and assume an economic abandonment rate (represented here by the black dashed line). The intersection of these two lines is our EUR.
```python
DSU_STREAM_PLOT(dataframe = dsu_df,  
            rate_cum = 1,
            material = 'Oil', 
            line_width = 2
            )
```
<p align="center">
  <img src="/images/GroupBy/dsu_rate_cum2.PNG?raw=true" height = "75%" width = "75%">
</p>

## Economics
This is where we start to see some truly impactful views. Assigning some cost to the drilling and stimulation of each well, we can also aggregate total DSU cost. This allows us to calculate the total oil produced / capital spent **through time** to see if our capital is just buying us accelerated volumes or EUR. Here the Enverus estimation for well cost is used, and it includes the cost of drilling the well and the stimulation. This allows for a better understanding of DSU/project level economics and the impact of our development strategy on our capital efficiency.
```python
DSU_STREAM_PLOT(dataframe = dsu_df,  
            material = 'Oil', 
            economics = 1,
            cumulative = 1, 
            line_width = 2
            )
```
<p align="center">
  <img src="/images/GroupBy/dsu_economics2.PNG?raw=true" height = "75%" width = "75%">
</p>

## Wrap Up
This workflow allows us to take advantage of public data and assess the results of different spacing and completion patterns in the basin. Assessing economics at a project level can yield insight into where resource might be under and over developed, and where operators might be overcapitalized. It allows us to view performance through time, to estimate the impact of spacing and completions on early time rates, midlife performance, as well as ultimate recovery. 
