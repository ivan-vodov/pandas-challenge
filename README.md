

```python
# Dependencies
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
```

# Observed trends
 - Top 5 schools by overall passing rate are all charter schools, whereas the lowest 5 are all district schools. Moreover all charter schools have higher passing rates than district schools.")
 - Math and reading passing rates are consistently similar across 9-11 grades for each school.")
 - Math, reading as well as overall passing rates are in reverse dependency on per student spending and school size. The rates are higher for charter schools than for district schools.")
 - Math passing rates depend stronger on the abovementioned factors than reading passing rates.")


```python
# Load in csv
schools_df = pd.read_csv("raw_data/schools_complete.csv")
schools_df=schools_df.rename(columns={"name":"school_name"})
students_df = pd.read_csv("raw_data/students_complete.csv")
students_df=students_df.rename(columns={"school":"school_name"})
```


```python
pass_score=71
```

# District Summary


```python
# dataframes with passing students
passing_math_students=students_df.loc[students_df["math_score"]>=pass_score,:]
passing_reading_students=students_df.loc[students_df["reading_score"]>=pass_score,:]
passing_overall_students=students_df.loc[(students_df["math_score"]+students_df["reading_score"])/2>=pass_score,:]

#prepare variables for output
total_schools=len(schools_df)
total_students=len(students_df)
total_budget=schools_df["budget"].sum()
avg_math_score=students_df["math_score"].mean()
avg_reading_score=students_df["reading_score"].mean()
math_pass_percent=100*len(passing_math_students)/len(students_df)
reading_pass_percent=100*len(passing_reading_students)/len(students_df)
overall_pass_percent=100*len(passing_overall_students)/len(students_df)
district_summary_df=pd.DataFrame({"Total Schools": [total_schools], "Total Students": [total_students], "Total Budget":[total_budget] , "Average Math Score": [avg_math_score], "Average Reading Score": [avg_reading_score], "% Passing Math": [math_pass_percent], "% Passing Reading": [reading_pass_percent], "% Overall Passing Rate": [overall_pass_percent]})
district_summary_df=district_summary_df.sort_index(axis=1,ascending=False)


district_summary_df
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
      <th>Total Students</th>
      <th>Total Schools</th>
      <th>Total Budget</th>
      <th>Average Reading Score</th>
      <th>Average Math Score</th>
      <th>% Passing Reading</th>
      <th>% Passing Math</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>39170</td>
      <td>15</td>
      <td>24649428</td>
      <td>81.87784</td>
      <td>78.985371</td>
      <td>82.971662</td>
      <td>72.392137</td>
      <td>87.033444</td>
    </tr>
  </tbody>
</table>
</div>



# School Summary


```python
students_grouped=pd.DataFrame(students_df[["school_name","reading_score","math_score"]].groupby(["school_name"]).mean())
school_summary=[]

#prepare dictionary of arrays of variables for output
for index, row in schools_df.iterrows():
    school_name=row["school_name"]
    school_type=row["type"]
    school_budget=row["budget"]
    total_students=len(students_df[students_df["school_name"]==school_name])
    per_student_budget=school_budget/total_students
    avg_math=students_grouped.get_value(school_name, "math_score")
    avg_read=students_grouped.get_value(school_name, "reading_score")
    math_pass_percent=100*len(passing_math_students[passing_math_students["school_name"]==school_name])/total_students
    reading_pass_percent=100*len(passing_reading_students[passing_reading_students["school_name"]==school_name])/total_students
    overall_pass_percent=100*len(passing_overall_students[passing_overall_students["school_name"]==school_name])/total_students
    school_summary.append([school_name, school_type, total_students, school_budget,per_student_budget, avg_math, avg_read, math_pass_percent,reading_pass_percent, overall_pass_percent])

labels = ["School Name","School Type","Total Students","Total School Budget","Per Student Budget","Average Math Score","Average Reading Score","% Passing Math","% Passing Reading","Overall Passing Rate"]
school_summary_df = pd.DataFrame.from_records(school_summary, columns=labels)
schools_sorted=school_summary_df.sort_values("Overall Passing Rate",ascending=False)

schools_sorted
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
      <th>School Name</th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.682222</td>
      <td>83.955000</td>
      <td>90.277778</td>
      <td>93.444444</td>
      <td>98.777778</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>90.932983</td>
      <td>93.254490</td>
      <td>98.598336</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>90.214067</td>
      <td>92.905199</td>
      <td>98.409786</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>89.558665</td>
      <td>93.864370</td>
      <td>98.331539</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>89.713896</td>
      <td>93.392371</td>
      <td>98.297003</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
      <td>600.0</td>
      <td>83.359455</td>
      <td>83.725724</td>
      <td>89.892107</td>
      <td>92.617831</td>
      <td>98.182851</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.803279</td>
      <td>83.814988</td>
      <td>90.632319</td>
      <td>92.740047</td>
      <td>97.892272</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>83.839917</td>
      <td>84.044699</td>
      <td>91.683992</td>
      <td>92.203742</td>
      <td>97.713098</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Bailey High School</td>
      <td>District</td>
      <td>4976</td>
      <td>3124928</td>
      <td>628.0</td>
      <td>77.048432</td>
      <td>81.033963</td>
      <td>64.630225</td>
      <td>79.300643</td>
      <td>82.355305</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Ford High School</td>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>77.102592</td>
      <td>80.746258</td>
      <td>65.753925</td>
      <td>77.510040</td>
      <td>82.037240</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>63.750424</td>
      <td>78.433367</td>
      <td>81.858257</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>77.289752</td>
      <td>80.934412</td>
      <td>64.746494</td>
      <td>78.187702</td>
      <td>81.855448</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>63.318478</td>
      <td>78.813850</td>
      <td>81.727803</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>63.852132</td>
      <td>78.281874</td>
      <td>81.726528</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>64.066017</td>
      <td>77.744436</td>
      <td>81.720430</td>
    </tr>
  </tbody>
</table>
</div>



# Top 5 schools


```python
top_perf_schools=schools_sorted.iloc[:5]
top_perf_schools

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
      <th>School Name</th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.682222</td>
      <td>83.955000</td>
      <td>90.277778</td>
      <td>93.444444</td>
      <td>98.777778</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>90.932983</td>
      <td>93.254490</td>
      <td>98.598336</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>90.214067</td>
      <td>92.905199</td>
      <td>98.409786</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>89.558665</td>
      <td>93.864370</td>
      <td>98.331539</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>89.713896</td>
      <td>93.392371</td>
      <td>98.297003</td>
    </tr>
  </tbody>
</table>
</div>



# Bottom 5 schools


```python
bottom_perf_schools=schools_sorted.iloc[-5:]
bottom_perf_schools
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
      <th>School Name</th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>63.750424</td>
      <td>78.433367</td>
      <td>81.858257</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>77.289752</td>
      <td>80.934412</td>
      <td>64.746494</td>
      <td>78.187702</td>
      <td>81.855448</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>63.318478</td>
      <td>78.813850</td>
      <td>81.727803</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>63.852132</td>
      <td>78.281874</td>
      <td>81.726528</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>64.066017</td>
      <td>77.744436</td>
      <td>81.720430</td>
    </tr>
  </tbody>
</table>
</div>



# Math Scores by Grade


```python
students_grouped_math_grade=pd.DataFrame(students_df[["school_name","grade","math_score"]].groupby(["school_name","grade"]).mean())
students_grouped_math_grade.unstack(level=1)
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
    <tr>
      <th></th>
      <th colspan="4" halign="left">math_score</th>
    </tr>
    <tr>
      <th>grade</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
      <th>9th</th>
    </tr>
    <tr>
      <th>school_name</th>
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



# Reading Scores by Grade


```python
students_grouped_read_grade=pd.DataFrame(students_df[["school_name","grade","reading_score"]].groupby(["school_name","grade"]).mean())
students_grouped_read_grade.unstack(level=1)
```

    Reading Scores by Grade
    




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
    <tr>
      <th></th>
      <th colspan="4" halign="left">reading_score</th>
    </tr>
    <tr>
      <th>grade</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
      <th>9th</th>
    </tr>
    <tr>
      <th>school_name</th>
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



# Scores by per student spending


```python
# number of bins to attempt to breakd down per student spending data into, on uniform scale
bins_count=4

# breaking down per student spending into bins - record bins in a dataframe with one record for each school
bins_df=pd.DataFrame(school_summary_df["Per Student Budget"].unique(), columns=["Per Student Budget"])
max_budget=bins_df["Per Student Budget"].max()
min_budget=bins_df["Per Student Budget"].min()
bins_df["bin"]=(bins_count*(bins_df["Per Student Budget"]-min_budget)//(max_budget*1.001-min_budget)+1).astype(int)

# dataframe of schools with bins by per student spending
schools_bins_df = pd.merge(school_summary_df[["School Name","Per Student Budget"]], bins_df, how='inner', on='Per Student Budget')
schools_bins_df["school_name"]=schools_bins_df["School Name"]

# dataframe of students with school bins by per student spending
students_school_bins_df=pd.merge(students_df, schools_bins_df[["school_name","bin"]], how='inner', on='school_name')
students_school_bins_grouped=students_school_bins_df[["bin","math_score","reading_score"]].groupby("bin").mean()

# list of actual uniform size bins used
bins_actual_df=pd.DataFrame(students_school_bins_grouped.reset_index()["bin"])
bins_actual_df=bins_actual_df.set_index("bin")

# actual bins used with from and to values for per student spending 
schools_bins_grouped_from=pd.DataFrame(schools_bins_df.groupby("bin").min()["Per Student Budget"]).rename(columns={"Per Student Budget":"from"})
schools_bins_grouped_to=pd.DataFrame(schools_bins_df.groupby("bin").max()["Per Student Budget"]).rename(columns={"Per Student Budget":"to"})
bins_actual_range_df=pd.merge(schools_bins_grouped_from, schools_bins_grouped_to, how='inner',left_index=True,right_index=True)

# dataframes with passing students, with bins included
passing_math_students_bin_df=students_school_bins_df.loc[students_school_bins_df["math_score"]>=pass_score,:]
passing_reading_students_bin_df=students_school_bins_df.loc[students_school_bins_df["reading_score"]>=pass_score,:]
passing_overall_students_bin_df=students_school_bins_df.loc[(students_school_bins_df["math_score"]+students_school_bins_df["reading_score"])/2>=pass_score,:]
passing_overall_students_bin_df.head()

#prepare dictionary of arrays of variables for output
school_summary_bin=[]
for index,row in bins_actual_range_df.iterrows():
    school_bin=index
    schools_count=len(schools_bins_df[schools_bins_df["bin"]==index])
    total_students=len(students_school_bins_df[students_school_bins_df["bin"]==school_bin])
    avg_math=students_school_bins_grouped.get_value(school_bin, "math_score")
    avg_read=students_school_bins_grouped.get_value(school_bin, "reading_score")
    math_pass_percent=100*len(passing_math_students_bin_df[passing_math_students_bin_df["bin"]==school_bin])/total_students
    reading_pass_percent=100*len(passing_reading_students_bin_df[passing_reading_students_bin_df["bin"]==school_bin])/total_students
    overall_pass_percent=100*len(passing_overall_students_bin_df[passing_overall_students_bin_df["bin"]==school_bin])/total_students

    school_summary_bin.append(["\$"+str(row["from"])+" - $"+str(row["to"]), schools_count, total_students, avg_math, avg_read, math_pass_percent,reading_pass_percent, overall_pass_percent])
    

labels_bin = ["Per Student Budget", "Schools #", "Total Students","Average Math Score","Average Reading Score","% Passing Math","% Passing Reading","Overall Passing Rate"]
school_summary_bin_df = pd.DataFrame.from_records(school_summary_bin, columns=labels_bin)

school_summary_bin_df 
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
      <th>Per Student Budget</th>
      <th>Schools #</th>
      <th>Total Students</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>\$578.0 - $583.0</td>
      <td>4</td>
      <td>6368</td>
      <td>83.363065</td>
      <td>83.964039</td>
      <td>90.326633</td>
      <td>93.451633</td>
      <td>98.523869</td>
    </tr>
    <tr>
      <th>1</th>
      <td>\$600.0 - $609.0</td>
      <td>2</td>
      <td>2723</td>
      <td>83.529196</td>
      <td>83.838414</td>
      <td>90.525156</td>
      <td>92.471539</td>
      <td>98.016893</td>
    </tr>
    <tr>
      <th>2</th>
      <td>\$625.0 - $628.0</td>
      <td>2</td>
      <td>6444</td>
      <td>78.484327</td>
      <td>81.667908</td>
      <td>70.344507</td>
      <td>82.510863</td>
      <td>85.986965</td>
    </tr>
    <tr>
      <th>3</th>
      <td>\$637.0 - $655.0</td>
      <td>7</td>
      <td>23635</td>
      <td>77.418997</td>
      <td>81.147112</td>
      <td>66.029194</td>
      <td>79.179183</td>
      <td>82.957478</td>
    </tr>
  </tbody>
</table>
</div>



# Scores by School Size


```python
# number of bins to attempt to breakd down school size data into, on uniform scale
bins_count_size=3

# breaking down school sizes into bins - record bins in a dataframe with one record for each school
bins_size_df=pd.DataFrame(schools_df["size"].unique(), columns=["size"])
max_size=bins_size_df["size"].max()
min_size=bins_size_df["size"].min()
bins_size_df["bin_size"]=(bins_count_size*(bins_size_df["size"]-min_size)//(max_size*1.001-min_size)+1).astype(int)

# dataframe of schools with bins by school size
schools_bins_size_df = pd.merge(schools_df, bins_size_df, how='inner', on='size')

# dataframe of students with school bins by school size
students_school_bins_size_df=pd.merge(students_df, schools_bins_size_df[["school_name","bin_size"]], how='inner', on='school_name')

students_school_bins_size_grouped=students_school_bins_size_df[["bin_size","math_score","reading_score"]].groupby("bin_size").mean()

# list of actual uniform size bins used
bins_size_actual_df=pd.DataFrame(students_school_bins_size_grouped.reset_index()["bin_size"])
bins_size_actual_df=bins_size_actual_df.set_index("bin_size")

# actual bins used with from and to values by school size 
schools_bins_size_grouped_from=pd.DataFrame(schools_bins_size_df.groupby("bin_size").min()["size"]).rename(columns={"size":"from"})
schools_bins_size_grouped_to=pd.DataFrame(schools_bins_size_df.groupby("bin_size").max()["size"]).rename(columns={"size":"to"})
bins_size_actual_range_df=pd.merge(schools_bins_size_grouped_from,schools_bins_size_grouped_to, how='inner',left_index=True,right_index=True)

# dataframes with passing students with school size bins included
passing_math_students_bin_size_df=students_school_bins_size_df.loc[students_school_bins_size_df["math_score"]>=pass_score,:]
passing_reading_students_bin_size_df=students_school_bins_size_df.loc[students_school_bins_size_df["reading_score"]>=pass_score,:]
passing_overall_students_bin_size_df=students_school_bins_size_df.loc[(students_school_bins_size_df["math_score"]+students_school_bins_df["reading_score"])/2>=pass_score,:]

#prepare dictionary of arrays of variables for output
school_summary_bin_size=[]
for index,row in bins_size_actual_range_df.iterrows():
    school_bin=index
    schools_count=len(schools_bins_size_df[schools_bins_size_df["bin_size"]==index])
    total_students=len(students_school_bins_size_df[students_school_bins_size_df["bin_size"]==school_bin])
    avg_math=students_school_bins_size_grouped.get_value(school_bin, "math_score")
    avg_read=students_school_bins_size_grouped.get_value(school_bin, "reading_score")
    math_pass_percent=100*len(passing_math_students_bin_size_df[passing_math_students_bin_size_df["bin_size"]==school_bin])/total_students
    reading_pass_percent=100*len(passing_reading_students_bin_size_df[passing_reading_students_bin_size_df["bin_size"]==school_bin])/total_students
    overall_pass_percent=100*len(passing_overall_students_bin_size_df[passing_overall_students_bin_size_df["bin_size"]==school_bin])/total_students

    school_summary_bin_size.append([str(row["from"])+" - "+str(row["to"]), schools_count, total_students, avg_math, avg_read, math_pass_percent,reading_pass_percent, overall_pass_percent])
    
labels_bin = ["School Size", "Schools #", "Total Students","Average Math Score","Average Reading Score","% Passing Math","% Passing Reading","Overall Passing Rate"]
school_summary_bin_size_df = pd.DataFrame.from_records(school_summary_bin_size, columns=labels_bin)

school_summary_bin_size_df 
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
      <th>School Size</th>
      <th>Schools #</th>
      <th>Total Students</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>427 - 1858</td>
      <td>7</td>
      <td>9911</td>
      <td>83.436586</td>
      <td>83.882857</td>
      <td>90.132176</td>
      <td>93.128847</td>
      <td>98.315004</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2283 - 2949</td>
      <td>4</td>
      <td>10888</td>
      <td>78.164034</td>
      <td>81.654758</td>
      <td>69.838354</td>
      <td>81.410727</td>
      <td>85.378398</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3999 - 4976</td>
      <td>4</td>
      <td>18371</td>
      <td>77.070764</td>
      <td>80.928365</td>
      <td>64.335093</td>
      <td>78.417070</td>
      <td>81.928039</td>
    </tr>
  </tbody>
</table>
</div>



# Scores by School Type


```python
schools_students_df=pd.merge(schools_df,students_df, how='inner', on='school_name')

bins_type=schools_df["type"].unique()
students_school_type_grouped=schools_students_df[["type","math_score","reading_score"]].groupby("type").mean()

# dataframes with passing students with school type included
passing_math_students_type_df=schools_students_df.loc[schools_students_df["math_score"]>=pass_score,:]
passing_reading_students_type_df=schools_students_df.loc[schools_students_df["reading_score"]>=pass_score,:]
passing_overall_students_type_df=schools_students_df.loc[(schools_students_df["math_score"]+schools_students_df["reading_score"])/2>=pass_score,:]

#prepare dictionary of arrays of variables for output
school_summary_type=[]
for school_type in bins_type:
    school_bin=school_type
    schools_count=len(schools_df[schools_df["type"]==school_type])
    total_students=len(schools_students_df[schools_students_df["type"]==school_bin])
    avg_math=students_school_type_grouped.get_value(school_type, "math_score")
    avg_read=students_school_type_grouped.get_value(school_type, "reading_score")
    math_pass_percent=100*len(passing_math_students_type_df[passing_math_students_type_df["type"]==school_bin])/total_students
    reading_pass_percent=100*len(passing_reading_students_type_df[passing_reading_students_type_df["type"]==school_bin])/total_students
    overall_pass_percent=100*len(passing_overall_students_type_df[passing_overall_students_type_df["type"]==school_bin])/total_students

    school_summary_type.append([school_bin, schools_count, total_students, avg_math, avg_read, math_pass_percent,reading_pass_percent, overall_pass_percent])
    
labels_bin = ["School Type", "Schools #", "Total Students","Average Math Score","Average Reading Score","% Passing Math","% Passing Reading","Overall Passing Rate"]
school_summary_type_df = pd.DataFrame.from_records(school_summary_type, columns=labels_bin)

school_summary_type_df 
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
      <th>School Type</th>
      <th>Schools #</th>
      <th>Total Students</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>District</td>
      <td>7</td>
      <td>26976</td>
      <td>76.987026</td>
      <td>80.962485</td>
      <td>64.305308</td>
      <td>78.369662</td>
      <td>81.909846</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Charter</td>
      <td>8</td>
      <td>12194</td>
      <td>83.406183</td>
      <td>83.902821</td>
      <td>90.282106</td>
      <td>93.152370</td>
      <td>98.368050</td>
    </tr>
  </tbody>
</table>
</div>


