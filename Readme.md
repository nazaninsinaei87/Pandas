
# Heros of Pymoli Data Analysis
Observed Trend One: The majority of the players are male with 81.15%
Observed Trend Two: The most profit for the company achieved through the purchases of age group 20-24.
Observed Trend Three: Players between age of 20-24 constitute the majority of players with 45.20%. following that, age group between 15-19 (17.45%) and 25-29 (15.18%).


```python
#import dependecies
import pandas as pd
import numpy as np
#Read the file into DataFrame
purchase_data = pd.read_json("purchase_data.json")
purchase_data.head()
```

# Player Count


```python
# Calculate the number of unique of players
players_count = len(purchase_data.SN.unique())
pd.DataFrame({"Total players":[players_count]})
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
      <th>Total players</th>
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



# Purchasing Analysis (Total)


```python
num_unique_items = purchase_data['Item ID'].nunique()
avg_price = purchase_data.Price.mean()
number_purchases = purchase_data.Price.count()
total_revenue = purchase_data.Price.sum()
purchase_analysis_total = pd.DataFrame({"Number of Unique Items": [num_unique_items],
                                 "Average Price": [avg_price],
                                 "Number of Purchases": [number_purchases],
                                 "Total Revenue": [total_revenue]})
purchase_analysis_total["Average Price"] = purchase_analysis_total["Average Price"].map("$ {:,.2f}".format)
purchase_analysis_total["Total Revenue"] = purchase_analysis_total["Total Revenue"].map("$ {:,.2f}".format)
purchase_analysis_total
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
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Number of Unique Items</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>$ 2.93</td>
      <td>780</td>
      <td>183</td>
      <td>$ 2,286.33</td>
    </tr>
  </tbody>
</table>
</div>



# Gender Demographics


```python
# the total number and percentage of each gender
gender_count = purchase_data.drop_duplicates(['SN']).Gender.value_counts()
percentage_player = round(purchase_data.drop_duplicates(['SN']).Gender.value_counts(normalize=True)*100,2)
gender_demographic = pd.DataFrame({"Total Count":gender_count,"Percentage of Players":percentage_player}, index=["Male","Female","Other / Non-Disclosed"])
gender_demographic
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
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>81.15</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.40</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis (Total)


```python
#group data by gender to get the purchase count, average price, total purchase value and normalized total
purchase_data_gender = purchase_data.groupby("Gender")
purchase_count = purchase_data.Gender.value_counts()
avg_price_gender = purchase_data_gender.Price.mean()
total_purchase_value = purchase_data_gender.Price.sum()
normalized_total = total_purchase_value/gender_count
purchasing_analysis_gender = pd.DataFrame({"Purchase Count":purchase_count,
                                   "Average Purchase Price": avg_price_gender,
                                   "Total Purchase Value": total_purchase_value,
                                   "Normalized Totals": normalized_total}, index=["Male","Female","Other / Non-Disclosed"])

purchasing_analysis_gender["Average Purchase Price"] = purchasing_analysis_gender["Average Purchase Price"].map("${:.2f}".format)
purchasing_analysis_gender["Total Purchase Value"] = purchasing_analysis_gender[ "Total Purchase Value"].map("${:.2f}".format)
purchasing_analysis_gender["Normalized Totals"] = purchasing_analysis_gender[ "Normalized Totals"].map("${:.2f}".format)


purchasing_analysis_gender
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
      <th>Average Purchase Price</th>
      <th>Normalized Totals</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>$2.95</td>
      <td>$4.02</td>
      <td>633</td>
      <td>$1867.68</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>$2.82</td>
      <td>$3.83</td>
      <td>136</td>
      <td>$382.91</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>$3.25</td>
      <td>$4.47</td>
      <td>11</td>
      <td>$35.74</td>
    </tr>
  </tbody>
</table>
</div>



# Age Demographics


```python
# Create the bins in which Data will be held
bins = [0, 9.90, 14.90, 19.90, 24.90, 29.90, 34.90, 39.90, 50]
# Create the labels for the bins
labels = ['<10', '10-14', '15-19', '20-24','25-29','30-34','35-39','40+']
purchase_data["Group Age"] =pd.cut(purchase_data['Age'], bins, labels=labels)
dedupe_purchase_data = purchase_data.drop_duplicates(['SN'])
total_count_age = dedupe_purchase_data['Group Age'].value_counts()
normalized_count_percentage = round(dedupe_purchase_data['Group Age'].value_counts(normalize=True)*100,2)
age_demographics = pd.DataFrame({"Percentage of Players": normalized_count_percentage,
                                "Total Count": total_count_age}, index=labels)
age_demographics
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
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>3.32</td>
      <td>19</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>4.01</td>
      <td>23</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>45.20</td>
      <td>259</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>15.18</td>
      <td>87</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>8.20</td>
      <td>47</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>4.71</td>
      <td>27</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>1.92</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis (Age)


```python
# Group data by group age and get the average price, total price, purchase count and normalized totals
purchase_count_age = purchase_data["Group Age"].value_counts()
purchase_data_groupage = purchase_data.groupby("Group Age")
avg_price_groupage = purchase_data_groupage.Price.mean()
total_purchase_groupage = purchase_data_groupage.Price.sum()
dedupe_groupage = purchase_data.drop_duplicates(['SN']).groupby('Group Age')
normalized_total_groupage = total_purchase_groupage/total_count_age
purchase_analysis_age = pd.DataFrame({"Purchase Count":purchase_count_age,
                                     "Average Purchase Price":avg_price_groupage,
                                     "Total Purchase Value": total_purchase_groupage,
                                     "Normalized Totals": normalized_total_groupage}, index=labels)
purchase_analysis_age["Average Purchase Price"] = purchase_analysis_age["Average Purchase Price"].map("${:.2f}".format)
purchase_analysis_age["Total Purchase Value"] = purchase_analysis_age[ "Total Purchase Value"].map("${:.2f}".format)
purchase_analysis_age["Normalized Totals"] = purchase_analysis_age[ "Normalized Totals"].map("${:.2f}".format)
purchase_analysis_age
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
      <th>Average Purchase Price</th>
      <th>Normalized Totals</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>$2.98</td>
      <td>$4.39</td>
      <td>28</td>
      <td>$83.46</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>$2.77</td>
      <td>$4.22</td>
      <td>35</td>
      <td>$96.95</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>$2.91</td>
      <td>$3.86</td>
      <td>133</td>
      <td>$386.42</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>$2.91</td>
      <td>$3.78</td>
      <td>336</td>
      <td>$978.77</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>$2.96</td>
      <td>$4.26</td>
      <td>125</td>
      <td>$370.33</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>$3.08</td>
      <td>$4.20</td>
      <td>64</td>
      <td>$197.25</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>$2.84</td>
      <td>$4.42</td>
      <td>42</td>
      <td>$119.40</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>$3.16</td>
      <td>$4.89</td>
      <td>17</td>
      <td>$53.75</td>
    </tr>
  </tbody>
</table>
</div>



# Top Spenders


```python
# Calculating the Top spenders
group_SN = purchase_data.groupby('SN')
total_purchase_SN = group_SN.Price.sum()
sorted_total_purchase = total_purchase_SN.sort_values(ascending=False)
top_5 = sorted_total_purchase.head()
top_spenders = purchase_data[purchase_data['SN'].isin(top_5.index.get_level_values(0))]
group_top_spenders = top_spenders.groupby('SN')
purchase_count = top_spenders.SN.value_counts()
avg_purchase_price = group_top_spenders.Price.mean()
Top_Spen = pd.DataFrame({"Purchase Count":purchase_count,
                        "Average Purchase Price": avg_purchase_price,
                        "Total Purchase Value": top_5})

Top_Spen["Average Purchase Price"] = Top_Spen["Average Purchase Price"].map("${:.2f}".format)
Top_Spen["Total Purchase Value"] = Top_Spen[ "Total Purchase Value"].map("${:.2f}".format)

Top_Spen
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
      <th>Average Purchase Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Eoda93</th>
      <td>$3.86</td>
      <td>3</td>
      <td>$11.58</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>$4.24</td>
      <td>3</td>
      <td>$12.73</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>$3.18</td>
      <td>4</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>$3.39</td>
      <td>4</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>Undirrala66</th>
      <td>$3.41</td>
      <td>5</td>
      <td>$17.06</td>
    </tr>
  </tbody>
</table>
</div>



# Most Popular Items


```python
# calculating the most popular items
new_purchase_data = purchase_data[["Item ID","Item Name","Price"]]
grouped_data_ID_Name = new_purchase_data.groupby(["Item ID","Item Name"])
total_purchase_value = grouped_data_ID_Name.Price.sum()
average_purchase_value = grouped_data_ID_Name.Price.mean()
purchase_count = grouped_data_ID_Name.Price.count()
pop = pd.DataFrame({"Total purchase value": total_purchase_value,"Purchase Count":purchase_count,"Item Price": average_purchase_value})

popular = pop.sort_values("Purchase Count", ascending = False).head(5)
profitable = pop.sort_values("Total purchase value", ascending = False).head(5)
popular["Item Price"] = popular["Item Price"].map("${:.2f}".format)
popular["Total purchase value"] = popular["Total purchase value"].map("${:.2f}".format)
popular
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
      <th></th>
      <th>Item Price</th>
      <th>Purchase Count</th>
      <th>Total purchase value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>$2.35</td>
      <td>11</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>$2.23</td>
      <td>11</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>$2.07</td>
      <td>9</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>$1.24</td>
      <td>9</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>$1.49</td>
      <td>9</td>
      <td>$13.41</td>
    </tr>
  </tbody>
</table>
</div>



# Most Profitable Items


```python
#Calculating the most profitable items
profitable["Item Price"] = profitable["Item Price"].map("${:.2f}".format)
profitable["Total purchase value"] = profitable["Total purchase value"].map("${:.2f}".format)
profitable
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
      <th></th>
      <th>Item Price</th>
      <th>Purchase Count</th>
      <th>Total purchase value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>$4.14</td>
      <td>9</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>$4.25</td>
      <td>7</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>$4.95</td>
      <td>6</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>$4.87</td>
      <td>6</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>$3.61</td>
      <td>8</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>


