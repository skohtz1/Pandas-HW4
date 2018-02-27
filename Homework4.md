

```python
import pandas as pd
import numpy as np
import os
import csv
```


```python
schoolfile = "raw_data/schools_complete.csv"
studentfile = "raw_data/students_complete.csv"
```


```python
df_school = pd.read_csv(schoolfile)
df_student = pd.read_csv(studentfile)
```


```python
del df_school["School ID"]
```

Analysis of City School Data
Observations: All schools have passing scores for the reading test, so the focus should be to improve math scores. It is important to note that smaller sized schools have higher scores. It is also interesting to note that schools with higher budgets do not necessarily get higher scores. This suggests that it is more immportant for students to have more attention for their teachers (at a smaller school), then for the school to have a higher budget. Also, Charter schools have overall higher test scores, as all of Charter School students passed both the reading and math test. 


```python
totalschool = df_school["name"].count()
totalstudent = df_student["name"].count()
totalbudget = df_school["budget"].sum()
avgmath = round(df_student["math_score"].mean(),2)
avgread = round(df_student["reading_score"].mean(),2)
numpassread = df_student.loc[df_student["reading_score"] > 59,:]
numpassmath = df_student.loc[df_student["math_score"]>59,:]
numpassboth = numpassmath.loc[numpassmath["reading_score"]>59,:]
percpassread = (numpassread["reading_score"].count()/totalstudent)*100
percpassmath = round((numpassmath["math_score"].count()/totalstudent)*100,2)
#overall average is the percentage of both passing
overallavg = round((numpassboth["math_score"].count()/totalstudent)*100,2)

```


```python
del df_school["size"]
```


```python
del df_student["Student ID"]
```

District Summary


```python
frame_df = pd.DataFrame({
    "Total Schools":[totalschool],
    "Total Students":[totalstudent],
    "Total Budget":[totalbudget],
    "Average Math Score":[avgmath],
    "Average Reading Score":[avgread],
    "% Passing Math":[percpassmath],
    "% Passing Reading":[percpassread],
    "% Overall Passing Rate":[overallavg],
})
frame_df
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
      <th>% Overall Passing Rate</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>Total Budget</th>
      <th>Total Schools</th>
      <th>Total Students</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>92.45</td>
      <td>92.45</td>
      <td>100.0</td>
      <td>78.99</td>
      <td>81.88</td>
      <td>24649428</td>
      <td>15</td>
      <td>39170</td>
    </tr>
  </tbody>
</table>
</div>



School Summary


```python
totalss2 = df_student.groupby(['school'])
totalss1 = df_school.groupby(['name'])
numpassread.groupby("school")
numpassmath.groupby("school")
numpassboth.groupby("school")
numpassr = numpassread["school"].value_counts()
numpassm = numpassmath["school"].value_counts()
numpassb = numpassboth["school"].value_counts()

district = totalss1["type"].any()
budgetsch = totalss1["budget"].sum()
mathavgschool = totalss2["math_score"].mean()
readavgschool = totalss2["reading_score"].mean()
totalstuds = totalss2["name"].count()
totalstuds = totalstuds.rename(columns = {"name":"Total Students"})
state_summary_table = pd.DataFrame({"Average Math Score":mathavgschool,
                                   "Average Reading Score":readavgschool,
                                    "Average Total Score": (mathavgschool+readavgschool)/2,
                                   "Total Students":totalstuds,
                                   "Total Budget per School": budgetsch,
                                   "% Pass Reading": (numpassr/totalstuds)*100,
                                   "% Pass Math": (numpassm/totalstuds)*100,
                                    "% Pass Both": (numpassb/totalstuds)*100,
                                   "Type": district})
state_summary_table["Budget per Student"] = state_summary_table["Total Budget per School"]/state_summary_table["Total Students"]

state_summary_table
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
      <th>% Pass Both</th>
      <th>% Pass Math</th>
      <th>% Pass Reading</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>Average Total Score</th>
      <th>Total Budget per School</th>
      <th>Total Students</th>
      <th>Type</th>
      <th>Budget per Student</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>89.529743</td>
      <td>89.529743</td>
      <td>100.0</td>
      <td>77.048432</td>
      <td>81.033963</td>
      <td>79.041198</td>
      <td>3124928</td>
      <td>4976</td>
      <td>District</td>
      <td>628.0</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>83.518837</td>
      <td>1081356</td>
      <td>1858</td>
      <td>Charter</td>
      <td>582.0</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>88.436758</td>
      <td>88.436758</td>
      <td>100.0</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>78.934893</td>
      <td>1884411</td>
      <td>2949</td>
      <td>District</td>
      <td>639.0</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>89.302665</td>
      <td>89.302665</td>
      <td>100.0</td>
      <td>77.102592</td>
      <td>80.746258</td>
      <td>78.924425</td>
      <td>1763916</td>
      <td>2739</td>
      <td>District</td>
      <td>644.0</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>83.584128</td>
      <td>917500</td>
      <td>1468</td>
      <td>Charter</td>
      <td>625.0</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>89.083064</td>
      <td>89.083064</td>
      <td>100.0</td>
      <td>77.289752</td>
      <td>80.934412</td>
      <td>79.112082</td>
      <td>3022020</td>
      <td>4635</td>
      <td>District</td>
      <td>652.0</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>83.803279</td>
      <td>83.814988</td>
      <td>83.809133</td>
      <td>248087</td>
      <td>427</td>
      <td>Charter</td>
      <td>581.0</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>88.858416</td>
      <td>88.858416</td>
      <td>100.0</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>78.906068</td>
      <td>1910635</td>
      <td>2917</td>
      <td>District</td>
      <td>655.0</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>89.182945</td>
      <td>89.182945</td>
      <td>100.0</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>79.019429</td>
      <td>3094650</td>
      <td>4761</td>
      <td>District</td>
      <td>650.0</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>83.839917</td>
      <td>84.044699</td>
      <td>83.942308</td>
      <td>585858</td>
      <td>962</td>
      <td>Charter</td>
      <td>609.0</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>88.547137</td>
      <td>88.547137</td>
      <td>100.0</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>78.793698</td>
      <td>2547363</td>
      <td>3999</td>
      <td>District</td>
      <td>637.0</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>83.359455</td>
      <td>83.725724</td>
      <td>83.542589</td>
      <td>1056600</td>
      <td>1761</td>
      <td>Charter</td>
      <td>600.0</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>83.633639</td>
      <td>1043130</td>
      <td>1635</td>
      <td>Charter</td>
      <td>638.0</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>83.631844</td>
      <td>1319574</td>
      <td>2283</td>
      <td>Charter</td>
      <td>578.0</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>83.682222</td>
      <td>83.955000</td>
      <td>83.818611</td>
      <td>1049400</td>
      <td>1800</td>
      <td>Charter</td>
      <td>583.0</td>
    </tr>
  </tbody>
</table>
</div>



Top Performing Schools


```python

top5 = state_summary_table.sort_values(["Average Total Score"], ascending=False)
top5 = top5.head(5)
top5
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
      <th>% Pass Both</th>
      <th>% Pass Math</th>
      <th>% Pass Reading</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>Average Total Score</th>
      <th>Total Budget per School</th>
      <th>Total Students</th>
      <th>Type</th>
      <th>Budget per Student</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Pena High School</th>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>83.839917</td>
      <td>84.044699</td>
      <td>83.942308</td>
      <td>585858</td>
      <td>962</td>
      <td>Charter</td>
      <td>609.0</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>83.682222</td>
      <td>83.955000</td>
      <td>83.818611</td>
      <td>1049400</td>
      <td>1800</td>
      <td>Charter</td>
      <td>583.0</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>83.803279</td>
      <td>83.814988</td>
      <td>83.809133</td>
      <td>248087</td>
      <td>427</td>
      <td>Charter</td>
      <td>581.0</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>83.633639</td>
      <td>1043130</td>
      <td>1635</td>
      <td>Charter</td>
      <td>638.0</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>83.631844</td>
      <td>1319574</td>
      <td>2283</td>
      <td>Charter</td>
      <td>578.0</td>
    </tr>
  </tbody>
</table>
</div>



Bottom Performing Schools


```python
bottom5 = state_summary_table.sort_values(["Average Total Score"], ascending = True)
bottom5 = bottom5.head(5)
bottom5
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
      <th>% Pass Both</th>
      <th>% Pass Math</th>
      <th>% Pass Reading</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>Average Total Score</th>
      <th>Total Budget per School</th>
      <th>Total Students</th>
      <th>Type</th>
      <th>Budget per Student</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Rodriguez High School</th>
      <td>88.547137</td>
      <td>88.547137</td>
      <td>100.0</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>78.793698</td>
      <td>2547363</td>
      <td>3999</td>
      <td>District</td>
      <td>637.0</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>88.858416</td>
      <td>88.858416</td>
      <td>100.0</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>78.906068</td>
      <td>1910635</td>
      <td>2917</td>
      <td>District</td>
      <td>655.0</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>89.302665</td>
      <td>89.302665</td>
      <td>100.0</td>
      <td>77.102592</td>
      <td>80.746258</td>
      <td>78.924425</td>
      <td>1763916</td>
      <td>2739</td>
      <td>District</td>
      <td>644.0</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>88.436758</td>
      <td>88.436758</td>
      <td>100.0</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>78.934893</td>
      <td>1884411</td>
      <td>2949</td>
      <td>District</td>
      <td>639.0</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>89.182945</td>
      <td>89.182945</td>
      <td>100.0</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>79.019429</td>
      <td>3094650</td>
      <td>4761</td>
      <td>District</td>
      <td>650.0</td>
    </tr>
  </tbody>
</table>
</div>



Reading Scores by Grade


```python
only_9 = df_student.loc[df_student["grade"] == "9th",:]
only_9 = only_9.groupby("school")
avgread9 = only_9["reading_score"].mean()

only_10 = df_student.loc[df_student["grade"] == "10th",:]
only_10 = only_10.groupby("school")
avgread10 = only_10["reading_score"].mean()

only_11 = df_student.loc[df_student["grade"] == "11th",:]
only_11 = only_11.groupby("school")
avgread11 = only_11["reading_score"].mean()

only_12 = df_student.loc[df_student["grade"] == "12th",:]
only_12 = only_12.groupby("school")
avgread12 = only_12["reading_score"].mean()


readg_summary_table = pd.DataFrame({"9th":avgread9,
                                   "10th":avgread10,
                                   "11th":avgread11,
                                   "12th":avgread12})
#reading scores summary by grade

readg_summary_table
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
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
      <th>9th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>80.907183</td>
      <td>80.945643</td>
      <td>80.912451</td>
      <td>81.303155</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>84.253219</td>
      <td>83.788382</td>
      <td>84.287958</td>
      <td>83.676136</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>81.408912</td>
      <td>80.640339</td>
      <td>81.384863</td>
      <td>81.198598</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>81.262712</td>
      <td>80.403642</td>
      <td>80.662338</td>
      <td>80.632653</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>83.706897</td>
      <td>84.288089</td>
      <td>84.013699</td>
      <td>83.369193</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>80.660147</td>
      <td>81.396140</td>
      <td>80.857143</td>
      <td>80.866860</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.324561</td>
      <td>83.815534</td>
      <td>84.698795</td>
      <td>83.677165</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>81.512386</td>
      <td>81.417476</td>
      <td>80.305983</td>
      <td>81.290284</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>80.773431</td>
      <td>80.616027</td>
      <td>81.227564</td>
      <td>81.260714</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.612000</td>
      <td>84.335938</td>
      <td>84.591160</td>
      <td>83.807273</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>80.629808</td>
      <td>80.864811</td>
      <td>80.376426</td>
      <td>80.993127</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>83.441964</td>
      <td>84.373786</td>
      <td>82.781671</td>
      <td>84.122642</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>84.254157</td>
      <td>83.585542</td>
      <td>83.831361</td>
      <td>83.728850</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>84.021452</td>
      <td>83.764608</td>
      <td>84.317673</td>
      <td>83.939778</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.812757</td>
      <td>84.156322</td>
      <td>84.073171</td>
      <td>83.833333</td>
    </tr>
  </tbody>
</table>
</div>



Math Scores by Grade


```python
only_9m = df_student.loc[df_student["grade"] == "9th",:]
only_9m = only_9m.groupby("school")
avgmath9 = only_9m["math_score"].mean()

only_10m = df_student.loc[df_student["grade"] == "10th",:]
only_10m = only_10m.groupby("school")
avgmath10 = only_10m["math_score"].mean()

only_11m = df_student.loc[df_student["grade"] == "11th",:]
only_11m = only_11m.groupby("school")
avgmath11 = only_11m["math_score"].mean()

only_12m = df_student.loc[df_student["grade"] == "12th",:]
only_12m = only_12m.groupby("school")
avgmath12 = only_12m["math_score"].mean()


mathg_summary_table = pd.DataFrame({"9th":avgmath9,
                                   "10th":avgmath10,
                                   "11th":avgmath11,
                                   "12th":avgmath12})
#mathscores summary by grade
mathg_summary_table
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
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
      <th>9th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>76.996772</td>
      <td>77.515588</td>
      <td>76.492218</td>
      <td>77.083676</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.154506</td>
      <td>82.765560</td>
      <td>83.277487</td>
      <td>83.094697</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>76.539974</td>
      <td>76.884344</td>
      <td>77.151369</td>
      <td>76.403037</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>77.672316</td>
      <td>76.918058</td>
      <td>76.179963</td>
      <td>77.361345</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>84.229064</td>
      <td>83.842105</td>
      <td>83.356164</td>
      <td>82.044010</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>77.337408</td>
      <td>77.136029</td>
      <td>77.186567</td>
      <td>77.438495</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.429825</td>
      <td>85.000000</td>
      <td>82.855422</td>
      <td>83.787402</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>75.908735</td>
      <td>76.446602</td>
      <td>77.225641</td>
      <td>77.027251</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>76.691117</td>
      <td>77.491653</td>
      <td>76.863248</td>
      <td>77.187857</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.372000</td>
      <td>84.328125</td>
      <td>84.121547</td>
      <td>83.625455</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>76.612500</td>
      <td>76.395626</td>
      <td>77.690748</td>
      <td>76.859966</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>82.917411</td>
      <td>83.383495</td>
      <td>83.778976</td>
      <td>83.420755</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.087886</td>
      <td>83.498795</td>
      <td>83.497041</td>
      <td>83.590022</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.724422</td>
      <td>83.195326</td>
      <td>83.035794</td>
      <td>83.085578</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>84.010288</td>
      <td>83.836782</td>
      <td>83.644986</td>
      <td>83.264706</td>
    </tr>
  </tbody>
</table>
</div>




```python
bins = [0, 600, 625, 650, 675]
group_names = ['<600', '<625', '<650', '<675']
state_summary_table["Budget Summary"] = pd.cut(state_summary_table["Budget per Student"], bins, labels=group_names)
```

Scores by School Budget per Student



```python
state_groups = state_summary_table.groupby("Budget Summary")
avgbudget = state_groups.mean()
del avgbudget["Total Budget per School"]
del avgbudget["Total Students"]
del avgbudget["Budget per Student"]

avgbudget
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
      <th>% Pass Both</th>
      <th>% Pass Math</th>
      <th>% Pass Reading</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>Average Total Score</th>
    </tr>
    <tr>
      <th>Budget Summary</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;600</th>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>83.436210</td>
      <td>83.892196</td>
      <td>83.664203</td>
    </tr>
    <tr>
      <th>&lt;625</th>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>83.595708</td>
      <td>83.930728</td>
      <td>83.763218</td>
    </tr>
    <tr>
      <th>&lt;650</th>
      <td>90.833208</td>
      <td>90.833208</td>
      <td>100.0</td>
      <td>78.032719</td>
      <td>81.416375</td>
      <td>79.724547</td>
    </tr>
    <tr>
      <th>&lt;675</th>
      <td>88.970740</td>
      <td>88.970740</td>
      <td>100.0</td>
      <td>76.959583</td>
      <td>81.058567</td>
      <td>79.009075</td>
    </tr>
  </tbody>
</table>
</div>



Scores by School Size


```python
bins1 = [0,1000,2000,3000,4000,5000]
group_names1 = ['Little <1000', 'Small <2000','Medium <3000','Big <4000','Large <5000']
state_summary_table["Size of School"] = pd.cut(state_summary_table["Total Students"], bins1, labels=group_names1)
state_groups1 = state_summary_table.groupby("Size of School")
avgsize = state_groups1.mean()
del avgsize["Total Budget per School"]
del avgsize["Budget per Student"]
del avgsize["Total Students"]
avgsize
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
      <th>% Pass Both</th>
      <th>% Pass Math</th>
      <th>% Pass Reading</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>Average Total Score</th>
    </tr>
    <tr>
      <th>Size of School</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Little &lt;1000</th>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>83.821598</td>
      <td>83.929843</td>
      <td>83.875721</td>
    </tr>
    <tr>
      <th>Small &lt;2000</th>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>83.374684</td>
      <td>83.864438</td>
      <td>83.619561</td>
    </tr>
    <tr>
      <th>Medium &lt;3000</th>
      <td>91.649460</td>
      <td>91.649460</td>
      <td>100.0</td>
      <td>78.429493</td>
      <td>81.769122</td>
      <td>80.099308</td>
    </tr>
    <tr>
      <th>Big &lt;4000</th>
      <td>88.547137</td>
      <td>88.547137</td>
      <td>100.0</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>78.793698</td>
    </tr>
    <tr>
      <th>Large &lt;5000</th>
      <td>89.265250</td>
      <td>89.265250</td>
      <td>100.0</td>
      <td>77.136883</td>
      <td>80.978256</td>
      <td>79.057569</td>
    </tr>
  </tbody>
</table>
</div>



Scores by School Type


```python
groupedtype = state_summary_table.groupby("Type")
groupedtype = groupedtype.mean()
del groupedtype["Total Budget per School"]
del groupedtype["Total Students"]
del groupedtype["Budget per Student"]
groupedtype
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
      <th>% Pass Both</th>
      <th>% Pass Math</th>
      <th>% Pass Reading</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>Average Total Score</th>
    </tr>
    <tr>
      <th>Type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>83.473852</td>
      <td>83.896421</td>
      <td>83.685136</td>
    </tr>
    <tr>
      <th>District</th>
      <td>88.991533</td>
      <td>88.991533</td>
      <td>100.0</td>
      <td>76.956733</td>
      <td>80.966636</td>
      <td>78.961685</td>
    </tr>
  </tbody>
</table>
</div>


