# Film Revenue Analysis with Scrapy, Pandas, and Plotly

**Description:** In this post I want to cover my approach to the problem presented in the Module 1 Final Project of Flatiron's Data Science Bootcamp. I have been tasked with  generating an analysis and creating a presentation exploring type of films and their economic success. There were 11 .csv files provided, however I chose to scrape my own dataset from the IMDB website. At a high level this was my approach:
1. Use a scrapy crawler to retirieve the dataset and store in a .csv file
2. Import the .csv file into Pandas for cleaning and manipulation
3. Perform exploratory data analysis on the dataset and select the 4-5 most impactful trends
4. Present these relationships in a susinct manner to a non-technical audience

## Web Scraping with Scrapy
I chose to use Scrapy instead of BS4 becuase in my opinion it is more straightforward and extensible. I ended up retrieving data for more than 80,000 films and followed thousands of links to do so, which would have been very difficult to do with beautiful soup. My crawler started from a predefined URL, which I chose to be all movies sorted by Box Office revenue descending, and from there followed the link to every movie on that page retrieving data. Once it scraped all of the fields from the movies on the first page, it followed the link to the next page and began this cycle again. I used Xpath notation to select the content and the links that I wanted to retrieve and exported all of the data into a csv file. 

I did learn something valuable doing this exercise in webscraping on IMDB. I was trying to apply some cleaning methods (.replace and .strip) on the values returned from my response object inside my script, and when those fields didn't exist for some of the pages the Spider would fail. It could not perform a string operation on `None`, so my spider wasn't returning *any* of the information from that page. I could have nested the operation inside of a conditional (`if/else`), but I found this to get messy very fast and instead I opted to do most of the cleaning in Pandas. In the future I plan to write a function that check is the item is `None` and not them perform some operation. 

## Cleaning and Manipulation with Pandas
Once I had all of my data stored in a .csv file, I used the Pandas library to import the data into a DataFrame structure for cleaning. I started with the usual looks, `df.info()`, `df.describe()`, and `df.isna()mean()`. I came to realize that although I had scraped information for 80,000+ titles, I only had budget/revenue data for ~9,500. I confirmed this by looking on the IMDB website and performing searches on a handful of films missing these fields to ensure the issue wasn't on my end. I considered trying to find a relationship between star rating and revenue so that I could grow my dataset, however I was not convinced in the strength of that relationship so I ended up droppping most of my data. I wanted to be able to perform a thorough EDA, so I cleaned all of the fields even if I didn't feel they had a great chance at being impactful (such as run-time). 

Working with the remaining ~9,500 films I cleaned the following fields: 
- **`date`** was split to remove the country name and then converted into a Pandas datetime object. A new column named `country` was created to capture this information.
  - Before:`3 April 2009 (South Africa)`
  - After: `2009-04-03`
      ```javascript
      # Remove the country code from the string, then convert to pandas datetime 
      date = df['date'].str.split("(",n=1,expand=True)
      df['date'] = pd.to_datetime(date[0],infer_datetime_format=True)
      df['country'] = date[1].str.replace(")","")
      ```
- **`run_time`** was split to separate the hours and minutes. Hours were cast to integers, converted to minutes, then added together. What was tricky here was sometimes there were hours and minutes, and other only one of these fields, so I wrote a function to handle these different scenarious with conditionals.
  - Before:`1h 34min`
  - After: `94`
      ```javascript
      def to_minutes(string):
          ''' Returns minutes as an integer given a string containing hours and minutes '''
          hours_in_minutes = 0
          minutes = 0
          if  "h" in string:
              hours_in_minutes = int(string.split()[0].replace("h",'')) * 60
          if "min" in string:
              minutes = int(string.split()[1].replace('min',''))    
          return hours_in_minutes + minutes

      df['run_time_min'] = df['run_time'].map(to_minutes)
      df = df.drop(columns=['run_time'])
      ```



