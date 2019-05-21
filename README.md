
# Understanding `SparkContext` - Lab

## Introduction

The following series of PySpark lessons and labs in this section will provide you with an introduction to **Apache Spark**, the leading framework for big data processing in jupyter notebooks and PySpark, using a PySpark docker image in a standalone mode. These lessons require you explore the spark documentation and explore and practice methods and properties which are not directly covered in the labs. In this first lesson, we'll look at creating a spark session , called a Spark Context and explore its properties. 

## Objectives

You will be able to:

* Describe Spark's parallalism with master and executor nodes
* Understand SparkContext() for managing connections in parallel applications 
* Provide an overview of major SparkContext() properties and methods

## Cluster Resource Manager

Spark comes bundled with a **Cluster Resource Manager** which divides and shares the physical resources of a cluster of machines between multiple Spark applications. Spark's **Standalone cluster manager** operates in the standalone mode and allows Spark to manage its own cluster on a local machine. We will mainly use the standalone cluster manager for resource allocation in these labs. 


In Spark computational model, communication routinely occurs between a **driver** and **executors**. The driver has Spark jobs that it needs to run and these jobs are split into tasks that are submitted to the workers for completion. The results from these tasks are delivered back to the driver. 

This image, taken from the [Spark documentation](https://spark.apache.org/docs/latest/spark-standalone.html) demonstrates this process well.

![](cluster.png)


The spark driver declares the transformations and actions on data and submits such requests to the **master**. 

> The machine on which the Spark cluster manager runs is called the **Master Node**. 

For our labs, this distributed arrangement will be simulated on a single machine allowing you to initialize master and worker nodes. 

## `SparkContext()`

In order to use Spark and its API we will need to use a **SparkContext**. SparkContext is how we are able to control what is happening in the Spark program from python. When we run any Spark application, a driver program starts, which has the main function and your SparkContext gets initiated here. The driver program then runs the operations inside the executors on worker nodes as shown above.

SparkContext uses Py4J to create a bridge between python and java, the language spark is built with. Even though all the code we'll be executing is in python, java is the code being executed underneath the hood in a JavaSparkConext. You'll see in error messages that they will frequently contain errors related specifically to Java. 

*Py4j provides a bridge between python and Java. [Click here](https://www.py4j.org/) to see more details on this. Here is a visual representation of how SparkContext functions found in the [Apache documentation](https://cwiki.apache.org/confluence/display/SPARK/PySpark+Internals)* 

![](./spark_context.png)

Spark applications driver program launches parallel operations on executor Java Virtual Machines (JVMs). This can occur either locally on a single machine using multiple cores to create parallel processing or across a cluster of computers that are controlled by a master computer. When running locally, "PySparkShell" is the driver program. The driver program contains the key instructions for the program and it determines how to best distribute datasets across the cluster and apply operations to those datasets.

The key takeaways for SparkContext are listed below:

- SparkContext is a client of Spark’s execution environment and it acts as the master of the Spark application.
- SparkContext sets up internal services and establishes a connection to a Spark execution environment. 
- The driver is the program that creates the SparkContext, connecting to a given Spark Master. 

After creation, SparkContext asks the master for some cores to use to do work. The master sets these cores aside and they are used to complete whatever operation they are assigned to do.

As stated before, a SparkContext object (usually shown as `sc`) is the main entry point for Spark functionality and can be used to create `Resilient Distributed Datasets` (RDDs) on a cluster as we will see in our next lab.

Lets start a spark application by importing pyspark, creating a spark context as `sc` and try printing out type of `sc`. For this SparkContext, we are going to assign the `master` parameter to 'local[ * ]' to indicate that we are running this SparkContext to be parallelized on our local machine.



```python
# Create a local spark context with pyspark
import pyspark
sc = pyspark.SparkContext('local[*]')
```


```python
# Display the type of the Spark Context
type(sc)

# pyspark.context.SparkContext
```




    pyspark.context.SparkContext




```python
# Create second spark context for double the computing power!
sc1 = pyspark.SparkContext('local[*]')
```


    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-8-6a3bdb55f42e> in <module>()
          1 # Create second spark context for double the computing power!
    ----> 2 sc1 = pyspark.SparkContext('local[*]')
    

    ~/anaconda3/lib/python3.6/site-packages/pyspark/context.py in __init__(self, master, appName, sparkHome, pyFiles, environment, batchSize, serializer, conf, gateway, jsc, profiler_cls)
        113         """
        114         self._callsite = first_spark_call() or CallSite(None, None, None)
    --> 115         SparkContext._ensure_initialized(self, gateway=gateway, conf=conf)
        116         try:
        117             self._do_init(master, appName, sparkHome, pyFiles, environment, batchSize, serializer,


    ~/anaconda3/lib/python3.6/site-packages/pyspark/context.py in _ensure_initialized(cls, instance, gateway, conf)
        306                         " created by %s at %s:%s "
        307                         % (currentAppName, currentMaster,
    --> 308                             callsite.function, callsite.file, callsite.linenum))
        309                 else:
        310                     SparkContext._active_spark_context = instance


    ValueError: Cannot run multiple SparkContexts at once; existing SparkContext(app=pyspark-shell, master=local[*]) created by __init__ at <ipython-input-1-ced9ce282d4f>:3 


As you can see, only one SparkContext can be created within a python kernel at once!

### SparkContext attributes

We can use Python's `dir()` function to get a list of all the attributes (including methods) accessible through the `sc` object.


```python
# Use Python's dir(obj) to get a list of all attributes of SparkContext
dir(sc)
```




    ['PACKAGE_EXTENSIONS',
     '__class__',
     '__delattr__',
     '__dict__',
     '__dir__',
     '__doc__',
     '__enter__',
     '__eq__',
     '__exit__',
     '__format__',
     '__ge__',
     '__getattribute__',
     '__getnewargs__',
     '__gt__',
     '__hash__',
     '__init__',
     '__init_subclass__',
     '__le__',
     '__lt__',
     '__module__',
     '__ne__',
     '__new__',
     '__reduce__',
     '__reduce_ex__',
     '__repr__',
     '__setattr__',
     '__sizeof__',
     '__str__',
     '__subclasshook__',
     '__weakref__',
     '_accumulatorServer',
     '_active_spark_context',
     '_batchSize',
     '_callsite',
     '_checkpointFile',
     '_conf',
     '_dictToJavaMap',
     '_do_init',
     '_ensure_initialized',
     '_gateway',
     '_getJavaStorageLevel',
     '_initialize_context',
     '_javaAccumulator',
     '_jsc',
     '_jvm',
     '_lock',
     '_next_accum_id',
     '_pickled_broadcast_vars',
     '_python_includes',
     '_repr_html_',
     '_serialize_to_jvm',
     '_temp_dir',
     '_unbatched_serializer',
     'accumulator',
     'addFile',
     'addPyFile',
     'appName',
     'applicationId',
     'binaryFiles',
     'binaryRecords',
     'broadcast',
     'cancelAllJobs',
     'cancelJobGroup',
     'defaultMinPartitions',
     'defaultParallelism',
     'dump_profiles',
     'emptyRDD',
     'environment',
     'getConf',
     'getLocalProperty',
     'getOrCreate',
     'hadoopFile',
     'hadoopRDD',
     'master',
     'newAPIHadoopFile',
     'newAPIHadoopRDD',
     'parallelize',
     'pickleFile',
     'profiler_collector',
     'pythonExec',
     'pythonVer',
     'range',
     'runJob',
     'sequenceFile',
     'serializer',
     'setCheckpointDir',
     'setJobDescription',
     'setJobGroup',
     'setLocalProperty',
     'setLogLevel',
     'setSystemProperty',
     'show_profiles',
     'sparkHome',
     'sparkUser',
     'startTime',
     'statusTracker',
     'stop',
     'textFile',
     'uiWebUrl',
     'union',
     'version',
     'wholeTextFiles']



Alternatively, you can use Python's `help()` function to get an easier to read list of all the attributes, including examples, that the sc object has.


```python
# Use Python's help ( help(object) ) function to get information on attributes and methods for sc object. 
help(sc)

```

    Help on SparkContext in module pyspark.context object:
    
    class SparkContext(builtins.object)
     |  Main entry point for Spark functionality. A SparkContext represents the
     |  connection to a Spark cluster, and can be used to create L{RDD} and
     |  broadcast variables on that cluster.
     |  
     |  Methods defined here:
     |  
     |  __enter__(self)
     |      Enable 'with SparkContext(...) as sc: app(sc)' syntax.
     |  
     |  __exit__(self, type, value, trace)
     |      Enable 'with SparkContext(...) as sc: app' syntax.
     |      
     |      Specifically stop the context on exit of the with block.
     |  
     |  __getnewargs__(self)
     |  
     |  __init__(self, master=None, appName=None, sparkHome=None, pyFiles=None, environment=None, batchSize=0, serializer=PickleSerializer(), conf=None, gateway=None, jsc=None, profiler_cls=<class 'pyspark.profiler.BasicProfiler'>)
     |      Create a new SparkContext. At least the master and app name should be set,
     |      either through the named parameters here or through C{conf}.
     |      
     |      :param master: Cluster URL to connect to
     |             (e.g. mesos://host:port, spark://host:port, local[4]).
     |      :param appName: A name for your job, to display on the cluster web UI.
     |      :param sparkHome: Location where Spark is installed on cluster nodes.
     |      :param pyFiles: Collection of .zip or .py files to send to the cluster
     |             and add to PYTHONPATH.  These can be paths on the local file
     |             system or HDFS, HTTP, HTTPS, or FTP URLs.
     |      :param environment: A dictionary of environment variables to set on
     |             worker nodes.
     |      :param batchSize: The number of Python objects represented as a single
     |             Java object. Set 1 to disable batching, 0 to automatically choose
     |             the batch size based on object sizes, or -1 to use an unlimited
     |             batch size
     |      :param serializer: The serializer for RDDs.
     |      :param conf: A L{SparkConf} object setting Spark properties.
     |      :param gateway: Use an existing gateway and JVM, otherwise a new JVM
     |             will be instantiated.
     |      :param jsc: The JavaSparkContext instance (optional).
     |      :param profiler_cls: A class of custom Profiler used to do profiling
     |             (default is pyspark.profiler.BasicProfiler).
     |      
     |      
     |      >>> from pyspark.context import SparkContext
     |      >>> sc = SparkContext('local', 'test')
     |      
     |      >>> sc2 = SparkContext('local', 'test2') # doctest: +IGNORE_EXCEPTION_DETAIL
     |      Traceback (most recent call last):
     |          ...
     |      ValueError:...
     |  
     |  __repr__(self)
     |      Return repr(self).
     |  
     |  accumulator(self, value, accum_param=None)
     |      Create an L{Accumulator} with the given initial value, using a given
     |      L{AccumulatorParam} helper object to define how to add values of the
     |      data type if provided. Default AccumulatorParams are used for integers
     |      and floating-point numbers if you do not provide one. For other types,
     |      a custom AccumulatorParam can be used.
     |  
     |  addFile(self, path, recursive=False)
     |      Add a file to be downloaded with this Spark job on every node.
     |      The C{path} passed can be either a local file, a file in HDFS
     |      (or other Hadoop-supported filesystems), or an HTTP, HTTPS or
     |      FTP URI.
     |      
     |      To access the file in Spark jobs, use
     |      L{SparkFiles.get(fileName)<pyspark.files.SparkFiles.get>} with the
     |      filename to find its download location.
     |      
     |      A directory can be given if the recursive option is set to True.
     |      Currently directories are only supported for Hadoop-supported filesystems.
     |      
     |      >>> from pyspark import SparkFiles
     |      >>> path = os.path.join(tempdir, "test.txt")
     |      >>> with open(path, "w") as testFile:
     |      ...    _ = testFile.write("100")
     |      >>> sc.addFile(path)
     |      >>> def func(iterator):
     |      ...    with open(SparkFiles.get("test.txt")) as testFile:
     |      ...        fileVal = int(testFile.readline())
     |      ...        return [x * fileVal for x in iterator]
     |      >>> sc.parallelize([1, 2, 3, 4]).mapPartitions(func).collect()
     |      [100, 200, 300, 400]
     |  
     |  addPyFile(self, path)
     |      Add a .py or .zip dependency for all tasks to be executed on this
     |      SparkContext in the future.  The C{path} passed can be either a local
     |      file, a file in HDFS (or other Hadoop-supported filesystems), or an
     |      HTTP, HTTPS or FTP URI.
     |  
     |  binaryFiles(self, path, minPartitions=None)
     |      .. note:: Experimental
     |      
     |      Read a directory of binary files from HDFS, a local file system
     |      (available on all nodes), or any Hadoop-supported file system URI
     |      as a byte array. Each file is read as a single record and returned
     |      in a key-value pair, where the key is the path of each file, the
     |      value is the content of each file.
     |      
     |      .. note:: Small files are preferred, large file is also allowable, but
     |          may cause bad performance.
     |  
     |  binaryRecords(self, path, recordLength)
     |      .. note:: Experimental
     |      
     |      Load data from a flat binary file, assuming each record is a set of numbers
     |      with the specified numerical format (see ByteBuffer), and the number of
     |      bytes per record is constant.
     |      
     |      :param path: Directory to the input data files
     |      :param recordLength: The length at which to split the records
     |  
     |  broadcast(self, value)
     |      Broadcast a read-only variable to the cluster, returning a
     |      L{Broadcast<pyspark.broadcast.Broadcast>}
     |      object for reading it in distributed functions. The variable will
     |      be sent to each cluster only once.
     |  
     |  cancelAllJobs(self)
     |      Cancel all jobs that have been scheduled or are running.
     |  
     |  cancelJobGroup(self, groupId)
     |      Cancel active jobs for the specified group. See L{SparkContext.setJobGroup}
     |      for more information.
     |  
     |  dump_profiles(self, path)
     |      Dump the profile stats into directory `path`
     |  
     |  emptyRDD(self)
     |      Create an RDD that has no partitions or elements.
     |  
     |  getConf(self)
     |  
     |  getLocalProperty(self, key)
     |      Get a local property set in this thread, or null if it is missing. See
     |      L{setLocalProperty}
     |  
     |  hadoopFile(self, path, inputFormatClass, keyClass, valueClass, keyConverter=None, valueConverter=None, conf=None, batchSize=0)
     |      Read an 'old' Hadoop InputFormat with arbitrary key and value class from HDFS,
     |      a local file system (available on all nodes), or any Hadoop-supported file system URI.
     |      The mechanism is the same as for sc.sequenceFile.
     |      
     |      A Hadoop configuration can be passed in as a Python dict. This will be converted into a
     |      Configuration in Java.
     |      
     |      :param path: path to Hadoop file
     |      :param inputFormatClass: fully qualified classname of Hadoop InputFormat
     |             (e.g. "org.apache.hadoop.mapred.TextInputFormat")
     |      :param keyClass: fully qualified classname of key Writable class
     |             (e.g. "org.apache.hadoop.io.Text")
     |      :param valueClass: fully qualified classname of value Writable class
     |             (e.g. "org.apache.hadoop.io.LongWritable")
     |      :param keyConverter: (None by default)
     |      :param valueConverter: (None by default)
     |      :param conf: Hadoop configuration, passed in as a dict
     |             (None by default)
     |      :param batchSize: The number of Python objects represented as a single
     |             Java object. (default 0, choose batchSize automatically)
     |  
     |  hadoopRDD(self, inputFormatClass, keyClass, valueClass, keyConverter=None, valueConverter=None, conf=None, batchSize=0)
     |      Read an 'old' Hadoop InputFormat with arbitrary key and value class, from an arbitrary
     |      Hadoop configuration, which is passed in as a Python dict.
     |      This will be converted into a Configuration in Java.
     |      The mechanism is the same as for sc.sequenceFile.
     |      
     |      :param inputFormatClass: fully qualified classname of Hadoop InputFormat
     |             (e.g. "org.apache.hadoop.mapred.TextInputFormat")
     |      :param keyClass: fully qualified classname of key Writable class
     |             (e.g. "org.apache.hadoop.io.Text")
     |      :param valueClass: fully qualified classname of value Writable class
     |             (e.g. "org.apache.hadoop.io.LongWritable")
     |      :param keyConverter: (None by default)
     |      :param valueConverter: (None by default)
     |      :param conf: Hadoop configuration, passed in as a dict
     |             (None by default)
     |      :param batchSize: The number of Python objects represented as a single
     |             Java object. (default 0, choose batchSize automatically)
     |  
     |  newAPIHadoopFile(self, path, inputFormatClass, keyClass, valueClass, keyConverter=None, valueConverter=None, conf=None, batchSize=0)
     |      Read a 'new API' Hadoop InputFormat with arbitrary key and value class from HDFS,
     |      a local file system (available on all nodes), or any Hadoop-supported file system URI.
     |      The mechanism is the same as for sc.sequenceFile.
     |      
     |      A Hadoop configuration can be passed in as a Python dict. This will be converted into a
     |      Configuration in Java
     |      
     |      :param path: path to Hadoop file
     |      :param inputFormatClass: fully qualified classname of Hadoop InputFormat
     |             (e.g. "org.apache.hadoop.mapreduce.lib.input.TextInputFormat")
     |      :param keyClass: fully qualified classname of key Writable class
     |             (e.g. "org.apache.hadoop.io.Text")
     |      :param valueClass: fully qualified classname of value Writable class
     |             (e.g. "org.apache.hadoop.io.LongWritable")
     |      :param keyConverter: (None by default)
     |      :param valueConverter: (None by default)
     |      :param conf: Hadoop configuration, passed in as a dict
     |             (None by default)
     |      :param batchSize: The number of Python objects represented as a single
     |             Java object. (default 0, choose batchSize automatically)
     |  
     |  newAPIHadoopRDD(self, inputFormatClass, keyClass, valueClass, keyConverter=None, valueConverter=None, conf=None, batchSize=0)
     |      Read a 'new API' Hadoop InputFormat with arbitrary key and value class, from an arbitrary
     |      Hadoop configuration, which is passed in as a Python dict.
     |      This will be converted into a Configuration in Java.
     |      The mechanism is the same as for sc.sequenceFile.
     |      
     |      :param inputFormatClass: fully qualified classname of Hadoop InputFormat
     |             (e.g. "org.apache.hadoop.mapreduce.lib.input.TextInputFormat")
     |      :param keyClass: fully qualified classname of key Writable class
     |             (e.g. "org.apache.hadoop.io.Text")
     |      :param valueClass: fully qualified classname of value Writable class
     |             (e.g. "org.apache.hadoop.io.LongWritable")
     |      :param keyConverter: (None by default)
     |      :param valueConverter: (None by default)
     |      :param conf: Hadoop configuration, passed in as a dict
     |             (None by default)
     |      :param batchSize: The number of Python objects represented as a single
     |             Java object. (default 0, choose batchSize automatically)
     |  
     |  parallelize(self, c, numSlices=None)
     |      Distribute a local Python collection to form an RDD. Using xrange
     |      is recommended if the input represents a range for performance.
     |      
     |      >>> sc.parallelize([0, 2, 3, 4, 6], 5).glom().collect()
     |      [[0], [2], [3], [4], [6]]
     |      >>> sc.parallelize(xrange(0, 6, 2), 5).glom().collect()
     |      [[], [0], [], [2], [4]]
     |  
     |  pickleFile(self, name, minPartitions=None)
     |      Load an RDD previously saved using L{RDD.saveAsPickleFile} method.
     |      
     |      >>> tmpFile = NamedTemporaryFile(delete=True)
     |      >>> tmpFile.close()
     |      >>> sc.parallelize(range(10)).saveAsPickleFile(tmpFile.name, 5)
     |      >>> sorted(sc.pickleFile(tmpFile.name, 3).collect())
     |      [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
     |  
     |  range(self, start, end=None, step=1, numSlices=None)
     |      Create a new RDD of int containing elements from `start` to `end`
     |      (exclusive), increased by `step` every element. Can be called the same
     |      way as python's built-in range() function. If called with a single argument,
     |      the argument is interpreted as `end`, and `start` is set to 0.
     |      
     |      :param start: the start value
     |      :param end: the end value (exclusive)
     |      :param step: the incremental step (default: 1)
     |      :param numSlices: the number of partitions of the new RDD
     |      :return: An RDD of int
     |      
     |      >>> sc.range(5).collect()
     |      [0, 1, 2, 3, 4]
     |      >>> sc.range(2, 4).collect()
     |      [2, 3]
     |      >>> sc.range(1, 7, 2).collect()
     |      [1, 3, 5]
     |  
     |  runJob(self, rdd, partitionFunc, partitions=None, allowLocal=False)
     |      Executes the given partitionFunc on the specified set of partitions,
     |      returning the result as an array of elements.
     |      
     |      If 'partitions' is not specified, this will run over all partitions.
     |      
     |      >>> myRDD = sc.parallelize(range(6), 3)
     |      >>> sc.runJob(myRDD, lambda part: [x * x for x in part])
     |      [0, 1, 4, 9, 16, 25]
     |      
     |      >>> myRDD = sc.parallelize(range(6), 3)
     |      >>> sc.runJob(myRDD, lambda part: [x * x for x in part], [0, 2], True)
     |      [0, 1, 16, 25]
     |  
     |  sequenceFile(self, path, keyClass=None, valueClass=None, keyConverter=None, valueConverter=None, minSplits=None, batchSize=0)
     |      Read a Hadoop SequenceFile with arbitrary key and value Writable class from HDFS,
     |      a local file system (available on all nodes), or any Hadoop-supported file system URI.
     |      The mechanism is as follows:
     |      
     |          1. A Java RDD is created from the SequenceFile or other InputFormat, and the key
     |             and value Writable classes
     |          2. Serialization is attempted via Pyrolite pickling
     |          3. If this fails, the fallback is to call 'toString' on each key and value
     |          4. C{PickleSerializer} is used to deserialize pickled objects on the Python side
     |      
     |      :param path: path to sequncefile
     |      :param keyClass: fully qualified classname of key Writable class
     |             (e.g. "org.apache.hadoop.io.Text")
     |      :param valueClass: fully qualified classname of value Writable class
     |             (e.g. "org.apache.hadoop.io.LongWritable")
     |      :param keyConverter:
     |      :param valueConverter:
     |      :param minSplits: minimum splits in dataset
     |             (default min(2, sc.defaultParallelism))
     |      :param batchSize: The number of Python objects represented as a single
     |             Java object. (default 0, choose batchSize automatically)
     |  
     |  setCheckpointDir(self, dirName)
     |      Set the directory under which RDDs are going to be checkpointed. The
     |      directory must be a HDFS path if running on a cluster.
     |  
     |  setJobDescription(self, value)
     |      Set a human readable description of the current job.
     |  
     |  setJobGroup(self, groupId, description, interruptOnCancel=False)
     |      Assigns a group ID to all the jobs started by this thread until the group ID is set to a
     |      different value or cleared.
     |      
     |      Often, a unit of execution in an application consists of multiple Spark actions or jobs.
     |      Application programmers can use this method to group all those jobs together and give a
     |      group description. Once set, the Spark web UI will associate such jobs with this group.
     |      
     |      The application can use L{SparkContext.cancelJobGroup} to cancel all
     |      running jobs in this group.
     |      
     |      >>> import threading
     |      >>> from time import sleep
     |      >>> result = "Not Set"
     |      >>> lock = threading.Lock()
     |      >>> def map_func(x):
     |      ...     sleep(100)
     |      ...     raise Exception("Task should have been cancelled")
     |      >>> def start_job(x):
     |      ...     global result
     |      ...     try:
     |      ...         sc.setJobGroup("job_to_cancel", "some description")
     |      ...         result = sc.parallelize(range(x)).map(map_func).collect()
     |      ...     except Exception as e:
     |      ...         result = "Cancelled"
     |      ...     lock.release()
     |      >>> def stop_job():
     |      ...     sleep(5)
     |      ...     sc.cancelJobGroup("job_to_cancel")
     |      >>> supress = lock.acquire()
     |      >>> supress = threading.Thread(target=start_job, args=(10,)).start()
     |      >>> supress = threading.Thread(target=stop_job).start()
     |      >>> supress = lock.acquire()
     |      >>> print(result)
     |      Cancelled
     |      
     |      If interruptOnCancel is set to true for the job group, then job cancellation will result
     |      in Thread.interrupt() being called on the job's executor threads. This is useful to help
     |      ensure that the tasks are actually stopped in a timely manner, but is off by default due
     |      to HDFS-1208, where HDFS may respond to Thread.interrupt() by marking nodes as dead.
     |  
     |  setLocalProperty(self, key, value)
     |      Set a local property that affects jobs submitted from this thread, such as the
     |      Spark fair scheduler pool.
     |  
     |  setLogLevel(self, logLevel)
     |      Control our logLevel. This overrides any user-defined log settings.
     |      Valid log levels include: ALL, DEBUG, ERROR, FATAL, INFO, OFF, TRACE, WARN
     |  
     |  show_profiles(self)
     |      Print the profile stats to stdout
     |  
     |  sparkUser(self)
     |      Get SPARK_USER for user who is running SparkContext.
     |  
     |  statusTracker(self)
     |      Return :class:`StatusTracker` object
     |  
     |  stop(self)
     |      Shut down the SparkContext.
     |  
     |  textFile(self, name, minPartitions=None, use_unicode=True)
     |      Read a text file from HDFS, a local file system (available on all
     |      nodes), or any Hadoop-supported file system URI, and return it as an
     |      RDD of Strings.
     |      
     |      If use_unicode is False, the strings will be kept as `str` (encoding
     |      as `utf-8`), which is faster and smaller than unicode. (Added in
     |      Spark 1.2)
     |      
     |      >>> path = os.path.join(tempdir, "sample-text.txt")
     |      >>> with open(path, "w") as testFile:
     |      ...    _ = testFile.write("Hello world!")
     |      >>> textFile = sc.textFile(path)
     |      >>> textFile.collect()
     |      ['Hello world!']
     |  
     |  union(self, rdds)
     |      Build the union of a list of RDDs.
     |      
     |      This supports unions() of RDDs with different serialized formats,
     |      although this forces them to be reserialized using the default
     |      serializer:
     |      
     |      >>> path = os.path.join(tempdir, "union-text.txt")
     |      >>> with open(path, "w") as testFile:
     |      ...    _ = testFile.write("Hello")
     |      >>> textFile = sc.textFile(path)
     |      >>> textFile.collect()
     |      ['Hello']
     |      >>> parallelized = sc.parallelize(["World!"])
     |      >>> sorted(sc.union([textFile, parallelized]).collect())
     |      ['Hello', 'World!']
     |  
     |  wholeTextFiles(self, path, minPartitions=None, use_unicode=True)
     |      Read a directory of text files from HDFS, a local file system
     |      (available on all nodes), or any  Hadoop-supported file system
     |      URI. Each file is read as a single record and returned in a
     |      key-value pair, where the key is the path of each file, the
     |      value is the content of each file.
     |      
     |      If use_unicode is False, the strings will be kept as `str` (encoding
     |      as `utf-8`), which is faster and smaller than unicode. (Added in
     |      Spark 1.2)
     |      
     |      For example, if you have the following files::
     |      
     |        hdfs://a-hdfs-path/part-00000
     |        hdfs://a-hdfs-path/part-00001
     |        ...
     |        hdfs://a-hdfs-path/part-nnnnn
     |      
     |      Do C{rdd = sparkContext.wholeTextFiles("hdfs://a-hdfs-path")},
     |      then C{rdd} contains::
     |      
     |        (a-hdfs-path/part-00000, its content)
     |        (a-hdfs-path/part-00001, its content)
     |        ...
     |        (a-hdfs-path/part-nnnnn, its content)
     |      
     |      .. note:: Small files are preferred, as each file will be loaded
     |          fully in memory.
     |      
     |      >>> dirPath = os.path.join(tempdir, "files")
     |      >>> os.mkdir(dirPath)
     |      >>> with open(os.path.join(dirPath, "1.txt"), "w") as file1:
     |      ...    _ = file1.write("1")
     |      >>> with open(os.path.join(dirPath, "2.txt"), "w") as file2:
     |      ...    _ = file2.write("2")
     |      >>> textFiles = sc.wholeTextFiles(dirPath)
     |      >>> sorted(textFiles.collect())
     |      [('.../1.txt', '1'), ('.../2.txt', '2')]
     |  
     |  ----------------------------------------------------------------------
     |  Class methods defined here:
     |  
     |  getOrCreate(conf=None) from builtins.type
     |      Get or instantiate a SparkContext and register it as a singleton object.
     |      
     |      :param conf: SparkConf (optional)
     |  
     |  setSystemProperty(key, value) from builtins.type
     |      Set a Java system property, such as spark.executor.memory. This must
     |      must be invoked before instantiating SparkContext.
     |  
     |  ----------------------------------------------------------------------
     |  Data descriptors defined here:
     |  
     |  __dict__
     |      dictionary for instance variables (if defined)
     |  
     |  __weakref__
     |      list of weak references to the object (if defined)
     |  
     |  applicationId
     |      A unique identifier for the Spark application.
     |      Its format depends on the scheduler implementation.
     |      
     |      * in case of local spark app something like 'local-1433865536131'
     |      * in case of YARN something like 'application_1433865536131_34483'
     |      
     |      >>> sc.applicationId  # doctest: +ELLIPSIS
     |      'local-...'
     |  
     |  defaultMinPartitions
     |      Default min number of partitions for Hadoop RDDs when not given by user
     |  
     |  defaultParallelism
     |      Default level of parallelism to use when not given by user (e.g. for
     |      reduce tasks)
     |  
     |  startTime
     |      Return the epoch time when the Spark Context was started.
     |  
     |  uiWebUrl
     |      Return the URL of the SparkUI instance started by this SparkContext
     |  
     |  version
     |      The version of Spark on which this application is running.
     |  
     |  ----------------------------------------------------------------------
     |  Data and other attributes defined here:
     |  
     |  PACKAGE_EXTENSIONS = ('.zip', '.egg', '.jar')
    


You should also have a look at [Spark's SparkContext Documentation Page](https://spark.apache.org/docs/0.6.0/api/core/spark/SparkContext.html) to explore these in further detail.

Let's try to check a few spark context attributes including `SparkContext.version` and `SparkContext.defaultParalellism` to check the current version of Apache Spark and number of cores being used for parallel processing. 



```python
# Check the number of cores being used
print ("Default number of cores being used:", sc.defaultParallelism) 

# Check for the current version of Spark
print ("Current version of Spark:", sc.version)

# Default number of cores being used: 2
# Current version of Spark: 2.3.1
```

    Default number of cores being used: 4
    Current version of Spark: 2.3.1


Let's also check the name of current application by using `SparkContext.appName` attribute. 


```python
# Check the name of application currently running in spark environment
sc.appName

# 'pyspark-shell'
```




    'pyspark-shell'



We can access complete configuration settings (including all defaults) for the current spark context using `_conf.getAll()` method. 


```python
 sc._conf.getAll()
```




    [('spark.driver.port', '52734'),
     ('spark.rdd.compress', 'True'),
     ('spark.serializer.objectStreamReset', '100'),
     ('spark.master', 'local[*]'),
     ('spark.executor.id', 'driver'),
     ('spark.submit.deployMode', 'client'),
     ('spark.driver.host', '10.248.4.30'),
     ('spark.app.id', 'local-1558359977714'),
     ('spark.ui.showConsoleProgress', 'true'),
     ('spark.app.name', 'pyspark-shell')]



A Spark Context can be shut down using `SparkContext.stop()` method. Let's use this method to shut down the current spark context. 


```python
#Shut down SparkContext
sc.stop()
```

Once shut down, you can no longer access spark functionality before starting a new SparkContext. 

## Additional Resources

- [Apache Spark Context](https://data-flair.training/blogs/learn-apache-spark-sparkcontext/)

## Summary

In this short lab, we saw how SparkContext is used as an entry point to Spark applications. We learned how to start a SparkContext, how to list and use some of the attributes and methods in SparkContext and how to shut it down. Students are encouraged to explore other attributes and methods offered by the sc object. Some of these, namely creating and transforming datasets as RDDs will be explored in later labs. 
