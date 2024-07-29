# Food Delivery Cost and Profitability Analysis
## Overview

This project analyzes food delivery operations to assess profitability. It calculates average commission and discount rates for profitable orders, compares these to overall averages, and provides recommendations for optimizing rates to boost profitability, supported by key visualizations.

## Data Preparation

### Importing Libraries and Dataset
```python
import pandas as pd

food_orders = pd.read_csv("food_delivery.csv")
print(food_orders.head())
```
![](/Images/1.png)

### Data Overview
```python
print(food_orders.info())
```
![](/Images/2.png)
### Data Cleaning and Preparation
```python
from datetime import datetime

# Convert date and time columns to datetime
food_orders['Order Date and Time'] = pd.to_datetime(food_orders['Order Date and Time'])
food_orders['Delivery Date and Time'] = pd.to_datetime(food_orders['Delivery Date and Time'])

# Function to extract numeric values from 'Discounts and Offers' string
def extract_discount(discount_str):
    if pd.isna(discount_str):
        return 0.0
    elif 'off' in discount_str:
        return float(discount_str.split(' ')[0])
    elif '%' in discount_str:
        return float(discount_str.split('%')[0])
    else:
        return 0.0

# Apply the function to create a new 'Discount Percentage' column
food_orders['Discount Percentage'] = food_orders['Discounts and Offers'].apply(lambda x: extract_discount(x))

# Calculate the discount amount based on the order value
food_orders['Discount Amount'] = food_orders.apply(lambda x: x['Order Value'] * x['Discount Percentage'] / 100
                                                   if x['Discount Percentage'] > 1
                                                   else x['Discount Percentage'], axis=1)

# Print the first few rows and data types to verify
print(food_orders[['Order Value', 'Discounts and Offers', 'Discount Percentage', 'Discount Amount']].head())
print(food_orders.dtypes)
```
![](/Images/3.png)
## Cost and Profitability Analysis

### 1. Total Costs and Revenue
```python
food_orders['Total Costs'] = food_orders['Delivery Fee'] + food_orders['Payment Processing Fee'] + food_orders['Discount Amount']
food_orders['Revenue'] = food_orders['Commission Fee']
food_orders['Profit'] = food_orders['Revenue'] - food_orders['Total Costs']
```
### 2. Aggregate Metrics
```python
total_orders = food_orders.shape[0]
total_revenue = food_orders['Revenue'].sum()
total_costs = food_orders['Total Costs'].sum()
total_profit = food_orders['Profit'].sum()

overall_metrics = {
    "Total Orders": total_orders,
    "Total Revenue": total_revenue,
    "Total Costs": total_costs,
    "Total Profit": total_profit
}

print(overall_metrics)
```
![](/Images/4.png)
Based on the analysis, the food delivery operations show a total of 1,000 orders with a revenue of 126,990 INR from commission fees. The total costs, which include delivery fees, payment processing fees, and discounts, amount to 232,709.85 INR. As a result, the overall profit (net loss in our case) stands at -105,719.85 INR.

## Visualizations

### Profit Distribution Histogram
```python
import matplotlib.pyplot as plt

plt.figure(figsize=(10, 6))
plt.hist(food_orders['Profit'], bins=50, color='skyblue', edgecolor='black')
plt.title('Profit Distribution per Order in Food Delivery')
plt.xlabel('Profit')
plt.ylabel('Number of Orders')
plt.axvline(food_orders['Profit'].mean(), color='red', linestyle='dashed', linewidth=1)
plt.show()
```
![](/Images/5.png)
### Cost Breakdown Pie Chart
```python
costs_breakdown = food_orders[['Delivery Fee', 'Payment Processing Fee', 'Discount Amount']].sum()
plt.figure(figsize=(7, 7))
plt.pie(costs_breakdown, labels=costs_breakdown.index, autopct='%1.1f%%', startangle=140, colors=['tomato', 'gold', 'lightblue'])
plt.title('Proportion of Total Costs in Food Delivery')
plt.show()
```
![](/Images/6.png)
### Revenue, Costs, and Profit Bar Chart
```python
totals = ['Total Revenue', 'Total Costs', 'Total Profit']
values = [total_revenue, total_costs, total_profit]

plt.figure(figsize=(8, 6))
plt.bar(totals, values, color=['green', 'red', 'blue'])
plt.title('Total Revenue, Costs, and Profit')
plt.ylabel('Amount (INR)')
plt.show()
```
![](/Images/7.png)
## A New Strategy for Profits

Discounts are leading to major losses. To boost profitability, we need to find the right balance between discounts and commissions by analyzing profitable order traits.
```python
# Calculate new metrics
profitable_orders.loc[:, 'Commission Percentage'] = (profitable_orders['Commission Fee'] / profitable_orders['Order Value']) * 100
profitable_orders.loc[:, 'Effective Discount Percentage'] = (profitable_orders['Discount Amount'] / profitable_orders['Order Value']) * 100

# Calculate averages
new_avg_commission_percentage = profitable_orders['Commission Percentage'].mean()
new_avg_discount_percentage = profitable_orders['Effective Discount Percentage'].mean()

print(new_avg_commission_percentage, new_avg_discount_percentage)
```

The analysis shows that to improve profitability, aim for a commission rate of about 30.51% and a discount rate of around 5.87%. This suggests that higher commissions and lower discounts could enhance profitability.

## Visualization of Profitability Comparison
```python
import seaborn as sns

recommended_commission_percentage = 30.0
recommended_discount_percentage = 6.0

food_orders['Simulated Commission Fee'] = food_orders['Order Value'] * (recommended_commission_percentage / 100)
food_orders['Simulated Discount Amount'] = food_orders['Order Value'] * (recommended_discount_percentage / 100)

food_orders['Simulated Total Costs'] = (food_orders['Delivery Fee'] +
                                        food_orders['Payment Processing Fee'] +
                                        food_orders['Simulated Discount Amount'])

food_orders['Simulated Profit'] = (food_orders['Simulated Commission Fee'] -
                                   food_orders['Simulated Total Costs'])

plt.figure(figsize=(14, 7))
sns.kdeplot(food_orders['Profit'], label='Actual Profitability', fill=True, alpha=0.5, linewidth=2)
sns.kdeplot(food_orders['Simulated Profit'], label='Estimated Profitability with Recommended Rates', fill=True, alpha=0.5, linewidth=2)
plt.title('Comparison of Profitability in Food Delivery: Actual vs. Recommended Discounts and Commissions')
plt.xlabel('Profit')
plt.ylabel('Density')
plt.legend(loc='upper left')
plt.show()
```
![](/Images/9.png)
## Conclusion

The analysis indicates that profitable orders have an average commission rate of 30.51% and an average discount rate of 5.87%. Compared to overall averages, these rates suggest that higher commissions and lower discounts contribute significantly to profitability. Visualizations comparing current profitability with simulated scenarios show that adjusting to a 30% commission and 6% discount could enhance overall profitability. Adopting these rates may lead to more favorable financial outcomes.

## License

This project is licensed under the [MIT License](https://github.com/ThisIsTPM/Food-Delivery-Cost-and-Profitability-Analysis?tab=MIT-1-ov-file). See the LICENSE file for details.

## Contact

For any questions or feedback, please contact [tanmayprasadmallick@gmail.com ](mailto:tanmayprasadmallick@gmail.com).
