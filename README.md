# Flat-to-DB2-to-DDL-for-SQL
 <h1> Lessons learned in transitioning </h1>
 Flat files are notorious for containing all sorts of data anywhere within the file. This is especially true if you come across multi-format flat files and need to convert them to SQL tables.
 

 Most System 36 flat files have a primary key that if you run a  DSPFFD command will contain a key K00001 and data F00001:
 
 ![image](https://github.com/user-attachments/assets/0ba60ec1-de51-43ae-bbfc-5b08f113486f)

 
The data in this file is multi formated 

![image](https://github.com/user-attachments/assets/05afec51-ea47-4b1a-a9de-3cb8756ed6ce)

Look at positions 15 - 19 , the second record has packed data while the other three have character data.
There is no way to define this in SQL, you need to create 2 tables, one that defines the packed data and another that defines the character.

You need to figure out what is unique about the format so you can filter out the data in the copy. In this case all packed records have 0000000000I starting at position 1.
<p>
The system 36 also is know to have lettered prefix's on the files to allow for multi-company occurances or instances of data that all reside in the QS36F files library (schema).
In this case there are multiple prefix's you will need to build the tables for each occurance. Create the table and copy the data to the destination omitting the appropriate data. 
	</p>
-------------------------------------

CREATE TABLE ntmtools.tbl_apwsavj for system name "APWSAVJ"

 (
 
	vendor_number                  for AWVEND NUMERIC(5, 0) NOT NULL DEFAULT 0 ,
 
	voucher_number                 for AWVCH# NUMERIC(5, 0) NOT NULL DEFAULT 0 ,
 
	record_type                    for AWRTYP CHAR(1) NOT NULL DEFAULT '' ,
 
	sequence_number                for AWSEQ# NUMERIC(3, 0) NOT NULL DEFAULT 0 ,
 
	invoice_number                 for AWINV# CHAR(12) NOT NULL DEFAULT '' ,
 
	manual_cheque                  for AWCHEQ CHAR(1) NOT NULL DEFAULT ''     
 
	)

 
	RCDFMT AWVCHRR    ;   
 
 
 LABEL ON COLUMN ntmtools.tbl_apwsavj
 
 ( AWVEND IS 'vendor Number' ,
 
	AWVCH# IS 'voucher Number' ,
 
	AWRTYP IS 'record Type' ,
 
	AWSEQ# IS 'sequence Number' ,
 
	AWINV# IS 'invoice Number' ,
 
	AWCHEQ IS 'manual cheque' ) ;  
 
<p> CL: CPYF FROMFILE(QS36F/J.APWSAV) TOFILE(NTMTOOLS/APWSAVJ) MBROPT(*REPLACE) INCCHAR(*RCD 1 *NE 0000000000I) FMTOPT(*NOCHK)   </p>   

---------------------------------------

Then build another table that contains the other data structure

-------------------------------------

CREATE TABLE ntmtools.tbl_apwsavj2

 for system name "APWSAVJ2"
 
(

	vendor_number                  for AWVEND NUMERIC(5, 0) NOT NULL DEFAULT 0 ,
 
	voucher_number                 for AWVCH# NUMERIC(5, 0) NOT NULL DEFAULT 0 ,
 
	record_type                    for AWRTYP CHAR(1) NOT NULL DEFAULT '' ,
 
	sequence_number                for AWSEQ# NUMERIC(3, 0) NOT NULL DEFAULT 0 ,
 
	voucher_number                 for AWINV# DECIMAL(7, 0) NOT NULL DEFAULT '' ,
 
 unused_00                      for AWUN00 CHAR(4) NOT NULL DEFAULT '',
 
	manual_cheque                  for AWCHEQ CHAR(1) NOT NULL DEFAULT ''     
 
	)
 
	RCDFMT AWVCHRR    ;   
 
 
 LABEL ON COLUMN ntmtools.tbl_apwsavj2
 
( AWVEND IS 'vendor Number' ,

	AWVCH# IS 'voucher Number' ,
 
	AWRTYP IS 'record Type' ,
 
	AWSEQ# IS 'sequence Number' ,
 
	AWINV# IS 'voucher Number' ,
 
	AWCHEQ IS 'manual cheque' ) ;  
 
     
<p> CL: CPYF FROMFILE(QS36F/J.APWSAV) TOFILE(NTMTOOLS/APWSAVJ2) MBROPT(*REPLACE) INCCHAR(*RCD 1 *EQ 0000000000I) FMTOPT(*NOCHK)      
---------------------------------------
 
