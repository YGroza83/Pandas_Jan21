```python
# Dependencies, load file and look at what we working with
import pandas as pd
purchase_data = pd.read_csv("Resources/purchase_data.csv")
#Optional: give the displayed data cleaner formatting - that way I only need to change it for %s couple of times.
pd.options.display.float_format = '${:,.2f}'.format
purchase_data.head(1)
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
      <th>Purchase ID</th>
      <th>SN</th>
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>Lisim78</td>
      <td>20</td>
      <td>Male</td>
      <td>108</td>
      <td>Extraction, Quickblade Of Trembling Hands</td>
      <td>$3.53</td>
    </tr>
  </tbody>
</table>
</div>



## Player Count


```python
#Display the total number of players
group_byname = purchase_data.groupby('SN')
number_ofPurchases = group_byname['SN'].count()
print('''Total number of players that bougt something:''', len(number_ofPurchases))
```

    Total number of players that bougt something: 576
    

## Purchasing Analysis (Total)


```python
group_byname = purchase_data.groupby('Item Name')
number_ofItems = group_byname['Item Name'].count()
print('''Number of Unique Items: ''', len(number_ofItems))
print('''Average Price: $''',round(purchase_data['Price'].mean(),2))
print('''Number of Purchases: ''', len(purchase_data['Purchase ID']))
print('''Total Revenue: $''', round(purchase_data['Price'].sum(),2))
```

    Number of Unique Items:  179
    Average Price: $ 3.05
    Number of Purchases:  780
    Total Revenue: $ 2379.77
    

## Gender Demographics


```python
group_nameNgender = purchase_data.groupby(['SN','Gender']).count()
byGender = group_nameNgender.groupby('Gender').count()
del byGender['Item ID']
del byGender['Item Name']
del byGender['Price']
byGender.columns = ['Total Count','Percentage of Players']
byGender.iloc[0,1] = byGender.iloc[0,0] / len(number_ofPurchases) * 100
byGender.iloc[1,1] = byGender.iloc[1,0] / len(number_ofPurchases) * 100
byGender.iloc[2,1] = byGender.iloc[2,0] / len(number_ofPurchases) * 100
byGender['Percentage of Players'] = byGender['Percentage of Players'].apply('{:,.2f}%'.format)
byGender
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
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>81</td>
      <td>14.06%</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>484</td>
      <td>84.03%</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>1.91%</td>
    </tr>
  </tbody>
</table>
</div>




## Purchasing Analysis (Gender)


```python
tmp1 = purchase_data.groupby('SN').agg({'Purchase ID':'count','Gender':'max','Price':'sum'})
by_gender = tmp1.groupby('Gender').agg({'Purchase ID':'sum','Price':['mean','sum']})
by_gender['Average Purchase Price'] = 0
by_gender.columns = ['Purcase Count','Avg per person','Total purchases','Avg per purchase']
by_gender.iloc[0,3] = by_gender.iloc[0,2] / by_gender.iloc[0,0]
by_gender.iloc[1,3] = by_gender.iloc[1,2] / by_gender.iloc[1,0]
by_gender.iloc[2,3] = by_gender.iloc[2,2] / by_gender.iloc[2,0]
by_gender
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
      <th>Purcase Count</th>
      <th>Avg per person</th>
      <th>Total purchases</th>
      <th>Avg per purchase</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>113</td>
      <td>$4.47</td>
      <td>$361.94</td>
      <td>$3.20</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>652</td>
      <td>$4.07</td>
      <td>$1,967.64</td>
      <td>$3.02</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>15</td>
      <td>$4.56</td>
      <td>$50.19</td>
      <td>$3.35</td>
    </tr>
  </tbody>
</table>
</div>



## Age Demographics


```python
bins = [0, 9, 14, 19, 24, 29, 34, 39, 100]
group_names = [" <10", "10-14", "15-19", "20-24", "25-29", "30-34","35-39","40+"]
purchase_data["Age Group"] = pd.cut(purchase_data["Age"], bins, labels=group_names, include_lowest=True)
tmp1 = purchase_data.groupby('SN').agg({'Purchase ID':'count','Age Group':'max'})
group_byAge = tmp1.groupby('Age Group').count()
group_byAge.columns=['Total Count']
group_byAge['Percentage of Players'] = 0
for i in range(8):
    group_byAge.iloc[i,1] = group_byAge.iloc[i,0] / len(number_ofPurchases) * 100
group_byAge['Percentage of Players'] = group_byAge['Percentage of Players'].apply('{:,.2f}%'.format)
group_byAge
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
    <tr>
      <th>Age Group</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>17</td>
      <td>2.95%</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>22</td>
      <td>3.82%</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>107</td>
      <td>18.58%</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>258</td>
      <td>44.79%</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>77</td>
      <td>13.37%</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>52</td>
      <td>9.03%</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>31</td>
      <td>5.38%</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>12</td>
      <td>2.08%</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Age)


```python
tmp1 = purchase_data.groupby('Age Group').agg({'Purchase ID':'count','Price':['mean','sum'],'SN':'count'})
tmp1.columns=['Purchase Count','Average Purchase Price','Total Purchase Value','Avg purchase per person']
for i in range(8):
    tmp1.iloc[i,3] = tmp1.iloc[i,2] / group_byAge.iloc[i,0]
tmp1
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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Avg purchase per person</th>
    </tr>
    <tr>
      <th>Age Group</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>23</td>
      <td>$3.35</td>
      <td>$77.13</td>
      <td>$4.54</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>28</td>
      <td>$2.96</td>
      <td>$82.78</td>
      <td>$3.76</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>136</td>
      <td>$3.04</td>
      <td>$412.89</td>
      <td>$3.86</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>365</td>
      <td>$3.05</td>
      <td>$1,114.06</td>
      <td>$4.32</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>101</td>
      <td>$2.90</td>
      <td>$293.00</td>
      <td>$3.81</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>73</td>
      <td>$2.93</td>
      <td>$214.00</td>
      <td>$4.12</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>41</td>
      <td>$3.60</td>
      <td>$147.67</td>
      <td>$4.76</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>13</td>
      <td>$2.94</td>
      <td>$38.24</td>
      <td>$3.19</td>
    </tr>
  </tbody>
</table>
</div>



## Top Spenders


```python
tmp1 = purchase_data.groupby('SN').agg({'Purchase ID':'count','Price':['mean','sum']})
tmp1.columns=['Purchase Count','Average Purchase Price','Total Purchase Value']
tmp2=tmp1.sort_values('Total Purchase Value',ascending=False,)
tmp2.head()
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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Lisosia93</th>
      <td>5</td>
      <td>$3.79</td>
      <td>$18.96</td>
    </tr>
    <tr>
      <th>Idastidru52</th>
      <td>4</td>
      <td>$3.86</td>
      <td>$15.45</td>
    </tr>
    <tr>
      <th>Chamjask73</th>
      <td>3</td>
      <td>$4.61</td>
      <td>$13.83</td>
    </tr>
    <tr>
      <th>Iral74</th>
      <td>4</td>
      <td>$3.40</td>
      <td>$13.62</td>
    </tr>
    <tr>
      <th>Iskadarya95</th>
      <td>3</td>
      <td>$4.37</td>
      <td>$13.10</td>
    </tr>
  </tbody>
</table>
</div>



## Most Popular Items


```python
tmp1 = purchase_data.groupby('Item ID').agg({'Item Name':'max','Purchase ID':'count','Price':['max','sum']})
tmp1.columns=['Item Name','Purchase Count','Item Price','Total Purchase Value']
tmp2=tmp1.sort_values('Purchase Count',ascending=False,)
tmp2.head()
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
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>92</th>
      <td>Final Critic</td>
      <td>13</td>
      <td>$4.88</td>
      <td>$59.99</td>
    </tr>
    <tr>
      <th>178</th>
      <td>Oathbreaker, Last Hope of the Breaking Storm</td>
      <td>12</td>
      <td>$4.23</td>
      <td>$50.76</td>
    </tr>
    <tr>
      <th>145</th>
      <td>Fiery Glass Crusader</td>
      <td>9</td>
      <td>$4.58</td>
      <td>$41.22</td>
    </tr>
    <tr>
      <th>132</th>
      <td>Persuasion</td>
      <td>9</td>
      <td>$3.33</td>
      <td>$28.99</td>
    </tr>
    <tr>
      <th>108</th>
      <td>Extraction, Quickblade Of Trembling Hands</td>
      <td>9</td>
      <td>$3.53</td>
      <td>$31.77</td>
    </tr>
  </tbody>
</table>
</div>



## Most Profitable Items


```python
tmp1=tmp2.sort_values('Total Purchase Value',ascending=False,)
tmp1.head()
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
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>92</th>
      <td>Final Critic</td>
      <td>13</td>
      <td>$4.88</td>
      <td>$59.99</td>
    </tr>
    <tr>
      <th>178</th>
      <td>Oathbreaker, Last Hope of the Breaking Storm</td>
      <td>12</td>
      <td>$4.23</td>
      <td>$50.76</td>
    </tr>
    <tr>
      <th>82</th>
      <td>Nirvana</td>
      <td>9</td>
      <td>$4.90</td>
      <td>$44.10</td>
    </tr>
    <tr>
      <th>145</th>
      <td>Fiery Glass Crusader</td>
      <td>9</td>
      <td>$4.58</td>
      <td>$41.22</td>
    </tr>
    <tr>
      <th>103</th>
      <td>Singed Scalpel</td>
      <td>8</td>
      <td>$4.35</td>
      <td>$34.80</td>
    </tr>
  </tbody>
</table>
</div>




```python

```
