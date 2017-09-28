# utl_interface_python_and-sas

    ```  * Ways to Interface SAS with python  ```
    ```    ```
    ```   utl_submit_py64 macro is on the ned of message  ```
    ```    ```
    ```   Case one no return SAS macro variable  ```
    ```    ```
    ```    ```
    ```       1.    HAVE  ```
    ```               "Dr. Juan Q. Xavier Velasquez y Garcia, Jr."  ```
    ```    ```
    ```             WANT  ```
    ```               title: 'Dr.'  ```
    ```               first: 'Juan'  ```
    ```               middle: 'Q. Xavier'  ```
    ```               last: 'Velasquez y Garcia'  ```
    ```               suffix: 'Jr.'  ```
    ```               nickname: ''  ```
    ```    ```
    ```    ```
    ```             %utl_submit_py64('  ```
    ```              import pyperclip;  ```
    ```              from nameparser import HumanName;  ```
    ```              name = HumanName("Dr. Juan Q. Xavier de la Vega III");  ```
    ```              parsed=str(list(name));  ```
    ```              pyperclip.copy(parsed);  ```
    ```              ');  ```
    ```    ```
    ```             The paste buffer will have  ```
    ```    ```
    ```             [u'Dr.', u'Juan', u'Q. Xavier', u'de la Vega', u'III']  ```
    ```    ```
    ```    ```
    ```        2. Read a and print a SAS dataset in Python  ```
    ```    ```
    ```            options validvarname=upcase;  ```
    ```            libname sd1 "d:/sd1";  ```
    ```            data sd1.class;  ```
    ```              set sashelp.class;  ```
    ```            run;quit;  ```
    ```    ```
    ```            %utl_submit_py64('  ```
    ```            import numpy as np;  ```
    ```            import pandas as pd;  ```
    ```            from sas7bdat import SAS7BDAT;  ```
    ```            with SAS7BDAT("d:/sd1/class.sas7bdat") as m:;  ```
    ```            .   mdata = m.to_data_frame();  ```
    ```            print(mdata);  ```
    ```            ');  ```
    ```    ```
    ```                   NAME SEX   AGE  HEIGHT  WEIGHT  ```
    ```            0    Alfred   M  14.0    69.0   112.5  ```
    ```            1     Alice   F  13.0    56.5    84.0  ```
    ```    ```
    ```    ```
    ```        3. Quoting  ```
    ```    ```
    ```          DOUBLE QUOTES  ```
    ```    ```
    ```           There is no additonal quoting done in the macro.  ```
    ```           Here is the key statement in the first datastep in the macro  ```
    ```    ```
    ```           pgm=&pgm  ```
    ```    ```
    ```           The macro expects the macro argument pgm to be quoted.  ```
    ```    ```
    ```           You can use doble quotes for single static macro variable or  ```
    ```           immediate execution of a SAS macro inside then call.  ```
    ```    ```
    ```           %macro txt2py(dsn=class);  ```
    ```            from sas7bdat import SAS7BDAT;  ```
    ```            with SAS7BDAT('d:/sd1/&dsn..sas7bdat') as m:;  ```
    ```            .   mdata = m.to_data_frame();  ```
    ```            print(mdata);  ```
    ```           %mend txt2py;  ```
    ```    ```
    ```           %utl_submit_py64("  ```
    ```            import numpy as np;  ```
    ```            import pandas as pd;  ```
    ```            %txt2py(dsn=class);  ```
    ```           ");  ```
    ```    ```
    ```            or  ```
    ```    ```
    ```            %let dsn=class;  ```
    ```    ```
    ```            %utl_submit_py64("  ```
    ```             import numpy as np;  ```
    ```             from sas7bdat import SAS7BDAT;  ```
    ```             with SAS7BDAT('d:/sd1/&dsn..sas7bdat') as m:;  ```
    ```             .   mdata = m.to_data_frame();  ```
    ```             print(mdata);  ```
    ```             import pandas as pd;  ```
    ```            ");  ```
    ```    ```
    ```          SINGLE QUOTES  ```
    ```    ```
    ```           WITHIN A MACRO  ```
    ```    ```
    ```             %macro dopy(dummy);  ```
    ```                %let lst=class class;  ```
    ```                %do i=1 %to 2;  ```
    ```                   %let dsn=%scan(&lst,&i);  ```
    ```                   %put *** &dsn;  ```
    ```                   %utl_submit_py64(resolve('  ```
    ```                    import numpy as np;  ```
    ```                    from sas7bdat import SAS7BDAT;  ```
    ```                    with SAS7BDAT("d:/sd1/&dsn..sas7bdat") as m:;  ```
    ```                    .   mdata = m.to_data_frame();  ```
    ```                    print(mdata);  ```
    ```                    import pandas as pd;  ```
    ```                   '));  ```
    ```                %end;  ```
    ```             %mend dopy;  ```
    ```    ```
    ```             %dopy;  ```
    ```    ```
    ```    ```
    ```        4. WITH A RETURN MACRO VARIABLE  ```
    ```    ```
    ```            * you need the paperclip python module  ```
    ```              because I use the clipboard to return macroi variable;  ```
    ```    ```
    ```            %symdel fromPy / nowarn;  ```
    ```            %utl_submit_py64(resolve('  ```
    ```              import os;  ```
    ```              import pyperclip;  ```
    ```              path = "c:/utl";  ```
    ```              if os.listdir(path)==[]:;  ```
    ```              .    &returnVarName.="EMPTY NO FILES";  ```
    ```              else:;  ```
    ```              .    &returnVarName.="HAS FILES";  ```
    ```              pyperclip.copy(&returnVarName.);  ```
    ```              exit();  ```
    ```             ')  ```
    ```           ,returnVar=Y  ```
    ```           ,returnVarName=fromPy  ```
    ```            );  ```
    ```    ```
    ```            %put &=fromPy;  ```
    ```    ```
    ```    ```
    ```            FROMPY=HAS FILES  ```
    ```    ```
    ```        5. Interation inside a datastep  ```
    ```    ```
    ```           User decides on the name of the macro variable ie fromPy.  ```
    ```           fromPy contains the text from Python  ```
    ```    ```
    ```           data want;  ```
    ```            length pth $32;  ```
    ```    ```
    ```            do pth= 'd:/tym/mty', 'd:/mty', 'd:/tip';  ```
    ```               call symputx('pth',pth);  ```
    ```    ```
    ```               rc=dosubl('  ```
    ```               %utl_submit_py64(resolve(''  ```
    ```                import os;  ```
    ```                import pyperclip;  ```
    ```                path = "&pth.";  ```
    ```                if os.listdir(path)==[]:;  ```
    ```                .    &returnVarName.="&pth. EMPTY NO FILES";  ```
    ```                else:;  ```
    ```                .    &returnVarName.="&pth. HAS FILES";  ```
    ```                pyperclip.copy(&returnVarName.);  ```
    ```                exit();  ```
    ```              '')  ```
    ```               ,returnVar=Y  ```
    ```               ,returnVarName=fromPy  ```
    ```               );  ```
    ```              ');  ```
    ```    ```
    ```               fromPy=symget('fromPy');  ```
    ```               output  ```
    ```           ;  ```
    ```            end;  ```
    ```    ```
    ```           run;quit;  ```
    ```    ```
    ```    ```
    ```    ```
    ```  %macro utl_submit_py64(  ```
    ```        pgm  ```
    ```       ,returnVar=N           /* set to Y if you want a return SAS macro variable from python */  ```
    ```       ,returnVarName=fromPy  /* name for the macro variable from Python */  ```
    ```       )/des="Semi colon separated set of python commands - drop down to python";  ```
    ```    ```
    ```    * write the program to a temporary file;  ```
    ```    filename py_pgm "%sysfunc(pathname(work))/py_pgm.py" lrecl=32766 recfm=v;  ```
    ```    data _null_;  ```
    ```      length pgm  $32755 cmd $1024;  ```
    ```      file py_pgm ;  ```
    ```      pgm=&pgm;  ```
    ```      semi=countc(pgm,';');  ```
    ```        do idx=1 to semi;  ```
    ```          cmd=cats(scan(pgm,idx,';'));  ```
    ```          if cmd=:'. ' then  ```
    ```             cmd=trim(substr(cmd,2));  ```
    ```           put cmd $char384.;  ```
    ```           putlog cmd $char384.;  ```
    ```        end;  ```
    ```    run;quit;  ```
    ```    %let _loc=%sysfunc(pathname(py_pgm));  ```
    ```    %put &_loc;  ```
    ```    filename rut pipe  "C:\Python_27_64bit/python.exe &_loc";  ```
    ```    data _null_;  ```
    ```      file print;  ```
    ```      infile rut;  ```
    ```      input;  ```
    ```      put _infile_;  ```
    ```    run;  ```
    ```    filename rut clear;  ```
    ```    filename py_pgm clear;  ```
    ```    ```
    ```    * use the clipboard to create macro variable;  ```
    ```    %if %upcase(%substr(&returnVar.,1,1))=Y %then %do;  ```
    ```      filename clp clipbrd ;  ```
    ```      data _null_;  ```
    ```       length txt $200;  ```
    ```       infile clp;  ```
    ```       input;  ```
    ```       putlog "*******  " _infile_;  ```
    ```       call symputx("&returnVarName.",_infile_,"G");  ```
    ```      run;quit;  ```
    ```    %end;  ```
    ```    ```
    ```  %mend utl_submit_py64;  ```
    ```    ```
