
# observations

1. There were almost the exact same ratio of repeat buyers for both men and women players(73% of Men and Women made more than one purchase) which says that gender doesn't play a part in whether someone makes a repeat purchase. 
2. Age group of under 10 was the least likely to make a repeat purchase (3% of under 10 made a repeat purchase)  while (20-24) were the most likley (77%). 
3. Looking at the normalized totals, men were expected to spend more money on game items than women and people over 40 were expected to spend the higest amount. Men over 40 years will spend the most on the game whereas a female between the ages of 20-24 might be expected to spend the least. 


```python
import pandas as pd
import numpy as np

df = pd.read_json('/Users/hanna/Downloads/UDEN201805DATA1-master/Week4/PandasHW/HeroesOfPymoli/purchase_data.json')


```

# Total Number of Players


```python
total_players = df.groupby('SN').count().shape[0]
total_num_players = pd.DataFrame({
    'Total Players' : [total_players],
})
total_num_players
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis 


```python

purchasing_analysis = pd.DataFrame({
    'Number of Unique Items': [num_unique_items],
    'Average Price' : [avg_price],
    'Total Purchases': [total_purchases],
    'Total Revenue' : [total_revenue],
})

purchasing_analysis


```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Price</th>
      <th>Number of Unique Items</th>
      <th>Total Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2.931192</td>
      <td>179</td>
      <td>780</td>
      <td>2286.33</td>
    </tr>
  </tbody>
</table>
</div>



# Gender Demographics


```python

df['SN'].unique()
df_unique = df.drop_duplicates(subset=['Gender', 'SN'], keep='first')
total_females = (df_unique.Gender == 'Female').sum()
total_males = (df_unique.Gender == 'Male').sum()
total_other = (df_unique.Gender == 'Other / Non-Disclosed').sum()

df_unique.Gender.value_counts()

gender_unique = pd.DataFrame(df_unique, columns = ['Gender'])
gender_unique = pd.DataFrame(df_unique['Gender'].value_counts())
gender_unique = Gender.rename(columns={'Gender': 'Total Count'})
gender_unique['Percentage of Players'] = gender_unique['Total Count']/total_players*100

gender_unique 

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Count</th>
      <th>Percentage of Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>465</td>
      <td>81.151832</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>100</td>
      <td>17.452007</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>8</td>
      <td>1.396161</td>
    </tr>
  </tbody>
</table>
</div>



# Gender Purchasing Analysis 


```python

gender_demo = df.groupby('Gender').Price.agg(['mean','count','sum']).reset_index()

gender_demo = gender_demo.rename(columns={'count': 'Purchase Count'})
gender_demo = gender_demo.rename(columns={'mean': 'Average Purchase Price'})
gender_demo = gender_demo.rename(columns={'sum': 'Total Purchase Value'})

normalized_count = []

for row in gender_demo['Gender']:
    if row == 'Female':
        normalized_count.append((df_unique.Gender == 'Female').sum())
    elif row == 'Male':
        normalized_count.append((df_unique.Gender == 'Male').sum())
    else:
        normalized_count.append((df_unique.Gender == 'Other / Non-Disclosed').sum())
        
gender_demo['Normalized Count'] = normalized_count
gender_demo['Normalized Totals'] = gender_demo['Total Purchase Value']/gender_demo['Normalized Count']

gender_demo['Average Purchase Price']= gender_demo['Average Purchase Price'].map('${:,.2f}'.format)
gender_demo['Total Purchase Value']= gender_demo['Total Purchase Value'].map('${:,.2f}'.format)
gender_demo['Normalized Totals']= gender_demo['Normalized Totals'].map('${:,.2f}'.format)

gender_demo
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Gender</th>
      <th>Average Purchase Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
      <th>Normalized Count</th>
      <th>Normalized Totals</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Female</td>
      <td>$2.82</td>
      <td>136</td>
      <td>$382.91</td>
      <td>100</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Male</td>
      <td>$2.95</td>
      <td>633</td>
      <td>$1,867.68</td>
      <td>465</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Other / Non-Disclosed</td>
      <td>$3.25</td>
      <td>11</td>
      <td>$35.74</td>
      <td>8</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>



# Age Demographics



```python
for_age = df[['Age', 'SN']].drop_duplicates()

bins = [0,9,14,19,24,29,34,39,49]
labels = ['<10','10-14', '15-19','20-24','25-29','30-34','35-39','>40',]

assert( len(bins) > len(labels))

df['age_group'] = pd.cut(df['Age'], bins, labels=labels)
for_age['age_group'] = pd.cut(for_age['Age'], bins, labels=labels)

age_out = pd.concat([for_age.age_group.value_counts(normalize=True),
                    for_age.age_group.value_counts()],
                   axis=1 )
age_out
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age_group</th>
      <th>age_group</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>20-24</th>
      <td>0.452007</td>
      <td>259</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>0.174520</td>
      <td>100</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>0.151832</td>
      <td>87</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>0.082024</td>
      <td>47</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>0.047120</td>
      <td>27</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>0.040140</td>
      <td>23</td>
    </tr>
    <tr>
      <th>&lt;10</th>
      <td>0.033159</td>
      <td>19</td>
    </tr>
    <tr>
      <th>&gt;40</th>
      <td>0.019197</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis by Age Group


```python
age_demo = df.groupby('age_group').Price.agg(['mean','count','sum']).reset_index()
age_demo.reset_index(inplace = False)
age_demo['unique_buyers'] = age_demo['age_group'].map(lambda x: age_out.to_dict()['age_group'].get(x) )
age_demo['normalized_totals'] = age_demo['sum']/age_demo['unique_buyers'].astype('float')

age_demo = age_demo.rename(columns={'count': 'Purchase Count'})
age_demo = age_demo.rename(columns={'mean': 'Average Purchase Price'})
age_demo = age_demo.rename(columns={'sum': 'Total Purchase Price'})

age_demo['Average Purchase Price']= age_demo['Average Purchase Price'].map('${:,.2f}'.format)
age_demo['Total Purchase Price']= age_demo['Total Purchase Price'].map('${:,.2f}'.format)

age_demo
```

    C:\Users\hanna\Anaconda3\envs\PythonData\lib\site-packages\ipykernel_launcher.py:3: UserWarning: DataFrame columns are not unique, some columns will be omitted.
      This is separate from the ipykernel package so we can avoid doing imports until
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age_group</th>
      <th>Average Purchase Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Price</th>
      <th>unique_buyers</th>
      <th>normalized_totals</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>&lt;10</td>
      <td>$2.98</td>
      <td>28</td>
      <td>$83.46</td>
      <td>19</td>
      <td>4.392632</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10-14</td>
      <td>$2.77</td>
      <td>35</td>
      <td>$96.95</td>
      <td>23</td>
      <td>4.215217</td>
    </tr>
    <tr>
      <th>2</th>
      <td>15-19</td>
      <td>$2.91</td>
      <td>133</td>
      <td>$386.42</td>
      <td>100</td>
      <td>3.864200</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20-24</td>
      <td>$2.91</td>
      <td>336</td>
      <td>$978.77</td>
      <td>259</td>
      <td>3.779035</td>
    </tr>
    <tr>
      <th>4</th>
      <td>25-29</td>
      <td>$2.96</td>
      <td>125</td>
      <td>$370.33</td>
      <td>87</td>
      <td>4.256667</td>
    </tr>
    <tr>
      <th>5</th>
      <td>30-34</td>
      <td>$3.08</td>
      <td>64</td>
      <td>$197.25</td>
      <td>47</td>
      <td>4.196809</td>
    </tr>
    <tr>
      <th>6</th>
      <td>35-39</td>
      <td>$2.84</td>
      <td>42</td>
      <td>$119.40</td>
      <td>27</td>
      <td>4.422222</td>
    </tr>
    <tr>
      <th>7</th>
      <td>&gt;40</td>
      <td>$3.16</td>
      <td>17</td>
      <td>$53.75</td>
      <td>11</td>
      <td>4.886364</td>
    </tr>
  </tbody>
</table>
</div>



# Top Spenders


```python

top_5_spenders = df.groupby(['SN','Price']).Price.agg(['mean','count','sum']).reset_index()


top_5_spenders = top_5_spenders.rename(columns ={'count': 'Purchase Count'})
top_5_spenders = top_5_spenders.rename(columns ={'mean': 'Average Purchase Price'})
top_5_spenders = top_5_spenders.rename(columns ={'sum': 'Total Purchase Value'})

top_5_spenders['Average Purchase Price'] = top_5_spenders['Average Purchase Price'].map('${:,.2f}'.format)
top_5_spenders['Price'] = top_5_spenders['Price'].map('${:,.2f}'.format)

top_5_spenders.nlargest(5,'Total Purchase Value')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>SN</th>
      <th>Price</th>
      <th>Average Purchase Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>446</th>
      <td>Lisistaya47</td>
      <td>$3.42</td>
      <td>$3.42</td>
      <td>2</td>
      <td>6.84</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Aeliriam77</td>
      <td>$4.95</td>
      <td>$4.95</td>
      <td>1</td>
      <td>4.95</td>
    </tr>
    <tr>
      <th>230</th>
      <td>Frichaststa61</td>
      <td>$4.95</td>
      <td>$4.95</td>
      <td>1</td>
      <td>4.95</td>
    </tr>
    <tr>
      <th>502</th>
      <td>Palurrian69</td>
      <td>$4.95</td>
      <td>$4.95</td>
      <td>1</td>
      <td>4.95</td>
    </tr>
    <tr>
      <th>531</th>
      <td>Qiluard68</td>
      <td>$4.95</td>
      <td>$4.95</td>
      <td>1</td>
      <td>4.95</td>
    </tr>
  </tbody>
</table>
</div>



# Top Popular Items 


```python

popular_items = pd.DataFrame(df.groupby(['Item Name','Item ID','Price']).Price.agg(['mean','count','sum']).reset_index())

popular_items = popular_items.rename(columns={'Price': 'Total Price'})
popular_items = popular_items.rename(columns={'count': 'Purchase Count'})
popular_items = popular_items.rename(columns={'mean': 'Average Purchase Price'})
popular_items = popular_items.rename(columns={'sum': 'Total Purchase Value'})

popular_items['Total Price'] = popular_items['Total Price'].map('${:,.2f}'.format)
popular_items['Average Purchase Price'] = popular_items['Average Purchase Price'].map('${:,.2f}'.format)
popular_items['Total Purchase Value'] = popular_items['Total Purchase Value'].map('${:,.2f}'.format)

popular_items.nlargest(5, 'Purchase Count')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item Name</th>
      <th>Item ID</th>
      <th>Total Price</th>
      <th>Average Purchase Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>8</th>
      <td>Arcane Gem</td>
      <td>84</td>
      <td>$2.23</td>
      <td>$2.23</td>
      <td>11</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Betrayal, Whisper of Grieving Widows</td>
      <td>39</td>
      <td>$2.35</td>
      <td>$2.35</td>
      <td>11</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>115</th>
      <td>Retribution Axe</td>
      <td>34</td>
      <td>$4.14</td>
      <td>$4.14</td>
      <td>9</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>122</th>
      <td>Serenity</td>
      <td>13</td>
      <td>$1.49</td>
      <td>$1.49</td>
      <td>9</td>
      <td>$13.41</td>
    </tr>
    <tr>
      <th>159</th>
      <td>Trickster</td>
      <td>31</td>
      <td>$2.07</td>
      <td>$2.07</td>
      <td>9</td>
      <td>$18.63</td>
    </tr>
  </tbody>
</table>
</div>



# Top Profitable Items 



```python

profitable_items = pd.DataFrame(df.groupby(['Item Name','Item ID','Price']).Price.agg(['mean','count','sum']).reset_index())
profitable_items = profitable_items.rename(columns={'Price': 'Total Price'})
profitable_items = profitable_items.rename(columns={'count': 'Purchase Count'})
profitable_items = profitable_items.rename(columns={'mean': 'Average Purchase Price'})
profitable_items = profitable_items.rename(columns={'sum': 'Total Purchase Value'})

profitable_items['Total Price'] = profitable_items['Total Price'].map('${:,.2f}'.format)
profitable_items['Average Purchase Price'] = profitable_items['Average Purchase Price'].map('${:,.2f}'.format)


profitable_items.nlargest(5,'Total Purchase Value')



```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item Name</th>
      <th>Item ID</th>
      <th>Total Price</th>
      <th>Average Purchase Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>115</th>
      <td>Retribution Axe</td>
      <td>34</td>
      <td>$4.14</td>
      <td>$4.14</td>
      <td>9</td>
      <td>37.26</td>
    </tr>
    <tr>
      <th>135</th>
      <td>Spectral Diamond Doomblade</td>
      <td>115</td>
      <td>$4.25</td>
      <td>$4.25</td>
      <td>7</td>
      <td>29.75</td>
    </tr>
    <tr>
      <th>98</th>
      <td>Orenmir</td>
      <td>32</td>
      <td>$4.95</td>
      <td>$4.95</td>
      <td>6</td>
      <td>29.70</td>
    </tr>
    <tr>
      <th>127</th>
      <td>Singed Scalpel</td>
      <td>103</td>
      <td>$4.87</td>
      <td>$4.87</td>
      <td>6</td>
      <td>29.22</td>
    </tr>
    <tr>
      <th>137</th>
      <td>Splitter, Foe Of Subtlety</td>
      <td>107</td>
      <td>$3.61</td>
      <td>$3.61</td>
      <td>8</td>
      <td>28.88</td>
    </tr>
  </tbody>
</table>
</div>


