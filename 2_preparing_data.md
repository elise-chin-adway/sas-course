# Preparing data

- [SAS Dataset](#sas-dataset)
- [Data Libraries](#data-libraries)
- [SAS Program Syntax](#sas-program-syntax)
- [Referencing Data in Librairies](#referencing-data-in-libraries)
- [Bringing Data into SAS](#bringing-data-into-sas)
    - [Input Styles](#input-styles)
        - [Options](#options)
        - [Column](#column)
        - [Pointers](#pointers)
    - [Import Files](#import-files)
- [Variables](#variables)
- [Character functions](#character-functions)
- [Formats](#formats)

## SAS Dataset

- It is a table
- Columns = variables
- Rows = observations
- Only 2 types of data: character and numeric   
- Missing values
    - Character --> null
    - Numeric --> a period
- Naming conventions
    - 1-32 characters long
    - Must begin with A-Z, a-z or _
    - Can continue with any combination of numbers, letters or underscores

## Data Libraries

- Datasets are stored in libraries, that can be found in the explorer window
- 2 types of libraries:
    - Temporary: lost when session is closed, for working dataset
        - In `WORK` folder
    - Permanent: can be retrieve any time from any SAS session
        - In `SASHELP`, `SASUSER` or `WEBWORK` folders
- Naming conventions:
    - 1-8 characters long
    - Must begin with A-Z, a-z or _
    - Can continue with any combination of numbers, letters or underscores

## SAS Program Syntax

```sas
data cars;
    set sashelp.cars;
run;

proc print data=cars;
run;
```

- SAS statement:
    - Usually begins with a SAS **keyword**
    - Always ends with a **semicolon**
- SAS code is **free text format**, **case insensitive**, and **can begin and end anywhere**
- Two modules:
    1. Data step: bloc of code that pertains to modify a dataset
    2. Proc step: does something with the data (reporting, printing, sorting, ...)

## Referencing data in Libraries

- Two level naming: `LibName.DatasetName`
- Single level naming: `DatasetName` if Library is Work by default (if not explicitly mentioned)

## Bringing data into SAS

1. From existing data within SAS: permanent lib to work lib

    ```SAS
    data cars;
        set sashelp.cars;
    run;
    ```
    Get Dataset *cars* from SASHELP and bring in into the WORK lib.

2. From creating your own data within SAS: SAS program to work lib

    ```SAS
    data patients;
    input name $ gender $ age weight;
    cards;
    John M 48 128.6
    Peter M 58 158.3
    ;
    run;
    ```
    $ to denote char 

3. By importing data from external spreadsheets (e.g. Excel): 
    - import wizard, functionality in SAS
    - Using `LIBNAME` statement
        ```SAS
        libname mylib 'home/elise';
        data class;
        set mylib.class;
        run;
        ```
        - For *.sas7bdat* format
    - Using `FILENAME` statement
        ```SAS
        libname myclass 'home/elise/class.dat';
        data class;
        infile myclass;
        input name $ gender $ age weight;
        run;
        ```
        - `myclass`: file reference
        - For *.dat* format

### Input styles

#### Options
- `DELIMITER`
    ```SAS
    data class;
    infile cards delimiter =',';
    input name $ gender $ age weight;
    cards;
    John,M,48,128.6
    Peter,M,58,158.3
    ;
    run;
    ```

- `DSD`: where comma in the data itself
    ```SAS
    data class;
    infile cards delimiter =',' dsd;
    input name $ gender $ age weight;
    cards;
    "John,K",M,48,128.6
    "Peter,P",M,58,158.3
    ;
    run;
    ```

#### Column
- Column input:
    ```SAS
    data class;
    input name $ 1-5 age 6-7 gender $ 8 weight 9-15;
    cards;
    John 48M128.6
    Peter58.158.3
    Liz . F115.5
    Joe 28M170.1
    ;
    run;
    ```
    - Column 1 name: 1-5
    - Column 2 age: 6-7
    - Column 3 gender: 8
    - Column 4 weight: 9-15

#### Pointers
- Column Pointer @ Symbol: starting position
    ```SAS
    data class;
    input name $ 1-5 age 6-7 gender $ @17 weight;
    cards;
    John 48M        128.6
    Peter58.        158.3
    Liz  . Female   115.5
    Joe  28Male     170.1
    ;
    run;
    ```
    Combination of column input, list input and @pointer:
    - Column 1 name: 1-5
    - Column 2 age: 6-7
    - List 3 gender
    - @pointer 4 weight: @17

- Line Pointer # Symbol: indicates the line number of the data
    ```SAS
    data class;
    input fname :$10. lname $13.
    #2 gender $ #3 age weight;
    cards;
    John Smith
    M
    48 128.6
    Elizabeth Stienkerchner
    F
    58 158.3
    Anastasia Miller
    Female
    . 115.5
    Joe Davis
    Male
    28 170.1
    ;
    run;
    - :$10. indicates the size of fname, we add : to only take into account char before a space, so only fname really
    - 13. indicates the size of lname (8 default)

- Line Pointer Slash / Symbol: indicates the next line
    ```SAS
    data class;
    input fname :$10. lname $13.
    / gender $ age weight;
    cards;
    John Smith
    M 48 128.6
    Elizabeth Stienkerchner
    F 58 158.3
    Anastasia Miller
    Female . 115.5
    Joe Davis
    Male 28 170.1
    ;
    run;

- Trailing @ symbol at the end of the input statement: ??
    ```SAS
    data class;
    input state $ @;
    if state = 'NJ' then delete;
    input fname $ lname :$13. gender $ age weight;
    cards;
    NJ
    John Smith M 48 128.6
    NJ
    Elizabeth Stienkerchner F 58 158.3
    PA
    Anastasia Miller emale . 115.5
    NY
    Joe Davis Male 28 170.1
    ;
    run;
    ```

- Trailing Double @ Symbol: multiple observations in one line
    ```SAS
    data class;
    input fname :$10. lname $13. gender $ age weight @@;
    cards;
    John Smith M 48 128.6 Elizabeth Stienkerchner F 58 158.3
    Anastasia Miller Female . 115.5 Joe Davis Male 28 170.1
    ;
    run;
    ```

### Import files

- XLS files: Proc import
    ```SAS
    FILENAME reffile '/path/to/file.xlsx'

    PROC IMPORT DATAFILE=reffile
        DBMS=xlsx
        OUT=class; -> save in work library
        GETNAMES=yes; -> first row as name
    
    RUN;
    ```

- TXT files: Proc import
    ```SAS
    FILENAME reffile '/path/to/file.txt'

    PROC IMPORT DATAFILE=reffile
        DBMS=dlm
        OUT=class REPLACE; *save in work library;
        GETNAMES=yes; *first row as name;
        DELIMITER=','; *comma, single space or tab;
    
    RUN;
    ```

## Variables

- Create a new variable from an existing one
    ```SAS
    data class;
        set sashelp.class;

        weightkg = weight * 0.454;
        heightm = height * 2.54/100;
    run;
    ```

- Keep, drop and rename a variable
    ```SAS
    data class;
        set sashelp.class;

        weightkg = weight * 0.454;

        keep name sex age weightkg;
        *or;
        drop weight;

        rename sex = gender;
    run;
    ```

- Create a variable from a conditional statement
    ```SAS
    data class;
        set sashelp.class;

        weightkg = weight * 0.454;
        heightm = height * 2.54/100;
        bmi = weightkg/(heightm*heightm);

        if bmi<=18 then status='Healthy weight';
        else if 18<bmi<21 then status='Overweight';
        else if bmi>21 then status='Obese';
    run;
    ```

- Filter data with `WHERE` clause
    - Data step: the data is getting shrunk
    ```SAS
    data class;
    set sashelp.class;
    where sex='F';
    run;
    ```
    - Proc step: the report and not the data
    ```SAS
    proc print data=sashelp.class;
    where sex='F';
    run;
    ```

- SAS Dates: numbers in SAS
    ```SAS
    data test;
    a = '1Jan1960'd; *0;
    b = '31Dec1960'd; *365;
    c = '29Dec2018'd; *21547;
    d = '10Apr2018'd; *21284;
    diff = c-d; *263;
    run;

## Character functions
`New var. = FUNCTION(var)`

Dataset:
```SAS
data class;
input fname $ lname $ class $;
cards;
John Doe STAT100
Liz Hick HIST200
Dave Higgs ECON100
AMY WRIGHT STAT200
Mary Jane HIST300
JASON powel ECON400
Chris Ryan HIST100
Peter Paul ECON400
;
run;
```

- `upcase`: elise -> ELISE
- `lowcase`: ELISE -> elise
- `propcase`:  elise -> Elise (mixed/camel case)
- `length`
- `cat`: concatenation

```SAS
data class1;
    set class;

    ufname = upcase(fname);
    lfname = lowcase(fname);

    plname = propcase(lname);

    lenlname = length(lname);
    
    fullname = cat(fname, lname);
run;
```

- `substr(var, x, y)`: x -> start position, y -> length
- `trim`: remove right spaces
- `left`: remove left spaces
- `strip`: remove left and right spaces
- `compress`: remove all and in-between spaces
    - '  United States of America  ' -> 'UnitedStatesofAmerica'

## Formats

```SAS

```