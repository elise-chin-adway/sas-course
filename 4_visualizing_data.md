# Visualizing data

- [Charts](#charts)
- [Plots](#plots)
- [Proc print](#proc-print)
- [Proc report](#proc-report)
- [Statistical procs](#statistical-procs)
- [Output Delivery System (ODS)](#output-delivery-system-ods)

## Charts

Ex: count of the number of males and females
```SAS
proc chart data=class;
vbar gender;
run;
```

- Vertical bar chart: `vbar`
- Horizontal bar chart: `hbar`
- Pie chart: `pie`

For numeric values:
- `DISCRETE` option
    ```SAS
    proc chart data=class;
    vbar course/discrete;
    run;
    ```

Groups and subgroups
```SAS
proc chart data=class;
vbar course/discrete group=major subgroup=gender sumvar=age type=mean;
run;
```
- `group=major`: group bars by each course major
- `subgroup=gender`: bars are represented by 'F' or 'M' -> proportion of male and female in each course
- `sumvar=age`: y-axis is the sum of ages in each course, instead of number of students
- `type=mean`: instead of the sum, take the mean


## Plots
2D data
```SAS
proc plot data=class;
plot age*weight = "*";
run;
```
## Proc print
Report output (to get a pretty table)
```SAS
proc print data=class;
var name gender age weight;
run;
```

## Proc report
Much more powerful and more visually appealing than `proc print`.

Options
- `DISPLAY`: display list of obs
- `ORDER`: sort
- `GROUP`
- `ANALYSIS`: summarize (sum, avg, ...)

```SAS
proc report data=class;
columns name gender age course major;
define name/display width=...;
define gender/order *or group;;
define age/display;
define course/display;
define major/display;
run;
```

Table of mean age by gender:
```SAS
proc report data=class;
columns gender age;
define gender/group;
define age/analysis mean;
run;
```

## Statistical procs
Gives summary reports

- `proc freq`: frequency, percent, cumulative frequency, cumulative percent, frequency missing
    ```SAS
    proc freq data=class;
    table gender;
    run;
    ```

- `proc means`: number of obs, mean, std, min, max
    ```SAS
    proc means data=class;
    var age;
    run;
    ```

- `proc univariate`: +skewness, kurtosis, coeff variation...
    ```SAS
    proc univariate data=class;
    var age;
    run;
    ```

## Output Delivery System (ODS)
Customize output appearance, in multiple formats (default: HTML)

```SAS
ods trace on;

proc freq data=class;
table gender;
run;

ods trace off;

*to get results in dataset;
ods output Freq.table1.OnewayFresq = myfreq; *path name in log;
proc freq data=class;
table gender;
run;

ods output off;
```