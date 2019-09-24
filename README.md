# utl-creating-a-crosstab-dataset-with-a-zero-row-using-proc-report-preloadfmt
Creating a crosstab dataset with a zero row using proc report preloadfmt                                                                                                                                         
    Creating a crosstab dataset with a zero row using proc report preloadfmt                                                     
                                                                                                                                 
    It is nore useful to have a output dataset than a proc tabulate printout                                                     
                                                                                                                                 
    github                                                                                                                       
    https://tinyurl.com/y5kzu3dc                                                                                                 
    https://github.com/rogerjdeangelis/utl-creating-a-crosstab-dataset-with-a-zero-row-using-proc-report-preloadfmt              
                                                                                                                                 
    SAS Forum                                                                                                                    
    https://tinyurl.com/y6apfco9                                                                                                 
    https://communities.sas.com/t5/SAS-Programming/Proc-Freq-to-display-0-frequency/m-p/590636                                   
                                                                                                                                 
    *_                   _                                                                                                       
    (_)_ __  _ __  _   _| |_                                                                                                     
    | | '_ \| '_ \| | | | __|                                                                                                    
    | | | | | |_) | |_| | |_                                                                                                     
    |_|_| |_| .__/ \__,_|\__|                                                                                                    
            |_|                                                                                                                  
    ;                                                                                                                            
                                                                                                                                 
    data have;                                                                                                                   
      input person firsttest $ secondtest $;                                                                                     
    cards4;                                                                                                                      
    1 Good Good                                                                                                                  
    2 Poor Good                                                                                                                  
    3 Good Bad                                                                                                                   
    4 Poor Bad                                                                                                                   
    5 Poor Poor                                                                                                                  
    ;;;;                                                                                                                         
    run;quit;                                                                                                                    
                                                                                                                                 
     WORK.HAVE total obs=5                                                                                                       
                                                                                                                                 
      PERSON    FIRSTTEST    SECONDTEST                                                                                          
                                                                                                                                 
         1        Good          Good                                                                                             
         2        Poor          Good                                                                                             
         3        Good          Bad                                                                                              
         4        Poor          Bad                                                                                              
         5        Poor          Poor                                                                                             
                                                                                                                                 
    *            _               _                                                                                               
      ___  _   _| |_ _ __  _   _| |_                                                                                             
     / _ \| | | | __| '_ \| | | | __|                                                                                            
    | (_) | |_| | |_| |_) | |_| | |_                                                                                             
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                                            
                    |_|                                                                                                          
    ;                                                                                                                            
                                                                                                                                 
    WORK.WANT total obs=4                                                                                                        
                                                                                                                                 
      FIRSTTEST    BAD    GOOD    POOR    SUM                                                                                    
                                                                                                                                 
        Bad         0       0       0      0                                                                                     
        Good        1       1       0      2                                                                                     
        Poor        1       1       1      3                                                                                     
        Sum         2       2       1      5                                                                                     
                                                                                                                                 
    *                                                                                                                            
     _ __  _ __ ___   ___ ___  ___ ___                                                                                           
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|                                                                                          
    | |_) | | | (_) | (_|  __/\__ \__ \                                                                                          
    | .__/|_|  \___/ \___\___||___/___/                                                                                          
    |_|                                                                                                                          
    ;                                                                                                                            
                                                                                                                                 
    proc format;                                                                                                                 
    value $ test_values                                                                                                          
       "Good" = "Good"                                                                                                           
       "Poor" = "Poor"                                                                                                           
       "Bad" = "Bad"                                                                                                             
    ;                                                                                                                            
     run;                                                                                                                        
                                                                                                                                 
    * unfortunately report dos not honor ods output - so you have to rename;                                                     
    proc report data=raw  completerows out=want (rename=(                                                                        
           /* report always uses alphabetic order */                                                                             
           _c2_=Bad                                                                                                              
           _c3_=Good                                                                                                             
           _c4_=Poor                                                                                                             
    ) drop=_break_);                                                                                                             
                                                                                                                                 
    col firsttest  secondtest sum;                                                                                               
    define firsttest / group  format=$test_values. preloadfmt;                                                                   
                                                                                                                                 
    define secondtest / across ;                                                                                                 
    define Sum / computed;                                                                                                       
                                                                                                                                 
    compute sum;                                                                                                                 
       sum=_c2_ + _c3_ + _c4_;                                                                                                   
    endcomp;                                                                                                                     
                                                                                                                                 
    compute firsttest;                                                                                                           
      if firsttest="" then firsttest="Sum";                                                                                      
    endcomp;                                                                                                                     
    rbreak after / summarize;                                                                                                    
                                                                                                                                 
    run;quit;                                                                                                                    
                                                                                                                                 
