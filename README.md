# Zillow-Api-End-to-End-Pipeline
What is an End-To-End Data Pipeline? 

These are all the processes needed to take data from a source to a report, basically making analytical and data-driven conclusions from data that was previously still at its source. Having explained the pipeline processes above, I'll move to the analytical stage. A data warehouse is very important when implementing an ETL data pipeline, it serves as a central repository of data integrated from multiple sources to serve as one single source-of-truth, storing current and historical data that has already been preprocessed in the pipeline before landing. This data is being structured for a specific purpose being analysis, either predictive or prescriptive analysis. Examples include AWS Redshift, Google BigQuery, etc.

Now its time to dive into my case-project, I built an end-to-end pipeline for Zillow API using AWS tools, I began by creating an EC2 instance on my user account on AWS, I did this because I want to host my Apache Airflow on an EC2 instance which is basically a compute engine or a virtual machine as its popularly regarded. The reason i did this was because i did not want to install a docker container on my local PC because of the performance demands and AWS already provides a host of virtual machines to choose from depending on your use case. I will drop all the Linux commands I used in my GitHub repo which I'll link at the end of this article. Next thing i did was to create 3 S3 buckets:
Input/landing bucket.
Intermediate bucket.
Transformed data / output bucket.

After creating the 3 S3 buckets, I went on to attach policies on my AWS EC2 instance, S3FullAccess to be specific, this will grant my AWS EC2 Instance which I'll be referring to as "Airflow-instance" the permission to access S3 buckets. There are 2 lambda triggers  set on the input and intermediate bucket which will trigger a load function to the next bucket, the function on the input bucket is a simple copy function to the end-to-end-intermediate-bucket.

https://cdn-images-1.medium.com/max/1200/1*73c_MzgGxiKcs369hLwIwQ.png

https://cdn-images-1.medium.com/max/1200/1*VVuyFolMx2PtvrAxWzxitA.png

Next thing was to create another function that will transform the data which has been loaded to the intermediate zone from a JSON document which is how most APIs are designed to output their data to a csv file using pandas module. The pandas module doesn't originally come on lambda but AWS provisioned a Pandas Layer which can be attached to our function to access the pandas module.

https://cdn-images-1.medium.com/max/1200/1*EmGODs8yJG47ctzJsyc04w.png

https://cdn-images-1.medium.com/max/1200/1*8bttncqxn-7C3wZBDSclgQ.png

https://cdn-images-1.medium.com/max/1200/1*rgGc_ST6BJAJmJGcTT3yvA.png

The code above is a lambda function that will be triggered when data with a suffix of .csv (I defined this when creating the trigger) lands on the intermediate zone. The function will send the transformed data to the output-orchestration-bucket (outputorcbucket).

The data in the output bucket would have to be manually added to our data warehouse, which is a Redshift serverless table. To automate the whole process from Extraction to loading, A Data Orchestration tool will be needed.

As per google, Data orchestration is the process of moving siloed data from multiple storage locations into a centralized repository where it can then be combined, cleaned, and enriched for activation (e.g., generating reports in a business intelligence tool).

Apache Airflow is my choice of data orchestrator and It's the most popular option among data engineers. I will also be leaving my python file for airflow in my GitHub repo. To be able to interact with my .py or python file seamlessly, rather than use the nano command-line-text-editor that comes with the Linux on my airflow-Instance, I used a VS Code extension called remote-ssh to connect with my Airflow-instance through VS Code. This was where I designed my airflow DAGs. Before explaining what a DAG is, I'd first upload a screenshot of my finished pipeline on airflow.

https://medium.com/dff700b2-990a-4568-92fc-f645bdf2a0fc