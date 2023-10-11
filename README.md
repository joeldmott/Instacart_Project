# Online Grocery Ordering Project
Online grocery shopping has become much more mainstream since its bumpy start with Webvan in 1996. Since the onset of the pandemic, both grocery deliveries and curbside-pickup have become even more common. As of August 2023, most of the largest fifty grocery retailers in the US offer curbside.

Even before COVID, grocery-shopping services such as Instacart and Shipt had grown popular. Their shoppers are still a common sight in stores today and many grocery chains even use Shipt (Target) and Instacart (Costco, Publix, Safeway/Tom Thumb/Randall's, Wegmans, and Sprouts) for their delivery orders. However, many of the largest chains now operate their own delivery services in addition to curbside-pickup, such as Walmart, Amazon, Kroger, ALDI, and HEB.

Recently, Instacart has faced challenges with more competition and the decline of pandemic-era grocery shopping order frequency, while evolving to contend with unpredictable restocking issues.

## Project Goals
I expand the use of this dataset to grocery chains with their own delivery/curbside services by using correlations with reordering to help promote new products. 

Specifically, I present three business recommendations based on which aspects of an Instacart dataset have the strongest correlations with popular, reordered groceries. These correlations may also help inform restocking, ways to simplify reordering, and predictions of future orders.

This project's presentation may be found [here.](https://github.com/joeldmott/Instacart_Project/blob/main/presentation.pdf)

## Instacart dataset
In 2017, Instacart held a competition on Kaggle.com with an anonymized dataset containing over three million order records. It's too large to include in this repo, but can be found here: 

https://www.kaggle.com/c/instacart-market-basket-analysis/data

This data is held in six seperate Excel spreadsheets, five of which I combine into a SQLite database below. The total size of the data used here is just over 122.16 MB.

This data shows information regarding orders, product details, whether the product constituted a new item for the user or a reorder, how often orders were placed, the order's day-of-week and time-of-day, and even the sequence in which each product was placed in the online cart.

## Project process steps
After converting the dataset's spreadsheets to csv files and reading them in as tables in a database via SQLite3, I begin with some preliminary exploratory data analysis (EDA) to understand the data and identify salient patterns or correlations with how likely a product is to be ordered *as a reorder*. 
Once some general findings are in place, I use linear regression analysis (through StatsModels' OLS module) to specify them into more concrete business recommendations. 

### EDA findings:

#### store departments
Not all grocery departments are created equal. The produce & dairy/egg departments dwarf all others in sales, even though they don't have the widest variety of products to offer.

![Amount_of_Products_Sold_by_Department](https://github.com/joeldmott/Instacart_Project/assets/51928528/ac10607f-ac43-4883-a268-3039bef46cf0)
![Variety_of_Products_by_Department](https://github.com/joeldmott/Instacart_Project/assets/51928528/1de50743-bf54-47b4-981d-5e6811afcc7f)

These two departments sell oft-used products with relatively shorter shelf lives. Scrutinizing the variety of stock for these popular, fresher, short-lived products seems especially vital.

#### reordered items are usually added to the cart first
This graph just shows the top 100 products. Including all 35,449 unique products makes these patterns hard to see. Later, regression analysis will help us specify the exact correlation with more (but not all) products.

![Added-to-Cart-Order   Product-Reorder-Percentage](https://github.com/joeldmott/Instacart_Project/assets/51928528/811746e7-a43e-4645-9a7a-a0bbfeff4d65)

#### when and how often users tend to order groceries
Many order groceries more often than once a week, but the largest spike is every seven days (and then smaller spikes every week thereafter). “30” days is almost certainly a placeholder for “30 or more days between orders”.

![Histogram of the Number of Days Since the Previous Order](https://github.com/joeldmott/Instacart_Project/assets/51928528/cbc5cc43-cc11-4ae9-bd8a-42f453dcf485)

Weekend grocery shopping is quite popular. This pattern informs the peaks in our previous graph on days since the prior order.

![Amount of Orders per Day of the Week](https://github.com/joeldmott/Instacart_Project/assets/51928528/7eef0353-cfb3-4920-b1f2-2c07a383ba67)

### Linear Regression Analysis
The overall process is threefold:
1. constructing a baseline model with only the single strongest aspect of our data that correlates with reorders
2. investigating the pros & cons of that model
3. improving on the baseline by adding and/or transforming more aspects of the data

#### baseline model
![presentation SnagIt image on product features considered](https://github.com/joeldmott/Instacart_Project/assets/51928528/a8917229-3e14-4091-8448-3ab9f93f72cf)

At first, even the strongest correlation produces a weak model with an R-squared score of 0.022, so it does not explain the data well at all. This has to do with the amount of times each product was ordered; some were ordered over 150,000 times while others only once.

#### revising the baseline with more helpful data
Eliminating rarely ordered products vastly improves the model to an R-squared score of 0.32. I define “rarely ordered products” as those with fewer than 100 orders. The remaining, more popular items tell us more about reordering habits in the first place.

#### improving on this new baseline
The next strongest aspect of the data is the average number of days since a product was ordered. Adding this improves the model (R-squared: 0.383). The next two strongest aspects regard order frequency: order hour of day & day of the week. However, we cannot include *both* because they are too interrelated (including both makes it hard to see their individual relation to reordering). Furthermore, the hour of day aspect proves problematic for other reasons.

This makes it easier to decide to exclude hour of day altogether since keeping day of the week works well. This improves the model's R-squared score to 0.434.

## **Final model results:** three recommendations for promoting new products based on two reordering correlations:
**correlation 1:** As the average add-to-cart order of a product increases by one, the likelihood of that product being ordered as a reorder decreases by 4.7%.

**recommendation 1:** Advertising new products would be more effective towards the end of the online ordering process.  

**recommendation 2:**  Streamlining the reorder process may allow greater user attention toward for newer product promotion (as in a "click/tap to reorder previous" option).

---------------------------------------------------------------------------------

**correlation 2:** Over the course of a week, as a product's average order day of the week increases away from Saturday by one whole day, the likelihood of that product being ordered as a reorder decreases by 15.2%.

**recommendation 3:** Promoting new products on Thursdays or Fridays may help increase interest in new products before the weekend rush.

## Conclusions
Instacart's helpful dataset can also inform grocery chains’ own delivery, curbside, and restocking operations as well as new product promotion.

Future or similar projects *tailored towards specific stores* may help:
- optimize aisle layouts 
- quantify reserved parking spaces for curbside pickup
- inform allocation of staff to delivery & curbside services
- improve grocery ordering app design
  
Future projects may also *include data on in-store shopping* to help refine these correlations with restocking.
This current project may also help inform online-only stores (aka “online fulfillment centers”).  

## Repository Structure:
├── images  

│   ├── Added-to-Cart-Order & Product-Reorder-Percentage.png  

│   ├── Amount of Orders per Day of the Week.png  

│   ├── Amount_of_Products_Sold_by_Department.png  

│   ├── Histogram of the Number of Days Since the Previous Order.png  

│   ├── Variety_of_Products_by_Department.png  

├── .gitignore  

├── README.md  

├── presentation.pdf  

└── project_notebook.ipynb
