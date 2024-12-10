---
title: Summary
---
# <SwmToken path="/app/cbl/CBACT04C.cbl" pos="3:7:7" line-data="      * Application : CardDemo                                          ">`CardDemo`</SwmToken> Interest Calculator

This project is an interest calculator program for the <SwmToken path="/app/cbl/CBACT04C.cbl" pos="3:7:7" line-data="      * Application : CardDemo                                          ">`CardDemo`</SwmToken> application. It processes transaction category balances and computes interest rates for accounts.

# Summary

This document provides an overview of the <SwmToken path="/app/cbl/CBACT04C.cbl" pos="3:7:7" line-data="      * Application : CardDemo                                          ">`CardDemo`</SwmToken> interest calculator program, highlighting its main objectives, involved technologies, and current development status. The program is a batch COBOL application designed to calculate interest for various accounts based on transaction category balances.

The main components of the program include:

- File handling for transaction category balances, cross-reference data, account data, and disclosure group data.
- Procedures for opening, reading, updating, and closing files.
- Interest computation logic.

# Key functionalities

## File handling

<SwmSnippet path="/app/cbl/CBACT04C.cbl" line="31">

---

The program handles multiple files, each with specific roles. The files are defined in the <SwmToken path="/app/cbl/CBACT04C.cbl" pos="27:1:3" line-data="       FILE-CONTROL.                                                            ">`FILE-CONTROL`</SwmToken> section and include transaction category balances, cross-reference data, account data, and disclosure group data.

```
                  RECORD KEY   IS FD-TRAN-CAT-KEY                               
                  FILE STATUS  IS TCATBALF-STATUS.                              
```

---

</SwmSnippet>

## Data division

<SwmSnippet path="/app/cbl/CBACT04C.cbl" line="58">

---

The data division defines the structure of the records for each file. This includes the transaction category balance record, cross-reference record, account record, and disclosure group record.

```
      *                                                                         
       DATA DIVISION.                                                           
       FILE SECTION.                                                            
       FD  TCATBAL-FILE.                                                        
       01  FD-TRAN-CAT-BAL-RECORD.                                              
           05 FD-TRAN-CAT-KEY.                                                  
              10 FD-TRANCAT-ACCT-ID             PIC 9(11).                      
              10 FD-TRANCAT-TYPE-CD             PIC X(02).                      
              10 FD-TRANCAT-CD                  PIC 9(04).                      
           05 FD-FD-TRAN-CAT-DATA               PIC X(33).                      
```

---

</SwmSnippet>

## Working storage

<SwmSnippet path="/app/cbl/CBACT04C.cbl" line="94">

---

The working storage section contains variables and status indicators used throughout the program. This includes file status indicators and miscellaneous variables for processing.

```
       WORKING-STORAGE SECTION.                                                 
                                                                                
      *****************************************************************         
       COPY CVTRA01Y.                                                           
       01  TCATBALF-STATUS.                                                     
           05  TCATBALF-STAT1      PIC X.                                       
           05  TCATBALF-STAT2      PIC X.                                       
```

---

</SwmSnippet>

## Procedure division

The procedure division contains the main logic of the program. It includes procedures for opening files, processing records, computing interest, and closing files.

### Opening files

<SwmSnippet path="/app/cbl/CBACT04C.cbl" line="179">

---

The program opens the necessary files at the start of execution.

```
      *****************************************************************         
       PROCEDURE DIVISION USING EXTERNAL-PARMS.                                 
           DISPLAY 'START OF EXECUTION OF PROGRAM CBACT04C'.                    
           PERFORM 0000-TCATBALF-OPEN.                                          
           PERFORM 0100-XREFFILE-OPEN.                                          
           PERFORM 0200-DISCGRP-OPEN.                                           
           PERFORM 0300-ACCTFILE-OPEN.                                          
           PERFORM 0400-TRANFILE-OPEN.                                          
```

---

</SwmSnippet>

### Processing records

<SwmSnippet path="/app/cbl/CBACT04C.cbl" line="188">

---

The program processes records in a loop until the end of the file is reached. It reads transaction category balance records, updates account data, and computes interest.

```
           PERFORM UNTIL END-OF-FILE = 'Y'                                      
               IF  END-OF-FILE = 'N'                                            
                   PERFORM 1000-TCATBALF-GET-NEXT                               
                   IF  END-OF-FILE = 'N'                                        
                     ADD 1 TO WS-RECORD-COUNT                                   
                     DISPLAY TRAN-CAT-BAL-RECORD                                
                     IF TRANCAT-ACCT-ID NOT= WS-LAST-ACCT-NUM                   
                       IF WS-FIRST-TIME NOT = 'Y'                               
                          PERFORM 1050-UPDATE-ACCOUNT                           
                       ELSE                                                     
                          MOVE 'N' TO WS-FIRST-TIME                             
                       END-IF                                                   
                       MOVE 0 TO WS-TOTAL-INT                                   
                       MOVE TRANCAT-ACCT-ID TO WS-LAST-ACCT-NUM                 
                       MOVE TRANCAT-ACCT-ID TO FD-ACCT-ID                       
                       PERFORM 1100-GET-ACCT-DATA                               
                       MOVE TRANCAT-ACCT-ID TO FD-XREF-ACCT-ID                  
                       PERFORM 1110-GET-XREF-DATA                               
                     END-IF                                                     
```

---

</SwmSnippet>

### Computing interest

<SwmSnippet path="/app/cbl/CBACT04C.cbl" line="461">

---

The program computes interest based on the transaction category balance and the interest rate from the disclosure group file.

```
      *---------------------------------------------------------------*         
       1300-COMPUTE-INTEREST.                                                   
                                                                                
           COMPUTE WS-MONTHLY-INT                                               
            = ( TRAN-CAT-BAL * DIS-INT-RATE) / 1200                             
                                                                                
           ADD WS-MONTHLY-INT  TO WS-TOTAL-INT                                  
           PERFORM 1300-B-WRITE-TX.                                             
```

---

</SwmSnippet>

### Closing files

<SwmSnippet path="/app/cbl/CBACT04C.cbl" line="224">

---

The program closes all files at the end of execution.

```
           PERFORM 9000-TCATBALF-CLOSE.                                         
           PERFORM 9100-XREFFILE-CLOSE.                                         
           PERFORM 9200-DISCGRP-CLOSE.                                          
           PERFORM 9300-ACCTFILE-CLOSE.                                         
           PERFORM 9400-TRANFILE-CLOSE.                                         
                                                                                
           DISPLAY 'END OF EXECUTION OF PROGRAM CBACT04C'.                      
```

---

</SwmSnippet>

# Technology used

- COBOL: The primary programming language used for the batch program.
- Indexed files: Used for storing and accessing transaction category balances, cross-reference data, account data, and disclosure group data.

# Dependencies/Referencia

```
└── Project
    ├── [V] COBOL
    ├── [V] Indexed Files
```

# Workflow

The workflow of the <SwmToken path="/app/cbl/CBACT04C.cbl" pos="3:7:7" line-data="      * Application : CardDemo                                          ">`CardDemo`</SwmToken> interest calculator program involves the following steps:

1. **Initialization**: The program starts by displaying a message and opening the necessary files.
2. **Processing**: The program processes transaction category balance records in a loop, updating account data and computing interest.
3. **Finalization**: The program closes all files and displays a message indicating the end of execution.

# Communication points

The program interacts with multiple files to read and write data. These files include:

- **Transaction category balance file**: Stores transaction category balances.
- **Cross-reference file**: Stores cross-reference data for accounts.
- **Account file**: Stores account data.
- **Disclosure group file**: Stores disclosure group data for interest rates.

# Recommendations

To improve the architecture and performance of the <SwmToken path="/app/cbl/CBACT04C.cbl" pos="3:7:7" line-data="      * Application : CardDemo                                          ">`CardDemo`</SwmToken> interest calculator program, consider the following recommendations:

1. **Error handling**: Enhance error handling mechanisms to provide more detailed error messages and recovery options.
2. **Code modularity**: Refactor the code to improve modularity and readability by breaking down large procedures into smaller, reusable functions.
3. **Performance optimization**: Optimize file access and processing logic to reduce execution time and improve efficiency.
4. **Security**: Implement security measures to protect sensitive data, such as encryption for stored data and secure access controls.

By following these recommendations, the program's architecture, code quality, security, and performance can be significantly improved.

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBYXdzLW1haW5mcmFtZS1tb2Rlcm5pemF0aW9uLWNhcmQtZGVtbyUzQSUzQXZhbGRlY2lyY29tcGFzc28=" repo-name="aws-mainframe-modernization-card-demo"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
