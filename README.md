# CBT513
Converted to GitHub via [cbt2git](https://github.com/wizardofzos/cbt2git)

This is still a work in progress. 
Due to amazing work by Alison Zhang and Jake Choi repos are no longer deleted.

```
//***FILE 513 is from Jose Maria Martinez Besteiro (Cap Gemini      *   FILE 513
//*           Spain and Angel Luis Dominguez Martin (BBVA Spain)    *   FILE 513
//*           It is about using OPERLOG in a Sysplex, for archiving *   FILE 513
//*           data. This package includes a useful tool for system  *   FILE 513
//*           programmers and others, to search and select OPERLOG  *   FILE 513
//*           data, as though it were SYSLOG data, interactively    *   FILE 513
//*           from ISPF.                                            *   FILE 513
//*                                                                 *   FILE 513
//*      - - - - - - - - - - - - - - - - - - - - - - - - - - - - -  *   FILE 513
//*      March, 2001 (updated November 2006)                        *   FILE 513
//*                                                                 *   FILE 513
//*        This library contains several modules, clist, proc,      *   FILE 513
//*        and panels to set up our way to take advantage on        *   FILE 513
//*        OPERLOG.                                                 *   FILE 513
//*                                                                 *   FILE 513
//*        We appreciate comments or suggestions bout this tool.    *   FILE 513
//*        Some modules were taken from old share tapes. You'll     *   FILE 513
//*        fing their author's credits on top of them.  Please,     *   FILE 513
//*        do not remove this data.                                 *   FILE 513
//*                                                                 *   FILE 513
//*        If you want anybody to blame, here we are:               *   FILE 513
//*                                                                 *   FILE 513
//*         Angel Luis DOMINGUEZ MARTIN                             *   FILE 513
//*         BBVA Banco Bilbao Vizcaya Argentaria - Spain            *   FILE 513
//*         email: u0z72b6@gmail.com                                *   FILE 513
//*                                                                 *   FILE 513
//*         Jose Maria MARTINEZ BESTEIRO                            *   FILE 513
//*         Capgemini - Spain                                       *   FILE 513
//*         email: josemmar@capgemini.es                            *   FILE 513
//*                                                                 *   FILE 513
//*      Antecedents: SYSLOG vs OPERLOG                             *   FILE 513
//*      -----------------------------------                        *   FILE 513
//*                                                                 *   FILE 513
//*        We use in the past IBM's "powerfull program VPW,         *   FILE 513
//*        Virtual Paper Writter, to archive data from SYSLOG,      *   FILE 513
//*        so we could be able to search and exploit data from      *   FILE 513
//*        SYSLOG, using clist, rexx or any other program, as       *   FILE 513
//*        well as doing searchs directly from ISPF.                *   FILE 513
//*                                                                 *   FILE 513
//*        Once we left being the Y2K transition, we scheduled      *   FILE 513
//*        the move to the new OPERLOG format, known as MDB         *   FILE 513
//*        records, in order to achieve as much knowledge as we     *   FILE 513
//*        could, over the whole sysplex, and every system          *   FILE 513
//*        within it.                                               *   FILE 513
//*                                                                 *   FILE 513
//*        VPW was "killed" and a new set of programs and           *   FILE 513
//*        utilities took place instead.                            *   FILE 513
//*                                                                 *   FILE 513
//*        Nostalgics can download in Binary without CR/LF the      *   FILE 513
//*        member VPWDOC to PC and print it. It is a WORD           *   FILE 513
//*        document but unfortunatley in Spanish.                   *   FILE 513
//*                                                                 *   FILE 513
//*        With all our systems using OPERLOG, we start using       *   FILE 513
//*        IBM's sample program, named IEAMDBLG, that you can       *   FILE 513
//*        find in SYS1.SAMPLIB. This facility lets you write to    *   FILE 513
//*        SYSOUT, using SYSLOG format, the contents of the         *   FILE 513
//*        OPERLOG log stream, as well as mark MDBs for             *   FILE 513
//*        deletion.                                                *   FILE 513
//*                                                                 *   FILE 513
//*        We found this program missed some options, that could    *   FILE 513
//*        be very ussefull, but decided, for compatibility         *   FILE 513
//*        reasons, not to write a new program, but to upgrade      *   FILE 513
//*        IEAMDBLG. Our version of the module, is fully            *   FILE 513
//*        compatible, with no JCL change, with the original        *   FILE 513
//*        one.                                                     *   FILE 513
//*                                                                 *   FILE 513
//*        We introduced four new options: COPY(ALL), COPY(MDB),    *   FILE 513
//*        DELETE(ALL) and ENQ. You can find a description of       *   FILE 513
//*        each in the program itself.                              *   FILE 513
//*                                                                 *   FILE 513
//*        This new version of the program is SMPE installable.     *   FILE 513
//*        You can find a SYSMOD sample in member @MVSX08. This     *   FILE 513
//*        usermod does mark the sample member IEAMDBLG, to get     *   FILE 513
//*        a warning message when another PTF updates it.           *   FILE 513
//*                                                                 *   FILE 513
//*        We scheduled the program to run every 60 minutes at      *   FILE 513
//*        xx:05 time.  We put MDBs on DASD dataset, under SMS      *   FILE 513
//*        control, with a management class criteria of keeping     *   FILE 513
//*        then on DASD for 10 days, making one backup copy, for    *   FILE 513
//*        recovery reasons, and deleting them.                     *   FILE 513
//*                                                                 *   FILE 513
//*        You will find a JCL sample for the STC in member         *   FILE 513
//*        OPERLOG.                                                 *   FILE 513
//*                                                                 *   FILE 513
//*        We manage a set of dataset with the following data       *   FILE 513
//*        set name pattern:                                        *   FILE 513
//*                  OPERLOG.PLEX$A.B0010328.T020500                *   FILE 513
//*                  OPERLOG.PLEX$A.B0010328.T030500                *   FILE 513
//*                  OPERLOG.PLEX$A.B0010328.T040500                *   FILE 513
//*                                                                 *   FILE 513
//*        Where PLEX$A is the SYSPLEX name, B0010328 is the        *   FILE 513
//*        same as 20010328 (AAAAMMDD form) and T020500 means       *   FILE 513
//*        the GMT time for first register inside this file.        *   FILE 513
//*        Depending on your GMT time zone, you can have a          *   FILE 513
//*        difference from local times inside and the dataset       *   FILE 513
//*        name outside.  This is in order to make this             *   FILE 513
//*        application DST compliant, avoiding problems when        *   FILE 513
//*        local time changes back and forward daylight saving.     *   FILE 513
//*                                                                 *   FILE 513
//*        If you start IEAMDBLG at IPL time and, after that, at    *   FILE 513
//*        an hourly basis, you will get about 24 files per day     *   FILE 513
//*        in normal operations.  Instruct your SMS routines to     *   FILE 513
//*        migrate, delete or BKUP this files.                      *   FILE 513
//*                                                                 *   FILE 513
//*        We maintain 10 days on disk to access data               *   FILE 513
//*        interactively from ISPF, but this number can be          *   FILE 513
//*        easily changed, and this notation is useful to           *   FILE 513
//*        dyanamically allocate the data we need.                  *   FILE 513
//*                                                                 *   FILE 513
//*        As referred to in APAR PQ07414, SDSF gets slow           *   FILE 513
//*        response time using the OPERLOG component. So it is      *   FILE 513
//*        much better to search MDBs within the unloaded data      *   FILE 513
//*        on DASD, using the accompanying VERLOG facility from     *   FILE 513
//*        ISPF.                                                    *   FILE 513
//*                                                                 *   FILE 513
//*      USING VERLOG                                               *   FILE 513
//*      --------------------                                       *   FILE 513
//*                                                                 *   FILE 513
//*        New MDB records can be accessed by browsing directly     *   FILE 513
//*        the data set, but MDB format is not really a friendly    *   FILE 513
//*        one. So we designed an ISPF application to get then      *   FILE 513
//*        as usual, with the aid of search arguments: sysids,      *   FILE 513
//*        start-date-time to end-date-time, and so on, making      *   FILE 513
//*        it ieasier accessing to LOG data. Search results can     *   FILE 513
//*        be put into a file, if you want, and you can save        *   FILE 513
//*        this selected log data for later use: i.e. reporting     *   FILE 513
//*        software and hardware problems to IBM or other ISVs.     *   FILE 513
//*                                                                 *   FILE 513
//*        For compatibility purposes, data obtained are in         *   FILE 513
//*        SYSLOG format, so you can look at it as you did in       *   FILE 513
//*        the past.                                                *   FILE 513
//*                                                                 *   FILE 513
//*        You can use clist VERLOG to start the search. Two        *   FILE 513
//*        panels, (members VLOGP00 and VLOGH00) and a program      *   FILE 513
//*        (member SYVERLOG) to do the search. You must tailor      *   FILE 513
//*        the clist to match your data set naming conventions,     *   FILE 513
//*        but we think it will not be a complex task for           *   FILE 513
//*        experienced system programmers that have started         *   FILE 513
//*        operlog and so on .... Of course this ISPF aplication    *   FILE 513
//*        will take into account differences between LOCAL and     *   FILE 513
//*        GMT time, so all your searchs will be performed using    *   FILE 513
//*        your local time.                                         *   FILE 513
//*                                                                 *   FILE 513
//*        To obtain the real and executable SYVERLOG program       *   FILE 513
//*        you MUST run the JCL in $$ASM member. Tailor it to       *   FILE 513
//*        suit your standards and provide an intermediate LOAD     *   FILE 513
//*        to do the assembly. It will produce several routines     *   FILE 513
//*        and the facility load module itself.                     *   FILE 513
//*                                                                 *   FILE 513
//*        Of course, put CLIST and ISPPLIB in your SYSPROC and     *   FILE 513
//*        ISPPLIB concatenation. We suggest to put SYVERLOG in     *   FILE 513
//*        a linklisted library, but this is optional.              *   FILE 513
//*                                                                 *   FILE 513
//*        Now, you are ready to enjoy this tool.                   *   FILE 513
//*                                                                 *   FILE 513
//*                                  - - - o o o O o o o - - -      *   FILE 513
//*                                                                 *   FILE 513
```
