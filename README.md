# SalesTaxWebScraper

Building a program that can consolidate and store United States 
sales tax data

------------------------------------------------------------------------

United States sales tax needs to be collected based on the location 
that an item is purchased. This means that even if we sell coffee from 
Arkansas, if the coffee is purchased in New York we pay sales tax to 
New York. Unfortunately, I was not able to find a free source of 
consolidated US sales tax information for our business records. Therefore, 
I decided to try and learn how to use BeautifulSoup (a powerful Python 
tool that allows a user to parse HTML code for valuable information) to 
collect this data from websites! The result is

Tax_Getter.ipynb!

------------------------------------------------------------------------
Step 1: Finding the necessary data

The first challenge is finding webpage(s) with easily accessible 
data for scraping. Some websites don't like it when you use a robot to
traverse their website. This can be for a number of reasons to do with
security and privacy. A website's permissions for web-scraping can be
found by going to the website's /robots.txt page

(ex. rwandadeluxecoffee.com/robots.txt), 

or by using my URL checking program,

Quickcheck.ipynb

to see what data you can get from a URL.

I eventually came across two useful websites:

http://www.sale-tax.com/ - US Sales tax information for each state and,

https://www.zip-codes.com/ - US Zip codes for each state.

*NOTE that taxes are not necessarily conducted by zipcode. Some states
and counties have bizzare sales tax rules and regulations. Zip code
seems to be the easiest metric to associate with sales tax 
for the majority of states, but I am no expert on taxes!*

-------------------------------------------------------------------------
Step 2: Extracting the Data

The HTML code could be extracted from these websites using Python's 
"requests" library. Once the HTML information was collected, BeautifulSoup
was used to get the total sales tax due for each city in a given state. 
Then, BeautifulSoup was used to extract the zipcodes of each state.

Thankfully both websites were easy to traverse because the URLs both 
followed an easy pattern. If I wanted to access the sales tax data for a
state like Arkansas, I could simply do so by adding

/Arkansas

to the end of sale-tax.com. If I wanted to access a state with a space
in the name (like New York) I would simply add

/NewYork

to the end of sale-tax.com.

While slightly different for zip-codes.com, it was still managable. If 
I wanted zipcodes for Arkansas neighborhoods, I would add

/state/ar.asp

to the end of zip-codes.com. If I wanted to get the zipcodes for New York
I would use NY, California CA, etc.

This meant that a list of states and their abbreviations (information 
available on the front page of sales-tax.com) I could easily iterate
through all 50 states of data.

-------------------------------------------------------------------------
Step 3: Cleaning and Storing the Data

Once the data was filtered in it was a mess! I needed to right-click on the
pages and view the source code of the website to look for the information
that I needed. Then, consulting the (very well-written) BeautifulSoup
documentation and the page's HTML source code, I was able to collect the
desired information: sales tax percentages and zip codes!

I stored this information in the form of dictionaries of lists. This way,
the data could be easily converted into a pandas dataframe. I made one 
function to collect and sort data for the sales-tax web page and another
for the zip-codes web page. I also made sure to put a 250 ms delay between
requests (I don't want to over-ping those sites and annoy them / cause 
server issues).

Finally, the two dataframes were merged on their common datapoint (city) 
and saved as a csv file, 

TaxData.csv

which is current as of October 9, 2020. Updating this file takes less than
one minute and is certainly quicker than manually searching for sales tax
information!

Once created, I moved around some of the columns around in the CSV to 
present the data more neatly. Of course, there are more ways to improve the
quality of data in this CSV. For one, more information could be collected 
about where the combined taxes are distributed between states, counties, etc.
within a given zip code. For now, this provides us with the basic total 
amount of sales tax that needs to be collected in a given location!

------------------------------------------------------------------------
Step 4: FIX A BIG ISSUE!

As I was about to upload the CSV file I wondered why it was over 100,000
rows. I looked up how many zipcodes existed in the United States and 
learned that the number was around 40,000. Therefore, I knew that I had
made a mistake somewhere. 

When I was merging the dataframes, I was only merging by city. This meant
that places like Hollis, NY and Hollis, OK were assigned both their own
zipcode AND each other's zipcode!

To fix this issue, I added the state abbreviation to the zip_codes dictionary
and merged the dataframes first by state THEN by city. That way, each state's
city got its own zipcode properly assigned.

This merge caused a much more reasonable 39,691 rows of data. With 40,589 
zip codes in the original dictionary, I did lose 898 zipcodes. This can be
looked into later if the situation calls for it, but even with the loss of
these 898 zipcodes that still leaves 97.8% of the zipcodes accounted for!
