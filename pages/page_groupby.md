# Aggregating Oil Production Streams at a DSU (Project) Level to Assess Development Schemes and Economics with Pandas GroupBy

**Description:** One of the primary concerns of operators developing acreage for oil and gas is how best to spend capital getting the resource out of the ground. Common questions
arise such as "how many wells should we drill?" and "how much fluid should we pump in each well?". This is a hot topic of debate in the industry, many operators in the same
basin are developing their assets very differently. In this project, I step through how to use Python and the Pandas GroupBy function to aggregate the production streams from all 
wells in a single DSU, then visualize the results of different development strategies in an informative context. 

## Problem:
Land in the Williston basin is divided up into Townships, Ranges, and Sections. A township is 6mi x 6mi, and a section is 1mi x 1 mi. Traditionally, a DSU (Drill Spacing Unit) is
two sections, forming a 2mi x 1mi stretch of land (shown in red). Operators will drill a number of wells horizontally in the longer direction (shown in blue).

<p align = 'center'>
  <img src="/images/GroupBy/DSU Explained.PNG?raw=true"/>
</p>

The question becomes: to most efficiently recovery the oil that is in the ground inside that DSU, how many wells do we drill and how to we stimulate/frac them? Here are a handful
possible scenarios to help further represent the problem. Blue sticks represent horizontal wells, and the yellow areas represent the area that is being drained. Here we are assuming that this area is primarily a function of the amount of fluid pumped into the well (the frac). This is simplistic view yet it sheds light on the complexity of the problem. 

<p align = 'center'>
  <img src="/images/GroupBy/DSU Cartoon.PNG?raw=true"/>
</p>
