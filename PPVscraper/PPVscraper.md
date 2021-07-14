# Web Scrape Vaccination Administration Centres in Malaysia
The objective of this project is to scrape vaccination administration centres (PPVs) in Malaysia from the [JKJAV](https://www.vaksincovid.gov.my/en/ppv/) website. The result will be a CSV file containing data of state, district, name of PPV and coordinates of PPV (latitude & longitude).

Python packages such as [Selenium](https://www.selenium.dev/documentation/en/) and [Pandas](https://pandas.pydata.org/docs/) were used in this project.

## Usage
```ppv_cleaned.csv``` - data cleaning have been perfromed and it contains only **unique** PPVs. (recommeded for usage)
```ppv_full.csv``` - scraped directly from the JKJAV website and contains duplicate PPVs.

## Warning
Coordinates of the PPVs are **NOT** 100% accurate as some of the PPV names couldn't be found on Google Maps, thus estimate coordinates are used.
