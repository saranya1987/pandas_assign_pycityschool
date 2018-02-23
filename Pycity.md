

```python
# Dependencies 
import pandas as pd 
import numpy as np
```


```python
# load CSV
school_table = pd.read_csv("../Resources/Schools.csv")
students_table = pd.read_csv("../Resources/Students.csv")  
```


```python
# Rename school name as school
d = {'name': 'school'}
schools_df = school_table.rename(columns=d) 
```


```python
# District Summary Calculation
total_budget = school_table['budget'].sum()
school_count = school_table['School ID'].count()
total_students = students_table['Student ID'].count() 
average_reading_score = students_table['reading_score'].sum()/total_students
average_math_score = students_table['math_score'].sum()/total_students
passing_maths = (students_table['math_score'].loc[students_table['math_score'] >= 70].count() / total_students)*100
passing_reading = (students_table['reading_score'].loc[students_table['reading_score'] >= 70].count() / total_students)*100
overall_passing_rate = (passing_maths + passing_reading)/2

```


```python
# Dataframe for District Summary
data = {'Total Schools': [school_count],'Total Students': [total_students],'Total Budget': [total_budget],'Average Math Score':[average_math_score],
        'Average Reading Score':[average_reading_score],'% Passing Math':[passing_maths],'% Passing Reading':[passing_reading],'% Overall Passing Rate':[overall_passing_rate]}
df = pd.DataFrame(data, columns = ['Total Schools','Total Students','Average Math Score','Average Reading Score','% Passing Math','% Passing Reading','% Overall Passing Rate'])
df  
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
      <th>Total Schools</th>
      <th>Total Students</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>39170</td>
      <td>78.985371</td>
      <td>81.87784</td>
      <td>74.980853</td>
      <td>85.805463</td>
      <td>80.393158</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Merge the two school and students table data frames on the name of the school
schools_df.school = schools_df.school.astype(str)
students_table.school = students_table.school.astype(str) 
df3 = pd.merge(schools_df, students_table, on='school', how='left') 
```


```python
# Rename type as school type
c = {'type': 'School Type'}
merge_res = df3.rename(columns=c) 
```


```python
# School Summary Calculation by grouping the school and school type
group_school = merge_res.groupby(['school','School Type'])
student_count_summ = group_school['Student ID'].count()
total_budget = group_school['budget'].unique() 
total_budget = total_budget.astype(float)
budget_per_student = total_budget / student_count_summ
avg_math = group_school['math_score'].sum()/student_count_summ
avg_read = group_school['reading_score'].sum()/student_count_summ 
```


```python
# School summary percentage calculation by setting the index as school
reset = merge_res.set_index('school') 
pass_maths = (reset['math_score'].loc[reset['math_score'] >= 70])
maths_pass_per = (pass_maths.groupby(['school']).count()/student_count_summ)*100
pass_read = (reset['reading_score'].loc[reset['reading_score'] >= 70])
read_pass_per = (pass_read.groupby(['school']).count()/student_count_summ)*100
overall_pass_rate = (maths_pass_per + read_pass_per)/2

```


```python
# Dataframe for School Summary
data = {'Total Students' : student_count_summ,'Total School Budget':total_budget,'Per Student Budget':budget_per_student,'Average Math Score': avg_math,'Average Reading Score': avg_read,
       '% Pass Math':maths_pass_per,'%Pass Reading':read_pass_per,'% Overall Passing Rate':overall_pass_rate}
df2 = pd.DataFrame(data, columns = ['Total Students','Total School Budget','Per Student Budget','Average Math Score','Average Reading Score','% Pass Math','%Pass Reading','% Overall Passing Rate'])

#Including the $ sign with Budget columns
df2["Total School Budget"] = df2["Total School Budget"].map("${0:,.0f}".format)
df2["Per Student Budget"] = df2["Per Student Budget"].map("${0:,.0f}".format)
# Text Alignment
d = dict(selector="th",
    props=[('text-align', 'left')])
df2.style.set_table_styles([d]) 
```




<style  type="text/css" >
    #T_2192b65e_17b7_11e8_9519_d46a6a472adc th {
          text-align: left;
    }</style>  
<table id="T_2192b65e_17b7_11e8_9519_d46a6a472adc" > 
<thead>    <tr> 
        <th class="blank" ></th> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Total Students</th> 
        <th class="col_heading level0 col1" >Total School Budget</th> 
        <th class="col_heading level0 col2" >Per Student Budget</th> 
        <th class="col_heading level0 col3" >Average Math Score</th> 
        <th class="col_heading level0 col4" >Average Reading Score</th> 
        <th class="col_heading level0 col5" >% Pass Math</th> 
        <th class="col_heading level0 col6" >%Pass Reading</th> 
        <th class="col_heading level0 col7" >% Overall Passing Rate</th> 
    </tr>    <tr> 
        <th class="index_name level0" >school</th> 
        <th class="index_name level1" >School Type</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel0_row0" class="row_heading level0 row0" >Bailey High School</th> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel1_row0" class="row_heading level1 row0" >District</th> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow0_col0" class="data row0 col0" >4976</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow0_col1" class="data row0 col1" >$3,124,928</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow0_col2" class="data row0 col2" >$628</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow0_col3" class="data row0 col3" >77.0484</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow0_col4" class="data row0 col4" >81.034</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow0_col5" class="data row0 col5" >66.6801</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow0_col6" class="data row0 col6" >81.9333</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow0_col7" class="data row0 col7" >74.3067</td> 
    </tr>    <tr> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel0_row1" class="row_heading level0 row1" >Cabrera High School</th> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel1_row1" class="row_heading level1 row1" >Charter</th> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow1_col0" class="data row1 col0" >1858</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow1_col1" class="data row1 col1" >$1,081,356</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow1_col2" class="data row1 col2" >$582</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow1_col3" class="data row1 col3" >83.0619</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow1_col4" class="data row1 col4" >83.9758</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow1_col5" class="data row1 col5" >94.1335</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow1_col6" class="data row1 col6" >97.0398</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow1_col7" class="data row1 col7" >95.5867</td> 
    </tr>    <tr> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel0_row2" class="row_heading level0 row2" >Figueroa High School</th> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel1_row2" class="row_heading level1 row2" >District</th> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow2_col0" class="data row2 col0" >2949</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow2_col1" class="data row2 col1" >$1,884,411</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow2_col2" class="data row2 col2" >$639</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow2_col3" class="data row2 col3" >76.7118</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow2_col4" class="data row2 col4" >81.158</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow2_col5" class="data row2 col5" >65.9885</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow2_col6" class="data row2 col6" >80.7392</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow2_col7" class="data row2 col7" >73.3639</td> 
    </tr>    <tr> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel0_row3" class="row_heading level0 row3" >Ford High School</th> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel1_row3" class="row_heading level1 row3" >District</th> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow3_col0" class="data row3 col0" >2739</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow3_col1" class="data row3 col1" >$1,763,916</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow3_col2" class="data row3 col2" >$644</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow3_col3" class="data row3 col3" >77.1026</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow3_col4" class="data row3 col4" >80.7463</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow3_col5" class="data row3 col5" >68.3096</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow3_col6" class="data row3 col6" >79.299</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow3_col7" class="data row3 col7" >73.8043</td> 
    </tr>    <tr> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel0_row4" class="row_heading level0 row4" >Griffin High School</th> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel1_row4" class="row_heading level1 row4" >Charter</th> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow4_col0" class="data row4 col0" >1468</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow4_col1" class="data row4 col1" >$917,500</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow4_col2" class="data row4 col2" >$625</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow4_col3" class="data row4 col3" >83.3515</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow4_col4" class="data row4 col4" >83.8168</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow4_col5" class="data row4 col5" >93.3924</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow4_col6" class="data row4 col6" >97.139</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow4_col7" class="data row4 col7" >95.2657</td> 
    </tr>    <tr> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel0_row5" class="row_heading level0 row5" >Hernandez High School</th> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel1_row5" class="row_heading level1 row5" >District</th> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow5_col0" class="data row5 col0" >4635</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow5_col1" class="data row5 col1" >$3,022,020</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow5_col2" class="data row5 col2" >$652</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow5_col3" class="data row5 col3" >77.2898</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow5_col4" class="data row5 col4" >80.9344</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow5_col5" class="data row5 col5" >66.753</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow5_col6" class="data row5 col6" >80.863</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow5_col7" class="data row5 col7" >73.808</td> 
    </tr>    <tr> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel0_row6" class="row_heading level0 row6" >Holden High School</th> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel1_row6" class="row_heading level1 row6" >Charter</th> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow6_col0" class="data row6 col0" >427</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow6_col1" class="data row6 col1" >$248,087</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow6_col2" class="data row6 col2" >$581</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow6_col3" class="data row6 col3" >83.8033</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow6_col4" class="data row6 col4" >83.815</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow6_col5" class="data row6 col5" >92.5059</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow6_col6" class="data row6 col6" >96.2529</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow6_col7" class="data row6 col7" >94.3794</td> 
    </tr>    <tr> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel0_row7" class="row_heading level0 row7" >Huang High School</th> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel1_row7" class="row_heading level1 row7" >District</th> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow7_col0" class="data row7 col0" >2917</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow7_col1" class="data row7 col1" >$1,910,635</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow7_col2" class="data row7 col2" >$655</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow7_col3" class="data row7 col3" >76.6294</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow7_col4" class="data row7 col4" >81.1827</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow7_col5" class="data row7 col5" >65.6839</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow7_col6" class="data row7 col6" >81.3164</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow7_col7" class="data row7 col7" >73.5002</td> 
    </tr>    <tr> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel0_row8" class="row_heading level0 row8" >Johnson High School</th> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel1_row8" class="row_heading level1 row8" >District</th> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow8_col0" class="data row8 col0" >4761</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow8_col1" class="data row8 col1" >$3,094,650</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow8_col2" class="data row8 col2" >$650</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow8_col3" class="data row8 col3" >77.0725</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow8_col4" class="data row8 col4" >80.9664</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow8_col5" class="data row8 col5" >66.0576</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow8_col6" class="data row8 col6" >81.2224</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow8_col7" class="data row8 col7" >73.64</td> 
    </tr>    <tr> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel0_row9" class="row_heading level0 row9" >Pena High School</th> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel1_row9" class="row_heading level1 row9" >Charter</th> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow9_col0" class="data row9 col0" >962</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow9_col1" class="data row9 col1" >$585,858</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow9_col2" class="data row9 col2" >$609</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow9_col3" class="data row9 col3" >83.8399</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow9_col4" class="data row9 col4" >84.0447</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow9_col5" class="data row9 col5" >94.5946</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow9_col6" class="data row9 col6" >95.9459</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow9_col7" class="data row9 col7" >95.2703</td> 
    </tr>    <tr> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel0_row10" class="row_heading level0 row10" >Rodriguez High School</th> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel1_row10" class="row_heading level1 row10" >District</th> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow10_col0" class="data row10 col0" >3999</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow10_col1" class="data row10 col1" >$2,547,363</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow10_col2" class="data row10 col2" >$637</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow10_col3" class="data row10 col3" >76.8427</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow10_col4" class="data row10 col4" >80.7447</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow10_col5" class="data row10 col5" >66.3666</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow10_col6" class="data row10 col6" >80.2201</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow10_col7" class="data row10 col7" >73.2933</td> 
    </tr>    <tr> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel0_row11" class="row_heading level0 row11" >Shelton High School</th> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel1_row11" class="row_heading level1 row11" >Charter</th> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow11_col0" class="data row11 col0" >1761</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow11_col1" class="data row11 col1" >$1,056,600</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow11_col2" class="data row11 col2" >$600</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow11_col3" class="data row11 col3" >83.3595</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow11_col4" class="data row11 col4" >83.7257</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow11_col5" class="data row11 col5" >93.8671</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow11_col6" class="data row11 col6" >95.8546</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow11_col7" class="data row11 col7" >94.8609</td> 
    </tr>    <tr> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel0_row12" class="row_heading level0 row12" >Thomas High School</th> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel1_row12" class="row_heading level1 row12" >Charter</th> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow12_col0" class="data row12 col0" >1635</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow12_col1" class="data row12 col1" >$1,043,130</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow12_col2" class="data row12 col2" >$638</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow12_col3" class="data row12 col3" >83.4183</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow12_col4" class="data row12 col4" >83.8489</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow12_col5" class="data row12 col5" >93.2722</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow12_col6" class="data row12 col6" >97.3089</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow12_col7" class="data row12 col7" >95.2905</td> 
    </tr>    <tr> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel0_row13" class="row_heading level0 row13" >Wilson High School</th> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel1_row13" class="row_heading level1 row13" >Charter</th> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow13_col0" class="data row13 col0" >2283</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow13_col1" class="data row13 col1" >$1,319,574</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow13_col2" class="data row13 col2" >$578</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow13_col3" class="data row13 col3" >83.2742</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow13_col4" class="data row13 col4" >83.9895</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow13_col5" class="data row13 col5" >93.8677</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow13_col6" class="data row13 col6" >96.5396</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow13_col7" class="data row13 col7" >95.2037</td> 
    </tr>    <tr> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel0_row14" class="row_heading level0 row14" >Wright High School</th> 
        <th id="T_2192b65e_17b7_11e8_9519_d46a6a472adclevel1_row14" class="row_heading level1 row14" >Charter</th> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow14_col0" class="data row14 col0" >1800</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow14_col1" class="data row14 col1" >$1,049,400</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow14_col2" class="data row14 col2" >$583</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow14_col3" class="data row14 col3" >83.6822</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow14_col4" class="data row14 col4" >83.955</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow14_col5" class="data row14 col5" >93.3333</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow14_col6" class="data row14 col6" >96.6111</td> 
        <td id="T_2192b65e_17b7_11e8_9519_d46a6a472adcrow14_col7" class="data row14 col7" >94.9722</td> 
    </tr></tbody> 
</table> 




```python
# Top Performing school(Passsing Rate)
df3 = df2.nlargest(5, '% Overall Passing Rate') 
d = dict(selector="th",
    props=[('text-align', 'left')])
df3.style.set_table_styles([d]) 

```




<style  type="text/css" >
    #T_21bd12ae_17b7_11e8_b6f3_d46a6a472adc th {
          text-align: left;
    }</style>  
<table id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adc" > 
<thead>    <tr> 
        <th class="blank" ></th> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Total Students</th> 
        <th class="col_heading level0 col1" >Total School Budget</th> 
        <th class="col_heading level0 col2" >Per Student Budget</th> 
        <th class="col_heading level0 col3" >Average Math Score</th> 
        <th class="col_heading level0 col4" >Average Reading Score</th> 
        <th class="col_heading level0 col5" >% Pass Math</th> 
        <th class="col_heading level0 col6" >%Pass Reading</th> 
        <th class="col_heading level0 col7" >% Overall Passing Rate</th> 
    </tr>    <tr> 
        <th class="index_name level0" >school</th> 
        <th class="index_name level1" >School Type</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adclevel0_row0" class="row_heading level0 row0" >Cabrera High School</th> 
        <th id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adclevel1_row0" class="row_heading level1 row0" >Charter</th> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow0_col0" class="data row0 col0" >1858</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow0_col1" class="data row0 col1" >$1,081,356</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow0_col2" class="data row0 col2" >$582</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow0_col3" class="data row0 col3" >83.0619</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow0_col4" class="data row0 col4" >83.9758</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow0_col5" class="data row0 col5" >94.1335</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow0_col6" class="data row0 col6" >97.0398</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow0_col7" class="data row0 col7" >95.5867</td> 
    </tr>    <tr> 
        <th id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adclevel0_row1" class="row_heading level0 row1" >Thomas High School</th> 
        <th id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adclevel1_row1" class="row_heading level1 row1" >Charter</th> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow1_col0" class="data row1 col0" >1635</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow1_col1" class="data row1 col1" >$1,043,130</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow1_col2" class="data row1 col2" >$638</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow1_col3" class="data row1 col3" >83.4183</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow1_col4" class="data row1 col4" >83.8489</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow1_col5" class="data row1 col5" >93.2722</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow1_col6" class="data row1 col6" >97.3089</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow1_col7" class="data row1 col7" >95.2905</td> 
    </tr>    <tr> 
        <th id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adclevel0_row2" class="row_heading level0 row2" >Pena High School</th> 
        <th id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adclevel1_row2" class="row_heading level1 row2" >Charter</th> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow2_col0" class="data row2 col0" >962</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow2_col1" class="data row2 col1" >$585,858</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow2_col2" class="data row2 col2" >$609</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow2_col3" class="data row2 col3" >83.8399</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow2_col4" class="data row2 col4" >84.0447</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow2_col5" class="data row2 col5" >94.5946</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow2_col6" class="data row2 col6" >95.9459</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow2_col7" class="data row2 col7" >95.2703</td> 
    </tr>    <tr> 
        <th id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adclevel0_row3" class="row_heading level0 row3" >Griffin High School</th> 
        <th id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adclevel1_row3" class="row_heading level1 row3" >Charter</th> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow3_col0" class="data row3 col0" >1468</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow3_col1" class="data row3 col1" >$917,500</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow3_col2" class="data row3 col2" >$625</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow3_col3" class="data row3 col3" >83.3515</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow3_col4" class="data row3 col4" >83.8168</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow3_col5" class="data row3 col5" >93.3924</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow3_col6" class="data row3 col6" >97.139</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow3_col7" class="data row3 col7" >95.2657</td> 
    </tr>    <tr> 
        <th id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adclevel0_row4" class="row_heading level0 row4" >Wilson High School</th> 
        <th id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adclevel1_row4" class="row_heading level1 row4" >Charter</th> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow4_col0" class="data row4 col0" >2283</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow4_col1" class="data row4 col1" >$1,319,574</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow4_col2" class="data row4 col2" >$578</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow4_col3" class="data row4 col3" >83.2742</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow4_col4" class="data row4 col4" >83.9895</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow4_col5" class="data row4 col5" >93.8677</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow4_col6" class="data row4 col6" >96.5396</td> 
        <td id="T_21bd12ae_17b7_11e8_b6f3_d46a6a472adcrow4_col7" class="data row4 col7" >95.2037</td> 
    </tr></tbody> 
</table> 




```python
# Bottom Performing School(by Passing Rate)
df4 = df2.nsmallest(5, '% Overall Passing Rate') 
d = dict(selector="th",
    props=[('text-align', 'left')])
df4.style.set_table_styles([d]) 

```




<style  type="text/css" >
    #T_21da244c_17b7_11e8_bef5_d46a6a472adc th {
          text-align: left;
    }</style>  
<table id="T_21da244c_17b7_11e8_bef5_d46a6a472adc" > 
<thead>    <tr> 
        <th class="blank" ></th> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Total Students</th> 
        <th class="col_heading level0 col1" >Total School Budget</th> 
        <th class="col_heading level0 col2" >Per Student Budget</th> 
        <th class="col_heading level0 col3" >Average Math Score</th> 
        <th class="col_heading level0 col4" >Average Reading Score</th> 
        <th class="col_heading level0 col5" >% Pass Math</th> 
        <th class="col_heading level0 col6" >%Pass Reading</th> 
        <th class="col_heading level0 col7" >% Overall Passing Rate</th> 
    </tr>    <tr> 
        <th class="index_name level0" >school</th> 
        <th class="index_name level1" >School Type</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_21da244c_17b7_11e8_bef5_d46a6a472adclevel0_row0" class="row_heading level0 row0" >Rodriguez High School</th> 
        <th id="T_21da244c_17b7_11e8_bef5_d46a6a472adclevel1_row0" class="row_heading level1 row0" >District</th> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow0_col0" class="data row0 col0" >3999</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow0_col1" class="data row0 col1" >$2,547,363</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow0_col2" class="data row0 col2" >$637</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow0_col3" class="data row0 col3" >76.8427</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow0_col4" class="data row0 col4" >80.7447</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow0_col5" class="data row0 col5" >66.3666</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow0_col6" class="data row0 col6" >80.2201</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow0_col7" class="data row0 col7" >73.2933</td> 
    </tr>    <tr> 
        <th id="T_21da244c_17b7_11e8_bef5_d46a6a472adclevel0_row1" class="row_heading level0 row1" >Figueroa High School</th> 
        <th id="T_21da244c_17b7_11e8_bef5_d46a6a472adclevel1_row1" class="row_heading level1 row1" >District</th> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow1_col0" class="data row1 col0" >2949</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow1_col1" class="data row1 col1" >$1,884,411</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow1_col2" class="data row1 col2" >$639</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow1_col3" class="data row1 col3" >76.7118</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow1_col4" class="data row1 col4" >81.158</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow1_col5" class="data row1 col5" >65.9885</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow1_col6" class="data row1 col6" >80.7392</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow1_col7" class="data row1 col7" >73.3639</td> 
    </tr>    <tr> 
        <th id="T_21da244c_17b7_11e8_bef5_d46a6a472adclevel0_row2" class="row_heading level0 row2" >Huang High School</th> 
        <th id="T_21da244c_17b7_11e8_bef5_d46a6a472adclevel1_row2" class="row_heading level1 row2" >District</th> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow2_col0" class="data row2 col0" >2917</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow2_col1" class="data row2 col1" >$1,910,635</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow2_col2" class="data row2 col2" >$655</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow2_col3" class="data row2 col3" >76.6294</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow2_col4" class="data row2 col4" >81.1827</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow2_col5" class="data row2 col5" >65.6839</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow2_col6" class="data row2 col6" >81.3164</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow2_col7" class="data row2 col7" >73.5002</td> 
    </tr>    <tr> 
        <th id="T_21da244c_17b7_11e8_bef5_d46a6a472adclevel0_row3" class="row_heading level0 row3" >Johnson High School</th> 
        <th id="T_21da244c_17b7_11e8_bef5_d46a6a472adclevel1_row3" class="row_heading level1 row3" >District</th> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow3_col0" class="data row3 col0" >4761</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow3_col1" class="data row3 col1" >$3,094,650</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow3_col2" class="data row3 col2" >$650</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow3_col3" class="data row3 col3" >77.0725</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow3_col4" class="data row3 col4" >80.9664</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow3_col5" class="data row3 col5" >66.0576</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow3_col6" class="data row3 col6" >81.2224</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow3_col7" class="data row3 col7" >73.64</td> 
    </tr>    <tr> 
        <th id="T_21da244c_17b7_11e8_bef5_d46a6a472adclevel0_row4" class="row_heading level0 row4" >Ford High School</th> 
        <th id="T_21da244c_17b7_11e8_bef5_d46a6a472adclevel1_row4" class="row_heading level1 row4" >District</th> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow4_col0" class="data row4 col0" >2739</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow4_col1" class="data row4 col1" >$1,763,916</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow4_col2" class="data row4 col2" >$644</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow4_col3" class="data row4 col3" >77.1026</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow4_col4" class="data row4 col4" >80.7463</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow4_col5" class="data row4 col5" >68.3096</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow4_col6" class="data row4 col6" >79.299</td> 
        <td id="T_21da244c_17b7_11e8_bef5_d46a6a472adcrow4_col7" class="data row4 col7" >73.8043</td> 
    </tr></tbody> 
</table> 




```python
# Math Score by grade Calculation by grouping school and grade
grp_school_grade = students_table.groupby(['school','grade'])
total_stud = grp_school_grade['Student ID'].count()
stacked_m = (grp_school_grade['math_score'].sum()/total_stud)

# Returns a new level of column labels
col_m = stacked_m.unstack()
d_m = dict(selector="th",
    props=[('text-align', 'left')])
col_m.style.set_table_styles([d_m])
```




<style  type="text/css" >
    #T_221f90ac_17b7_11e8_91ea_d46a6a472adc th {
          text-align: left;
    }</style>  
<table id="T_221f90ac_17b7_11e8_91ea_d46a6a472adc" > 
<thead>    <tr> 
        <th class="index_name level0" >grade</th> 
        <th class="col_heading level0 col0" >10th</th> 
        <th class="col_heading level0 col1" >11th</th> 
        <th class="col_heading level0 col2" >12th</th> 
        <th class="col_heading level0 col3" >9th</th> 
    </tr>    <tr> 
        <th class="index_name level0" >school</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_221f90ac_17b7_11e8_91ea_d46a6a472adclevel0_row0" class="row_heading level0 row0" >Bailey High School</th> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow0_col0" class="data row0 col0" >76.9968</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow0_col1" class="data row0 col1" >77.5156</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow0_col2" class="data row0 col2" >76.4922</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow0_col3" class="data row0 col3" >77.0837</td> 
    </tr>    <tr> 
        <th id="T_221f90ac_17b7_11e8_91ea_d46a6a472adclevel0_row1" class="row_heading level0 row1" >Cabrera High School</th> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow1_col0" class="data row1 col0" >83.1545</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow1_col1" class="data row1 col1" >82.7656</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow1_col2" class="data row1 col2" >83.2775</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow1_col3" class="data row1 col3" >83.0947</td> 
    </tr>    <tr> 
        <th id="T_221f90ac_17b7_11e8_91ea_d46a6a472adclevel0_row2" class="row_heading level0 row2" >Figueroa High School</th> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow2_col0" class="data row2 col0" >76.54</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow2_col1" class="data row2 col1" >76.8843</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow2_col2" class="data row2 col2" >77.1514</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow2_col3" class="data row2 col3" >76.403</td> 
    </tr>    <tr> 
        <th id="T_221f90ac_17b7_11e8_91ea_d46a6a472adclevel0_row3" class="row_heading level0 row3" >Ford High School</th> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow3_col0" class="data row3 col0" >77.6723</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow3_col1" class="data row3 col1" >76.9181</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow3_col2" class="data row3 col2" >76.18</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow3_col3" class="data row3 col3" >77.3613</td> 
    </tr>    <tr> 
        <th id="T_221f90ac_17b7_11e8_91ea_d46a6a472adclevel0_row4" class="row_heading level0 row4" >Griffin High School</th> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow4_col0" class="data row4 col0" >84.2291</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow4_col1" class="data row4 col1" >83.8421</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow4_col2" class="data row4 col2" >83.3562</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow4_col3" class="data row4 col3" >82.044</td> 
    </tr>    <tr> 
        <th id="T_221f90ac_17b7_11e8_91ea_d46a6a472adclevel0_row5" class="row_heading level0 row5" >Hernandez High School</th> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow5_col0" class="data row5 col0" >77.3374</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow5_col1" class="data row5 col1" >77.136</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow5_col2" class="data row5 col2" >77.1866</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow5_col3" class="data row5 col3" >77.4385</td> 
    </tr>    <tr> 
        <th id="T_221f90ac_17b7_11e8_91ea_d46a6a472adclevel0_row6" class="row_heading level0 row6" >Holden High School</th> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow6_col0" class="data row6 col0" >83.4298</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow6_col1" class="data row6 col1" >85</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow6_col2" class="data row6 col2" >82.8554</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow6_col3" class="data row6 col3" >83.7874</td> 
    </tr>    <tr> 
        <th id="T_221f90ac_17b7_11e8_91ea_d46a6a472adclevel0_row7" class="row_heading level0 row7" >Huang High School</th> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow7_col0" class="data row7 col0" >75.9087</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow7_col1" class="data row7 col1" >76.4466</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow7_col2" class="data row7 col2" >77.2256</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow7_col3" class="data row7 col3" >77.0273</td> 
    </tr>    <tr> 
        <th id="T_221f90ac_17b7_11e8_91ea_d46a6a472adclevel0_row8" class="row_heading level0 row8" >Johnson High School</th> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow8_col0" class="data row8 col0" >76.6911</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow8_col1" class="data row8 col1" >77.4917</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow8_col2" class="data row8 col2" >76.8632</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow8_col3" class="data row8 col3" >77.1879</td> 
    </tr>    <tr> 
        <th id="T_221f90ac_17b7_11e8_91ea_d46a6a472adclevel0_row9" class="row_heading level0 row9" >Pena High School</th> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow9_col0" class="data row9 col0" >83.372</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow9_col1" class="data row9 col1" >84.3281</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow9_col2" class="data row9 col2" >84.1215</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow9_col3" class="data row9 col3" >83.6255</td> 
    </tr>    <tr> 
        <th id="T_221f90ac_17b7_11e8_91ea_d46a6a472adclevel0_row10" class="row_heading level0 row10" >Rodriguez High School</th> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow10_col0" class="data row10 col0" >76.6125</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow10_col1" class="data row10 col1" >76.3956</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow10_col2" class="data row10 col2" >77.6907</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow10_col3" class="data row10 col3" >76.86</td> 
    </tr>    <tr> 
        <th id="T_221f90ac_17b7_11e8_91ea_d46a6a472adclevel0_row11" class="row_heading level0 row11" >Shelton High School</th> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow11_col0" class="data row11 col0" >82.9174</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow11_col1" class="data row11 col1" >83.3835</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow11_col2" class="data row11 col2" >83.779</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow11_col3" class="data row11 col3" >83.4208</td> 
    </tr>    <tr> 
        <th id="T_221f90ac_17b7_11e8_91ea_d46a6a472adclevel0_row12" class="row_heading level0 row12" >Thomas High School</th> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow12_col0" class="data row12 col0" >83.0879</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow12_col1" class="data row12 col1" >83.4988</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow12_col2" class="data row12 col2" >83.497</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow12_col3" class="data row12 col3" >83.59</td> 
    </tr>    <tr> 
        <th id="T_221f90ac_17b7_11e8_91ea_d46a6a472adclevel0_row13" class="row_heading level0 row13" >Wilson High School</th> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow13_col0" class="data row13 col0" >83.7244</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow13_col1" class="data row13 col1" >83.1953</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow13_col2" class="data row13 col2" >83.0358</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow13_col3" class="data row13 col3" >83.0856</td> 
    </tr>    <tr> 
        <th id="T_221f90ac_17b7_11e8_91ea_d46a6a472adclevel0_row14" class="row_heading level0 row14" >Wright High School</th> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow14_col0" class="data row14 col0" >84.0103</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow14_col1" class="data row14 col1" >83.8368</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow14_col2" class="data row14 col2" >83.645</td> 
        <td id="T_221f90ac_17b7_11e8_91ea_d46a6a472adcrow14_col3" class="data row14 col3" >83.2647</td> 
    </tr></tbody> 
</table> 




```python
# Reading Score by grade Calculation by grouping school and grade
stacked_r = (grp_school_grade['reading_score'].sum()/total_stud)
col_r = stacked_r.unstack()
d_r = dict(selector="th",
    props=[('text-align', 'left')])
col_r.style.set_table_styles([d_r])
```




<style  type="text/css" >
    #T_22346452_17b7_11e8_9860_d46a6a472adc th {
          text-align: left;
    }</style>  
<table id="T_22346452_17b7_11e8_9860_d46a6a472adc" > 
<thead>    <tr> 
        <th class="index_name level0" >grade</th> 
        <th class="col_heading level0 col0" >10th</th> 
        <th class="col_heading level0 col1" >11th</th> 
        <th class="col_heading level0 col2" >12th</th> 
        <th class="col_heading level0 col3" >9th</th> 
    </tr>    <tr> 
        <th class="index_name level0" >school</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_22346452_17b7_11e8_9860_d46a6a472adclevel0_row0" class="row_heading level0 row0" >Bailey High School</th> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow0_col0" class="data row0 col0" >80.9072</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow0_col1" class="data row0 col1" >80.9456</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow0_col2" class="data row0 col2" >80.9125</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow0_col3" class="data row0 col3" >81.3032</td> 
    </tr>    <tr> 
        <th id="T_22346452_17b7_11e8_9860_d46a6a472adclevel0_row1" class="row_heading level0 row1" >Cabrera High School</th> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow1_col0" class="data row1 col0" >84.2532</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow1_col1" class="data row1 col1" >83.7884</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow1_col2" class="data row1 col2" >84.288</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow1_col3" class="data row1 col3" >83.6761</td> 
    </tr>    <tr> 
        <th id="T_22346452_17b7_11e8_9860_d46a6a472adclevel0_row2" class="row_heading level0 row2" >Figueroa High School</th> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow2_col0" class="data row2 col0" >81.4089</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow2_col1" class="data row2 col1" >80.6403</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow2_col2" class="data row2 col2" >81.3849</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow2_col3" class="data row2 col3" >81.1986</td> 
    </tr>    <tr> 
        <th id="T_22346452_17b7_11e8_9860_d46a6a472adclevel0_row3" class="row_heading level0 row3" >Ford High School</th> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow3_col0" class="data row3 col0" >81.2627</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow3_col1" class="data row3 col1" >80.4036</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow3_col2" class="data row3 col2" >80.6623</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow3_col3" class="data row3 col3" >80.6327</td> 
    </tr>    <tr> 
        <th id="T_22346452_17b7_11e8_9860_d46a6a472adclevel0_row4" class="row_heading level0 row4" >Griffin High School</th> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow4_col0" class="data row4 col0" >83.7069</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow4_col1" class="data row4 col1" >84.2881</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow4_col2" class="data row4 col2" >84.0137</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow4_col3" class="data row4 col3" >83.3692</td> 
    </tr>    <tr> 
        <th id="T_22346452_17b7_11e8_9860_d46a6a472adclevel0_row5" class="row_heading level0 row5" >Hernandez High School</th> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow5_col0" class="data row5 col0" >80.6601</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow5_col1" class="data row5 col1" >81.3961</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow5_col2" class="data row5 col2" >80.8571</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow5_col3" class="data row5 col3" >80.8669</td> 
    </tr>    <tr> 
        <th id="T_22346452_17b7_11e8_9860_d46a6a472adclevel0_row6" class="row_heading level0 row6" >Holden High School</th> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow6_col0" class="data row6 col0" >83.3246</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow6_col1" class="data row6 col1" >83.8155</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow6_col2" class="data row6 col2" >84.6988</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow6_col3" class="data row6 col3" >83.6772</td> 
    </tr>    <tr> 
        <th id="T_22346452_17b7_11e8_9860_d46a6a472adclevel0_row7" class="row_heading level0 row7" >Huang High School</th> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow7_col0" class="data row7 col0" >81.5124</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow7_col1" class="data row7 col1" >81.4175</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow7_col2" class="data row7 col2" >80.306</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow7_col3" class="data row7 col3" >81.2903</td> 
    </tr>    <tr> 
        <th id="T_22346452_17b7_11e8_9860_d46a6a472adclevel0_row8" class="row_heading level0 row8" >Johnson High School</th> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow8_col0" class="data row8 col0" >80.7734</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow8_col1" class="data row8 col1" >80.616</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow8_col2" class="data row8 col2" >81.2276</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow8_col3" class="data row8 col3" >81.2607</td> 
    </tr>    <tr> 
        <th id="T_22346452_17b7_11e8_9860_d46a6a472adclevel0_row9" class="row_heading level0 row9" >Pena High School</th> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow9_col0" class="data row9 col0" >83.612</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow9_col1" class="data row9 col1" >84.3359</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow9_col2" class="data row9 col2" >84.5912</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow9_col3" class="data row9 col3" >83.8073</td> 
    </tr>    <tr> 
        <th id="T_22346452_17b7_11e8_9860_d46a6a472adclevel0_row10" class="row_heading level0 row10" >Rodriguez High School</th> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow10_col0" class="data row10 col0" >80.6298</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow10_col1" class="data row10 col1" >80.8648</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow10_col2" class="data row10 col2" >80.3764</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow10_col3" class="data row10 col3" >80.9931</td> 
    </tr>    <tr> 
        <th id="T_22346452_17b7_11e8_9860_d46a6a472adclevel0_row11" class="row_heading level0 row11" >Shelton High School</th> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow11_col0" class="data row11 col0" >83.442</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow11_col1" class="data row11 col1" >84.3738</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow11_col2" class="data row11 col2" >82.7817</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow11_col3" class="data row11 col3" >84.1226</td> 
    </tr>    <tr> 
        <th id="T_22346452_17b7_11e8_9860_d46a6a472adclevel0_row12" class="row_heading level0 row12" >Thomas High School</th> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow12_col0" class="data row12 col0" >84.2542</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow12_col1" class="data row12 col1" >83.5855</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow12_col2" class="data row12 col2" >83.8314</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow12_col3" class="data row12 col3" >83.7289</td> 
    </tr>    <tr> 
        <th id="T_22346452_17b7_11e8_9860_d46a6a472adclevel0_row13" class="row_heading level0 row13" >Wilson High School</th> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow13_col0" class="data row13 col0" >84.0215</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow13_col1" class="data row13 col1" >83.7646</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow13_col2" class="data row13 col2" >84.3177</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow13_col3" class="data row13 col3" >83.9398</td> 
    </tr>    <tr> 
        <th id="T_22346452_17b7_11e8_9860_d46a6a472adclevel0_row14" class="row_heading level0 row14" >Wright High School</th> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow14_col0" class="data row14 col0" >83.8128</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow14_col1" class="data row14 col1" >84.1563</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow14_col2" class="data row14 col2" >84.0732</td> 
        <td id="T_22346452_17b7_11e8_9860_d46a6a472adcrow14_col3" class="data row14 col3" >83.8333</td> 
    </tr></tbody> 
</table> 




```python
# Scores by school spending by setting the bin values
bins = [0,585,615,645,675]
p = ["<$585", "$585-615", "$615-645", "$645-675"]
```


```python
# Data frame for scores by school spending calculated values
data = {'Total Students' : student_count_summ,'Total School Budget':total_budget,'Per Student Budget':budget_per_student,'Average Math Score': avg_math,'Average Reading Score': avg_read,
       '% Pass Math':maths_pass_per,'%Pass Reading':read_pass_per,'% Overall Passing Rate':overall_pass_rate}
df = pd.DataFrame(data, columns = ['Total Students','Total School Budget','Per Student Budget','Average Math Score','Average Reading Score','% Pass Math','%Pass Reading','% Overall Passing Rate'])

```


```python
pd.cut(df["Per Student Budget"],bins,labels=p).head()
```




    school                School Type
    Bailey High School    District       $615-645
    Cabrera High School   Charter           <$585
    Figueroa High School  District       $615-645
    Ford High School      District       $615-645
    Griffin High School   Charter        $615-645
    Name: Per Student Budget, dtype: category
    Categories (4, object): [<$585 < $585-615 < $615-645 < $645-675]




```python
# Bin values are set to the column "Per Student Budget"
df["Per Student Budget"] = pd.cut(df["Per Student Budget"],bins,labels=p)  
b = {'Per Student Budget': 'Spending Ranges per student'}
mer_res = df.rename(columns=b) 

```


```python
#Calculation based on the groupby spending ranges per student
school_grp = mer_res.groupby('Spending Ranges per student')
avg_math = school_grp['Average Math Score'].mean()
avg_read = school_grp['Average Reading Score'].mean()
pass_maths = school_grp['% Pass Math'].mean()
pass_reading = school_grp['%Pass Reading'].mean() 
overall_pass = (pass_maths+pass_reading)/2

```


```python
data = {'Average Math Score': avg_math,'Average Reading Score': avg_read,'% Pass Math':pass_maths,'%Pass Reading':pass_reading,'% Overall Passing Rate':overall_pass}
df5 = pd.DataFrame(data, columns = ['Average Math Score','Average Reading Score','% Pass Math','%Pass Reading','% Overall Passing Rate'])
b = dict(selector="th",
    props=[('text-align', 'left')])
df5.style.set_table_styles([b])

```




<style  type="text/css" >
    #T_22ad1512_17b7_11e8_8682_d46a6a472adc th {
          text-align: left;
    }</style>  
<table id="T_22ad1512_17b7_11e8_8682_d46a6a472adc" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Average Math Score</th> 
        <th class="col_heading level0 col1" >Average Reading Score</th> 
        <th class="col_heading level0 col2" >% Pass Math</th> 
        <th class="col_heading level0 col3" >%Pass Reading</th> 
        <th class="col_heading level0 col4" >% Overall Passing Rate</th> 
    </tr>    <tr> 
        <th class="index_name level0" >Spending Ranges per student</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_22ad1512_17b7_11e8_8682_d46a6a472adclevel0_row0" class="row_heading level0 row0" ><$585</th> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow0_col0" class="data row0 col0" >83.4554</td> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow0_col1" class="data row0 col1" >83.9338</td> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow0_col2" class="data row0 col2" >93.4601</td> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow0_col3" class="data row0 col3" >96.6109</td> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow0_col4" class="data row0 col4" >95.0355</td> 
    </tr>    <tr> 
        <th id="T_22ad1512_17b7_11e8_8682_d46a6a472adclevel0_row1" class="row_heading level0 row1" >$585-615</th> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow1_col0" class="data row1 col0" >83.5997</td> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow1_col1" class="data row1 col1" >83.8852</td> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow1_col2" class="data row1 col2" >94.2309</td> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow1_col3" class="data row1 col3" >95.9003</td> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow1_col4" class="data row1 col4" >95.0656</td> 
    </tr>    <tr> 
        <th id="T_22ad1512_17b7_11e8_8682_d46a6a472adclevel0_row2" class="row_heading level0 row2" >$615-645</th> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow2_col0" class="data row2 col0" >79.0792</td> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow2_col1" class="data row2 col1" >81.8914</td> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow2_col2" class="data row2 col2" >75.6682</td> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow2_col3" class="data row2 col3" >86.1066</td> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow2_col4" class="data row2 col4" >80.8874</td> 
    </tr>    <tr> 
        <th id="T_22ad1512_17b7_11e8_8682_d46a6a472adclevel0_row3" class="row_heading level0 row3" >$645-675</th> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow3_col0" class="data row3 col0" >76.9972</td> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow3_col1" class="data row3 col1" >81.0278</td> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow3_col2" class="data row3 col2" >66.1648</td> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow3_col3" class="data row3 col3" >81.134</td> 
        <td id="T_22ad1512_17b7_11e8_8682_d46a6a472adcrow3_col4" class="data row3 col4" >73.6494</td> 
    </tr></tbody> 
</table> 




```python
# Bin values for calculating Scores by school size
bin1 = [0,1000,2000,5000]
a = ["Small (<1000)", "Medium (1000-2000)", "Large (2000-5000)"]
pd.cut(df["Total Students"],bin1,labels=a).head()
```




    school                School Type
    Bailey High School    District        Large (2000-5000)
    Cabrera High School   Charter        Medium (1000-2000)
    Figueroa High School  District        Large (2000-5000)
    Ford High School      District        Large (2000-5000)
    Griffin High School   Charter        Medium (1000-2000)
    Name: Total Students, dtype: category
    Categories (3, object): [Small (<1000) < Medium (1000-2000) < Large (2000-5000)]




```python
df["Total Students"] = pd.cut(df["Total Students"],bin1,labels=a)  
j = {'Total Students': 'School Size'}
mer_res = df.rename(columns=j)
```


```python
# Scores by school size is calculated by grouping 'School Size'
school_grp = mer_res.groupby('School Size')
avg_maths = school_grp['Average Math Score'].mean()
avg_reads = school_grp['Average Reading Score'].mean()
pass_math = school_grp['% Pass Math'].mean()
pass_read = school_grp['%Pass Reading'].mean() 
overall_pass_percent = (pass_math+pass_read)/2
```


```python
# Data frame for scores by school size calculation
data = {'Average Math Score': avg_maths,'Average Reading Score': avg_reads,'% Pass Math':pass_math,'%Pass Reading':pass_read,'% Overall Passing Rate':overall_pass_percent}
df6 = pd.DataFrame(data, columns = ['Average Math Score','Average Reading Score','% Pass Math','%Pass Reading','% Overall Passing Rate'])
d = dict(selector="th",
    props=[('text-align', 'left')])
df6.style.set_table_styles([d]) 
```




<style  type="text/css" >
    #T_2333200c_17b7_11e8_bbff_d46a6a472adc th {
          text-align: left;
    }</style>  
<table id="T_2333200c_17b7_11e8_bbff_d46a6a472adc" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Average Math Score</th> 
        <th class="col_heading level0 col1" >Average Reading Score</th> 
        <th class="col_heading level0 col2" >% Pass Math</th> 
        <th class="col_heading level0 col3" >%Pass Reading</th> 
        <th class="col_heading level0 col4" >% Overall Passing Rate</th> 
    </tr>    <tr> 
        <th class="index_name level0" >School Size</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_2333200c_17b7_11e8_bbff_d46a6a472adclevel0_row0" class="row_heading level0 row0" >Small (<1000)</th> 
        <td id="T_2333200c_17b7_11e8_bbff_d46a6a472adcrow0_col0" class="data row0 col0" >83.8216</td> 
        <td id="T_2333200c_17b7_11e8_bbff_d46a6a472adcrow0_col1" class="data row0 col1" >83.9298</td> 
        <td id="T_2333200c_17b7_11e8_bbff_d46a6a472adcrow0_col2" class="data row0 col2" >93.5502</td> 
        <td id="T_2333200c_17b7_11e8_bbff_d46a6a472adcrow0_col3" class="data row0 col3" >96.0994</td> 
        <td id="T_2333200c_17b7_11e8_bbff_d46a6a472adcrow0_col4" class="data row0 col4" >94.8248</td> 
    </tr>    <tr> 
        <th id="T_2333200c_17b7_11e8_bbff_d46a6a472adclevel0_row1" class="row_heading level0 row1" >Medium (1000-2000)</th> 
        <td id="T_2333200c_17b7_11e8_bbff_d46a6a472adcrow1_col0" class="data row1 col0" >83.3747</td> 
        <td id="T_2333200c_17b7_11e8_bbff_d46a6a472adcrow1_col1" class="data row1 col1" >83.8644</td> 
        <td id="T_2333200c_17b7_11e8_bbff_d46a6a472adcrow1_col2" class="data row1 col2" >93.5997</td> 
        <td id="T_2333200c_17b7_11e8_bbff_d46a6a472adcrow1_col3" class="data row1 col3" >96.7907</td> 
        <td id="T_2333200c_17b7_11e8_bbff_d46a6a472adcrow1_col4" class="data row1 col4" >95.1952</td> 
    </tr>    <tr> 
        <th id="T_2333200c_17b7_11e8_bbff_d46a6a472adclevel0_row2" class="row_heading level0 row2" >Large (2000-5000)</th> 
        <td id="T_2333200c_17b7_11e8_bbff_d46a6a472adcrow2_col0" class="data row2 col0" >77.7464</td> 
        <td id="T_2333200c_17b7_11e8_bbff_d46a6a472adcrow2_col1" class="data row2 col1" >81.3445</td> 
        <td id="T_2333200c_17b7_11e8_bbff_d46a6a472adcrow2_col2" class="data row2 col2" >69.9634</td> 
        <td id="T_2333200c_17b7_11e8_bbff_d46a6a472adcrow2_col3" class="data row2 col3" >82.7666</td> 
        <td id="T_2333200c_17b7_11e8_bbff_d46a6a472adcrow2_col4" class="data row2 col4" >76.365</td> 
    </tr></tbody> 
</table> 




```python
# Scores by school type calculation by grouping 'School Type'
school_grp = mer_res.groupby('School Type')
avg_maths_score = school_grp['Average Math Score'].mean()
avg_reads_score = school_grp['Average Reading Score'].mean()
pass_math_score = school_grp['% Pass Math'].mean()
pass_read_score = school_grp['%Pass Reading'].mean() 
overall_pass_per = (pass_math_score+pass_read_score)/2
```


```python
# Data frame for Scores by school Type 
data = {'Average Math Score': avg_maths_score,'Average Reading Score': avg_reads_score,'% Pass Math':pass_math_score,'%Pass Reading':pass_read_score,'% Overall Passing Rate':overall_pass_per}
df7 = pd.DataFrame(data, columns = ['Average Math Score','Average Reading Score','% Pass Math','%Pass Reading','% Overall Passing Rate'])
n = dict(selector="th",
    props=[('text-align', 'left')])
df7.style.set_table_styles([n]) 
```




<style  type="text/css" >
    #T_2356cf38_17b7_11e8_badd_d46a6a472adc th {
          text-align: left;
    }</style>  
<table id="T_2356cf38_17b7_11e8_badd_d46a6a472adc" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Average Math Score</th> 
        <th class="col_heading level0 col1" >Average Reading Score</th> 
        <th class="col_heading level0 col2" >% Pass Math</th> 
        <th class="col_heading level0 col3" >%Pass Reading</th> 
        <th class="col_heading level0 col4" >% Overall Passing Rate</th> 
    </tr>    <tr> 
        <th class="index_name level0" >School Type</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_2356cf38_17b7_11e8_badd_d46a6a472adclevel0_row0" class="row_heading level0 row0" >Charter</th> 
        <td id="T_2356cf38_17b7_11e8_badd_d46a6a472adcrow0_col0" class="data row0 col0" >83.4739</td> 
        <td id="T_2356cf38_17b7_11e8_badd_d46a6a472adcrow0_col1" class="data row0 col1" >83.8964</td> 
        <td id="T_2356cf38_17b7_11e8_badd_d46a6a472adcrow0_col2" class="data row0 col2" >93.6208</td> 
        <td id="T_2356cf38_17b7_11e8_badd_d46a6a472adcrow0_col3" class="data row0 col3" >96.5865</td> 
        <td id="T_2356cf38_17b7_11e8_badd_d46a6a472adcrow0_col4" class="data row0 col4" >95.1037</td> 
    </tr>    <tr> 
        <th id="T_2356cf38_17b7_11e8_badd_d46a6a472adclevel0_row1" class="row_heading level0 row1" >District</th> 
        <td id="T_2356cf38_17b7_11e8_badd_d46a6a472adcrow1_col0" class="data row1 col0" >76.9567</td> 
        <td id="T_2356cf38_17b7_11e8_badd_d46a6a472adcrow1_col1" class="data row1 col1" >80.9666</td> 
        <td id="T_2356cf38_17b7_11e8_badd_d46a6a472adcrow1_col2" class="data row1 col2" >66.5485</td> 
        <td id="T_2356cf38_17b7_11e8_badd_d46a6a472adcrow1_col3" class="data row1 col3" >80.7991</td> 
        <td id="T_2356cf38_17b7_11e8_badd_d46a6a472adcrow1_col4" class="data row1 col4" >73.6738</td> 
    </tr></tbody> 
</table> 


