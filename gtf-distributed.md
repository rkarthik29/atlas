This document should help you undersand some configurations for extracting data in a fast parallel manner from your db. Below is how a sample flow will look like in nifi 1.8 onwards.

<img src="Screen%20Shot%202019-06-19%20at%2012.16.45%20AM.png" alt="nifi flow" width="1000" height="250">

The GenerateTableFetch is responsible for generating SQL queries that we can now distribute for parallel processing. This should be pinned to the primary node as shown below

<img src="Screen%20Shot%202019-06-19%20at%2012.21.24%20AM.png" alt="pin to primary" width="1000" height="500">

Set a suitable partition size and partitioning column in GenerateTableFetch as below

<img src="Screen%20Shot%202019-06-19%20at%2012.22.12%20AM.png" alt="partition" width="1000" height="500">


GTF will now generate queries that will extract rows of data from the table based on your partition size setting.


Sincce 1.8 you can distribute the flowfiles within a connection. So open the connection configuration and select load balance policy to round robin. This will distribute the generated queries to all the nodes in the cluster.

<img src="Screen%20Shot%202019-06-19%20at%2012.28.38%20AM.png" alt="distribute" width="1000" height="5000">


You can also play with the concurrent task settings in the executesqlrecord and puts3object processor. This will allow to create a pipeline where multiple threads could be executing the query and retrieve data from your db , while in seperate thread s3 processor is uploading the extracted data to s3. See below

<img src="Screen%20Shot%202019-06-19%20at%2012.27.51%20AM.png" alt="distribute" width="1000" height="500">

I would also not do a merge operation unless the size of the resultset returned by query is below a decent block size, 64MB is a good reference point.
