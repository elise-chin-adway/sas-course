# Optimizing code

- [Macro variables](#macro-variables)
- [Ampersand resolutions](#ampersand-resolutions)
- [SAS Macros](#sas-macros)
- [Macro functions](#macro-functions)



## Macro variables
Everything is text in the Macro world vs Data world.

Create a variable with a numeric value:
- Data world: `a = 'xyz'` can't do it!
    ```SAS
    data test;
    a = 10;
    run;
    ```
- Macro world: `a = 'xyz'` is OK!
    ```SAS
    %let a = 10;
    ```

To view the value of a macro variable, only in the log window
```SAS
%put -----> &a; *same as in C language;
```

Usage:
```SAS
...
%let sex = M;

proc print data=class;
var name age major;
where gender="&sex"; *importance of "";
run;
```

## Ampersand resolutions
1. Left to right
2. All consecutive double &s are resolved into a single &
3. Till all &s are resolved

```SAS
%let b = 10;
%let a = b;

%put ---> &b; *10;
%put ---> &a; *b;
%put ---> &&a; *b; 
%put ---> &&&a; *10;
```

## SAS Macros
To avoid repeated code.

```SAS
%macro myreport(lib, dsn, statvar);
proc means data=&lib..&dsn;
var &statvar;
run;
proc print data=&lib..&dsn;
run;
%mend;

%myreport(sashelp, class, age);
```

## Macro functions
`%function(var);`
- Char-like values: `upcase`, `lowcase`, `substr`, `length`
- Num-like values: `eval`, `sysevalf` (for float)

```SAS
%let a = United States;
%let b = %upcase(&a);
%put --> &b;
```

```SAS
%let x = 3;
%let y = 5;
%let z = %eval(&x + &y); *8;
```