# Scraping Production data for Oil and Gas Wells and Storing the data in a SQLite3 Database

**Description:** In this project I use Scrapy to retrieve and organize data for ~15,000 oil and gas wells. There are two structures of data that need to be scraped, one being the general information for each well, and the second being the production history of the well (time-series format). Two Scrapy Spiders are created as well as two pipelines to push this data into a local, lightweight SQLite 3 database making the data suitable for analysis. 

## Getting the URLs (start_urls)
The links for each subsequent page are not found within the HTML of previous pages, so new responses cannot be generated this way. URLs will need to be explicitly listed in the start_urls attribute. Inspecting the URL, the tail end has a query string parameter with a file number. The website allows for the export of these file numbers with some other useful metrics. The file numbers were exported and filtered as to only keep those for relevant wells, then imported into Python and used with a Python list comprehension to create this master list. 

```javascript
df = pd.read_csv(r'C:\Users\johno\Python\CSVs\file_numbers.csv')
file_nums = df['FileNo'].astype(str).tolist()

url = 'https://www.dmr.nd.gov/oilgas/feeservices/getwellprod.asp?filenumber='

class HeadersSpider(scrapy.Spider):
    name = 'headers'
    start_urls = [url + file_num for file_num in file_nums]
```

## Getting Access
The data scraped here comes from a webpage that requires a subscription, and therefore has sign-in credentials. Default request headers are overwritten in the settings.py file and basic credentials are provided with base64 encoding

```
DEFAULT_REQUEST_HEADERS = {'Authorization': 'Basic am9obm9kb25uZWxsOiNIdW1ibGU0VFg='}
```

## Parse Method (for headers/general data)
Each page looks slighly different, fields are missing/out of order/etc. The structure of the HTML is irregular, and requeres some creativity. I needed to by very specific with queries on the response object, therefore xPath was used. I selected the node which contained some text (label), then selected the following sibling node to get the data we need.

After some unsucessful tests in the Scrapy Shell, it was found that some <tbody> elements were left out by the developer, and were added in by Chrome. Replacing these elements with a forward slash was the remedy. 

<img src="images/scrapy/yielded dictionary.PNG?raw=true"/>

## Parse Method (for production/time-series data)
This was more straightforward as this data is organized in a table and is consistent betweeen pages. A varaible is created for the collection of table rows, and is then looped through as data for each row is extracted. I chose to also grab another field from the page outside of the table to become my common key between these two datasets. 

<img src="images/scrapy/production yielded dictionary.PNG?raw=true"/>

## Pipelines
Two pipelines were created allowing for the creation of two separate but related tables in our database. I chose to use SQLite 3 due to its lightweight nature. Here I will show the Production pipeline, as it is short and essentially the same as the other. A new class is defined with 3 methods. The method open_spider creates the connection to our database (and creates the database if it does not exist), it creates the table with the specified fields and data types, then commits those changes. The close_spider is called at the very end and closes our connection to the database. 

<img src="images/scrapy/open and close spider.PNG?raw=true"/>

In the process_item method we use our cursor object along with an INSERT statement to fill our table. I chose to use the .get( ) method instead of the usual indexing operator to avoid key errors. The changes are committed and we return our item. 

<img src="images/scrapy/process item.PNG?raw=true"/>

The pipeilnes were activated by definition in the ITEM_PIPELINES dictionary with their priority numbers. 

<img src="images/scrapy/ITEM_PIPELINE.PNG?raw=true"/>

## Result
The SQLite database now has 2 tables, one for header data, the other with production/time-series data. Both tables are related by the UWI key and can be related with a simple SQL JOIN statement. Originally, this data was essentially useless due to its structure and lack of accessability and it is now is a structured, organized, and accessible database. 

<img src="images/scrapy/database_result.PNG?raw=true"/>


