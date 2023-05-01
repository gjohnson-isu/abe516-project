# ABE 516 Project Data Wrangling 2

## Nitrate Concentration Data


```python
#imports
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import math
import numpy as np
import datetime as dt

# allow plots to appear directly in the notebook
%matplotlib inline
```

**Read in Data**


```python
wq_raw = pd.read_csv("SH_WQ_raw_2022.csv")
wq_raw.head()
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
      <th>Case Number</th>
      <th>Scientist</th>
      <th>Matrix</th>
      <th>Project Name</th>
      <th>Submission Date</th>
      <th>Comments</th>
      <th>Units</th>
      <th>Sample Number</th>
      <th>Site ID</th>
      <th>Bottle No</th>
      <th>NO3-N</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>4/12/2022</td>
      <td>NaN</td>
      <td>mg/L</td>
      <td>22x0132-001</td>
      <td>SH210101</td>
      <td>1.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>4/12/2022</td>
      <td>NaN</td>
      <td>mg/L</td>
      <td>22x0132-002</td>
      <td>SH210102</td>
      <td>2.0</td>
      <td>0.4</td>
    </tr>
    <tr>
      <th>2</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>4/12/2022</td>
      <td>NaN</td>
      <td>mg/L</td>
      <td>22x0132-003</td>
      <td>SH210103</td>
      <td>3.0</td>
      <td>&lt; 0.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>4/12/2022</td>
      <td>NaN</td>
      <td>mg/L</td>
      <td>22x0132-004</td>
      <td>SH210104</td>
      <td>4.0</td>
      <td>6.1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>4/12/2022</td>
      <td>NaN</td>
      <td>mg/L</td>
      <td>22x0132-005</td>
      <td>SH210105</td>
      <td>5.0</td>
      <td>5.8</td>
    </tr>
  </tbody>
</table>
</div>



**Change Data Types**


```python
wq_raw.dtypes
```




    Case Number         object
    Scientist           object
    Matrix              object
    Project Name        object
    Submission Date     object
    Comments            object
    Units               object
    Sample Number       object
    Site ID             object
    Bottle No          float64
    NO3-N               object
    dtype: object




```python
wq_raw = wq_raw.astype({"Case Number":"string",
                       "Scientist":"string",
                       "Matrix":"string",
                       "Project Name":"string", 
                       "Comments":"string",
                       "Units":"string",
                       "Sample Number":"string",
                       "Site ID":"string", 
                        })
wq_raw["Submission Date"]=pd.to_datetime(wq_raw["Submission Date"])
print(wq_raw.dtypes)
```

    Case Number                string
    Scientist                  string
    Matrix                     string
    Project Name               string
    Submission Date    datetime64[ns]
    Comments                   string
    Units                      string
    Sample Number              string
    Site ID                    string
    Bottle No                 float64
    NO3-N                      object
    dtype: object
    

**Change Blanks so conversion of datatypes later is correct**


```python
wq_raw.loc[wq_raw["Site ID"] == "BLANK", "Site ID"] = "000000BL"
```

### Separate Site ID Column into Separate Columns


```python
wq_raw["Site ID"][0][0:1]
```




    'S'




```python
wq_raw["Site Code"]= wq_raw["Site ID"].str.slice(0,2)
wq_raw["Year Code"]= wq_raw["Site ID"].str.slice(2,3)
wq_raw["DOY"]= wq_raw["Site ID"].str.slice(3,6)
wq_raw["Sample Location"]= wq_raw["Site ID"].str.slice(6,8)
wq_raw.head()
wq_raw[5:25]
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
      <th>Case Number</th>
      <th>Scientist</th>
      <th>Matrix</th>
      <th>Project Name</th>
      <th>Submission Date</th>
      <th>Comments</th>
      <th>Units</th>
      <th>Sample Number</th>
      <th>Site ID</th>
      <th>Bottle No</th>
      <th>NO3-N</th>
      <th>Site Code</th>
      <th>Year Code</th>
      <th>DOY</th>
      <th>Sample Location</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-006</td>
      <td>SH210106</td>
      <td>6.0</td>
      <td>1.9</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>06</td>
    </tr>
    <tr>
      <th>6</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-007</td>
      <td>SH2101BX</td>
      <td>7.0</td>
      <td>11.2</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>BX</td>
    </tr>
    <tr>
      <th>7</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-008</td>
      <td>SH2101CK</td>
      <td>8.0</td>
      <td>13.6</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>CK</td>
    </tr>
    <tr>
      <th>8</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-009</td>
      <td>000000BL</td>
      <td>9.0</td>
      <td>&lt; 0.3</td>
      <td>00</td>
      <td>0</td>
      <td>000</td>
      <td>BL</td>
    </tr>
    <tr>
      <th>9</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-001</td>
      <td>SH211601</td>
      <td>1.0</td>
      <td>2.2</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>01</td>
    </tr>
    <tr>
      <th>10</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-002</td>
      <td>SH211602</td>
      <td>2.0</td>
      <td>0.6</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>02</td>
    </tr>
    <tr>
      <th>11</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-003</td>
      <td>SH211603</td>
      <td>3.0</td>
      <td>&lt; 0.3</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>03</td>
    </tr>
    <tr>
      <th>12</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-004</td>
      <td>SH211604</td>
      <td>4.0</td>
      <td>5.9</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>04</td>
    </tr>
    <tr>
      <th>13</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-005</td>
      <td>SH211605</td>
      <td>5.0</td>
      <td>6.6</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>05</td>
    </tr>
    <tr>
      <th>14</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-006</td>
      <td>SH211606</td>
      <td>6.0</td>
      <td>4</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>06</td>
    </tr>
    <tr>
      <th>15</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-007</td>
      <td>SH2116BX</td>
      <td>7.0</td>
      <td>18.9</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>BX</td>
    </tr>
    <tr>
      <th>16</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-008</td>
      <td>SH2116CK</td>
      <td>8.0</td>
      <td>13.8</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>CK</td>
    </tr>
    <tr>
      <th>17</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-009</td>
      <td>000000BL</td>
      <td>9.0</td>
      <td>&lt; 0.3</td>
      <td>00</td>
      <td>0</td>
      <td>000</td>
      <td>BL</td>
    </tr>
    <tr>
      <th>18</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-001</td>
      <td>SH212901</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>01</td>
    </tr>
    <tr>
      <th>19</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-002</td>
      <td>SH212902</td>
      <td>2.0</td>
      <td>&lt; 0.3</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>02</td>
    </tr>
    <tr>
      <th>20</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-003</td>
      <td>SH212903</td>
      <td>3.0</td>
      <td>&lt; 0.3</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>03</td>
    </tr>
    <tr>
      <th>21</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-004</td>
      <td>SH212904</td>
      <td>4.0</td>
      <td>5.4</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>04</td>
    </tr>
    <tr>
      <th>22</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-005</td>
      <td>SH212905</td>
      <td>5.0</td>
      <td>3.2</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>05</td>
    </tr>
    <tr>
      <th>23</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-006</td>
      <td>SH212906</td>
      <td>6.0</td>
      <td>3.6</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>06</td>
    </tr>
    <tr>
      <th>24</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-007</td>
      <td>SH2129BX</td>
      <td>7.0</td>
      <td>23.5</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>BX</td>
    </tr>
  </tbody>
</table>
</div>




```python
wq_raw.dtypes
```




    Case Number                string
    Scientist                  string
    Matrix                     string
    Project Name               string
    Submission Date    datetime64[ns]
    Comments                   string
    Units                      string
    Sample Number              string
    Site ID                    string
    Bottle No                 float64
    NO3-N                      object
    Site Code                  string
    Year Code                  string
    DOY                        string
    Sample Location            string
    dtype: object




```python
wq_raw["DOY"] = wq_raw["DOY"].astype(int)
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
      <th>Case Number</th>
      <th>Scientist</th>
      <th>Matrix</th>
      <th>Project Name</th>
      <th>Submission Date</th>
      <th>Comments</th>
      <th>Units</th>
      <th>Sample Number</th>
      <th>Site ID</th>
      <th>Bottle No</th>
      <th>NO3-N</th>
      <th>Site Code</th>
      <th>Year Code</th>
      <th>DOY</th>
      <th>Sample Location</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-006</td>
      <td>SH210106</td>
      <td>6.0</td>
      <td>1.9</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>06</td>
    </tr>
    <tr>
      <th>6</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-007</td>
      <td>SH2101BX</td>
      <td>7.0</td>
      <td>11.2</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>BX</td>
    </tr>
    <tr>
      <th>7</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-008</td>
      <td>SH2101CK</td>
      <td>8.0</td>
      <td>13.6</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>CK</td>
    </tr>
    <tr>
      <th>8</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-009</td>
      <td>000000BL</td>
      <td>9.0</td>
      <td>&lt; 0.3</td>
      <td>00</td>
      <td>0</td>
      <td>0</td>
      <td>BL</td>
    </tr>
    <tr>
      <th>9</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-001</td>
      <td>SH211601</td>
      <td>1.0</td>
      <td>2.2</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>01</td>
    </tr>
    <tr>
      <th>10</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-002</td>
      <td>SH211602</td>
      <td>2.0</td>
      <td>0.6</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>02</td>
    </tr>
    <tr>
      <th>11</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-003</td>
      <td>SH211603</td>
      <td>3.0</td>
      <td>&lt; 0.3</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>03</td>
    </tr>
    <tr>
      <th>12</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-004</td>
      <td>SH211604</td>
      <td>4.0</td>
      <td>5.9</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>04</td>
    </tr>
    <tr>
      <th>13</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-005</td>
      <td>SH211605</td>
      <td>5.0</td>
      <td>6.6</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>05</td>
    </tr>
    <tr>
      <th>14</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-006</td>
      <td>SH211606</td>
      <td>6.0</td>
      <td>4</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>06</td>
    </tr>
    <tr>
      <th>15</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-007</td>
      <td>SH2116BX</td>
      <td>7.0</td>
      <td>18.9</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>BX</td>
    </tr>
    <tr>
      <th>16</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-008</td>
      <td>SH2116CK</td>
      <td>8.0</td>
      <td>13.8</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>CK</td>
    </tr>
    <tr>
      <th>17</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-009</td>
      <td>000000BL</td>
      <td>9.0</td>
      <td>&lt; 0.3</td>
      <td>00</td>
      <td>0</td>
      <td>0</td>
      <td>BL</td>
    </tr>
    <tr>
      <th>18</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-001</td>
      <td>SH212901</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>01</td>
    </tr>
    <tr>
      <th>19</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-002</td>
      <td>SH212902</td>
      <td>2.0</td>
      <td>&lt; 0.3</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>02</td>
    </tr>
    <tr>
      <th>20</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-003</td>
      <td>SH212903</td>
      <td>3.0</td>
      <td>&lt; 0.3</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>03</td>
    </tr>
    <tr>
      <th>21</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-004</td>
      <td>SH212904</td>
      <td>4.0</td>
      <td>5.4</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>04</td>
    </tr>
    <tr>
      <th>22</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-005</td>
      <td>SH212905</td>
      <td>5.0</td>
      <td>3.2</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>05</td>
    </tr>
    <tr>
      <th>23</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-006</td>
      <td>SH212906</td>
      <td>6.0</td>
      <td>3.6</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>06</td>
    </tr>
    <tr>
      <th>24</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-007</td>
      <td>SH2129BX</td>
      <td>7.0</td>
      <td>23.5</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>BX</td>
    </tr>
  </tbody>
</table>
</div>



**Create Sample Date Column**


```python
MMDD = "01/01"
YY = wq_raw["Year Code"][0]+wq_raw["Year Code"][1] # Use the first and second rows as a check
first_day_of_year = MMDD+"/"+YY
print(first_day_of_year)
```

    01/01/22
    


```python
first_day_of_year = pd.to_datetime(first_day_of_year)
```


```python
wq_raw["Sample Date"] = first_day_of_year
wq_raw["Sample Date"] = wq_raw["Sample Date"] + pd.to_timedelta((wq_raw["DOY"]-1), unit="d")
#first_day_of_year + dt.timedelta(days = wq_raw["DOY"])
wq_raw.head()
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
      <th>Case Number</th>
      <th>Scientist</th>
      <th>Matrix</th>
      <th>Project Name</th>
      <th>Submission Date</th>
      <th>Comments</th>
      <th>Units</th>
      <th>Sample Number</th>
      <th>Site ID</th>
      <th>Bottle No</th>
      <th>NO3-N</th>
      <th>Site Code</th>
      <th>Year Code</th>
      <th>DOY</th>
      <th>Sample Location</th>
      <th>Sample Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-001</td>
      <td>SH210101</td>
      <td>1.0</td>
      <td>3.3</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>01</td>
      <td>2022-04-11</td>
    </tr>
    <tr>
      <th>1</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-002</td>
      <td>SH210102</td>
      <td>2.0</td>
      <td>0.4</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>02</td>
      <td>2022-04-11</td>
    </tr>
    <tr>
      <th>2</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-003</td>
      <td>SH210103</td>
      <td>3.0</td>
      <td>&lt; 0.3</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>03</td>
      <td>2022-04-11</td>
    </tr>
    <tr>
      <th>3</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-004</td>
      <td>SH210104</td>
      <td>4.0</td>
      <td>6.1</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>04</td>
      <td>2022-04-11</td>
    </tr>
    <tr>
      <th>4</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-005</td>
      <td>SH210105</td>
      <td>5.0</td>
      <td>5.8</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>05</td>
      <td>2022-04-11</td>
    </tr>
  </tbody>
</table>
</div>




```python
wq_raw[5:25]
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
      <th>Case Number</th>
      <th>Scientist</th>
      <th>Matrix</th>
      <th>Project Name</th>
      <th>Submission Date</th>
      <th>Comments</th>
      <th>Units</th>
      <th>Sample Number</th>
      <th>Site ID</th>
      <th>Bottle No</th>
      <th>NO3-N</th>
      <th>Site Code</th>
      <th>Year Code</th>
      <th>DOY</th>
      <th>Sample Location</th>
      <th>Sample Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-006</td>
      <td>SH210106</td>
      <td>6.0</td>
      <td>1.9</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>06</td>
      <td>2022-04-11</td>
    </tr>
    <tr>
      <th>6</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-007</td>
      <td>SH2101BX</td>
      <td>7.0</td>
      <td>11.2</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>BX</td>
      <td>2022-04-11</td>
    </tr>
    <tr>
      <th>7</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-008</td>
      <td>SH2101CK</td>
      <td>8.0</td>
      <td>13.6</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>CK</td>
      <td>2022-04-11</td>
    </tr>
    <tr>
      <th>8</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-009</td>
      <td>000000BL</td>
      <td>9.0</td>
      <td>&lt; 0.3</td>
      <td>00</td>
      <td>0</td>
      <td>0</td>
      <td>BL</td>
      <td>2021-12-31</td>
    </tr>
    <tr>
      <th>9</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-001</td>
      <td>SH211601</td>
      <td>1.0</td>
      <td>2.2</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>01</td>
      <td>2022-04-26</td>
    </tr>
    <tr>
      <th>10</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-002</td>
      <td>SH211602</td>
      <td>2.0</td>
      <td>0.6</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>02</td>
      <td>2022-04-26</td>
    </tr>
    <tr>
      <th>11</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-003</td>
      <td>SH211603</td>
      <td>3.0</td>
      <td>&lt; 0.3</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>03</td>
      <td>2022-04-26</td>
    </tr>
    <tr>
      <th>12</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-004</td>
      <td>SH211604</td>
      <td>4.0</td>
      <td>5.9</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>04</td>
      <td>2022-04-26</td>
    </tr>
    <tr>
      <th>13</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-005</td>
      <td>SH211605</td>
      <td>5.0</td>
      <td>6.6</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>05</td>
      <td>2022-04-26</td>
    </tr>
    <tr>
      <th>14</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-006</td>
      <td>SH211606</td>
      <td>6.0</td>
      <td>4</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>06</td>
      <td>2022-04-26</td>
    </tr>
    <tr>
      <th>15</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-007</td>
      <td>SH2116BX</td>
      <td>7.0</td>
      <td>18.9</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>BX</td>
      <td>2022-04-26</td>
    </tr>
    <tr>
      <th>16</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-008</td>
      <td>SH2116CK</td>
      <td>8.0</td>
      <td>13.8</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>CK</td>
      <td>2022-04-26</td>
    </tr>
    <tr>
      <th>17</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-009</td>
      <td>000000BL</td>
      <td>9.0</td>
      <td>&lt; 0.3</td>
      <td>00</td>
      <td>0</td>
      <td>0</td>
      <td>BL</td>
      <td>2021-12-31</td>
    </tr>
    <tr>
      <th>18</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-001</td>
      <td>SH212901</td>
      <td>1.0</td>
      <td>0.8</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>01</td>
      <td>2022-05-09</td>
    </tr>
    <tr>
      <th>19</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-002</td>
      <td>SH212902</td>
      <td>2.0</td>
      <td>&lt; 0.3</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>02</td>
      <td>2022-05-09</td>
    </tr>
    <tr>
      <th>20</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-003</td>
      <td>SH212903</td>
      <td>3.0</td>
      <td>&lt; 0.3</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>03</td>
      <td>2022-05-09</td>
    </tr>
    <tr>
      <th>21</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-004</td>
      <td>SH212904</td>
      <td>4.0</td>
      <td>5.4</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>04</td>
      <td>2022-05-09</td>
    </tr>
    <tr>
      <th>22</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-005</td>
      <td>SH212905</td>
      <td>5.0</td>
      <td>3.2</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>05</td>
      <td>2022-05-09</td>
    </tr>
    <tr>
      <th>23</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-006</td>
      <td>SH212906</td>
      <td>6.0</td>
      <td>3.6</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>06</td>
      <td>2022-05-09</td>
    </tr>
    <tr>
      <th>24</th>
      <td>22x0211</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-05-13</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0211-007</td>
      <td>SH2129BX</td>
      <td>7.0</td>
      <td>23.5</td>
      <td>SH</td>
      <td>2</td>
      <td>129</td>
      <td>BX</td>
      <td>2022-05-09</td>
    </tr>
  </tbody>
</table>
</div>



**Replace Nitrate Concentration values below detection limit with zero**


```python
wq_raw["NO3-N"] = wq_raw["NO3-N"].astype(str)
```


```python
wq_raw["NO3-N-clean"] = np.where(wq_raw["NO3-N"].str.contains("<"), "0.0", wq_raw["NO3-N"])
wq_raw["NO3-N-clean"] = wq_raw["NO3-N-clean"].astype(float)
wq_raw[5:15]
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
      <th>Case Number</th>
      <th>Scientist</th>
      <th>Matrix</th>
      <th>Project Name</th>
      <th>Submission Date</th>
      <th>Comments</th>
      <th>Units</th>
      <th>Sample Number</th>
      <th>Site ID</th>
      <th>Bottle No</th>
      <th>NO3-N</th>
      <th>Site Code</th>
      <th>Year Code</th>
      <th>DOY</th>
      <th>Sample Location</th>
      <th>Sample Date</th>
      <th>NO3-N-clean</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-006</td>
      <td>SH210106</td>
      <td>6.0</td>
      <td>1.9</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>06</td>
      <td>2022-04-11</td>
      <td>1.9</td>
    </tr>
    <tr>
      <th>6</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-007</td>
      <td>SH2101BX</td>
      <td>7.0</td>
      <td>11.2</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>BX</td>
      <td>2022-04-11</td>
      <td>11.2</td>
    </tr>
    <tr>
      <th>7</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-008</td>
      <td>SH2101CK</td>
      <td>8.0</td>
      <td>13.6</td>
      <td>SH</td>
      <td>2</td>
      <td>101</td>
      <td>CK</td>
      <td>2022-04-11</td>
      <td>13.6</td>
    </tr>
    <tr>
      <th>8</th>
      <td>22x0132</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-12</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0132-009</td>
      <td>000000BL</td>
      <td>9.0</td>
      <td>&lt; 0.3</td>
      <td>00</td>
      <td>0</td>
      <td>0</td>
      <td>BL</td>
      <td>2021-12-31</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-001</td>
      <td>SH211601</td>
      <td>1.0</td>
      <td>2.2</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>01</td>
      <td>2022-04-26</td>
      <td>2.2</td>
    </tr>
    <tr>
      <th>10</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-002</td>
      <td>SH211602</td>
      <td>2.0</td>
      <td>0.6</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>02</td>
      <td>2022-04-26</td>
      <td>0.6</td>
    </tr>
    <tr>
      <th>11</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-003</td>
      <td>SH211603</td>
      <td>3.0</td>
      <td>&lt; 0.3</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>03</td>
      <td>2022-04-26</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-004</td>
      <td>SH211604</td>
      <td>4.0</td>
      <td>5.9</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>04</td>
      <td>2022-04-26</td>
      <td>5.9</td>
    </tr>
    <tr>
      <th>13</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-005</td>
      <td>SH211605</td>
      <td>5.0</td>
      <td>6.6</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>05</td>
      <td>2022-04-26</td>
      <td>6.6</td>
    </tr>
    <tr>
      <th>14</th>
      <td>22x0167</td>
      <td>Natalia Rogovska</td>
      <td>Water</td>
      <td>Shearer</td>
      <td>2022-04-28</td>
      <td>&lt;NA&gt;</td>
      <td>mg/L</td>
      <td>22x0167-006</td>
      <td>SH211606</td>
      <td>6.0</td>
      <td>4</td>
      <td>SH</td>
      <td>2</td>
      <td>116</td>
      <td>06</td>
      <td>2022-04-26</td>
      <td>4.0</td>
    </tr>
  </tbody>
</table>
</div>



### Create Final Clean NO3-N Concentration Dataset


```python
wq_clean = wq_raw[["Sample Date", "Site ID", "Site Code", "Sample Location", "NO3-N-clean"]]
wq_clean
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
      <th>Sample Date</th>
      <th>Site ID</th>
      <th>Site Code</th>
      <th>Sample Location</th>
      <th>NO3-N-clean</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2022-04-11</td>
      <td>SH210101</td>
      <td>SH</td>
      <td>01</td>
      <td>3.3000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2022-04-11</td>
      <td>SH210102</td>
      <td>SH</td>
      <td>02</td>
      <td>0.4000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2022-04-11</td>
      <td>SH210103</td>
      <td>SH</td>
      <td>03</td>
      <td>0.0000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2022-04-11</td>
      <td>SH210104</td>
      <td>SH</td>
      <td>04</td>
      <td>6.1000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2022-04-11</td>
      <td>SH210105</td>
      <td>SH</td>
      <td>05</td>
      <td>5.8000</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>103</th>
      <td>2022-08-05</td>
      <td>SH221704</td>
      <td>SH</td>
      <td>04</td>
      <td>15.2390</td>
    </tr>
    <tr>
      <th>104</th>
      <td>2022-08-05</td>
      <td>SH221705</td>
      <td>SH</td>
      <td>05</td>
      <td>16.0490</td>
    </tr>
    <tr>
      <th>105</th>
      <td>2022-08-05</td>
      <td>SH221706</td>
      <td>SH</td>
      <td>06</td>
      <td>9.5212</td>
    </tr>
    <tr>
      <th>106</th>
      <td>2022-08-05</td>
      <td>SH2217BX</td>
      <td>SH</td>
      <td>BX</td>
      <td>19.4780</td>
    </tr>
    <tr>
      <th>107</th>
      <td>2022-08-05</td>
      <td>SH2217CK</td>
      <td>SH</td>
      <td>CK</td>
      <td>8.5377</td>
    </tr>
  </tbody>
</table>
<p>108 rows × 5 columns</p>
</div>




```python
wq_clean.to_csv("no3_conc.csv", index = False)
```
