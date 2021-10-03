# Part 1.1 - Scrapping App Reviews

Scrapping app reviews from Google Play Store (GPS) and Apple Store (AS) for the following apps:
- Syfe
- Endowus
- StashAway

Part 1: Scrap reviews from Apple Store  
Part 2: Scrap reviews from Google Play Store  
Part 3: Combining both reviews

Reference: 
- Apple Store Scrapper: https://python.plainenglish.io/scraping-app-store-reviews-with-python-90e4117ccdfb
- Google Play Store Scraper: https://python.plainenglish.io/scraping-storing-google-play-app-reviews-with-python-5640c933c476


```python
## Importing relevant libraries
import pandas as pd
import numpy as np

# for scraping app info from App Store
from itunes_app_scraper.scraper import AppStoreScraper

# for scraping app reviews from App Store
from app_store_scraper import AppStore

# for scraping app reviews from GPS
from google_play_scraper import app, Sort, reviews

# for pretty printing data structures
from pprint import pprint

# for keeping track of timing
import datetime as dt
from tzlocal import get_localzone

# for building in wait times
import random
import time
```

# Part 1 - Scrap reviews from Apple Store


```python
## Read in file containing app names and IDs
apple_app_df = pd.read_excel('app_info.xlsx', sheet_name='apple')
print(f"""
Printing first few rows of app's info in the csv file:
------------------------------------------------------
{apple_app_df.head()}
""")

## Get list of app names and app IDs
apple_app_names = list(apple_app_df['app_name'])
apple_app_ids = list(apple_app_df['iOS_app_id'])
```

    
    Printing first few rows of app's info in the csv file:
    ------------------------------------------------------
        app_name                 iOS_app_name  iOS_app_id
    0       Syfe           syfe-invest-better  1497156434
    1    Endowus  endowus-invest-cpf-srs-cash  1531067679
    2  StashAway    stashaway-invest-and-save  1229966330
    



```python
## Set up App Store Scraper
scraper = AppStoreScraper()
apple_app_store_list = list(scraper.get_multiple_app_details(apple_app_ids))
```

    https://itunes.apple.com/lookup?id=1497156434&country=nl&entity=software
    https://itunes.apple.com/lookup?id=1531067679&country=nl&entity=software
    https://itunes.apple.com/lookup?id=1229966330&country=nl&entity=software



```python
# Converting list into dataframe
apple_app_info_df = pd.DataFrame(apple_app_store_list)
```

Given that there are no user rating counts, we can ignore itunes ratings in our analysis. 

## Scrapping App Reviews from Apple Store


```python
# Empty list for storing reviews
apple_app_reviews = []

## Set up loop to go through all apps
for app_name, app_id in zip(apple_app_names, apple_app_ids):
    
    # Get start time
    start = dt.datetime.now(tz=get_localzone())
    fmt= "%m/%d/%y - %T %p"
    
    # Print starting output for app
    print('---'*20)
    print('---'*20)    
    print(f'***** {app_name} started at {start.strftime(fmt)}')
    print()
    
    # Instantiate AppStore for app
    app_ = AppStore(country='sg', app_name=app_name, app_id=app_id)
    
    # Scrape reviews posted since February 28, 2020 and limit to 10,000 reviews
    app_.review(how_many=10000,
                after=dt.datetime(2020, 2, 28),
                sleep=random.randint(20,25))
    
    reviews = app_.reviews
    
    # Add keys to store information about which app each review is for
    for rvw in reviews:
        rvw['app_name'] = app_name
        rvw['app_id'] = app_id
    
    # Print update that scraping was completed
    print(f"""Done scraping {app_name}. 
    Scraped a total of {app_.reviews_count} reviews.\n""")

     # Convert list of dicts to Pandas DataFrame
    review_df = pd.DataFrame(reviews)
    apple_app_reviews.append(review_df)
    
    # Get end time
    end = dt.datetime.now(tz=get_localzone())
    
    # Print ending output for app
    print(f"""Successfully wrote {app_name} reviews to df
    at {end.strftime(fmt)}.\n""")
    print(f'Time elapsed for {app_name}: {end-start}')
    print('---'*20)
    print('---'*20)
    print('\n')
    
    # Wait 5 to 10 seconds to start scraping next app
    time.sleep(random.randint(5,10))
```

    ------------------------------------------------------------
    ------------------------------------------------------------
    ***** Syfe started at 10/03/21 - 16:20:08 PM
    


    2021-10-03 16:20:09,532 [INFO] Base - Initialised: AppStore('sg', 'syfe', 1497156434)
    2021-10-03 16:20:09,534 [INFO] Base - Ready to fetch reviews from: https://apps.apple.com/sg/app/syfe/id1497156434
    2021-10-03 16:20:30,811 [INFO] Base - [id:1497156434] Fetched 20 reviews (20 fetched in total)
    2021-10-03 16:21:13,458 [INFO] Base - [id:1497156434] Fetched 57 reviews (57 fetched in total)
    2021-10-03 16:21:13,755 [INFO] Base - [id:1497156434] Fetched 67 reviews (67 fetched in total)


    Done scraping Syfe. 
        Scraped a total of 67 reviews.
    
    Successfully wrote Syfe reviews to df
        at 10/03/21 - 16:21:13 PM.
    
    Time elapsed for Syfe: 0:01:05.326312
    ------------------------------------------------------------
    ------------------------------------------------------------
    
    
    ------------------------------------------------------------
    ------------------------------------------------------------
    ***** Endowus started at 10/03/21 - 16:21:21 PM
    


    2021-10-03 16:21:23,187 [INFO] Base - Initialised: AppStore('sg', 'endowus', 1531067679)
    2021-10-03 16:21:23,188 [INFO] Base - Ready to fetch reviews from: https://apps.apple.com/sg/app/endowus/id1531067679
    2021-10-03 16:21:44,489 [INFO] Base - [id:1531067679] Fetched 20 reviews (20 fetched in total)
    2021-10-03 16:22:27,085 [INFO] Base - [id:1531067679] Fetched 60 reviews (60 fetched in total)
    2021-10-03 16:22:27,500 [INFO] Base - [id:1531067679] Fetched 74 reviews (74 fetched in total)


    Done scraping Endowus. 
        Scraped a total of 74 reviews.
    
    Successfully wrote Endowus reviews to df
        at 10/03/21 - 16:22:27 PM.
    
    Time elapsed for Endowus: 0:01:05.738188
    ------------------------------------------------------------
    ------------------------------------------------------------
    
    
    ------------------------------------------------------------
    ------------------------------------------------------------
    ***** StashAway started at 10/03/21 - 16:22:35 PM
    


    2021-10-03 16:22:36,806 [INFO] Base - Initialised: AppStore('sg', 'stashaway', 1229966330)
    2021-10-03 16:22:36,807 [INFO] Base - Ready to fetch reviews from: https://apps.apple.com/sg/app/stashaway/id1229966330
    2021-10-03 16:22:59,209 [INFO] Base - [id:1229966330] Fetched 17 reviews (17 fetched in total)
    2021-10-03 16:23:43,855 [INFO] Base - [id:1229966330] Fetched 50 reviews (50 fetched in total)
    2021-10-03 16:24:28,497 [INFO] Base - [id:1229966330] Fetched 82 reviews (82 fetched in total)
    2021-10-03 16:25:13,169 [INFO] Base - [id:1229966330] Fetched 119 reviews (119 fetched in total)
    2021-10-03 16:25:57,920 [INFO] Base - [id:1229966330] Fetched 148 reviews (148 fetched in total)
    2021-10-03 16:26:42,669 [INFO] Base - [id:1229966330] Fetched 180 reviews (180 fetched in total)
    2021-10-03 16:27:27,424 [INFO] Base - [id:1229966330] Fetched 213 reviews (213 fetched in total)
    2021-10-03 16:28:12,182 [INFO] Base - [id:1229966330] Fetched 244 reviews (244 fetched in total)
    2021-10-03 16:28:56,825 [INFO] Base - [id:1229966330] Fetched 273 reviews (273 fetched in total)
    2021-10-03 16:29:41,572 [INFO] Base - [id:1229966330] Fetched 309 reviews (309 fetched in total)
    2021-10-03 16:30:26,278 [INFO] Base - [id:1229966330] Fetched 337 reviews (337 fetched in total)
    2021-10-03 16:31:10,974 [INFO] Base - [id:1229966330] Fetched 366 reviews (366 fetched in total)
    2021-10-03 16:31:55,623 [INFO] Base - [id:1229966330] Fetched 391 reviews (391 fetched in total)
    2021-10-03 16:31:55,944 [INFO] Base - [id:1229966330] Fetched 391 reviews (391 fetched in total)


    Done scraping StashAway. 
        Scraped a total of 391 reviews.
    
    Successfully wrote StashAway reviews to df
        at 10/03/21 - 16:31:55 PM.
    
    Time elapsed for StashAway: 0:09:20.442317
    ------------------------------------------------------------
    ------------------------------------------------------------
    
    



```python
 # Convert list of dfs to Pandas DataFrame and write to csv
apple_reviews = pd.concat(apple_app_reviews)
```

# Part 2 - Scrap reviews from Google Play Store


```python
## Extracting data and relevant app names + Ids
google_app_df = pd.read_excel('app_info.xlsx',sheet_name='google')
print(google_app_df.head())

## Get list of app names and app IDs
google_app_names = list(google_app_df['app_name'])
google_app_ids = list(google_app_df['app_id'])
```

        app_name                 app_id
    0       Syfe               com.syfe
    1    Endowus  com.endowus.mobileapp
    2  StashAway      com.awp.stashaway



```python
## Loop through app IDs to get app info
google_app_info = []
for i in google_app_ids:
    info = app(i)
    del info['comments']
    google_app_info.append(info)

## Pretty print the data for the first app
pprint(google_app_info[0])

google_app_infos = pd.DataFrame(google_app_info)
# app_infos_df.to_csv('apps.csv', index=None, header=True)
# google_app_infos
```

    {'adSupported': None,
     'androidVersion': None,
     'androidVersionText': None,
     'appId': 'com.syfe',
     'containsAds': False,
     'contentRating': None,
     'contentRatingDescription': None,
     'currency': None,
     'description': None,
     'descriptionHTML': None,
     'developer': None,
     'developerAddress': None,
     'developerEmail': None,
     'developerId': None,
     'developerInternalID': None,
     'developerWebsite': None,
     'editorsChoice': False,
     'free': None,
     'genre': None,
     'genreId': None,
     'headerImage': None,
     'histogram': [0, 0, 0, 0, 0],
     'icon': None,
     'inAppProductPrice': None,
     'installs': None,
     'minInstalls': None,
     'offersIAP': False,
     'originalPrice': None,
     'price': None,
     'privacyPolicy': None,
     'ratings': None,
     'recentChanges': None,
     'recentChangesHTML': None,
     'released': None,
     'reviews': None,
     'sale': False,
     'saleText': None,
     'saleTime': None,
     'score': None,
     'screenshots': [],
     'size': None,
     'summary': None,
     'summaryHTML': None,
     'title': None,
     'updated': None,
     'url': 'https://play.google.com/store/apps/details?id=com.syfe&hl=en&gl=us',
     'version': [None,
                 [[[[[None,
                      [None,
                       [[None,
                         2,
                         [512, 512],
                         [None,
                          None,
                          'https://play-lh.googleusercontent.com/_TcrYZaOKkM12SLSZyKWO4l_QgHSkhvXi1m0tm7OnwyxzAY3YrTUKYSpmhp5QM1gf-zF'],
                         None,
                         None,
                         None,
                         None,
                         None,
                         2],
                        [None,
                         2,
                         [512, 512],
                         [None,
                          None,
                          'https://play-lh.googleusercontent.com/_TcrYZaOKkM12SLSZyKWO4l_QgHSkhvXi1m0tm7OnwyxzAY3YrTUKYSpmhp5QM1gf-zF'],
                         None,
                         None,
                         None,
                         None,
                         None,
                         2],
                        [None,
                         2,
                         [512, 512],
                         [None,
                          None,
                          'https://play-lh.googleusercontent.com/_TcrYZaOKkM12SLSZyKWO4l_QgHSkhvXi1m0tm7OnwyxzAY3YrTUKYSpmhp5QM1gf-zF'],
                         None,
                         None,
                         None,
                         None,
                         None,
                         2]],
                       2,
                       2],
                      'SGX Mobile',
                      None,
                      [[['SGX',
                         [None,
                          None,
                          None,
                          None,
                          [None, None, '/store/apps/developer?id=SGX']],
                         True]],
                       [None,
                        [None,
                         [None,
                          'Live market data, news and company announcements of all '
                          'SGX-listed companies']]]],
                      [],
                      [[None, None, [None, ['4.1', 4.0926642]]]],
                      [],
                      [5, 4, 5],
                      [None,
                       None,
                       None,
                       None,
                       [None, None, '/store/apps/details?id=com.sgx.SGXandroid']],
                      None,
                      ['CAIaWAoaEhgKEmNvbS5zZ3guU0dYYW5kcm9pZBABGAMQADITCKDuzMPsrfMCFbqhSwUdxdUFmnITCNKM1PjrrfMCFfWESwUdtUoDVIoBDQgAEgkKBWVuLVVTEACqAl0aWwgAEhoKGAoSY29tLnNneC5TR1hhbmRyb2lkEAEYA0oTCKDuzMPsrfMCFbqhSwUdxdUFmpoBEwjSjNT4663zAhX1hEsFHbVKA1T6AQ8KDQgAEgkKBWVuLVVTEAA='],
                      ['com.sgx.SGXandroid', 7]],
                     [None,
                      [None,
                       [[None,
                         2,
                         [0, 0],
                         [None,
                          None,
                          'https://play-lh.googleusercontent.com/HJUz_In6O2_dQ-cLMju7pt9qq5xFXzp25xCr_P663EFr3f3C2rcraCvNtrIF9YrX5FxI'],
                         None,
                         None,
                         None,
                         None,
                         None,
                         2],
                        [None,
                         2,
                         [0, 0],
                         [None,
                          None,
                          'https://play-lh.googleusercontent.com/HJUz_In6O2_dQ-cLMju7pt9qq5xFXzp25xCr_P663EFr3f3C2rcraCvNtrIF9YrX5FxI'],
                         None,
                         None,
                         None,
                         None,
                         None,
                         2],
                        [None,
                         2,
                         [0, 0],
                         [None,
                          None,
                          'https://play-lh.googleusercontent.com/HJUz_In6O2_dQ-cLMju7pt9qq5xFXzp25xCr_P663EFr3f3C2rcraCvNtrIF9YrX5FxI'],
                         None,
                         None,
                         None,
                         None,
                         None,
                         2]],
                       2,
                       2],
                      'UOBAM Invest',
                      None,
                      [[['UOB Asset Management Ltd',
                         [None,
                          None,
                          None,
                          None,
                          [None,
                           None,
                           '/store/apps/developer?id=UOB+Asset+Management+Ltd']],
                         True]],
                       [None,
                        [None,
                         [None,
                          'UOBAM Invest is your personal robo-adviser to help you '
                          'build your future wealth.']]]],
                      [],
                      [[None, None, [None, ['3.8', 3.8282828]]]],
                      [],
                      [5, 4, 5],
                      [None,
                       None,
                       None,
                       None,
                       [None,
                        None,
                        '/store/apps/details?id=com.uobam.uobaminvest']],
                      None,
                      ['CAIaWwodEhsKFWNvbS51b2JhbS51b2JhbWludmVzdBABGAMQATITCKDuzMPsrfMCFbqhSwUdxdUFmnITCNKM1PjrrfMCFfWESwUdtUoDVIoBDQgAEgkKBWVuLVNHEACqAmAaXggBEh0KGwoVY29tLnVvYmFtLnVvYmFtaW52ZXN0EAEYA0oTCKDuzMPsrfMCFbqhSwUdxdUFmpoBEwjSjNT4663zAhX1hEsFHbVKA1T6AQ8KDQgAEgkKBWVuLVNHEAA='],
                      ['com.uobam.uobaminvest', 7]],
                     [None,
                      [None,
                       [[None,
                         2,
                         [0, 0],
                         [None,
                          None,
                          'https://play-lh.googleusercontent.com/BxJeLxjKGNka1wdqF8SF5hXq3gRbDYBDDSJN14T4QwvtsKhqgVgUT4ms9yvtt-O1QPEU'],
                         None,
                         None,
                         None,
                         None,
                         None,
                         2],
                        [None,
                         2,
                         [0, 0],
                         [None,
                          None,
                          'https://play-lh.googleusercontent.com/BxJeLxjKGNka1wdqF8SF5hXq3gRbDYBDDSJN14T4QwvtsKhqgVgUT4ms9yvtt-O1QPEU'],
                         None,
                         None,
                         None,
                         None,
                         None,
                         2],
                        [None,
                         2,
                         [0, 0],
                         [None,
                          None,
                          'https://play-lh.googleusercontent.com/BxJeLxjKGNka1wdqF8SF5hXq3gRbDYBDDSJN14T4QwvtsKhqgVgUT4ms9yvtt-O1QPEU'],
                         None,
                         None,
                         None,
                         None,
                         None,
                         2]],
                       2,
                       2],
                      'StashAway: Invest and save',
                      None,
                      [[['Asia Wealth Platform Pte Ltd',
                         [None,
                          None,
                          None,
                          None,
                          [None,
                           None,
                           '/store/apps/developer?id=Asia+Wealth+Platform+Pte+Ltd']],
                         True]],
                       [None, [None, [None, 'Personal finance and investing']]]],
                      [],
                      [[None, None, [None, ['4.1', 4.1464176]]]],
                      [],
                      [5, 4, 5],
                      [None,
                       None,
                       None,
                       None,
                       [None, None, '/store/apps/details?id=com.awp.stashaway']],
                      None,
                      ['CAIaVwoZEhcKEWNvbS5hd3Auc3Rhc2hhd2F5EAEYAxACMhMIoO7Mw+yt8wIVuqFLBR3F1QWachMI0ozU+Out8wIV9YRLBR21SgNUigENCAASCQoFZW4tVVMQAKoCXBpaCAISGQoXChFjb20uYXdwLnN0YXNoYXdheRABGANKEwig7szD7K3zAhW6oUsFHcXVBZqaARMI0ozU+Out8wIV9YRLBR21SgNU+gEPCg0IABIJCgVlbi1VUxAA'],
                      ['com.awp.stashaway', 7]],
                     [None,
                      [None,
                       [[None,
                         2,
                         [512, 512],
                         [None,
                          None,
                          'https://play-lh.googleusercontent.com/8606sVmXHRX7HJTtoS8jSuVS7HVl4BXt-SLqVo7tKNEw4dDMP27KvEcd3d2NXH3hkpE'],
                         None,
                         None,
                         None,
                         None,
                         None,
                         2],
                        [None,
                         2,
                         [512, 512],
                         [None,
                          None,
                          'https://play-lh.googleusercontent.com/8606sVmXHRX7HJTtoS8jSuVS7HVl4BXt-SLqVo7tKNEw4dDMP27KvEcd3d2NXH3hkpE'],
                         None,
                         None,
                         None,
                         None,
                         None,
                         2],
                        [None,
                         2,
                         [512, 512],
                         [None,
                          None,
                          'https://play-lh.googleusercontent.com/8606sVmXHRX7HJTtoS8jSuVS7HVl4BXt-SLqVo7tKNEw4dDMP27KvEcd3d2NXH3hkpE'],
                         None,
                         None,
                         None,
                         None,
                         None,
                         2]],
                       2,
                       2],
                      'Tiger Trade-Global Invest&Save',
                      None,
                      [[['TIGER BROKERS',
                         [None,
                          None,
                          None,
                          None,
                          [None, None, '/store/apps/developer?id=TIGER+BROKERS']],
                         True]],
                       [None,
                        [None, [None, 'ETF,Options,Futures&amp;Free Quote']]]],
                      [],
                      [[None, None, [None, ['4.5', 4.451025]]]],
                      [],
                      [5, 4, 5],
                      [None,
                       None,
                       None,
                       None,
                       [None,
                        None,
                        '/store/apps/details?id=com.tigerbrokers.stock']],
                      None,
                      ['CAIaXAoeEhwKFmNvbS50aWdlcmJyb2tlcnMuc3RvY2sQARgDEAMyEwig7szD7K3zAhW6oUsFHcXVBZpyEwjSjNT4663zAhX1hEsFHbVKA1SKAQ0IABIJCgVlbi1VUxAAqgJhGl8IAxIeChwKFmNvbS50aWdlcmJyb2tlcnMuc3RvY2sQARgDShMIoO7Mw+yt8wIVuqFLBR3F1QWamgETCNKM1PjrrfMCFfWESwUdtUoDVPoBDwoNCAASCQoFZW4tVVMQAA=='],
                      ['com.tigerbrokers.stock', 7]],
                     [None,
                      [None,
                       [[None,
                         2,
                         [0, 0],
                         [None,
                          None,
                          'https://play-lh.googleusercontent.com/pHMOIRJ21PkHLMdk1yjQJPsVnyx-CKgdtjd3VOnGb1JY7inJECHe_o7hFljJa8wcHlA']],
                        [None,
                         2,
                         [0, 0],
                         [None,
                          None,
                          'https://play-lh.googleusercontent.com/pHMOIRJ21PkHLMdk1yjQJPsVnyx-CKgdtjd3VOnGb1JY7inJECHe_o7hFljJa8wcHlA']],
                        [None,
                         2,
                         [0, 0],
                         [None,
                          None,
                          'https://play-lh.googleusercontent.com/pHMOIRJ21PkHLMdk1yjQJPsVnyx-CKgdtjd3VOnGb1JY7inJECHe_o7hFljJa8wcHlA']]],
                       2,
                       2],
                      'Wahed Invest',
                      None,
                      [[['Wahed Inc.',
                         [None,
                          None,
                          None,
                          None,
                          [None, None, '/store/apps/developer?id=Wahed+Inc.']],
                         True]],
                       [None, [None, [None, 'Ethical Investing Made Simple']]]],
                      [],
                      [[None, None, [None, ['3.7', 3.6796117]]]],
                      [],
                      [5, 4, 5],
                      [None,
                       None,
                       None,
                       None,
                       [None, None, '/store/apps/details?id=com.wahed.mobile']],
                      None,
                      ['CAIaVgoYEhYKEGNvbS53YWhlZC5tb2JpbGUQARgDEAQyEwig7szD7K3zAhW6oUsFHcXVBZpyEwjSjNT4663zAhX1hEsFHbVKA1SKAQ0IABIJCgVlbi1VUxAAqgJbGlkIBBIYChYKEGNvbS53YWhlZC5tb2JpbGUQARgDShMIoO7Mw+yt8wIVuqFLBR3F1QWamgETCNKM1PjrrfMCFfWESwUdtUoDVPoBDwoNCAASCQoFZW4tVVMQAA=='],
                      ['com.wahed.mobile', 7]]],
                    'Similar',
                    None,
                    [None,
                     None,
                     None,
                     None,
                     [None,
                      None,
                      '/store/apps/collection/cluster?clp=ogoWCBEqAggIMg4KCGNvbS5zeWZlEAEYAw%3D%3D:S:ANO1ljIao8I&gsr=ChmiChYIESoCCAgyDgoIY29tLnN5ZmUQARgD:S:ANO1ljJbwVo']],
                    True,
                    2,
                    None,
                    [None,
                     'CjWC0_-4Ay8KJvqegZ0DIAgGEKHj-8YKEIqQ4PEDEOLnka0JEMrhhc4KEL_S040MEI-SyKrELxAFGhmiChYIESoCCAgyDgoIY29tLnN5ZmUQARgD'],
                    True],
                   None,
                   None,
                   ['CBSqARUKEwiw4c7D7K3zAhW6oUsFHcXVBZo=']]],
                 None,
                 [],
                 True],
     'video': None,
     'videoImage': None}


## Scrapping Google Play Store reviews


```python
# for scraping app reviews from Google Play Store
from google_play_scraper import app, Sort, reviews

# Empty list for storing reviews
google_app_reviews = []

## Loop through apps to get reviews
for app_name, app_id in zip(google_app_names, google_app_ids):
    
    # Get start time
    start = dt.datetime.now(tz=get_localzone())
    fmt= "%m/%d/%y - %T %p"    
    
    # Print starting output for app
    print('---'*20)
    print('---'*20)    
    print(f'***** {app_name} started at {start.strftime(fmt)}')
    print()
    
    # Number of reviews to scrape per batch
    count = 200
    
    # To keep track of how many batches have been completed
    batch_num = 0
     
    # Retrieve reviews (and continuation_token) with reviews function
    rvws, token = reviews(
        app_id,           # found in app's url
        lang='en',        # defaults to 'en'
        country='us',     # defaults to 'us'
        sort=Sort.NEWEST, # start with most recent
        count=count       # batch size
    )
    
    # For each review obtained
    for r in rvws:
        r['app_name'] = app_name # add key for app's name
        r['app_id'] = app_id     # add key for app's id
     
    
    # Add the list of review dicts to overall list
    google_app_reviews.extend(rvws)
    
    # Increase batch count by one
    batch_num +=1 
    print(f'Batch {batch_num} completed.')
    
    # Wait 1 to 5 seconds to start next batch
    time.sleep(random.randint(1,5))
    
    # Append review IDs to list prior to starting next batch
    pre_review_ids = []
    for rvw in google_app_reviews:
        pre_review_ids.append(rvw['reviewId'])
    
    # Loop through at most max number of batches
    for batch in range(4999):
        rvws, token = reviews( # store continuation_token
            app_id,
            lang='en',
            country='us',
            sort=Sort.NEWEST,
            count=count,
            # using token obtained from previous batch
            continuation_token=token
        )
        
        # Append unique review IDs from current batch to new list
        new_review_ids = []
        for r in rvws:
            new_review_ids.append(r['reviewId'])
            
            # And add keys for name and id to each review dict
            r['app_name'] = app_name # add key for app's name
            r['app_id'] = app_id     # add key for app's id
     
        # Add the list of review dicts to main app_reviews list
        google_app_reviews.extend(rvws)
        
        # Increase batch count by one
        batch_num +=1
        
        # Break loop and stop scraping for current app if most recent batch
          # did not add any unique reviews
        all_review_ids = pre_review_ids + new_review_ids
        if len(set(pre_review_ids)) == len(set(all_review_ids)):
            print(f'No reviews left to scrape. Completed {batch_num} batches.\n')
            break
        
        # all_review_ids becomes pre_review_ids to check against 
          # for next batch
        pre_review_ids = all_review_ids
        
        # Wait 1 to 5 seconds to start next batch
        time.sleep(random.randint(1,5))
      
    
    # Print update when max number of batches has been reached
      # OR when last batch didn't add any unique reviews
    print(f'Done scraping {app_name}.')
    print(f'Scraped a total of {len(set(pre_review_ids))} unique reviews.\n')
    
    # Get end time
    end = dt.datetime.now(tz=get_localzone())
    
    # Print ending output for app
    print(f"""
    Successfully inserted all {app_name} reviews into collection
    at {end.strftime(fmt)}.\n
    """)
    print(f'Time elapsed for {app_name}: {end-start}')
    print('---'*20)
    print('---'*20)
    print('\n')
    
    # Wait 1 to 5 seconds to start scraping next app
    time.sleep(random.randint(1,5))
```

    ------------------------------------------------------------
    ------------------------------------------------------------
    ***** Syfe started at 09/04/21 - 23:33:25 PM
    
    Batch 1 completed.
    No reviews left to scrape. Completed 2 batches.
    
    Done scraping Syfe.
    Scraped a total of 110 unique reviews.
    
    
        Successfully inserted all Syfe reviews into collection
        at 09/04/21 - 23:33:31 PM.
    
        
    Time elapsed for Syfe: 0:00:05.276724
    ------------------------------------------------------------
    ------------------------------------------------------------
    
    
    ------------------------------------------------------------
    ------------------------------------------------------------
    ***** Endowus started at 09/04/21 - 23:33:32 PM
    
    Batch 1 completed.
    No reviews left to scrape. Completed 2 batches.
    
    Done scraping Endowus.
    Scraped a total of 250 unique reviews.
    
    
        Successfully inserted all Endowus reviews into collection
        at 09/04/21 - 23:33:34 PM.
    
        
    Time elapsed for Endowus: 0:00:02.298619
    ------------------------------------------------------------
    ------------------------------------------------------------
    
    
    ------------------------------------------------------------
    ------------------------------------------------------------
    ***** StashAway started at 09/04/21 - 23:33:38 PM
    
    Batch 1 completed.
    No reviews left to scrape. Completed 8 batches.
    
    Done scraping StashAway.
    Scraped a total of 1515 unique reviews.
    
    
        Successfully inserted all StashAway reviews into collection
        at 09/04/21 - 23:34:00 PM.
    
        
    Time elapsed for StashAway: 0:00:22.199429
    ------------------------------------------------------------
    ------------------------------------------------------------
    
    



```python
# Converting output to dataframe
google_reviews = pd.DataFrame(google_app_reviews)
```

## Part 3: Combining both Apple and Google Store reviews


```python
print(f'Apple Store: {np.shape(apple_reviews)[0]} rows and {np.shape(apple_reviews)[1]} columns.')
print(f'Google Play Store: {np.shape(google_reviews)[0]} rows and {np.shape(google_reviews)[1]} columns.')
```

    Apple Store: 524 rows and 9 columns.
    Google Play Store: 1515 rows and 12 columns.



```python
new_apple = apple_reviews[['app_name','review']] # Selecting app_name and review from apple reviews into new df
new_apple.rename(columns={'review':'content'},inplace=True) # rename review content column
new_google = google_reviews[['app_name','content']] # subset app_name and content from google reviews into new df
total_reviews = pd.concat([new_apple,new_google]) # Concat both dfs into one

# saving reviews into csv file
total_reviews.to_csv('app_reviews.csv', index=False, header=True)
```

    /Users/a844133yara.com/.pyenv/versions/3.9.5/envs/python_playground/lib/python3.9/site-packages/pandas/core/frame.py:5034: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      return super().rename(

