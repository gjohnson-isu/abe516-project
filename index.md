---
layout: default
---

# ABE 516 Project
**Gabe Johnson**, PhD Student in Agricultural and Biosystems Engineering and Sustainable Agriculture

This website demonstrates the use of data science concepts learned in ABE 516 applied to my PhD disseration research on saturated buffers. Saturated buffers are an edge-of-field practice for reducing nitrate loss in agricultural subsurface drainage. Current research has shown these practices to be effective at removing nitrate from drainage water, but overall effectiveness is limited by the total amount of flow treated. My larger research project is focused on optimization of these practices for nitrate load reduction, analyzing the effectiveness over a longer period (5+ years), and using empirical data to predict future performance at existing or new sites.

# Research Question
The specific research question for this course project is:

**Can saturated buffer nitrate load reduction be predicted from design parameters?**

Our lab has been monitoring multiple saturated buffer sites for several years, resulting in a database of 40+ site-years of performance effectiveness data. From past and ongoing work, we know the major drivers of the performance are the length of the distribution pipe (which effectively controls the size of the practice and greatly impacts the volume of water that can be treated) and the size of the contributing drainage area. The larger the drainage area, the larger the flow and nitrate load. Thus, as the pipe length and drainage area increase, the total nitrate load removed increases. From our large dataset, we would like to be able to estimate the nitrate load removal for a new site (or an existing site in a new year) given the design parameters and other information such as precipitation. 

# Data Description
Data for this project consists of saturated buffer flow data (flow in, flow treated, flow bypassed), nitrate concentration data from inlet and outlet points (the saturated buffer control structure and various groundwater monitoring wells), and nitrate load data. Nitrate load (mass) is calculated from flow (volume) and concentration data (mass per volume) for annual sampling dates. 

We use pressure transducers to monitor water level in control structures at each site. This data is stored on a server shared with our partners at the USDA-ARS National Laboratory for Agriculture and the Environment. This data is used with weir flow equations to calculate flow data. Water samples are analyzed for nitrate concentration both by our ISU lab and NLAE. This data is generated into excel and csv files for each sample data that get combined into one file for the year. 

## Data wrangling
### Description
Data wrangling consists of processing flow and nitrate concentration data into clean, quality-controlled files, calculating nitrate load, and summarizing data for individual site-years and across site-years. 

Flow:
* hourly flowrates (in, bypass, treated) calculated from monitored water levels and processed for missing or extreme data
* cumulative flow volumes calculated 

Nitrate Concentration:
* raw concentration data files processed to extract sampling dates and concentration values for sample locations
* creation of clean dataframe or csv file of concentration summarized by sample data and inlet and outlet concentrations

NO3-N Load:
* incremental and cumulative loads (in, diverted, treated) calculated from cumulative flows and clean nitrate concentration data

Site-Year Summary Data:
* Annual summary metrics (total NO3-N load removed, total flow treated, percent NO3-N load reduction, and many others) calculated and organized into one tidy dataframe or csv file

### Implementation
Flow data wrangling can be seen in [this jupyter notebook](./project-wrangling-1.md).

Nitrate concentration data wrangling can be seen in [this jupyter notebook](./project-wrangling-2.md)

Nitrate load and summary data wrangling can be seen in [this jupyter notebook](./project-wrangling-3.md)

# Data Exploration
For intial data exploration, I read in the site-year summary file. This file has data for 35 site years across 6 sites, from 2011-2021 (I did not have time to process all of the 2022 data). I calculated averages across groups of sites and years, as well as mean, median, and standard deviation across all the data. 
Next, I pulled out a few specific variables to plot with boxplots and scatterplots to explore the descriptive statistics further and look for the relationships I was hypothesizing.

**Boxplot of total NO3-N Load removed organized by site:**
![N removed boxplot](./N removed boxplot.png)

**Boxplot of in-buffer removal percentage organized by site:**
![buffer removal boxplot](./buffer removal boxplot.png)

**Boxplot of edge-of-field removal percentage organized by site:**
![EOF removal boxplot](./EOF removal boxplot.png)

**Scatterplot of the total nitrate load removed vs. the product of pipe length and drainage area:**
![pipelength-drainage area vs load](./pipelength_da_loadremoval.png)

**Scatterplot of the edge-of-field removal percentage vs the flow diverted percentage:**
![EOF removal vs flow percent](./EOF vs flow percent.png)

So far, this data looks good. It matches the plots I created in Excel, and the scatterplots show the relationships I was expecting.

# Resllts and Discussion
Full details of the analysis can be found in the jupyter notebook (in markdown format) [here](./project-analysis-1.md).

I created linear regression models and performed principal components analysis and K-means clustering analysis on the site-year summary data.
## Linear Regression
I created a multiple linear regression model to predict nitrate load removal from two variables: (1) the product of distribution pipe length and contributing drainage area, and (2) the annual precipitation. This resulted in a coefficient of determination of 0.49, which is not great but something I can work with. A prior study did this same analysis with only the product of pipe length and drainage area, so it was disappointing that adding in precipitation didn't result in a larger increase. 

**Scatterplot of the total nitrate load removed vs. the product of pipe length and drainage area with multiple regression line:**
![pipelength-drainage area vs load with regression](./pipelength_da_loadremoval_regression.png)

## PCA
I ran PCA on the numerical site summary data excluding the nitrate load removed. I chose to include 4 principal components. This analysis resulted in principal component 1 and 2 explaining a majority of the variation in the data. I then used the PCA data in a multiple linear regression to predict nitrate load removed. However, this only resulted in a coefficient of determination of 0.48, which is marginally worse than with the raw data.

**PCA components plot:**

![PCA plot](./PCA_plot.png)

**PCA Regression Plot:**

![PCA regression plot](./PCA_regression_plot.png)

## Clustering
I used K-means cluster analysis as an unsupervised method to look for groups in the data. I obviously am aware of certain groups in the data (sites, years, sites with similar design parameters, etc), but the K-means clustering used unlabeled data. I nitially tried 6 clusters because there are six sites in the summary data. However, this resulted in a couple clusters of just one site-year. I suspected that a couple of the sites are very similiar in overall parameters (design parameters and performance variables), so I reduced the number of clusters to four. This resulted in good clustering across the sites. It correctly identified site BC-2 in one cluster as this is the largest site by drainage area and annual flow. This also resulted in separation of BC-1 data into separate clusters from 2011-2013 and 2014-2021 which matches up with the reduction in drainage area for this site. 

**4-Cluster Results:**

![cluster table](./cluster_table.png)

# Conclusions and Next Steps
I completed data wrangling on two major aspects of my saturated buffer site data. From this clean data, I was able to explore the data and relationships between different variables. Finally, I created a linear regression model and ran principle components analysis (PCA) to address my research question. 

My next steps include improving my data wrangling pipeline to ensure it is adaptible and reproducible for my other sites, using the data wrangling pipeline to process my additional sites, rebuilding these models with additional data, and building new models to further answer this research question and my other research questions. The current models also need additional verification. Finally, I need to ensure that this project website is built properly and my files on GitHub are organized and up to date to ensure good, FAIR data practices. 

_______________________________________________________
