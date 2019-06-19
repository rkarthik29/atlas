This document should help you undersand some configurations for extracting data in a fast parallel manner from your db. Below is how a sample flow will look like in nifi 1.8 onwards.

The GenerateTableFetch is responsible for generating SQL queries that we can now distribute for parallel processing. This should be pinned to the primary node as shown below

Set a suitable partition size and partitioning column. 


GTF will now generate queries that will extract rows of data from the table based on your partition size setting.


Sincce 1.8 you can distribute the flowfiles within a connection. So open the connection configuration and select load balance policy to round robin. This will distribute the generated queries to all the nodes in the cluster. 

You can also play with the concurrent task settings in the executesqlrecord and puts3object processor. This will allow to create a pipeline where multiple threads could be executing the query and retrieve data from your db , while in seperate thread s3 processor is uploading the extracted data to s3. I would not do a merge operation unless the size of the resultset returned by query is below a decent block size, 64MB is a good reference point.
