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

### Reading a wells log (.las file) into  Pandas DataFrame:
We see that we have a DataFrame indexed by depth, with data every 0.5 feet. With 21543 rows that leaves us with ~ 10771' of data with 55 logs. 
We won't be using 55 logs in our analysis, as most of them are either irrelevant for this study or are highly correlated with another log.

<img src="images/Cluster/Las Import.PNG?raw=true"/>

## Trimming our DataFrame
Dropping logs, only keeping the few most informative. We are also dropping the first 2000' of data as it is a homogenous unit, and is simply not interesting for this project!
<img src="images/Cluster/DataFrame Clean.PNG?raw=true"/>




