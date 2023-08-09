# utl-harmonizing-variables-length-and-type-before-concatenating-n-datasets
Harmonizing variables length and type before concatenating N dataset
    %let pgm=utl-harmonizing-variables-length-and-type-before-concatenating-n-datasets;

    Harmonizing variables length and type before concatenating N datasets

    https://tinyurl.com/2dra77hx
    https://github.com/rogerjdeangelis/utl-harmonizing-variables-length-and-type-before-concatenating-n-datasets

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */
    /*---- create empty folder for datasets TEMPLATE    s  ----*/

    Options validvarname=upcase;

    libname hav "d:/hav";

    data hav.hav0; /*--- wanted final types and lengths     ---*/
      attrib
        x1 length=$10
        x2 length=8
        x3 length=$30
        x4 length=$10
        x5 length=8
     ;
    run;quit;

    data hav.hav1;
        x1 = 1.1      ;  /*--- change to character $10  ---*/
        x2 = 1.1      ;  /*--- no change numeric 8      ---*/
        x3 = "1.1"    ;  /*--- change to $30            ---*/
        x4 = "1.1"    ;  /*--- change to character $10  ---*/
        x5 = 1.1      ;  /*--- no change numeric 8      ---*/
    run;quit;


    data hav.hav2;
        x1 = 2.2      ;  /*--- change to character $10  ---*/
        x2 = "2.2"    ;  /*--- change to numeric 8      ---*/
        x3 = "2.2"    ;  /*--- change to $30            ---*/
        x4 = "1.1"    ;  /*--- change to $10            ---*/
        x5 = 1.1      ;  /*--- no change numeric 8      ---*/
    run;quit;


    data hav.hav3;
        x1 = 3.3      ;  /*--- change to character $10  ---*/
        x2 = 3.3      ;  /*---  change numeric 8        ---*/
        x3 = "3.3"    ;  /*--- change to $30            ---*/
        x4 = "3.3"    ;  /*--- change to $10            ---*/
        x5 = 1.1      ;  /*--- no change numeric 8      ---*/
    run;quit;


    /**************************************************************************************************************************/
    /*                                |                                                                                       */
    /* TEMPLATE                       |     NEED TO HARMONIZE THESE INPUT DATASETS                                            */
    /*                                |                                                                                       */
    /* HAV.HAV0                       |           HAV.HAV1                      HAV.HAV2                      HAV.HAV3        */
    /*                                | ============================   ===========================  ========================= */
    /*  Variables in Creation Order   | Variables in Creation Order  Variables in Creation Order  Variables in Creation Order */
    /*                                |                                                                                       */
    /* #    Variable    Type    Len   |     Variable    Type    Len      Variable    Type    Len      Variable    Type    Len */
    /*                                |                                                                                       */
    /* 1    X1          Char     10   |     X1          Num       8      X1          Num       8      X1          Num     8   */
    /* 2    X2          Num       8   |     X2          Num       8      X2          Char      3      X2          Num     8   */
    /* 3    X3          Char     30   |     X3          Char      3      X3          Char      3      X3          Char    3   */
    /* 4    X4          Char     10   |     X4          Char      3      X4          Char      3      X4          Char    3   */
    /* 5    X5          Num       8   |     X5          Num       8      X5          Num       8      X5          Num     8   */
    /*                                                                                                                        */
    /**************************************************************************************************************************/


    /*----  GET TYPE AND LENGTH FROM ALL 4 DATASTES                          ----*/

    proc sql;
       create
          table meta as
       select
          l.memname as tpl_memname
         ,l.name    as tpl_name
         ,l.type    as tpl_type
         ,l.length  as tpl_length
         ,r.memname
         ,r.name
         ,r.type
         ,r.length
         ,' ' as mapVar length=200
       from
          sashelp.vcolumn as l, sashelp.vcolumn as r
       where
              l.libname =   "HAV"
          and r.libname =   "HAV"
          and l.memname =   "HAV0"  /*--- the template   ---*/
          and r.memname ne  "HAV0"  /*--- the template   ---*/
          and l.name    =   r.name
    ;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Up to 40 obs from META total obs=15                                                                                    */
    /*                                                                                                                        */
    /*     TEMPLATE WHAT OP WANTS                             RAW DARA                                                        */
    /* =================================================   =================================                                  */
    /*                                                                                                                        */
    /*         TPL_                               TPL_                                                                        */
    /* Obs    MEMNAME    TPL_NAME    TPL_TYPE    LENGTH    MEMNAME    NAME    TYPE    LENGTH                                  */
    /*                                                                                                                        */
    /*   1     HAV0         X1         char        10       HAV1       X1     num        8                                    */
    /*   2     HAV0         X2         num          8       HAV1       X2     num        8                                    */
    /*   3     HAV0         X3         char        30       HAV1       X3     char       3                                    */
    /*   4     HAV0         X4         char        10       HAV1       X4     char       3                                    */
    /*   5     HAV0         X5         num          8       HAV1       X5     num        8                                    */
    /*                                                                                                                        */
    /*   6     HAV0         X1         char        10       HAV2       X1     num        8                                    */
    /*   7     HAV0         X2         num          8       HAV2       X2     char       3                                    */
    /*   8     HAV0         X3         char        30       HAV2       X3     char       3                                    */
    /*   9     HAV0         X4         char        10       HAV2       X4     char       3                                    */
    /*  10     HAV0         X5         num          8       HAV2       X5     num        8                                    */
    /*                                                                                                                        */
    /*  11     HAV0         X1         char        10       HAV3       X1     num        8                                    */
    /*  12     HAV0         X2         num          8       HAV3       X2     num        8                                    */
    /*  13     HAV0         X3         char        30       HAV3       X3     char       3                                    */
    /*  14     HAV0         X4         char        10       HAV3       X4     char       3                                    */
    /*  15     HAV0         X5         num          8       HAV3       X5     num        8                                    */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/


    /*----  GET TYPE AND LENGTH FROM ALL 4 DATASTES                          ----*/

    data mapHav;
      set meta;
      if      tpl_type="char" and type="num"  then mapVar=catx(" ","  put(",tpl_name,",32.)   as",tpl_name,"length=",tpl_length);
      else if tpl_type="char" and type="char" then mapVar=catx(" "         ,tpl_name,        "as",tpl_name,"length=",tpl_length);
      else if tpl_type="num"  and type="char" then mapVar=catx(" ", "input(",tpl_name ,"32.)  as",tpl_name,"length=",tpl_length);
      else if tpl_type="num"  and type="num"  then mapVar=catx(" "         ,tpl_name,        "as",tpl_name,"length=8");
      put type tpl_type mapVar= ;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*  Up to 40 obs from MAPHAV total obs=15                                                                                 */
    /*                                                                                                                        */
    /*       TPL_                               TPL_                                                                          */
    /*  Obs MEMNAME    TPL_NAME    TPL_TYPE    LENGTH    MEMNAME    NAME    TYPE    LENGTH    MAPVAR                          */
    /*                                                                                                                        */
    /*    1  HAV0         X1         char        10       HAV1       X1     num        8      input( X1 ,best.) as X1         */
    /*    2  HAV0         X2         num          8       HAV1       X2     num        8      X2 as X2 length=8               */
    /*    3  HAV0         X3         char        30       HAV1       X3     char       3      X3 as X3 length= 30             */
    /*    4  HAV0         X4         char        10       HAV1       X4     char       3      X4 as X4 length= 10             */
    /*    5  HAV0         X5         num          8       HAV1       X5     num        8      X5 as X5 length=8               */
    /*    6  HAV0         X1         char        10       HAV2       X1     num        8      input( X1 ,best.) as X1         */
    /*    7  HAV0         X2         num          8       HAV2       X2     char       3      putn( X2 32.)    as X2 length= 8*/
    /*    8  HAV0         X3         char        30       HAV2       X3     char       3      X3 as X3 length= 30             */
    /*    9  HAV0         X4         char        10       HAV2       X4     char       3      X4 as X4 length= 10             */
    /*   10  HAV0         X5         num          8       HAV2       X5     num        8      X5 as X5 length=8               */
    /*   11  HAV0         X1         char        10       HAV3       X1     num        8      input( X1 ,best.) as X1         */
    /*   12  HAV0         X2         num          8       HAV3       X2     num        8      X2 as X2 length=8               */
    /*   13  HAV0         X3         char        30       HAV3       X3     char       3      X3 as X3 length= 30             */
    /*   14  HAV0         X4         char        10       HAV3       X4     char       3      X4 as X4 length= 10             */
    /*   15  HAV0         X5         num          8       HAV3       X5     num        8      X5 as X5 length=8               */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    %array(_ha,data=mapHav(where=(memname="HAV1")),var=mapVar);
    %array(_hb,data=mapHav(where=(memname="HAV2")),var=mapVar);
    %array(_hc,data=mapHav(where=(memname="HAV3")),var=mapVar);

    %put &_han;
    %put &_ha3;

    %macro allTre(dsn);

      proc sql;
        create
          table remapHav&dsn as
        select
          %do_over(_hv,phrase=%str(
              ?),between=comma)
        from
          hav.hav&dsn
      ;quit;

    %mend allTre;

    %allTre(1);
    %allTre(2);
    %allTre(3);

    /*__ _                           _               _
     / _(_)_ __   __ _    ___  _   _| |_ _ __  _   _| |_
    | |_| | `_ \ / _` |  / _ \| | | | __| `_ \| | | | __|
    |  _| | | | | (_| | | (_) | |_| | |_| |_) | |_| | |_
    |_| |_|_| |_|\__,_|  \___/ \__,_|\__| .__/ \__,_|\__|
                                        |_|
    */


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  WORK.REMAPHAV1                         WORK.REMAPHAV2                     WORK.REMAPHAV3                              */
    /*                                                                                                                        */
    /*   Variables in Creation Order         Variables in Creation Order        Variables in Creation Order                   */
    /*                                                                                                                        */
    /*  #    Variable    Type    Len        #    Variable    Type    Len       #    Variable    Type    Len                   */
    /*                                                                                                                        */
    /*  1    X1          Char     10        1    X1          Char     10       1    X1          Char     10                   */
    /*  2    X2          Num       8        2    X2          Num       8       2    X2          Num       8                   */
    /*  3    X3          Char     30        3    X3          Char     30       3    X3          Char     30                   */
    /*  4    X4          Char     10        4    X4          Char     10       4    X4          Char     10                   */
    /*  5    X5          Num       8        5    X5          Num       8       5    X5          Num       8                   */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
