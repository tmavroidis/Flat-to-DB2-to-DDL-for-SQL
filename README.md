# Flat-to-DB2-to-DDL-for-SQL
 <h1> Lessons learned in transitioning </h1>
 Flat files are notorious for containing all sorts of data anywhere within the file. This is especially true if you come across multi-format flat files and need to convert them to SQL tables.

 Most System 36 flat files have a primary key that if you run a  DSPFFD command will contain a key K00001 and data F00001:
 ![image](https://github.com/user-attachments/assets/0ba60ec1-de51-43ae-bbfc-5b08f113486f)
 
