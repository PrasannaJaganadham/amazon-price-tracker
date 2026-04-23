# Amazon Price Tracker

A small Python project I built to learn web scraping. It checks the price of a product on Amazon every day, saves the data to a CSV file, and sends me an email if the price drops below my target.

---

## What it does

I was following some YouTube tutorials on web scraping and wanted to actually build something useful instead of just watching. So I made this price tracker for a Pokémon Charizard T-shirt I wanted to buy on amazon.fr (I'm based in France).

The script does four things:

1. Opens the Amazon product page and reads the HTML
2. Pulls out the product title and current price
3. Saves it to a CSV file with today's date
4. Emails me if the price drops below 20 euros

If I leave the script running with a 24-hour sleep timer, it tracks the price every day automatically.

---

## Tools I used

- **Python 3.13**
- **BeautifulSoup4** - for reading the HTML
- **requests** - for downloading the page
- **smtplib** - Python's built-in email library (used Gmail SMTP)
- **csv** - for saving the data
- **pandas** - just to view the CSV cleanly in Jupyter
- **Jupyter Notebook** - I did everything in a notebook

---

## Files in this repo

```
amazon-price-tracker/
├── amazon_web_scraping.ipynb    # The main notebook
├── AWSDataset.csv               # The price data my scraper collected
└── README.md                    # This file
```

---

## The product I tracked

- Pokémon Charizard Men's Short Sleeve T-Shirt (Black)
- amazon.fr
- Current price: 22.00 euros
- My target: under 20 euros

---

## How the code is organized

I split the code into two functions:

**`check_price()`** - does the scraping. It sends a request to Amazon with some fake browser headers (so Amazon doesn't block me), reads the HTML with BeautifulSoup, pulls out the title and price, and adds them to the CSV. At the end, it checks if the price is below 20 and calls the email function if it is.

**`send_mail()`** - sends the email through Gmail's SMTP server on port 465. I had to set up a Gmail App Password for this because Google doesn't let you use your regular password for scripts.

Then there's a `while True` loop at the bottom that runs `check_price()` and sleeps for 24 hours, so the script keeps tracking every day.

---

## How to run it

### What you'll need

```bash
pip install beautifulsoup4 requests pandas
```

### Setup

1. Clone or download this repo
2. Open the notebook in Jupyter
3. In the `send_mail()` function, replace `YOUR_EMAIL@gmail.com` with your Gmail and `YOUR_APP_PASSWORD` with your 16-digit app password
   - To get an app password: go to your Google Account > Security > enable 2-Step Verification > then go to App Passwords and generate one
4. If you want to track a different product, just change the URL in `check_price()` to any Amazon product link
5. Run the cells in order

One thing to watch out for: don't run the `while True:` loop cell while testing! It will block Jupyter forever. I kept it commented out until the script was fully working.

---

## Sample data from my CSV

| Title | Price | Date |
|---|---|---|
| Pokémon Charizard Men's Short Sleeve T-Shirt | 22.00 | 2026-04-23 |
| Pokémon Charizard Men's Short Sleeve T-Shirt | 22.00 | 2026-04-24 |
| Pokémon Charizard Men's Short Sleeve T-Shirt | 21.50 | 2026-04-25 |

---

## Problems I ran into (and how I fixed them)

### Amazon blocked my script
When I first ran `requests.get()`, Amazon gave me some weird page or CAPTCHA. I learned that Amazon checks the `User-Agent` header to detect bots. I added a full set of browser headers (User-Agent, Accept-Language, etc.) copied from my own Chrome browser (found using F12 > Network tab) and after that it worked.

### The tutorial's price selector didn't work
The tutorial I followed used `soup.find(id='priceblock_ourprice')` but this gave me a `NoneType` error. I opened Chrome DevTools on the product page and realized that ID doesn't exist anymore - Amazon changed the HTML. The current version uses `<span class="a-price-whole">` and `<span class="a-price-fraction">` for the price. I updated the code and added a fallback to `a-offscreen` in case those change too.

### Price had weird formatting
Since I'm on amazon.fr, the price showed up as "22,00" with a comma instead of a dot. Python's `float()` can't convert that, so I had to use `.replace()` to clean it up before converting.

### Gmail wouldn't let me log in
Using my normal Gmail password with smtplib gave an authentication error. Google blocks this for security. I had to turn on 2-Step Verification and generate a 16-character "App Password" specifically for the script.

### CSV kept getting messed up
When I ran the append code multiple times, the file ended up with duplicate "Title, Price, Date" rows in the middle. That's because I was writing the header every time. I fixed it by only writing the header once (when creating the file) and then just appending data rows after that.

### Jupyter got stuck
I accidentally used "Run All Cells" while the `while True:` loop was active. The kernel got stuck for 11 minutes because the loop never ends. Had to restart the kernel and comment out the loop during testing.

---

## What I learned

- How HTTP headers work and why websites block bots
- How to use Chrome DevTools to inspect HTML and find the right selectors
- Why tutorials go out of date - the websites they scrape keep changing
- How to write Python functions and split code into reusable parts
- Why `while True:` loops are dangerous in notebooks
- How email works at the protocol level (SMTP)
- How to handle errors with if-checks so the script doesn't crash

---

## What I want to add next

These are ideas I'd like to try when I have more time:

- Track multiple products instead of just one
- Make a chart showing how the price changed over time (matplotlib)
- Store data in SQLite instead of CSV
- Put the Gmail password in a `.env` file instead of hardcoding it
- Try deploying it on AWS Lambda so my laptop doesn't need to stay on
- Track the same product on amazon.fr, amazon.de, and amazon.co.uk to compare prices

---

## About me

I'm Prasanna, a student at Burgundy School of Business in Dijon, France. I'm doing a dual degree in Data Science and Management, and looking for Data Analyst or BI Developer roles in France and Europe starting June 2026.

- Email: jaganadhamprasanna@gmail.com
- LinkedIn:https://www.linkedin.com/in/prasanna-jaganadham-190780228/

---

## Note

This is a learning project. Scraping Amazon technically goes against their Terms of Service, so I'm only using it for personal price tracking and portfolio purposes. If you try this, be respectful - don't spam the website, add delays between requests, and use it for your own learning only.
