# utl-using-column-position-in-sas-tables-instead-of-column-names
Using column position instead of column names when common column positions have the correct data
    %let pgm=utl-using-column-position-in-sas-tables-instead-of-column-names;

    %stop_submission;

    Using column position instead of column names when commmon column positions have the correct data.

    Problem
      Compute the trend in freshman and software midterm and finals scores.

    github
    https://tinyurl.com/22kerf8e
    https://github.com/rogerjdeangelis/utl-using-column-position-in-sas-tables-instead-of-column-names

    SOAPBOX ON
    In the problem below we need to subtract column 6 from column 4 column in both datasets.
    Note column 4 and column 6 have different column names in the freshman and sophmore years, beuse the test were ate different dates.
    I think this has legs, especially when dealing with mispellings and excel sheets with and without column names.
    There are other ways to do this, but this seems self explanatory and maintainable.

    Note this is trivial in r anf python because column names and column positions are interchangeable.
    SOPABOX OFF


    related repos
    https://github.com/rogerjdeangelis/utl-using-column-position-instead-of-excel-column-names-due-to-misspellings-sas-r-python
    https://github.com/rogerjdeangelis/utl-working-with-column-positions-instead-of-column-names

    /***************************************************************************************************************************/
    /* INPUT                                     | PROCESS                                     |OUTPUT                         */
    /* =====                                     | =======                                     |======                         */
    /* WORK.FRESHMAN                             | SAS MACRO ARRAY (SIMPLIFIES ANALYSIS)       |  YEAR   CLASS  NAME   TREND   */
    /*                      MIDTERM        FINAL_|                                             |                               */
    /*   YEAR   CLASS  NAME 2020_07 NAME1 2021_05|                                             |Freshman Math  Alfred 10 +10pt */
    /*                                           | %array(fresh,values=%utl_varlist(freshman));|Freshman Lab   Alice  11 +11pt */
    /* Freshman Math  Alfred  77    Alfred   87  | %array(soph,values=%utl_varlist(sophmore)); |Sophmore Math  Alfred  8 +08pt */
    /* Freshman Lab   Alice   84    Alice    95  |                                             |Sophmore Lab   Alice  15 +15pt */
    /*                                           | data want;                                  |                               */
    /* WORK.SOPHMORE                             |   set freshman(in=a) sophmore;              |                               */
    /*                      MIDTERM       FINAL_ |   if a then trend=&fresh6-&fresh4;          |                               */
    /*   YEAR   CLASS  NAME 2020_08 NAME1 2021_06|   else trend=&soph6-&soph4;                 |                               */
    /*                                           |                                             |                               */
    /* Sophmore Math  Alfred  81   Alfred   89   |   drop name1 &soph6 &soph4 &fresh6 &fresh4; |                               */
    /* Sophmore Lab   Alice   75   Alice    90   |                                             |                               */
    /*                                           | run;quit;                                   |                               */
    /*                                           |                                             |                               */
    /* options validvarname=upcase;              |                                             |                               */
    /* data freshman;                            |                                             |                               */
    /* retain year 'Freshman';                   |                                             |                               */
    /*   input                                   |                                             |                               */
    /*     class$                                |                                             |                               */
    /*     name$                                 |                                             |                               */
    /*     midterm_2020_07                       |                                             |                               */
    /*     name1$                                |                                             |                               */
    /*     final_2021_05                         |                                             |                               */
    /*     ;                                     |                                             |                               */
    /* cards4;                                   |                                             |                               */
    /* Math Alfred 77 Alfred 87                  |                                             |                               */
    /* Lab Alice 84 Alice 95                     |                                             |                               */
    /* ;;;;                                      |                                             |                               */
    /* run;quit;                                 |                                             |                               */
    /*                                           |                                             |                               */
    /* options validvarname=upcase;              |                                             |                               */
    /* data sophmore;                            |                                             |                               */
    /* retain year 'Sophmore';                   |                                             |                               */
    /*   input                                   |                                             |                               */
    /*     class$                                |                                             |                               */
    /*     name$                                 |                                             |                               */
    /*     midterm_2020_08                       |                                             |                               */
    /*     name1$                                |                                             |                               */
    /*     final_2021_06                         |                                             |                               */
    /*     ;                                     |                                             |                               */
    /* cards4;                                   |                                             |                               */
    /* Math Alfred 81 Alfred 89                  |                                             |                               */
    /* Lab Alice 75 Alice 90                     |                                             |                               */
    /* ;;;;                                      |                                             |                               */
    /* run;quit;                                 |                                             |                               */
    /***************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options validvarname=upcase;
    data freshman;
    retain year 'Freshman';
      input
        class$
        name$
        midterm_2020_07
        name1$
        final_2021_05
        ;
    cards4;
    Math Alfred 77 Alfred 87
    Lab Alice 84 Alice 95
    ;;;;
    run;quit;

    data sophmore;
    retain year 'Sophmore';
      input
        class$
        name$
        midterm_2020_08
        name1$
        final_2021_06
        ;
    cards4;
    Math Alfred 81 Alfred 89
    Lab Alice 75 Alice 90
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                            | WANT  || WORK.SOPHMORE                              |                     */
    /*                      MIDTERM        FINAL_ |       ||                      MIDTERM       FINAL_  |                     */
    /*   YEAR   CLASS  NAME 2020_07 NAME1 2021_05 | TREND ||   YEAR   CLASS  NAME 2020_08 NAME1 2021_06 |                     */
    /*                                            |       ||                                            |                     */
    /* Freshman Math  Alfred  77    Alfred   87   | 87-77 || Sophmore Math  Alfred  81   Alfred   89    | 89=82               */
    /* Freshman Lab   Alice   84    Alice    95   | 94-84 || Sophmore Lab   Alice   75   Alice    90    | 90-75               */
    /**************************************************************************************************************************/

    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    */

    %array(fresh,values=%utl_varlist(freshman));
    %array(soph,values=%utl_varlist(sophmore));

    data want;
      set freshman(in=a) sophmore;
      if a then trend=&fresh6-&fresh4;
      else trend=&soph6-&soph4;

      drop name1 &soph6 &soph4 &fresh6 &fresh4;

    run;quit;

    /**************************************************************************************************************************/
    /*     YEAR      CLASS     NAME     TREND                                                                                 */
    /*                                                                                                                        */
    /*   Freshman    Math     Alfred      10                                                                                  */
    /*   Freshman    Lab      Alice       11                                                                                  */
    /*   Sophmore    Math     Alfred       8                                                                                  */
    /*   Sophmore    Lab      Alice       15                                                                                  */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
