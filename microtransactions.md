
# Project: Hypothesis Testing for Microtransactions
Brian is a Product Manager at FarmBurg, a company that makes a farming simulation social network game.  In the FarmBurg game, you can plow, plant, and harvest different crops.

Today, you will be acting as Brian's data analyst for an A/B Test that he has been conducting.

## Part 1: Testing for Significant Difference

Start by importing the following modules that you'll need for this project:
- `pandas` as `pd`


```python
import pandas as pd
```

Brian tells you that he ran an A/B test with three different groups: A, B, and C.  You're kind of busy today, so you don't ask too many questions about the differences between A, B, and C.  Maybe they were shown three different versions of an ad.  Who cares?

(HINT: you will care later)

Brian gives you a CSV of results called `clicks.csv`.  It has the following columns:
- `user_id`: a unique id for each visitor to the FarmBerg site
- `ab_test_group`: either `A`, `B`, or `C` depending on which group the visitor was assigned to
- `click_day`: only filled in *if* the user clicked on a link to purchase

Load `clicks.csv` into the variable `df`.


```python
df = pd.read_csv('clicks.csv')

df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>user_id</th>
      <th>group</th>
      <th>click_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>8e27bf9a-5b6e-41ed-801a-a59979c0ca98</td>
      <td>A</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>eb89e6f0-e682-4f79-99b1-161cc1c096f1</td>
      <td>A</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>7119106a-7a95-417b-8c4c-092c12ee5ef7</td>
      <td>A</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>e53781ff-ff7a-4fcd-af1a-adba02b2b954</td>
      <td>A</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>02d48cf1-1ae6-40b3-9d8b-8208884a0904</td>
      <td>A</td>
      <td>Saturday</td>
    </tr>
  </tbody>
</table>
</div>



Define a new column called `is_purchase` which is `Purchase` if `click_day` is not `None` and `No Purchase` if `click_day` is `None`.  This will tell us if each visitor clicked on the Purchase link.


```python
import numpy as np

df['is_purchase'] = np.where(pd.isnull(df['click_day']), 'No Purchase', 'Purchase')

df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>user_id</th>
      <th>group</th>
      <th>click_day</th>
      <th>is_purchase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>8e27bf9a-5b6e-41ed-801a-a59979c0ca98</td>
      <td>A</td>
      <td>NaN</td>
      <td>No Purchase</td>
    </tr>
    <tr>
      <th>1</th>
      <td>eb89e6f0-e682-4f79-99b1-161cc1c096f1</td>
      <td>A</td>
      <td>NaN</td>
      <td>No Purchase</td>
    </tr>
    <tr>
      <th>2</th>
      <td>7119106a-7a95-417b-8c4c-092c12ee5ef7</td>
      <td>A</td>
      <td>NaN</td>
      <td>No Purchase</td>
    </tr>
    <tr>
      <th>3</th>
      <td>e53781ff-ff7a-4fcd-af1a-adba02b2b954</td>
      <td>A</td>
      <td>NaN</td>
      <td>No Purchase</td>
    </tr>
    <tr>
      <th>4</th>
      <td>02d48cf1-1ae6-40b3-9d8b-8208884a0904</td>
      <td>A</td>
      <td>Saturday</td>
      <td>Purchase</td>
    </tr>
  </tbody>
</table>
</div>



We want to count the number of users who made a purchase from each group.  Use `groupby` to count the number of `Purchase` and `No Purchase` from each `group`.  Save your answer to the variable `purchase_counts`.

**Hint**: Group by `group` and `is_purchase` and the function `count` on the column `user_id`.


```python
purchase_counts = df.groupby(['group', 'is_purchase'])['user_id'].count()

print purchase_counts

groupA_not_purchases = purchase_counts[0]
groupA_purchases = purchase_counts[1]

groupB_not_purchases = purchase_counts[2]
groupB_purchases = purchase_counts[3]

groupC_not_purchases = purchase_counts[4]
groupC_purchases = purchase_counts[5]

```

    group  is_purchase
    A      No Purchase    1350
           Purchase        316
    B      No Purchase    1483
           Purchase        183
    C      No Purchase    1583
           Purchase         83
    Name: user_id, dtype: int64


This data is *categorical* and there are *more than 2* conditions, so we'll want to use a chi-squared test to see if there is a significant difference between the three conditions.

Start by filling in the contingency table below with the correct values:
```py
contingency = [[groupA_purchases, groupA_not_purchases],
               [groupB_purchases, groupB_not_purchases],
               [groupC_purchases, groupC_not_purchases]]
```


```python
contingency = [[groupA_purchases, groupA_not_purchases],
               [groupB_purchases, groupB_not_purchases],
               [groupC_purchases, groupC_not_purchases]]
```

Now import the function `chi2_contingency` from `scipy.stats` and perform the chi-squared test.

Recall that the *p-value* is the second output of `chi2_contingency`.


```python
from scipy.stats import chi2_contingency
```


```python
chi2_contingency(contingency)

chi2_contingency(contingency)[1] #Get second output of chi2_contingency
```




    2.4126213546684264e-35



Great! It looks like a significantly greater portion of users from Group A made a purchase.

## Part 2: Testing for Exceeding a Goal

Your day is a little less busy than you expected, so you decide to ask Brian about his test.

**You**: Hey Brian! What was that test you were running anyway?

**Brian**: It was awesome! We are trying to get users to purchase a small FarmBurg upgrade package.  It's called a microtransaction.  We're not sure how much to charge for it, so we tested three different price points: \$0.99, \$1.99, and \$4.99.  It looks like significantly more people bought the upgrade package for \$0.99, so I guess that's what we'll charge.

**You**: Oh no! I should have asked you this before we did that chi-squared test.  I don't think that this was the right test at all.  It's true that more people wanted purchase the upgrade at \$0.99; you probably expected that.  What we really want to know is if each price point allows us to make enough money that we can exceed some target goal.  Brian, how much do you think it cost to build this feature?

**Brian**: Hmm.  I guess that we need to generate a minimum of $1000 per week in order to justify this project.

**You**: We have some work to do!

How many visitors came to the site this week?

Hint: Look at the length of `df`.


```python
total_visitors = len(df)
print total_visitors
```

    4998


Let's assume that this is how many visitors we generally get each week.  Given that, calculate the percent of visitors who would need to purchase the upgrade package at each price point (\$0.99, \$1.99, \$4.99) in order to generate \$1000 per week.


```python
n = 1000/0.99 #Calculate the number of people who would need to purchase a $0.99 upgrade in order to generate $1000
n/total_visitors #Divide by the number of people who visit the site each week
```




    0.20210104243717691




```python
n = 1000/1.99 #Calculate the number of people who would need to purchase a $1.99 upgrade in order to generate $1000
n/total_visitors #Divide by the number of people who visit the site each week.
```




    0.10054272965467594




```python
n = 1000/4.99 #Calculate the number of people who would need to purchase a $4.99 upgrade in order to generate $1000
n/total_visitors #Divide by the number of people who visit the site each week
```




    0.040096198800161346



Note that you need a smaller percentage of purchases for higher price points.

Now, for each group, perform a binomial test using `binom_test` from `scipy.stats`.
- `x` will be the number of purchases for that group
- `n` will be the total number of visitors assigned to that group
- `p` will be the target percent of purchases for that price point (calculated above)

Recall that:
- Group `A` is the \$0.99 price point
- Group `B` is the \$1.99 price point
- Group `C` is the \$4.99 price point


```python
from scipy.stats import binom_test
```


```python
#Group A test
binom_test(groupA_purchases, groupA_purchases+groupA_not_purchases, 0.20210104243717691)
```




    0.2111287299402726




```python
#Group B test
binom_test(groupB_purchases, groupB_purchases+groupB_not_purchases, 0.10054272965467594)
```




    0.20660209246555486




```python
#Group C test
binom_test(groupC_purchases, groupC_purchases+groupC_not_purchases, 0.040096198800161346)
```




    0.045623672477172125



If any of the groups passed the binomial test with $p < 0.05$, then we can be confident that enough people will buy the upgrade package at that price point to justify the feature.

Which price point should Brian go with?  Did this surprise you?

### Answer:

Brian should go with the highest price point ($4.99). This is a little surprising, since significantly more people purchased the upgrade at the lowest price point. However, when you consider that the highest price point is 5x more than the lowest one, it makes more sense that a much lower number of people need to purchase at the highest price point for the feature to be justified.
