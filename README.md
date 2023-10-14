# Maca data scientist screener 

Welcome to the take home technical screener! We want to see how you work in a real-world  environment, rather than your ability to solve obscure algorithms. You have 3 days to complete the exercise. Once this is done, we’ll schedule a follow up session to pair on the application and talk through your work.

## Prompt: 
Using the customer churn data in this repo, please analyze and answer the following: 
 * How did you approach cleaning the data?
 * What are the strongest indicators of what would cause a customer to churn? 
 * What could a company do to reduce churn given this data? 
 * If you had to retain customers, who would you prioritize to spend money on? 

Provide a README of:
 * A reasonable decision log of what you think is important. We like to see thoughtful work.
 * Approximately how long did it take to build this solution

Please feel free to reach out with questions! We would rather spend a few minutes making sure you have a clear understanding of the problem set rather losing hours on a project that doesn't make sense
Using the customer churn data in this repo, please analyze and answer the following:

# Maca data scientist screener 

The goal of this exercise is to understand what can lead to churn and strategies/insights to reduce it. Here are my responses to the prompt below:

## How did you approach cleaning the data?

First, I looked at all the customer ids to check whether there were duplicates - each row is 1 unique customer. For the training dataset, I checked for any nulls/blank values and there was 1 row that was completely blank so it was dropped. The test dataset had more cleaning steps. I renamed the column names to match with the headers in the training set. Then, I checked the unique values for each field and found that age, gender, support calls, subscription type, and contract length required cleaning. These were the steps I applied for each of these fields:

- Age: This field had numbers that were in text form and  "nan" values. I identified and converted the non-numeric values to numbers e.g. Twenty-nine was set to 29. There are 6 rows that had "nan" values, and so they were dropped from the data since they make up less than 0.01% of the dataset. I had considered applying an imputation but because the number of records is so low, I dropped them instead. Lastly, I converted the column to numeric type.
- Gender: There were four values for gender - Female, Male, Man, and Woman. To align with the training dataset, I set Man to Male and Woman to Female.
- Support Calls: Like the Age field, there were also some values that were in text form, so I converted them to numbers. The row that had "10+" as a value was set to 10, since 10 seemed to be the maximum number of calls in the dataset. There were 6 "nan" values which were dropped, since they make up less than 0.01% of the dataset. I then converted the column to numeric.
- Subscription Type: There were 3 missing values - these were dropped.
- Contract Length: The standard values were "Monthly," "Annual," and "Quarterly." There were 3 rows with different values. The row with "12 months" was set to "Annual" and the row with "Once a month" was set to "Monthly." The row with "Q3" was dropped since it was challenging to assume the interpretation.

Once these fields were cleaned, the data types of each were converted to match with the data types of the training set.

Here were the interpretations/assumptions made for the fields:
Customer ID
Age - customer's age
Gender - customer's gender
Tenure - how long customer has been on the product (maybe in months)
Usage Frequency - how often the product is used on a certain time cadence (days in a month)
Support Calls - number of support calls customer had with company
Payment Delay - number of days the customer was late in paying
Subscription Type - the plan the customer's on
Contract length - type of contract
Total Spend - Maybe amount invested on customer
Last Interaction - Number of days since last interaction with customer
Churn - whether the customer stopped doing business with the company

## What are the strongest indicators of what would cause a customer to churn?
I visualized and summarized the churn volume and proportion against each variable and leveraged a few classification models to identify these indicators. 

### Findings from the EDA of the training dataset (see visuals within "EDA on Training Dataset")

- Comparing the proportion of customers by age who churn, the trend shows that the churn rate decreases steadily up until the age of 50, to which the rate spikes to 100%. This might indicate that customers who are between 30-50 are less inclined to churn.
- Gender also seems to play a role - almost 67% of females churn compared to 49% of males
- All customers on the monthly contract churned
- For tenure and usage frequency, there is a similar trend where the churn proportion dips at a certain value - for tenure, it's at about 25, and for frequency it's at 10. This makes sense since the longer a customer is with the product, potentially the satisfied/engaged they are with the product. It seems that these 2 fields have an inverse relationship with churn. 
- After around 5 support calls, the churn rate spikes to 100%. Increased support calls may indicate more issues with the product leading to churn
- After a certain length of delay for payment (20 days), similar to support calls, the churn rate goes up to 100%.
- For spend, it seems like the higher the spend, the lower the churn potential. Less spend means less investment in the customer, which could lead to the customer having less engagement with the company's product and hence leading to churn. I grouped spend in increments of 200 to check for trends among each group, and it seems that customers that are spent <400 churned in the training data (this may not reflect the same in the test data).
- Last interaction between the customer and company also follows a similar trend as with support calls and payment delay. Once the last interaction reaches beyond 15 days, the churn rate goes up significantly to around 67% for those customers whose last interaction was more than 2 weeks ago.

For modeling, 3 methods were used: Logistic Regression, Decision Tree, and Extra Trees (similar to Random Forest but instead of being built based on bootstrapping, it's based on the whole dataset)

The logistic regression model was better performing overall at 70% accuracy - I looked into the feature importance based on the coefficients of the model, and the top features were calls, spend, delay, gender, and last interaction. See first graph within the "Modeling" section for reference. An increasing number of support calls can indicate more issues and negative feedback on the product, or trouble reaching customer support. Spend also has an impact on churn, as seen in the visualization in the EDA, the less spend for a customer may indicate less investment in the product and less commitment in a way. It's easier to let go of a product when not as much is spent. Payment delays (assuming this means the number of days late a customer paid) could indicate not as much focus/attention on using the product and/or leading to forgetting the payment deadline. The last interaction between the customer and company is important as well, since the longer that there's no communication, the less the customer is engaged with the product.

## What could a company do to reduce churn given this data?

Because support calls and last interaction impact churn, there may need to be a balance of better support and troubleshooting to the point where there are less calls needed, but still have consistent interactions where the customer is engaged/knows what's up to date. For example, maybe hiring more customer support reps or providing incentives for quicker responses/proactiveness with customers to increase customer service (conducting more quota/bonus-based performance reviews) and make it easier for them to reach out for help. To keep up with interactions, approaches include sending out newsletters to customers on the latest features/product roadmap or having the customer success team build more personalized relationships with customers (especially those the company has not had a recent interaction with) on a more weekly basis. To encourage on time payments, an approach could be to offer reminders with incentives to encourage timely payments. Sending out surveys to customers for feedback would help paint a real picture of what they think of the product and customer service. Focusing on the customers who have a spend of less than 400 and understanding why most of them churned (across the whole dataset - about 90%), and considering whether to increase spend for them.


## If you had to retain customers, who would you prioritize to spend money on?

With respect to the training set, I would prioritize the customers who had more than 5 support calls, since they make up about 32.5% of customers. For customers who had more than 5 support calls, more than 94% of them churned. The next group I would focus on are customers that had a spend of <400 because all churned - these make up almost 20% of customers

Solution took about 12 hours to build
