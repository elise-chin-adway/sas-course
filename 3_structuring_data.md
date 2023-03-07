# Structuring data

- [Stacking data](#stacking-data)
- [Interleaving data](#interleaving-data)
- [Sorting data](#sorting-data)
- [Removing duplicates](#removing-duplicates)
- [Merging data and Joins](#merging-data-and-joins)
- [Proc SQL](#proc-sql)
- [Transposing data](#transposing-data)
- [Retain statement](#retain-statement)


## Stacking data
Combine >=2 datasets together, containing the **same variables**. Stacking or appending.

- Using a data step
    ```SAS
    data C;
        set A B;
    run;
    ```
- Using Proc Append: for bigger datasets (+10000 obs)
    ```SAS
    ...
    proc append base=C data=A;
    run;
    proc append base=C data=B;
    run;
    ```

## Interleaving data
Interleaving = sorting/grouping + stacking.

Final output is slightly different from simple stacking. Rearrange observations based on different criteria.
```SAS
proc sort data=A;
by gender; *group by gender;
run;

proc sort data=B;
by gender;
run;

data C;
set A B;
by gender;
run;
```

## Sorting data
Rearranging data in a specified order.

- One-level sorting
    ```SAS
    proc sort data=A out=B;
    by name; *default=ascending;
    *or;
    by descending name; 
    run;
    ```
- Multi-level sorting: no limit of variables
    ```SAS
    proc sort data=A out=B;
    by gender age;
    run;
    ```

## Removing duplicates
```SAS
proc sort data=class out=sortclass OPTION;
by VARIABLE1 [VARIABLE2...];
run;
```

- Using `NODUP` option: when every value in all variables is exact duplicates
- Using `NODUPKEY` option: when every value in all variables specified in by statement are exactly the same

## Merging data and Joins
Bring >= 2 together with different set of variables, with one common variable.

```SAS
data C;
merge A B;
by name; */!\ A and B need to be pre-sorted;
run;
```

Using joins
```SAS
data joineddata;
merge classweight(in=A) classheight(in=B); *classweight alias A, classheight alias B;
by name; */!\ A and B need to be pre-sorted;
if JOIN; 
run;
```

- Inner join: `if A and B;`
- Full join: `if A or B;` 
- Outer left join: `if A;` (A + inner)
- Outer right join: `if B;` (inner + B)
- Far left join: `if A and not B;` 

## Proc SQL

- Copy data
```SAS
proc sql;
create table class as
select * from sashelp.class;
quit;
```

- Filter data
```SAS
proc sql;
create table class as
select * from sashelp.class
where sex = 'M'
;
quit;
```

- Sort data
```SAS
proc sql;
create table class as
select * from sashelp.class
order by sex, age
;
quit;
```

- Remove duplicates (exact values in all variables NODUP)
```SAS
proc sql;
create table sortclass as
select distinct * from sashelp.class
;
quit;
```

## Transposing data
Transpose A.T

- ID statement
```SAS
proc transpose data=class out=t_class;
var gender age weight;
id name;
run;
```

- BY statement
```SAS
proc transpose data=vitals out=t_vitals;
var value;
id test;
by sid name;
run;
```

## Retain statement
Pre-fill specific values even before the observation is fully completed.

Ex: cumulative salary  

```SAS
data salary1;
    set salary;
    by name;
    retain totalsal 0;
    totalsal = totalsal + salary;
    if first.name then totalsal = salary; *first.name indicates 1st occ of the variable name;
run;
```