---
layout: post
title: Scrapy tutorial #1 - Basics of using Scrapy framework to scrape data from web
---

Scrapy is a web scraping framework with a lot of functionality. In this tutorial I will look into the basics of how to download and use scrapy to scrape data from a website.
---
# Installing scrapy and basics


## Installation
It is a good practice to use python virtual environment to isolate the project.
To create a python virtual environment use command `python -m venv venv`.
Virtual environment can be activated by `source venv/bin/activate` and deactivated with `deactivate` command.
### Installing scrapy 
```bash 
$ pip install scrapy
```
### Creating a scrapy project
```bash
$ scrapy startproject books # creates a scrapy project
$ cd books # change to the newly created project directory.
$ scrapy genspider books_spider https://books.toscrape.com # Creates spider with name books_spider for the chosen website
```

## Scrapy files
Scrapy creates alot of files when creating the project and it can be confusing at beginning of what they do and their purpose.
```
- books
  - scrapy.cfg            # Scrapy deployment config file
  - books/                 
    - items.py            # Structure the data found by the spider
    - middlewares.py      # How scrapy makes the requests
    - pipelines.py        # Used to clean the data and connect to database or output
    - settings.py         # Project settings
    - spiders/            # Spiders are saved here
      - books_spider.py   # Spider for the books.toscrape.com website
```

## Using scrapy shell to find the data on webpage
`$ scrapy shell https://books.toscrape.com`

``` bash
>>> response.css("article.product_pod") # gets all the books on page
>>> response.css("a::attr(title)").get() # gets the first books title
>>> response.css("a::attr(title)").getall() # using .getall() gets all the book titles
>>> response.css("div.product_price p.price_color::text").get() # gets the first books price
```

Putting the code into the spider file in books/books/spiders/books_spider.py
```python
import scrapy

class BooksSpiderSpider(scrapy.Spider):
    name = "books_spider"
    allowed_domains = ["books.toscrape.com"]
    start_urls = ["http://books.toscrape.com/"]

    def parse(self, response):
    books = response.css("article.product_pod")
    for book in books:
        yield {
            'book_name': book.css("h3 a::attr(title)").get(),
            'price': book.css("div.product_price p.price_color::text").get(),
            'link': book.css("h3 a").attrib['href']
        }
```

Now the spider gets the book name, price and link for every book on the page (20 on single page).

## Running the spider
To run the scrapy spider use `scrapy crawl <spiderName>`
`scrapy crawl books_spider -O books.json` in project directory runs the spider and saves the results into json file

# Summary
This was a short tutorial about installing and using the scrapy framework to get data from webpage.
On next tutorial I will be showing how to do pagination to get all the books on different pages.
