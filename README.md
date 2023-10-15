# Maca data scientist screener 

The goal of this exercise is to understand what can lead to churn and strategies/insights to reduce it. Here are my responses to the prompt below:

## How did you approach cleaning the data?

I first renamed the column names for both the training and testing datasets to be lowercased and each as one complete string without spaces. I looked at all the customer ids to check whether there were duplicates - each row is 1 unique customer. 

For the training dataset, I checked for any nulls/blank values and there was 1 row that was completely blank so it was dropped. I checked the unique values for each field and all seemed to have consistent data types and no missing values.

The test dataset had more cleaning steps. I checked the unique values for each field to see if there were any missing or inconsistent values, and found that age, gender, support calls, subscription type, and contract length required cleaning. These were the steps I applied for each of these fields:

- Age: This field had numbers that were in text form and also had "nan" values. I identified and converted the non-numeric values to numbers e.g. Twenty-nine was set to 29. There are 6 rows that had "nan" values, and so they were dropped from the data since they make up less than 0.01% of the dataset. I had considered applying an imputation but because the number of records is so low, I dropped them instead. Lastly, I converted the column to numeric.
- Gender: There were four values for gender - Female, Male, Man, and Woman. To align with the training dataset, I set Man to Male and Woman to Female - 2 rows were updated.
- Support Calls: Like the Age field, there were also some values that were in text form, so I converted them to numbers. The row that had "10+" as a value was set to 10, since 10 seemed to be the maximum number of calls in the dataset - I also wanted to retain this information in the case it impacts churn. There were 6 "nan" values which were dropped, since they make up less than 0.01% of the dataset. I then converted the column to numeric.
- Subscription Type: There were 3 missing values - these were dropped.
- Contract Length: The standard values were "Monthly," "Annual," and "Quarterly." There were 3 rows with different values. The row with "12 months" was set to "Annual" and the row with "Once a month" was set to "Monthly." The row with "Q3" was dropped since it was challenging to assume any interpretation.

Once these fields were cleaned, the data types of each variable were converted to match with the data types of the training set.

Here were the interpretations/assumptions made for the fields:

* Customer ID
* Age - customer's age
* Gender - customer's gender
* Tenure - how long the customer has been on the product (maybe in months)
* Usage Frequency - how often the product is used on a certain time cadence (days in a month)
* Support Calls - number of support calls customer had with company
* Payment Delay - number of days the customer was late in paying
* Subscription Type - the plan the customer's on
* Contract length - type of contract
* Total Spend - maybe the amount customer spent
* Last Interaction - number of days since the last communication with the customer (assuming mutual conversation/engagement)
* Churn - whether the customer stopped doing business with the company

To set up the datasets, I converted the categorical variables (gender, subscription, and contractlength) to numerical codes. Then, I set up the X and y training and testing sets. I dropped the customer id and spend_group since id is a unique identifier and spend is already in the dataset.

## What are the strongest indicators of what would cause a customer to churn?

I visualized and summarized the churn proportion trends against each variable, and leveraged a few classification models to identify the indicators. 

### Findings from the EDA of the training dataset (see visuals within "EDA on Training Dataset")

- Comparing the proportion of customers by age who churn, the trend shows that the churn rate decreases steadily up until the age of 50, to which the rate spikes to 100%. This might indicate that customers who are between 30-50 are less inclined to churn.
- Gender also seems to play a role - almost 67% of females churn compared to 49% of males.
- For tenure and usage frequency, there is a similar trend where the churn proportion dips at a certain value - for tenure, it's at about 25, and for frequency it's at 10. This makes sense since the longer a customer is with the product, this might indicate the more satisfied/engaged they are with using the product. If the customer had a shorter tenure, this may lead to less commitment to the product. It seems that these 2 fields have an inverse relationship with churn. For tenure, however, there is an interesting dip between 5-11, maybe another optimal amount of time for a customer to do business with the company.
- For support calls, the churn proportion significantly goes up after 2 calls. After around 5 support calls, the churn rate spikes to 100%. Increased support calls may indicate more issues with the product leading to churn or attempts to connect with customer support.
- After a certain length of delay for payment (20 days), similar to support calls, the churn rate goes up to 100%.
- For each subscription plan, around the same proportion of customers churned (55-58%)
- All customers on the monthly contract churned.
- For spend, it seems like the higher the spend, the lower the churn potential. I grouped spend in increments of 200 to check for trends among each group, and it seems that all customers that spent <400 churned in the training data (this may not reflect the same in the test data). Less spend means the customer probably has a lower commitment, which could lead to the customer having less engagement with the company's product and hence lead to churn. 
- Last interaction between the customer and company also follows a similar trend as with support calls and payment delay. Once the last interaction reaches beyond 15 days, the churn rate goes up significantly to around 67% for those customers whose last interaction was more than 2 weeks ago.

### Modeling, Comparing Associations, and Results
For modeling the training dataset, 3 methods were used: Logistic Regression, Decision Tree, and Extra Trees (similar to Random Forest but instead of trees being built based on bootstrapping, they're built based on the whole dataset)

The logistic regression model was better performing overall at 69-70% accuracy and 84% from cross-validation - I looked into the feature importance based on the absolute value of the coefficients of the model, and the top features were calls, spend, delay, gender, and last interaction. See first graph within the "Modeling" section for reference. 

The trees models were performing lower at 51-62% accuracy, but the feature importance showed that the spend, calls, delay, contractlength, and last interaction had higher importance.

Finally, I also used a heatmap to compare the associations between each pair of variables. The variables with the stronger relationship with churn are calls, delay, age, last interaction, and spend. 

Taking the results from each approach above, the top indicators of churn would be support calls, payment delays, and spend.

Support calls and churn have a correlation of 0.57. An increasing number of support calls can indicate more issues and negative feedback on the product, or trouble reaching customer support. Payment delays (assuming this means the number of days late a customer paid) has an association with churn at 0.31 - longer delays could indicate not as much focus/attention on using the product and/or leading to forgetting the payment deadline. Spend also has an impact on churn, but an inverse relationship at -0.4. As seen in the visualization in the EDA section, the less spend for a customer may lead to the customer having less investment in the product and lower commitment. 

Less so but still impactful: Age also plays a role in churn (0.22 correlation), as the data shows that for customers beyond the age of 50, the churn proportion goes up to 100%. Gender also plays a role, where more females churn. The last interaction (0.15) between the customer and company is important as well, since the longer that there's no communication, the less informed/aware the customer is of the product.

Other callouts from the Association heatmap:
- Calls and payment delay has some positive association at 0.16 - more support calls could mean issues with payment/product and less incentive to pay on time
- Calls and total spend have an inverse association at -0.21 - possibly more calls/troubleshooting is associated with a lower willingness to spend

## What could a company do to reduce churn given this data?

Because support calls and last interaction both impact churn, there may need to be a balance of better responsiveness/support and troubleshooting to the point where there are less calls needed, but also consistent interactions where the customer is engaged and knows what's up to date. To address the high number of calls that lead to churn, approaches could be hiring more qualified customer support reps or providing incentives for quicker responses/proactiveness with customers to improve service (conducting more quota/bonus-based performance reviews). The goal would be to make it easier for customers to reach out for help and to get the help they need more quickly and more effectively. 

To keep up with interactions, approaches include sending out newsletters to customers on the latest features/product roadmap or having the customer success team build more personalized relationships with customers (especially those that the company has not had a recent interaction with) on a more consistent basis (less than 2 weeks). 

To encourage on time payments, an approach could be to first understand and survey customers on what's causing the delay and why they churned. Sending out surveys to customers for feedback would help paint a real picture of what they think of the product and customer service, and if that causes any delays. Then, on top of keeping engaged with the customer, offer reminders with incentives to encourage timely payments. 

To address spend, focus on the customers that spent less than 600 (they make up about 41% of customers and 57% of all customers who churned in the training data), and consider whether to increase incentives or exclusive access to new updates/features. Establishing loyalty programs with offerings and having the customer success team offer upsells can help customers be more encouraged to continue with the company.

In addition to the above approaches based on this data, having consistent qualitative feedback/context from customers would also be important in determining the strategies to reduce churn.

## If you had to retain customers, who would you prioritize to spend money on?

With respect to the training dataset and using the strategies above, I would prioritize the customers who had 5 or more support calls, since they make up about 32.5% of customers but almost 57% of all who churned, and since support calls has the strongest relationship/impact on churn. For customers who had more than 5 support calls, 100% of them churned. 

The next group I would focus on are customers who had spent under 600 (there is overlap with the customers in the group above) - these customers make up 57% of the people who churned. I would apply the strategies above on encouraging loyalty and increasing incentives for these customers.


#### Notes:
Solution (this exercise) took about 13 hours to build
Investigating association/indicators, not necessarily causation (correlation does not mean causation)
