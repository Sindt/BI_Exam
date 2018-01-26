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

## Part 1.2 Data Preprocessing
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
    
```python
import urllib.request, json 
data = ''
with urllib.request.urlopen("http://138.68.86.32/locs2.json") as url:
    data = json.loads(url.read().decode())
    
def get_location_for(address):
    try:
        print(data[address])
    except:
        pass

to_code_addresses = df['where'].unique()
locations = [get_location_for(a) for a in to_code_addresses]
```

    [55.671907, 12.5552659]
    [55.6680529, 12.5439823]
    [55.679125, 12.577278]
    [55.673854, 12.597078]
    [55.76075609999999, 12.4568021]
    [55.6815933, 12.5761475]
    [55.6252114, 12.5736741]
    [55.68111589999999, 12.5528412]
    [55.68379270000001, 12.5762312]
    [55.6575405, 12.5905654]
    [55.73196, 12.3625764]
    [55.6478938, 12.6049463]
    [55.6712453, 12.5908425]
    [55.612953, 12.356095]
    [55.6865864, 12.4914135]
    [55.6680687, 12.5444302]
    [55.691864, 12.5345859]
    [55.691235, 12.5592894]
    [55.6711643, 12.6203919]
    [55.6966243, 12.5446128]
    [55.65525909999999, 12.2956019]
    [55.678747, 12.498775]
    [55.6766342, 12.5836015]
    [55.780023, 12.480222]
    [55.703503, 12.5763167]
    [55.6715744, 12.5603597]
    [55.7320396, 12.5406703]
    [55.66796, 12.5480313]
    [55.6580822, 12.6092902]
    [55.68506199999999, 12.5892159]
    [55.6915274, 12.560277]
    [None, None]
    [55.6756788, 12.5735544]
    [55.736265, 12.5554159]
    [55.681945, 12.582381]
    [None, None]
    [55.6725191, 12.5872964]
    [55.68221, 12.5525251]
    [55.7305364, 12.3522152]
    [55.698861, 12.5885095]
    [55.686089, 12.533412]
    [55.68100339999999, 12.5303327]
    [55.6808557, 12.5331994]
    [55.6787723, 12.5314573]
    [55.684364, 12.541787]
    [55.69547499999999, 12.581511]
    [55.76257409999999, 12.5927498]
    [55.7442907, 12.4946641]
    [55.7165482, 12.533708]
    [55.67933859999999, 12.5723587]
    [55.6729387, 12.5942102]
    [55.68181879999999, 12.4566722]
    [55.7563963, 12.4763035]
    [55.79529, 12.4630711]
    [55.7589175, 12.5968742]
    [55.6817515, 12.5894416]
