1. Install Java 1.8(sdk), set  JAVA_HOME and add to path %JAVA_HOME%\bin, if need be
2. Install Anaconda (for python) or Python 3.8 and pip install jupyter
3. Install Apache Spark - http://spark.apache.org/downloads.html
   - Choose a Spark release: 3.1.2 (Jun 01 2021)
   - Choose a package type:  
     Pre-built for Apache Hadoop 2.7
   - Download Spark: spark-3.1.2-bin-hadoop2.7.tgz
   - set SPARK_HOME=C:\Users\<your_user_name>\Desktop\Spark\<yoursparkversion-spark-2.4.0-bin-hadoop2.7>
     in my case, set SPARK_HOME=C:\Users\padma\spark\spark-sql\tools\spark-3.1.2-bin-hadoop2.7
   - Add to path --> set PATH=%SPARK_HOME%\bin:%PATH%
   - my JAVA_HOME, set JAVA_HOME=C:\Program Files\Java\jdk1.8.0_291
# Install winutils.exe
- Please locate winutils.exe http://github.com/steveloughran/winutils
- Copy the bin folder from here, https://github.com/steveloughran/winutils/tree/master/hadoop-2.7.1 into
hadoop\bin folder in the SPARK_HOME folder
- create HADOOP_HOME environment variable using a value of %SPARK_HOME%\hadoop.
  set HADOOP_HOME=%SPARK_HOME%\hadoop

#  Using Spark from Jupyter
- Click on Windows and search “Anacnoda Prompt”. 
- Open Anaconda prompt and type “python -m pip install findspark”.
- findspark package is necessary to run spark from Jupyter notebook. 
  ~~set PYSPARK_DRIVER_PYTHON=jupyter --> this may not be needed ~~
  ~~set PYSPARK_DRIVER_PYTHON_OPTS=notebook --> this may not be needed~~
- Start "jupyter notebook" and hit enter. This would open a jupyter notebook from your browser. 
   From Jupyter notebook--> New --> Select Python3

# Cell 1, Run 
- import findspark
- findspark.init()

# Cell2, run
- import pyspark
- from pyspark.sql import SparkSession

- spark = SparkSession.builder.getOrCreate()
- df = spark.sql("select 'spark' as hello ")
- df.show()

# cell3, run
- sc = spark.sparkContext
- print(f'sparkContext: {sc}')

# cell4, run
- sqlContext = spark


ref: Getting Started with Spark 2


## Notes/appendix
-------------------------------------------------------------------------------------------
# Setup pyspark on windows
Follow Janani Ravi Getting started Spark2 - see chapter  Demo: Installing Spark 2
https://bigdata-madesimple.com/guide-to-install-spark-and-use-pyspark-from-jupyter-in-windows/
Use hadoop 2.7.1

Did not setup bash profile in windows, copied bunch of files for winutils.exe


# How to start jupyter notebook with pyspark
setup pyspark with jupyter notebook

https://changhsinlee.com/install-pyspark-windows-jupyter/
find spark module

~~set PYSPARK_DRIVER_PYTHON=jupyter ~~
~~set PYSPARK_DRIVER_PYTHON_OPTS=notebook ~~

jupyter notebook
Once inside Jupyter notebook, open a Python 3 notebook

import findspark
findspark.init()

import pyspark
from pyspark.sql import SparkSession
spark = SparkSession.builder.getOrCreate()

df = spark.sql('''select 'spark' as hello ''')
df.show()

print(f'sparkContext: {spark.sparkContext}')

# pyspark jupyter project location
- spark-2-getting-started/02/demos/code

AWS CLI
------------------
#Install aws cli
- python -m pip aws
- install aws cli msi executable, https://s3.amazonaws.com/aws-cli/AWSCLI64PY3.msi

# run  'aws configure' from command line
- AWS Access Key ID [None]: accesskey
- AWS Secret Access Key [None]: secretkey
- Default region name [None]: 
- Default output format [None]

