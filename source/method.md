# Scraping Methods

## URL
URL is the webpage link. It contains "PATH" and searching parameters.

A good url looks like this:
`https://we.51job.com/pc/search?jobArea=010000&keyword=xxx`

The part before `?` is the PATH and won't change.

The part after `?` are the parameters and indicates the keywords you care, seperating by `&`. In this example, `jobArea` is a parameter name, and `010000` is the code for Beijing, the area we are caring for this time. 

How to know the city code? It varies according to the website developer. You may find a json file containing the city codes by inspecting the website. However, you can just copy the url when you are browsing the website.

Take another example:
`https://www.zhipin.com/web/geek/job?query=xxx&city=101010100&page=1`

`query` is the same with `keyword`; this time Beijing has another city code; and there's a number indicating the page. With or without the page parameter are both common to see. Changing the page number to scrape is easy. Otherwise, you need to figure out how to click the "next" button to get new information in the same url.

The page information sometimes lives in the PATH too:
`https://bj.ganji.com/job/pn1/?key=xxx`

## Request
If the website has an API, whether open or hidden, the best way is always to do GET request then other user-mimicking scraping methods.
```python
import requests
response = requests.get(url, headers=headers, cookies=cookies)
```
The key is to find the right url and the parameters for the header. Not using header is okay, but not very polite, and you get higher chances to get blocked.

Sometimes the website has a tutorial generously written for developers to use their API. If not, you can try right-clicking on anywhere on the website - inspect - network - Fetch/XHR, and do the searching one more time.  

Here is a more detailed tutorial of [Finding Undocumented APIs](https://inspectelement.org/apis.html) written by Leon Yin.

If the request doesn't work and you want to know the reason:
```python
if response.status_code == 200:
    return response.text
else:
    print(f"Request failed: {response.status_code}")
    return None
```

After getting the html by request, then usually parse it with **beautifulsoup**:
```python
from bs4 import BeautifulSoup
soup = BeautifulSoup(response, 'html.parser')
job_items = soup.find_all('li', class_='job_item')
for item in job_items:
    job_title = item.find('span', class_='name')
```

You can locate and select the text by `class(.)` and `id(#)`. 

When the website is not static and changes the class name, you can locate the text by finding its parent element - get the children element list - locate by order.

For CSS selectors, you may find this [cheatsheet](https://frontend30.com/css-selectors-cheatsheet/) little game helpful.

## Playwright
If request doesn't work, some packages can help you disguise as a user. They can be Selenium, Playwright, or DrissionPage. The newer the package is, the less likely it will be recognized and blocked by the website.

The package that Columbia Data Journalism Program chooses to teach (at least in 2024) is Playwright. It's newer then Selenium and can apply in most cases.

```python
from playwright.sync_api import sync_playwright
with sync_playwright() as p:
    browser = p.chromium.launch(headless=False, executable_path="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome",args=["--mute-audio"]) # for Mac, or find your own path for the browser
    browser = p.chromium.launch(headless=False, executable_path="C:/Program Files (x86)/Microsoft/Edge/Application/msedge.exe",args=["--headless=new","--mute-audio"]) # for Windows
```

`headless=False` means you can see the browser running. If you feel confident enough and don't want to be disturbed, you can turn it to `True`. 

If the website requires an user account, you can store the login information (cookies) in a file and set it as the "context" for your browser.

```python
context = browser.new_context(storage_state=storage_state_file)
page = context.new_page()
page.goto(URL)
```

## Drissionpage
If Playwright doesn't work either, try Drissionpage instead. In our case, among 6 job seeking platform websites in China, only 1 needs to use Drissionpage. However, using it at the first place might make the job easier.

```python
from DrissionPage import ChromiumPage, ChromiumOptions
page = ChromiumPage()
page.get(URL)
page.wait.load_start()
```

Here are some common functions for html parsing, comparing beautifulsoup, playwright and drissionpage:
|Beautifulsoup|Playwright|Drissionpage|
|--|--|--|
|.find()|.query_selector()|.ele()|
|.find_all()|.query_selector_all()|.eles()|
|.get()|.get_attribute()|.attr()|
|.text()|.inner_text()|.text()|