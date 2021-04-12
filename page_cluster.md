# Cluster Analysis for Rock Typing

**Description:** K-means clustering is used to discover primary rock types in a deep well drilled in the Williston Basin. At first, the entire column of rock is viewed, 
then we focus in on the primary oil target in the basin, the Bakken Petroleum System.

### Data Context: 
Operators drilling for oil and gas want to understand the subsurface reservoirs that they are trying to produce from, and one of the most common ways of doing so is by "logging" a 
well. A well is drilled vertically to some depth, then a tool is run inside the hole with sensors and measurements are taken. From these measurements, we infer properties about
the rock. Different rock types such as sandstone, limestone, and clay have very different readings on these tools. Here I hope to provide a **very** high level overview of 
some of the common logs found in North Dakotas Williston Basin. 

- Gamma Ray (GR): Measures the natural radioactivity of the rock
- Density (RHOB): Yields information about the density of the rock
  - Density could be driven by mineralogical composition or porosity (where the oil/gas exists)
- Neutron (NPHI): Measures the hydrogen content of the formation which can be related to its porosity or mineralogy
- Sonic (DTC): Measures the transit time of a compressional sound wave through the rock which is impacted by rock type and porosity 
- Photoelectric (PE): Provides information about the mineralogy
- Resisitivity (RT): Yields information about the electrical conductivity of the formation
  - This tells us about the saturations, oil is resistive, water is conductive. 


