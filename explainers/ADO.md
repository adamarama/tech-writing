# What is ADO?

**ADO** stands for **A**ctiveX **D**ata **O**bjects. It is a programming model developed by Microsoft and provides an interface to OLE-DB. ADO allows C++ and Visual Basic programs to connect to the cloud-based Azure SQL Database, SQL Server, and other databases.

ADO has three primary objects:
+ *Connection* connects to a database management system (DBMS) or other data source and can send a query to the database.
+ *Command* provides an alternate method of sending a query to the database.
+ *Recordset* contains the resulting set of records.

## ADO

ADO has several object libraries used to process data. ADO allows the application to access data from various sources through an OLE-DB provider. It is fast, easy to use, and does not burden system resources significantly. It supports key features for building Web and client/server applications.

## ADO MD

The MD in ADO MD stands for "Multidimensional". It provides easy access to multidimensional data from Visual C++ and Visual Basic. It extends ADO to include objects unique to multidimensional data, such as the Cellset and CubeDef objects. 

ADO MD also uses the OLE-DB provider to access data. To work with ADO MD, the provider must be a multidimensional data provider (MDP). MDPs differ from tabular data providers (TDPs) because they present data in multidimensional views.

## RDS

Remote Data Service (RDS) is a feature of ADO. In a single round trip, it can:
+ move data from a server to a client application or Web page
+ manipulate client data
+ return updates to the server

## ADOX

ADOX stands for ActiveX Data Objects Extensions for Data Definition Language and Security. That's a lot to cover, so what exactly does it mean? As it says, ADOX is an extension to the ADO model. It includes objects to create and modify schemas and security. It enables you to write code that will work with various data sources regardless of their native syntaxes because it is an object-based approach to schema manipulation.

ADOX is a companion to the core ADO objects. It offers additional objects, such as tables and procedures for creating, modifying, and deleting schema objects. It also includes security objects that you can use to maintain users, groups, and permissions.

## ADO.NET

ADO.NET is the .NET version of ADO. It is very different from ADO. .NET Data Providers are used as an interface layer between the application and the databases. It also supports XML documents.

## Recommended content

+ [ADO Programmer's Guide](https://learn.microsoft.com/en-us/sql/ado/guide/ado-programmer-s-guide?view=sql-server-ver16)
+ [ADO Programmer's Reference](https://learn.microsoft.com/en-us/sql/ado/reference/ado-programmer-s-reference?view=sql-server-ver16)
+ [ADO Glossary](https://learn.microsoft.com/en-us/sql/ado/ado-glossary?view=sql-server-ver16)