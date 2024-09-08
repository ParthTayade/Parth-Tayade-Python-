# Parth-Tayade-Python-
import requests
from  bs4 import BeautifulSoup
import csv

# link = 'https://www.flipkart.com/search?q=phone&otracker=search&otracker1=search&marketplace=FLIPKART&as-show=on&as=off'
link = input('url: ')

r = requests.get(link)
scrap = BeautifulSoup(r.content, 'html.parser')

data = []
# products = scrap.findAll('div', class_='tUxRFH')
pages = int(scrap.find('span', class_='BUOuZu').text.split(' ')[3])

for page in range(0, pages):
    r = requests.get(link+f'&page={page+1}')
    scrap = BeautifulSoup(r.content, 'html.parser')
    products = scrap.findAll('div', class_='tUxRFH')
    print(f'Scraping Page - {page+1} / {pages}')
    for product in products:
        try:        
            name = product.find('div', class_='KzDlHZ').text or product.find('div', class_='wjcEIp')
            price = product.find('div', class_='Nx9bqj').text
            rating = product.find('div', class_='XQDdHH').text
            reviews = product.find('span',class_='hG7V+4').find_next_sibling('span').text[:-len(' Reviews')]

            try:   
                product_link = product.find('a', class_='CGtC98')['href']
                x_scrap = BeautifulSoup(requests.get('https://www.flipkart.com' + product_link).content, 'html.parser')
                x_scrap.findChild('div')
                description = x_scrap.find('div', class_='yN+eNk').text
            except Exception:
                description = 'NA'
        except Exception:
            print('Either there\'s problem with link provided or connection issue between software and website (so try again)...')

        data.append([name, price, rating, reviews, description])

if len(link) <= 39:
    file = 'flipkart.csv'
else:
    file = link.replace('https://www.flipkart.com/search?q=', '')
    file = file[:file.index('&')] + '.csv'

with open(file, 'w', newline='', encoding='utf-8') as f:
    writer = csv.writer(f)
    writer.writerow(['Name', 'Price', 'Rating', 'Reviews', 'Description'])
    writer.writerows(data)


# https://www.flipkart.com/search?q=laptop&otracker=search&otracker1=search&marketplace=FLIPKART&as-show=on&as=off
# https://www.flipkart.com/search?q=laptop&otracker=search&otracker1=search&marketplace=FLIPKART&as-show=on&as=off&page=2
