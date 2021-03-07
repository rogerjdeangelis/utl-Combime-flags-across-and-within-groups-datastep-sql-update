# utl-Combime-flags-across-and-within-groups-datastep-sql-update
Combime flags across and within groups datastep sql update

    Combime flags across and within groups datastep sql update

    GitHub
    https://tinyurl.com/3m3y96tx
    https://github.com/rogerjdeangelis/utl-Combime-flags-across-and-within-groups-datastep-sql-update

    Inspired by
    https://tinyurl.com/34jeek7s
    https://communities.sas.com/t5/SAS-Programming/Combine-flags-across-within-group/m-p/724300

       Three Solutions

            a. update
            b. sql
            c. datastep

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    Data Have;
    infile datalines delimiter = "," dsd;
    input ID flagA $ FlagB $ FlagC $;
    cards;
    1,F,,
    2,F,,
    2,,,F
    3,F,,
    3,,F,
    3,,,F
    4,,,F
    5,,F,

    ;
                                     | RULES
      WORK.HAVE total obs=8          |
                                     |
      ID    FLAGA    FLAGB    FLAGC  | ID    FLAGA    FLAGB    FLAG
                                     |
       1      F                      |  1      F

       2      F                      |  **   COMBINE BOTH  OBS *****
       2                        F    |  2      F                 F

       3      F                      |  **   COMBINE ALL 3 OBS *****
       3               F             |
       3                        F    |  3      F        F        F

       4                        F    |  4                        F
       5               F             |  5               F

    *
     _ __  _ __ ___   ___ ___  ___ ___
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|                        _       _
      __ _     _   _ _ __   __| | __ _| |_ ___
     / _` |   | | | | '_ \ / _` |/ _` | __/ _ \
    | (_| |_  | |_| | |_) | (_| | (_| | ||  __/
     \__,_(_)  \__,_| .__/ \__,_|\__,_|\__\___|
                    |_|
    ;

    data want;

      update have(obs=0) have;
      by id;

    run;quit;

    *_                  _
    | |__     ___  __ _| |
    | '_ \   / __|/ _` | |
    | |_) |  \__ \ (_| | |
    |_.__(_) |___/\__, |_|
                     |_|
    ;

     proc sql;
        create
           table wantsql as
        select
           id
          ,max(flaga)
          ,max(flagb)
          ,max(flagc)
       from
          have
       group
          by id
    ;quit;

    *             _       _            _
      ___      __| | __ _| |_ __ _ ___| |_ ___ _ __
     / __|    / _` |/ _` | __/ _` / __| __/ _ \ '_ \
    | (__ _  | (_| | (_| | || (_| \__ \ ||  __/ |_) |
     \___(_)  \__,_|\__,_|\__\__,_|___/\__\___| .__/
                                              |_|
    ;

     data wantby;
        retain a b c " ";
        set have;
        by id;
        a=a<>flaga;
        b=b<>flagb;
        c=c<>flagc;
        if last.id then do;
           output;
           call missing (a,b,c);
           flaga =a ;
           flagb =b ;
           flagc =c ;
        end;
        drop a b c;
    run;quit;

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;


     WANT total obs=5

     ID    FLAGA    FLAGB    FLAGC

      1      F
      2      F                 F
      3      F        F        F
      4                        F
      5               F
