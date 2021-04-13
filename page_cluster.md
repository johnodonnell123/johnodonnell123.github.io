# Cluster Analysis for Rock Typing

**Description:** K-means clustering is used to discover primary rock types in a deep well drilled in the Williston Basin. At first, the entire column of rock is viewed, 
then we focus in on the primary oil target in the basin, the Bakken Petroleum System.

### Data Context: 
Operators drilling for oil and gas want to understand the subsurface reservoirs that they are trying to produce from, and one of the most common ways of doing so is by "logging" a 
well. A well is drilled vertically to some depth, then a tool is run inside the hole with sensors and measurements are taken. From these measurements, we infer properties about
the rock. Different rock types such as sandstone, limestone, and clay have very different readings on these tools. Here I hope to provide a **very** high level overview of 
some of the common logs found in North Dakotas Williston Basin. 

- Gamma Ray (GR): Measures the natural radioactivity of the rock
- Density (RHOB): Measures the electron density of the sample, which is closely tied to the bulk density
- Neutron (NPHI): Measures the hydrogen content of the formation which is related to its porosity or mineralogy

If you are interested in learning more, [here](johnodonnell123.github.io/pdf/Atlas_of_Log_Responses_Atlas_of_Log_Resp.pdf) is a useful quick-look chart that shows many more logs and their typical response in different rock types!

### Machine Learning Context: What is K-Means Clustering?
K-Means clustering is an unsupervised learning algorithm that attempts to uncover structures within the dataset it is provided. In one sentence: the algorithm will cross plot all of our variables against each other, and see if there are any obvious groups (clusters) in which the points can then be categorized. In the context of this project we will be looking to see if different rock types (sandstone, carbonate, salt) are easily discernable with this methodology. This is something that petrophysicists have been doing for decades manually and has proven incredibly valuable. Why use machine learning instead of doing this manually?

- Time: This process can be done for thousands of wells, millions of feet of data, in a matter of minutes. This type of projects would take weeks if not months traditionally.
- Bias: It is repeatable, the subjectivity of the interpreter is minimized. 
- Exploration: In many cases, the algorithm will find relationships that we didn't know existed, but make sense and can be very valuable once we are aware of them. 


### Reading a well log (.las file) into  Pandas DataFrame:
We see that we have a DataFrame indexed by depth, with data every 0.5 feet. With 21543 rows that leaves us with ~ 10771' of data with 55 logs. 

<img src="images/Cluster/Las Import.PNG?raw=true"/>

## Trimming our DataFrame
We won't be using 55 logs in our analysis, as most of them are either irrelevant for this study or are highly correlated with another log.
The RHOZ log is trimmed to reasonable values, and fortunately this removes all of the other bad data. 
We are also dropping the first 2000' of data as it is a homogenous unit, and is simply not interesting for this project!

<img src="images/Cluster/DataFrame Clean.PNG?raw=true"/>

## Viewing Logs with Matplotlib
Credit here to [Andy Mcdonald](http://andymcdonald.scot/python-and-petrophysics) and all of the work he has done paving the way doing petrophysics in Python. 
Here we have two tracks. The first has our Gamma Ray log that has been shaded to highlight variablility. The second track contains the NPHI & RHOB logs. They have
been placed on the same track and their crossover relationship has been shaded (a common petrophysical technique).

<img src="images/Cluster/Log Preview.PNG?raw=true"/>

## Preparing Data for Clustering:
We need to scale our data so that they are all on similar scaled for comparison. The algorithm will want to make these clusters more/less round in crossplot space, so if we have extremely different scales this will lead to the variables with smaller variance getting more weight. Our scales here aren't terribly different, but it never hurts to standardize and is considered good practice. 

Scikit-Learn's StandardScaler will transform our distribution to have a mean of 0 and a standard deviation of 1.

<img src="images/Cluster/StandardScaler.PNG?raw=true"/>

## Create the model
We create our model
<img src="images/Cluster/Cluster Model.PNG?raw=true"/>

## View Results
There are a couple of ways to view results. 

### 1) 2D and 3D Crossplots
<img src="images/Cluster/2D Crossplot.PNG?raw=true"/>
<img src="images/Cluster/3D Crossplot.PNG?raw=true"/>

These can be very interesting when we have clearly definable groups that separate out nicely, however when we are working with a medium such as a rock (variable combination of minerals), the log plot is more insightful.

### 2) Log Plot
<img src="images/Cluster/Log Preview Cluster.PNG?raw=true"/>

This is a much more intuitive view! We can see visually how some of our log responses are translating into different clusters. Lets zoom in on an area:

# Interpretation:




















