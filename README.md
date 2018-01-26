# Exam

## Part 1.1 Data Collection:



```python
import os
import csv
import requests
import platform
import statistics
import matplotlib
matplotlib.use('agg')
import matplotlib.pyplot as plt

import bs4
import requests
import csv
import os
```


```python
class Scraper:
    
    def __init__(self, url):
        self.url = url
        
    def parseToInt(self, text):
        try:
            return int(text)
        except ValueError:
            return text    

    def scrape(self):
        data = []
        
        
        r = requests.get(self.url)
        soup = bs4.BeautifulSoup(r.content.decode('utf-8'), 'html5lib')
        
        table = soup.find('table', {'class': 'printbredde'})
        what_rows = table.find_all('h3')
        rows = table.find_all('td', {'valign': 'top'})
        
        what = ""
        when = ""
        price = ""
        where = ""
        for inforow in rows:
            info = inforow.find('h3')
            span = inforow.find_all('span',{'class':'notranslate'})
            if len(span) > 1:
                whenprice = span[0].text.strip().split('pm.')
            else:
                continue
            if len(whenprice)> 1:
                when = whenprice[0].strip()
                
                if whenprice[1] is not '':
                    price = whenprice[1].strip()
                else:
                    price = 'Ukendt'
                
                    
                if info is not None:
                    what = info.text.strip()
                    
                if span is not None:             
                    where = span[1].text.strip()
               
                decoded_row = (what, where, when, price)
                data.append(decoded_row)
                break
        
        return data
        
        
                                
class Main:
    
    def getHtmls(host):
        
        htmlList = []
        
        r = requests.get(host)
        soup = bs4.BeautifulSoup(r.content.decode('utf-8'), 'html5lib')
        
        table_body = soup.find('body')
        
                               
        rows = table_body.find_all('a')
        
        for row in rows:
            if ".html" in row.text:
                htmlList.append(row.text)
                
        
        return htmlList

        
      
    host = 'http://138.68.86.32'
    htmls = getHtmls(host)
    
    def save_to_csv(data, path):
    
        if os.path.exists(path):
            with open(path, 'a', encoding='utf-8') as output_file:
                output_writer = csv.writer(output_file)
                for row in data:
                    output_writer.writerow(row)
        else:
            with open(path, 'w', encoding='utf-8') as output_file:
                output_writer = csv.writer(output_file)
                output_writer.writerow(['what', 'where', 'when', 'price'])

                for row in data:
                    output_writer.writerow(row)             
    
    out_dir = './out'
    if not os.path.exists(out_dir):
        os.mkdir(out_dir)
    
    for html in htmls:
        url = os.path.join(host, html)
        scraper = Scraper(url)
        data = scraper.scrape()
        save_to_file = os.path.join(out_dir,'scraped_events.csv')
        save_to_csv(data,save_to_file)
        
        

```


```python
import pandas as pd
df = pd.read_csv('out/scraped_events.csv')
df.tail()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>what</th>
      <th>where</th>
      <th>when</th>
      <th>price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>75</th>
      <td>Nytårskoncert med Københavns Kantatekor og -or...</td>
      <td>Stengård Kirke, Triumfvej 2, Kongens Lyngby</td>
      <td>Thur 18 January 2018, 7.30 pm  - 11</td>
      <td>Ukendt</td>
    </tr>
    <tr>
      <th>76</th>
      <td>Nytårskoncert.</td>
      <td>Virum Kirke, Kirkebakken 10, Virum</td>
      <td>Sat 20 January 2018, 4 pm  - 6</td>
      <td>Ukendt</td>
    </tr>
    <tr>
      <th>77</th>
      <td>The Lone Bellow.</td>
      <td>VEGA, Enghavevej 40, Copenhagen V</td>
      <td>Wed 24 January 2018, 8</td>
      <td>(Entrance fee: 220,00 DKK)</td>
    </tr>
    <tr>
      <th>78</th>
      <td>Lis Sørensen.</td>
      <td>Skovshoved Hotel, Strandvejen 267, Charlottenlund</td>
      <td>Sat 27 January 2018, 6.30</td>
      <td>(Entrance fee: 1220,00 DKK)</td>
    </tr>
    <tr>
      <th>79</th>
      <td>Orgelkoncert.</td>
      <td>Garnisonskirken, Sankt Annæ Plads 4, Copenhagen K</td>
      <td>Wed 31 January 2018, 7.30</td>
      <td>(Free admission)</td>
    </tr>
  </tbody>
</table>
</div>




```python
def get_price(price_str):
    #price_regexp = r"(?P<price>\d+)"
    if 'Free admission' in price_str:
        price = 0
    elif 'ratis' in price_str:
        price = 0
    else:
       # m = re.search(price_regexp, price_str)
        try:
            price = int(price_str)
        except:
            price = None
    return price        

for el in df['price'].values:
    try:
        price = el.split(':')[1].split(' ')[1]
        print(get_price(price))        
    except:
        pass
       
        
```

    105
    None
    None
    300
    None
    99
    50
    None
    55
    180
    60
    110
    None
    None
    60
    250
    180
    None
    None
    None
    205
    98
    None
    100
    None
    None
    90
    None
    None
    None
    100
    260
    175
    100
    0
    149
    None
    None
    248
    None
    None
    None
    None
    None

