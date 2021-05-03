# Film Revenue Analysis with Scrapy, Pandas, and Plotly

**Description:** In this post I want to cover my approach to the problem presented in the Module 1 Final Project of Flatiron's Data Science Bootcamp. I have been tasked with  generating an analysis and creating a presentation exploring type of films and their economic success. There were 11 .csv files provided, however I chose to scrape my own dataset from the IMDB website. At a high level this was my approach:
1. Use a scrapy crawler to retirieve the dataset and store in a .csv file
2. Import the .csv file into Pandas for cleaning and manipulation
3. Perform exploratory data analysis on the dataset and select the 4-5 most impactful trends
4. Present these relationships in a susinct manner to a non-technical audience

## Web Scraping with Scrapy

