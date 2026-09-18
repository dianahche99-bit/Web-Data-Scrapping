Web Data Scraping — Countries of the World

Scraping country data (name, capital, population, and area) from scrapethissite.com's "Countries of the World" page using Python, and exporting the results to a clean CSV file.

Overview

A complete web scraping pipeline: fetch a live webpage, parse its HTML structure, extract repeating data blocks, and store the results as structured data. Unlike a simple table-based scrape, this page required identifying and looping through country "card" `<div>` elements rather than table rows — reinforcing that the same fetch → parse → find → collect pattern adapts to different HTML structures.

Tools:Python, Google Colab
Libraries:`requests` (fetch HTML)
          `BeautifulSoup` (parse HTML) 
          `pandas` (structure and export data)
Source: [scrapethissite.com/pages/simple](https://www.scrapethissite.com/pages/simple/) — no external dataset needed, scraped directly

Approach

1. Fetch — Retrieved the page with `requests.get()`, confirmed success via a `200` status code
2. Parse— Parsed the HTML with BeautifulSoup and located all 250 country blocks using `find_all('div', class_='country')`
3. Extract — For each country block, pulled the name, capital, population, and area from their respective tags (`h3.country-name`, `span.country-capital`, `span.country-population`, `span.country-area`) using `.get_text(strip=True)`
4. Structure— Stored each country's data as a dictionary, appended to a list, then loaded into a pandas DataFrame
5. Export— Saved the final DataFrame to `countries.csv`

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd

url = "https://www.scrapethissite.com/pages/simple/"
response = requests.get(url)

soup = BeautifulSoup(response.text, "html.parser")
countries = soup.find_all('div', class_='country')

data = []
for c in countries:
    name = c.find('h3', class_='country-name').get_text(strip=True)
    capital = c.find('span', class_='country-capital').get_text(strip=True)
    population = c.find('span', class_='country-population').get_text(strip=True)
    area = c.find('span', class_='country-area').get_text(strip=True)
    data.append({"Country": name, "Capital": capital, "Population": population, "Area": area})

df = pd.DataFrame(data)
df.to_csv("countries.csv", index=False)
```

Results

Successfully scraped **250 rows × 4 columns** (Country, Capital, Population, Area)
Verified extraction accuracy against known values — e.g. Andorra: capital Andorra la Vella, population 84,000, area 468.0 km²
Final dataset exported cleanly to `countries.csv`, ready for further analysis

What I would do differently

With more time, this could be extended to scrape additional pages on the site (e.g. the JavaScript-rendered or paginated versions), which would require handling dynamic content or pagination rather than a single static page.

Links

- [Full notebook (Google Colab)](https://colab.research.google.com/drive/1CwByVmYFCUVLmpSYfUpSKxQPKdmVOuq7?usp=sharing)
- Output data: `countries.csv` (in this repo)
