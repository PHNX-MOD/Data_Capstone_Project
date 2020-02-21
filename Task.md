### Importing the necessary libraries


```python
import pandas as pd #importing pandas
import seaborn as sns # importing seaborn library
import matplotlib.pyplot as plt #importing matpoltlib library
%matplotlib inline
```


```python
from plotly import __version__
from plotly.offline import download_plotlyjs, init_notebook_mode, plot, iplot
print(__version__) # requires version >= 1.9.0

import cufflinks as cf
init_notebook_mode(connected=True)
cf.go_offline()
```

    4.4.1



<script type="text/javascript">
window.PlotlyConfig = {MathJaxConfig: 'local'};
if (window.MathJax) {MathJax.Hub.Config({SVG: {font: "STIX-Web"}});}
if (typeof require !== 'undefined') {
require.undef("plotly");
requirejs.config({
    paths: {
        'plotly': ['https://cdn.plot.ly/plotly-latest.min']
    }
});
require(['plotly'], function(Plotly) {
    window._Plotly = Plotly;
});
}
</script>




<script type="text/javascript">
window.PlotlyConfig = {MathJaxConfig: 'local'};
if (window.MathJax) {MathJax.Hub.Config({SVG: {font: "STIX-Web"}});}
if (typeof require !== 'undefined') {
require.undef("plotly");
requirejs.config({
    paths: {
        'plotly': ['https://cdn.plot.ly/plotly-latest.min']
    }
});
require(['plotly'], function(Plotly) {
    window._Plotly = Plotly;
});
}
</script>



### Importing csv files from the directory


```python
baskets = pd.read_csv('baskets.csv') #reading baskets csv file as dataframe baskets.
customers = pd.read_csv('customers.csv') #reading customers csv file as dataframe customers.
transactions_df = pd.read_csv('transactions.csv') #reading transactions file as dataframe df_transactions.
```

### Analyzing Baskets table


```python
baskets['status'].value_counts()  # counting the different status to filter the necessary status.
```




    delivered            10154
    canceled_by_admin    10000
    returned              1173
    done                   443
    Name: status, dtype: int64




```python
baskets_df_by_year=baskets_df.groupby('year').count()
baskets_df_by_year['customer_id'].plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7fc80fdea310>




![png](output_7_1.png)


Fitering the baskets table with 'done' and 'dilivered' status



```python
baskets_df = baskets[(baskets['status']=='delivered') | ((baskets['status']=='done'))] #Fitering the baskets table with 'done' and 'dilivered' status
```

creating a 'year' column in the baskets_df table


```python
baskets_df['timestamp'] =  pd.to_datetime(baskets_df['timestamp']) # converting time 'str' to date format
baskets_df['year']= baskets_df['timestamp'].apply(lambda time: time.year) #creating a 'year' column in the baskets_df table
```


```python
baskets_df['year'].value_counts()
```




    2019    8034
    2020    2179
    2018     281
    2017     103
    Name: year, dtype: int64



year 2019: 8034,   year 2020:2179,  year 2018: 281, year 2017: 103


```python
baskets['status'].value_counts()   #plotting different 'status' in the baskets 
sns.countplot(x='status', data=baskets)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f4c464a9f10>




![png](output_14_1.png)


delivered     :       10154,
canceled_by_admin :    10000,
returned           :   1173,
done                :   443,

### Analysing Customers table


```python
customers['role'].value_counts()  #counting the number of customers and admin 
```




    customer    4217
    admin          6
    Name: role, dtype: int64



### Filtering the customers table with only 'customer' 'role'


```python
customers_df = customers[customers['role']=='customer'] #filtering customers from admin in the 'role' column
```


```python
customers_df['registered_at'] =  pd.to_datetime(customers_df['registered_at']) # converting 'str' registred date to datetime format
```


```python
customers_df['year']= customers_df['registered_at'].apply(lambda time: time.year) ##creating a 'year' column in the Customers table from the registred date
```

### Analysing Transaction table


```python
transactions_df['item_name'].value_counts() #Counting the different products of Coffee Crate Company
```




    coffee-mugbox             12021
    coffee-treats              9629
    coffee-scented-candles     2358
    coffee-stationery           918
    coffee-apparel              475
    Name: item_name, dtype: int64




```python
plt.figure(figsize=(9,5)) #plotting the products of Coffee Crate Company
sns.countplot(x='item_name', data=transactions_df)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7fb63519fb50>




![png](output_24_1.png)


## Merging tables

Customers table (Customers_df) has "id", the customer id is the main value. 
To return this result per customer id, each customer buys a basket (customer_id), and each basket has transactions. So, you can considering further, step by step:
First, I should get to know how many baskets each customer bought:

### Customers  c Inner join baskets b on c.id = b.customer_id 


```python
group_by_bc = pd.concat([customers_df,baskets_df]) #Merging the cutomers and baskets table
```


```python
group_by_bc_count= group_by_bc.groupby('year').count() #grouping by year
```


```python
group_by_bc.describe().transpose()
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
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>auth_id</th>
      <td>4217.0</td>
      <td>3.234902e+06</td>
      <td>1.815862e+06</td>
      <td>102030.0</td>
      <td>1656818.0</td>
      <td>3242448.0</td>
      <td>4794600.0</td>
      <td>6397631.0</td>
    </tr>
    <tr>
      <th>contact_id</th>
      <td>4217.0</td>
      <td>1.248503e+05</td>
      <td>1.434364e+04</td>
      <td>99993.0</td>
      <td>112564.0</td>
      <td>124673.0</td>
      <td>137317.0</td>
      <td>149836.0</td>
    </tr>
    <tr>
      <th>customer_id</th>
      <td>10597.0</td>
      <td>4.103412e+03</td>
      <td>2.201337e+03</td>
      <td>1.0</td>
      <td>2273.0</td>
      <td>3722.0</td>
      <td>5634.0</td>
      <td>9476.0</td>
    </tr>
    <tr>
      <th>discount_percentage</th>
      <td>10597.0</td>
      <td>7.858828e+00</td>
      <td>1.316015e+01</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>20.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>id</th>
      <td>14814.0</td>
      <td>1.810127e+05</td>
      <td>8.287419e+05</td>
      <td>1000.0</td>
      <td>7994.5</td>
      <td>92951.5</td>
      <td>207202.5</td>
      <td>10010102.0</td>
    </tr>
    <tr>
      <th>payment_id</th>
      <td>4217.0</td>
      <td>4.481868e+05</td>
      <td>1.367543e+05</td>
      <td>210392.0</td>
      <td>330032.0</td>
      <td>447639.0</td>
      <td>566931.0</td>
      <td>684350.0</td>
    </tr>
    <tr>
      <th>year</th>
      <td>14814.0</td>
      <td>2.019094e+03</td>
      <td>4.612838e-01</td>
      <td>2017.0</td>
      <td>2019.0</td>
      <td>2019.0</td>
      <td>2019.0</td>
      <td>2020.0</td>
    </tr>
  </tbody>
</table>
</div>



## Second, to get the sum of each basket bought 


```python
group_by_bt = pd.concat([baskets_df,transactions_df]) #merging the Basket table and the Transaction table
```


```python
group_by_bt['id'].value_counts().sum() #Number of baskets
```




    35998




```python
group_by_bt['price'].sum() #sum of all the baskets 
```




    57024500.0



### 1584.1018945496971 $ / basket



```python
df= group_by_bc_count.drop(['auth_id','contact_id','customer_id','discount_percentage','id','status','payment_id','registered_at','role','timestamp','tracker_code'],axis=1)
df['Number of baskets']=group_by_bc_count['id']
df['Number of customes'] =  group_by_bc_count ['status']
df['Revenue/customer']=(df['Number of baskets']/df['Number of customes'])*1584.10
df
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
      <th>Number of baskets</th>
      <th>Number of customes</th>
      <th>Revenue/customer</th>
    </tr>
    <tr>
      <th>year</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017</th>
      <td>156</td>
      <td>103</td>
      <td>2399.219417</td>
    </tr>
    <tr>
      <th>2018</th>
      <td>479</td>
      <td>281</td>
      <td>2700.298577</td>
    </tr>
    <tr>
      <th>2019</th>
      <td>12000</td>
      <td>8034</td>
      <td>2366.094100</td>
    </tr>
    <tr>
      <th>2020</th>
      <td>2179</td>
      <td>2179</td>
      <td>1584.100000</td>
    </tr>
  </tbody>
</table>
</div>



### Assuming production cost + acquisition cost + discount upto 60 percent of the revenue, Profit can be calculated as  


```python
df['Profit/customer']=df['Revenue/customer']*0.4
df
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
      <th>Number of baskets</th>
      <th>Number of customes</th>
      <th>Revenue/customer</th>
      <th>Profit/customer</th>
    </tr>
    <tr>
      <th>year</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017</th>
      <td>156</td>
      <td>103</td>
      <td>2399.219417</td>
      <td>959.687767</td>
    </tr>
    <tr>
      <th>2018</th>
      <td>479</td>
      <td>281</td>
      <td>2700.298577</td>
      <td>1080.119431</td>
    </tr>
    <tr>
      <th>2019</th>
      <td>12000</td>
      <td>8034</td>
      <td>2366.094100</td>
      <td>946.437640</td>
    </tr>
    <tr>
      <th>2020</th>
      <td>2179</td>
      <td>2179</td>
      <td>1584.100000</td>
      <td>633.640000</td>
    </tr>
  </tbody>
</table>
</div>



### Taking an average of 1.5 years of customer lifetime, then the Customer Lifetime Value can given as 


```python
df['CLTV']=df['Profit/customer']*1.5
df
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
      <th>Number of baskets</th>
      <th>Number of customes</th>
      <th>Revenue/customer</th>
      <th>Profit/customer</th>
      <th>CLTV</th>
    </tr>
    <tr>
      <th>year</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017</th>
      <td>156</td>
      <td>103</td>
      <td>2399.219417</td>
      <td>959.687767</td>
      <td>1439.531650</td>
    </tr>
    <tr>
      <th>2018</th>
      <td>479</td>
      <td>281</td>
      <td>2700.298577</td>
      <td>1080.119431</td>
      <td>1620.179146</td>
    </tr>
    <tr>
      <th>2019</th>
      <td>12000</td>
      <td>8034</td>
      <td>2366.094100</td>
      <td>946.437640</td>
      <td>1419.656460</td>
    </tr>
    <tr>
      <th>2020</th>
      <td>2179</td>
      <td>2179</td>
      <td>1584.100000</td>
      <td>633.640000</td>
      <td>950.460000</td>
    </tr>
  </tbody>
</table>
</div>



# Plotting the graph from the above table
From the graph it can be seen that the increase in all the parameters in 2019, 2020 downfall, this might be the reason of inaccurate data


```python
df[['Number of baskets','Number of customes','Revenue/customer','Profit/customer','CLTV']].iplot(kind='spread')
```


<div>


            <div id="1da81297-3f50-467b-acf3-c90346934d03" class="plotly-graph-div" style="height:525px; width:100%;"></div>
            <script type="text/javascript">
                require(["plotly"], function(Plotly) {
                    window.PLOTLYENV=window.PLOTLYENV || {};
                    window.PLOTLYENV.BASE_URL='https://plot.ly';

                if (document.getElementById("1da81297-3f50-467b-acf3-c90346934d03")) {
                    Plotly.newPlot(
                        '1da81297-3f50-467b-acf3-c90346934d03',
                        [{"line": {"color": "rgba(255, 153, 51, 1.0)", "dash": "solid", "shape": "linear", "width": 1.3}, "mode": "lines", "name": "Number of baskets", "text": "", "type": "scatter", "x": [2017, 2018, 2019, 2020], "y": [156, 479, 12000, 2179]}, {"line": {"color": "rgba(55, 128, 191, 1.0)", "dash": "solid", "shape": "linear", "width": 1.3}, "mode": "lines", "name": "Number of customes", "text": "", "type": "scatter", "x": [2017, 2018, 2019, 2020], "y": [103, 281, 8034, 2179]}, {"line": {"color": "rgba(50, 171, 96, 1.0)", "dash": "solid", "shape": "linear", "width": 1.3}, "mode": "lines", "name": "Revenue/customer", "text": "", "type": "scatter", "x": [2017, 2018, 2019, 2020], "y": [2399.219417475728, 2700.2985765124554, 2366.0941000746825, 1584.1]}, {"line": {"color": "rgba(128, 0, 128, 1.0)", "dash": "solid", "shape": "linear", "width": 1.3}, "mode": "lines", "name": "Profit/customer", "text": "", "type": "scatter", "x": [2017, 2018, 2019, 2020], "y": [959.6877669902912, 1080.1194306049822, 946.437640029873, 633.64]}, {"line": {"color": "rgba(219, 64, 82, 1.0)", "dash": "solid", "shape": "linear", "width": 1.3}, "mode": "lines", "name": "CLTV", "text": "", "type": "scatter", "x": [2017, 2018, 2019, 2020], "y": [1439.5316504854368, 1620.1791459074734, 1419.6564600448096, 950.46]}, {"connectgaps": false, "fill": "tozeroy", "line": {"color": "green", "dash": "solid", "shape": "linear", "width": 0.5}, "mode": "lines", "name": "Spread", "showlegend": false, "type": "scatter", "x": [2017, 2018, 2019, 2020], "xaxis": "x2", "y": [53.0, 198.0, 3966.0, 0.0], "yaxis": "y2"}, {"connectgaps": false, "fill": "tozeroy", "line": {"color": "red", "dash": "solid", "shape": "linear", "width": 0.5}, "mode": "lines", "name": "Spread", "showlegend": false, "type": "scatter", "x": [2017, 2018, 2019, 2020], "xaxis": "x2", "y": ["", "", "", ""], "yaxis": "y2"}],
                        {"hovermode": "x", "legend": {"bgcolor": "#F5F6F9", "font": {"color": "#4D5663"}}, "paper_bgcolor": "#F5F6F9", "plot_bgcolor": "#F5F6F9", "template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "pie": [{"automargin": true, "type": "pie"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "coloraxis": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}}}, "title": {"font": {"color": "#4D5663"}}, "xaxis": {"gridcolor": "#E1E5ED", "showgrid": true, "tickfont": {"color": "#4D5663"}, "title": {"font": {"color": "#4D5663"}, "text": ""}, "zerolinecolor": "#E1E5ED"}, "xaxis2": {"anchor": "y2", "gridcolor": "#E1E5ED", "showgrid": true, "showticklabels": false, "tickfont": {"color": "#4D5663"}, "title": {"font": {"color": "#4D5663"}, "text": ""}, "zerolinecolor": "#E1E5ED"}, "yaxis": {"domain": [0.3, 1], "gridcolor": "#E1E5ED", "showgrid": true, "tickfont": {"color": "#4D5663"}, "title": {"font": {"color": "#4D5663"}, "text": ""}, "zerolinecolor": "#E1E5ED"}, "yaxis2": {"domain": [0, 0.25], "gridcolor": "#E1E5ED", "showgrid": true, "tickfont": {"color": "#4D5663"}, "title": {"font": {"color": "#4D5663"}, "text": "Spread"}, "zerolinecolor": "#E1E5ED"}},
                        {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly", "responsive": true}
                    ).then(function(){

var gd = document.getElementById('1da81297-3f50-467b-acf3-c90346934d03');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })
                };
                });
            </script>
        </div>



```python
df_CLTV_avg=df.drop([2020],axis=0) # dropping year 2020 data
df_CLTV_avg['CLTV'].mean() # Taking average from last 3 years
```




    1493.1224188125732



### Since due to the lack of accurate data in the past year, I have taken the mean of last 3 years CLTV, that would be 1493.122$


```python

```
