# utl-read-the-last-line-in-a-text-file
Read the last line in a text file
    Read the last line in a text file                                                                                     
                                                                                                                          
      Method                                                                                                              
        1. Use powershell to get the number of lines in the file                                                          
        2. Use sas input modifier #<lines> to read last line                                                              
                                                                                                                          
    github                                                                                                                
    https://tinyurl.com/ycvobajl                                                                                          
    https://github.com/rogerjdeangelis/utl-read-the-last-line-in-a-text-file                                              
                                                                                                                          
    macros                                                                                                                
    https://tinyurl.com/y9nfugth                                                                                          
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories                            
                                                                                                                          
    *_                   _                                                                                                
    (_)_ __  _ __  _   _| |_                                                                                              
    | | '_ \| '_ \| | | | __|                                                                                             
    | | | | | |_) | |_| | |_                                                                                              
    |_|_| |_| .__/ \__,_|\__|                                                                                             
            |_|                                                                                                           
    ;                                                                                                                     
                                                                                                                          
     data _null_;                                                                                                         
                                                                                                                          
      file "d:/txt/back.txt";                                                                                             
                                                                                                                          
      do rec=1 to 10000;                                                                                                  
        strng=cats("A",rec)!!' ROGER';                                                                                    
        put strng;                                                                                                        
      end;                                                                                                                
                                                                                                                          
    run;quit;                                                                                                             
                                                                                                                          
    * 10,000 line files                                                                                                   
                                                                                                                          
    "d:/txt/back.txt"                                                                                                     
                                                                                                                          
       A1 ROGER                                                                                                           
       A2 ROGER                                                                                                           
       A3 ROGER                                                                                                           
       ...                                                                                                                
       A9998 ROGER                                                                                                        
       A9999 ROGER                                                                                                        
       A10000 ROGER                                                                                                       
                                                                                                                          
                                                                                                                          
    d:/txt/lastline.txt                                                                                                   
                                                                                                                          
       A10000 ROGER                                                                                                       
                                                                                                                          
    *                                                                                                                     
     _ __  _ __ ___   ___ ___  ___ ___                                                                                    
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|                                                                                   
    | |_) | | | (_) | (_|  __/\__ \__ \                                                                                   
    | .__/|_|  \___/ \___\___||___/___/                                                                                   
    |_|                                                                                                                   
    ;                                                                                                                     
                                                                                                                          
    * drop down to powershell;                                                                                            
    * put the numser of lines into the clipboard;                                                                         
    %utl_submit_ps64('Get-Content -Path d:/txt/back.txt | Measure-Object -Line | clip;');                                 
                                                                                                                          
    /*                                                                                                                    
    This si what is in the past buffer                                                                                    
    <blank line>                                                                                                          
    Lines Words Characters Property                                                                                       
    ----- ----- ---------- --------                                                                                       
    10000                                                                                                                 
    */                                                                                                                    
                                                                                                                          
    * read the paste buffer get the number of lines then read the last line;                                              
    data _null_;                                                                                                          
                                                                                                                          
      * get the number of lines into macro var;                                                                           
                                                                                                                          
      if _n_=0 then do; %dosubl('                                                                                         
          filename clippy clipbrd;                                                                                        
          data _null_;                                                                                                    
            infile clippy;                                                                                                
            input #4;                                                                                                     
            putlog _infile_;                                                                                              
            call symputx("endline",cats(_infile_));                                                                       
          run;quit;                                                                                                       
          ');                                                                                                             
      end;                                                                                                                
                                                                                                                          
      * read the last line;                                                                                               
                                                                                                                          
      infile "d:/txt/back.txt";                                                                                           
      file "d:/txt/lastline.txt";                                                                                         
      input #&endline;                                                                                                    
      put _infile_;                                                                                                       
      putlog _infile_;                                                                                                    
    run;quit;                                                                                                             
                                                                                                                          
    *                                                                                                                     
     _ __ ___   __ _  ___ _ __ ___  ___                                                                                   
    | '_ ` _ \ / _` |/ __| '__/ _ \/ __|                                                                                  
    | | | | | | (_| | (__| | | (_) \__ \                                                                                  
    |_| |_| |_|\__,_|\___|_|  \___/|___/                                                                                  
                                                                                                                          
    ;                                                                                                                     
                                                                                                                          
    %macro dosubl(arg);                                                                                                   
      %let rc=%qsysfunc(dosubl(&arg));                                                                                    
    %mend dosubl;                                                                                                         
                                                                                                                          
    %macro utl_submit_ps64(                                                                                               
          pgm                                                                                                             
         ,return=  /* name for the macro variable from Powershell */                                                      
         )/des="Semi colon separated set of python commands - drop down to python";                                       
                                                                                                                          
                                                                                                                          
      /*                                                                                                                  
          %let pgm='Get-Content -Path d:/txt/back.txt | Measure-Object -Line | clip;';                                    
      */                                                                                                                  
                                                                                                                          
      * write the program to a temporary file;                                                                            
      filename py_pgm "%sysfunc(pathname(work))/py_pgm.ps1" lrecl=32766 recfm=v;                                          
      data _null_;                                                                                                        
        length pgm  $32755 cmd $1024;                                                                                     
        file py_pgm ;                                                                                                     
        pgm=&pgm;                                                                                                         
        semi=countc(pgm,';');                                                                                             
          do idx=1 to semi;                                                                                               
            cmd=cats(scan(pgm,idx,';'));                                                                                  
            if cmd=:'. ' then                                                                                             
               cmd=trim(substr(cmd,2));                                                                                   
             put cmd $char384.;                                                                                           
             putlog cmd $char384.;                                                                                        
          end;                                                                                                            
      run;quit;                                                                                                           
      %let _loc=%sysfunc(pathname(py_pgm));                                                                               
      %put &_loc;                                                                                                         
      filename rut pipe  "powershell.exe -executionpolicy bypass -file &_loc ";                                           
      data _null_;                                                                                                        
        file print;                                                                                                       
        infile rut;                                                                                                       
        input;                                                                                                            
        put _infile_;                                                                                                     
        putlog _infile_;                                                                                                  
      run;                                                                                                                
      filename rut clear;                                                                                                 
      filename py_pgm clear;                                                                                              
                                                                                                                          
      * use the clipboard to create macro variable;                                                                       
      %if "&return" ^= "" %then %do;                                                                                      
        filename clp clipbrd ;                                                                                            
        data _null_;                                                                                                      
         length txt $200;                                                                                                 
         infile clp;                                                                                                      
         input;                                                                                                           
         putlog "*******  " _infile_;                                                                                     
         call symputx("&return",_infile_,"G");                                                                            
        run;quit;                                                                                                         
      %end;                                                                                                               
                                                                                                                          
    %mend utl_submit_ps64;                                                                                                
                                                                                                                          
                                                                                                                          
    * alternate way to run poershell;                                                                                     
                                                                                                                          
    * get the number of lines;                                                                                            
    %let cmd=%str(powershell Get-Content -Path d:/txt/back.txt | Measure-Object -Line | clip;);                           
                                                                                                                          
    * had issues with xcnd, sysexec and call system - systask seems to work better;                                       
    options xwait xsync;                                                                                                  
                                                                                                                          
    systask kill _ps1;                                                                                                    
    systask command "&cmd" taskname=_ps1;                                                                                 
    waitfor _ps1;                                                                                                         
                                                                                                                          
