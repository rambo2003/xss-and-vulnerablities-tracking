import requests
from bs4 import BeautifulSoup

def scan_vulnerabilities(url):
    print(f"Scanning {url} for vulnerabilities")
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')

    forms = soup.find_all('form')
    print(f"Found {len(forms)} forms")

    for form in forms:
        action = form.get('action')
        method = form.get('method', 'get').lower()
        inputs = form.find_all('input')
        
        data = {}
        for input in inputs:
            name = input.get('name')
            if name:
                data[name] = 'test'

        if method == 'post':
            res = requests.post(url + action, data=data)
        else:
            res = requests.get(url + action, params=data)

        if 'sql' in res.text.lower() or 'error' in res.text.lower():
            print("Possible SQL Injection Vulnerability found!")
        if '<script>' in res.text.lower():
            print("Possible XSS Vulnerability found!")

url = input("Enter website URL: ")
scan_vulnerabilities(url)        
