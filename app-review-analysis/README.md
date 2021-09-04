# app-review-analysis

## 1.1 - Scraping reviews

Relevant files:
- app-review-scrap.ipynb: scraps reviews from Google Play Store and Apple Store, combines relevant reviews into one dataframe and export as csv file
- app_info.xlsx: excel file containing 2 sheets (Apple and Google) with relevant info required for scrapping the reviews
- app_reviews.csv: exported file containing app reviews using app-review-scrap-ipynb
- multiple-rule-based-sentiment-analysis.ipynb: uses various lexicon based approaches to perform sentiment analysis on app reviews
