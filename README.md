# PySpark_onJupyter
### Install Instructions for PySpark library on Mac OS to run inside Anaconda Jupyter Notebook 

I’m a relatively new data engineer at Grubhub, and I needed to quickly learn to use Apache Spark, the data processing engine our Enterprise Data Warehouse runs on. At my previous job, I designed and built enterprise data warehouses (EDW) at Fortune 100-type companies using business intelligence (BI) tools supplied by SAP and Oracle. In that world, all databases were relational and many visual tools provided enhanced modelling and construction. They emphasized understanding business processes as part of the report cube design.

In my new world, Grubhub defines their database tables with a Hive metadata layer and a Presto DB engine on top to facilitate data analysis with SQL, which all lives in AWS S3 backend physical cloud storage. Because we can interact with our cloud data via SQL, it’s easy to get fooled into thinking you are still working in a relational DB world when you are not. We do not have relational-type indexes on our data platform to speed up queries. Because of this, we have to think about our physical partitioning of Hive tables carefully.

This is all to say I cannot be a good data engineer if I only have a surface understanding of Spark/Hive/AWS S3. PySpark is our extract, transform, load (ETL) language workhorse. I had difficult time initially trying to learn it in terminal sessions connected to a server on an AWS cluster. It looked like the green code streams on Neo's screen saver in the Matrix movies.

This led me on a quest to install the Apache Spark libraries on my local Mac OS and use Anaconda Jupyter notebooks as my Pyspark learning environment. I prefer a visual programming environment with the ability to save code examples and learnings from mistakes. I went down the rabbit hole, reading a lot of sites/blogs/Github links to figure out what the heck the correct installation sequence was.

This article is intended to alleviate you from wasting time chasing parallel rabbit holes and simplify learning PySpark. You should be able to do so locally without buying time on a cloud server.

**Requirements- Java JDK and Jupyter Python notebook pre-installed:**
I’m going to assume that you have a working Anaconda Jupyter notebook running a Python version you are happy with. 
I will give you tips on installing Java as it’s critical to getting PySpark working.

This article will take you through three major processes: 
#### Installing Java;
#### Finding and installing a Spark version of your choice;
#### Setting up the minimum set of Python environment variables to run Spark inside a Jupyter notebook session.

**Note that all instructions here will assume you run a Mac OS.** 

#### Installing Java on your local machine
There are a couple of ways you can install the JDK. 
You can use Homebrew, https://brew.sh , on a Mac to install Java with "brew cask install java". 
Prior to an install, you can get more info with command "brew cask info java". 
Or you can go to Oracle's download site, http://www.oracle.com/technetwork/java/javase/downloads/index-jsp-138363.html ,
and choose the JDK platform you want, then download and install the *.dmg file.

Verify the exact folder on your machine's hard drive where the Java library resides. 
Now update your *.bash_profile* with the two environment variable statements shown below so that anything that needs Java will know where it is.

export JAVA_HOME=/Library/Java/JavaVirtualMachines/<your_jdk_version_directory>.jdk/Contents/Home
export PATH=$PATH:$JAVA_HOME/bin

So in this example, the value, "/Library/Java/JavaVirtualMachines/jdk1.8.0_144.jdk/Contents/Home/bin", 
is appended to what is already in $PATH.

#### Installing Apache Spark on your local machine

1) Go to the download site for Apache Spark, http://spark.apache.org/downloads.html ,and choose the version you want. 
You’ll want it to match either your organization’s version or an appropriate learning version.

2) Download and unzip the "*.tgz" file by double clicking it or using an unzip application.

3) I consider it consider safest to install/copy developer tools/software manually into "usr/local/" 
as a safe haven root directory for software you manually installed.  
Future software installs should treat the files you copied there ("usr/local") as a protected zone not to be touched.

4) I chose to create a new Spark directory named "/usr/local/spark/" and copy the downloaded Spark files there.

5) Either use a GUI tool like Finder (press control^ + shift + . to see hidden folders/files) or a terminal session to copy recursively the entire contents of the Apache Spark files directory with the Unix copy files command using “-r”, recursive parameter: 
“cp -r <source directory> /usr/local/spark/”

6) Do quick test after a successful Spark install. In a terminal session, go to your new Spark directory (/usr/local/spark/) and run "./bin/pyspark" to see if a successful Spark session starts up.

7) If there was an environment variable "IPYTHON" in your .bash_profile, 
you will need to reset that variable with this command:   “export IPYTHON="" ”. 
Check it is blank, with command: “echo $IPYTHON”. 
If it's not blank, Spark will complain with a message like :
 --> "Error in pyspark startup: IPYTHON and IPYTHON_OPTS are removed in Spark 2.0+. Remove these variables from the environment and set variables PYSPARK_DRIVER_PYTHON and PYSPARK_DRIVER_PYTHON_OPTS instead".

If you want to start a Spark session with IPython, 
set the environment variable as “PYSPARK_DRIVER_PYTHON=ipython pyspark” as suggested by 
this Coursera Big Data Intro Course, https://www.coursera.org/learn/big-data-essentials/lecture/o6oKt/getting-started-with-spark-python


#### PySpark environment variables to add to your .bash_profile

1) Make note of the locations of the files shown below.

I recommend you add these four lines to your .bash_profile:
export SPARK_HOME=/usr/local/spark 
export PYTHONPATH=$SPARK_HOME/python:$PYTHONPATH
export PYTHONPATH=$SPARK_HOME/python/lib/py4j-0.10.3-src.zip:$PYTHONPATH
export PATH=$PATH:$SPARK_HOME/bin

You may need to adjust them depending on where you did your specific Spark installation on your machine. 
Take note of the two zip files highlighted above as they will need to be referenced in a later section: 
  '/usr/local/spark/python/lib/pyspark.zip',
 	'/usr/local/spark/python/lib/py4j-0.10.3-src.zip'

2) Notice that we setup a root home directory for SPARK_HOME and re-use the SPARK_HOME value 
and concatenate it with other values to add to whatever is already in $PYTHONPATH. 
This is similar to what we did above with the $JAVA_HOME environment variable. 

You could enter these lines in a Jupyter notebook session, but it’s likely more convenient to update your .bash_profile.
Once you make any changes to your .bash_profile, you need to re-run it to load all the changes into your terminal session. 
If your .bash_profile is in directory /Users/<username>/, then run:
  source ./Users/<username>/.bash_profile

In summary, here's a list of the new environment variables to add to your .bash_profile so far:

export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_144.jdk/Contents/Home
export PATH=$PATH:$JAVA_HOME/bin
export SPARK_HOME=/usr/local/spark
export PYTHONPATH=$SPARK_HOME/python:$PYTHONPATH
export PYTHONPATH=$SPARK_HOME/python/lib/py4j-0.10.3-src.zip:$PYTHONPATH
export PATH=$PATH:$SPARK_HOME/bin


#### Environment variables to setup inside a Jupyter notebook:

1) The “PYLIB” environment variable seems to only be required as when running Spark in Jupyter notebook. 
I found I needed the next three environment variables by experimenting with the directions in this link 
from the Anaconda.org website: https://anaconda.org/anaconda-cluster/notebook-pyspark-language/notebook .

Run the following commands  inside your Jupyter notebook:
  import os
  os.environ["PYLIB"] = os.environ["SPARK_HOME"] + "/python/lib"
  print 'PYLIB: ', os.environ.get('PYLIB')

	**Output**: PYLIB: /usr/local/spark/python/lib

2) The “PYSPARK_PYTHON” environment variable must be set for the correct Python version you are using in your Jupyter notebook. 
In this example, I want to specify the Python 2.7 environment, as I have both Python 3.x and Python 2.7 installed.

Find the path to your Anaconda Python installation and then execute the commands below (adjusted to reflect your Anaconda install location) inside your Jupyter notebook. Take special care to determine the correct path, as you can get misleading permission errors if this is not correct.

  os.environ["PYSPARK_PYTHON"] = "/usr/local/anaconda3/envs/Py27root/bin/python"
  print 'PYSPARK_PYTHON: ', os.environ.get('PYSPARK_PYTHON')

	**Output**: PYSPARK_PYTHON: /usr/local/anaconda3/envs/Py27root/bin/python
  
 3) Add the two Spark ZIP file locations to the PATH environment variable.
Back in the section about modifying your .bash_profile with PySpark environment variables, 
I specified two ZIP files to add to your .bash_profile:
  '/usr/local/spark/python/lib/pyspark.zip',
 	'/usr/local/spark/python/lib/py4j-0.10.3-src.zip'

They need to be added to the PATH environment variable. Use these two commands below in your Jupyter notebook. To future-proof the variables, concatenate the file names with the PYLIB path reference:
	sys.path.insert(0, os.environ["PYLIB"] +"/py4j-0.10.3-src.zip")
  sys.path.insert(0, os.environ["PYLIB"] +"/pyspark.zip")

In addition, insert an entry for “SPARK_HOME/bin” with this command:
	sys.path.insert(0, os.environ["SPARK_HOME"] +"/bin")

Verify the three PATH entries were added properly with a print command: 
  print 'sys.path: ', '\n'

4) Two final environment variables PYSPARK_DRIVER and PYSPARK_DRIVER_OPTS.
I have found these two last environment variables to be very important to run PySpark inside a Jupyter notebook. Add the following:

  os.environ["PYSPARK_DRIVER_PYTHON"] = "ipython"
  os.environ["PYSPARK_DRIVER_PYTHON_OPTS"] = "notebook"

5) Start your PySpark session using the “shell.py” program, located inside the SPARK_HOME subdirectories.

If you can't find the “shell.py” program, print out your SPARK_HOME environment variable 
and then search its subdirectories. It should be very similar to the example below:
  usr->local->spark->python->pyspark->shell.py
  
Double check your “SPARK_HOME” environment variable by getting it with this command and print it.

  spark_home = os.environ.get('SPARK_HOME', None)
  print spark_home
 
	**Output**: /usr/local/spark

Now setup a variable to reference the path location of “shell.py” like this and print it to verify:

  spark_shell = spark_home + "/python/pyspark/shell.py"
  print spark_shell

  **Output**: '/usr/local/spark/python/pyspark/shell.py'

Now you are ready to initialize the PySpark session inside your Jupyter notebook with this command:
	exec(open(spark_shell).read())

	Output:

Hopefully, you should see the “Welcome to Spark” logo appear, 
which indicates that you have successfully initiated a PySpark session on your local Jupyter notebook. 
Happy learnings!




