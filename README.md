# Understanding SparkContext - Codealong

## Introduction

SparkContext is the entry point for using the Unstructured API of Spark. In this lesson we'll go over how SparkContext works in PySpark, create a SparkContext called `sc`, and explore `sc`'s properties.

## Objectives

You will be able to:

- Define a SparkContext and why it is important to a Spark application
- Create a SparkContext with PySpark
- List the major properties and methods of SparkContext

## The Purpose of the SparkContext

### Spark Application Architecture

Recall this figure from the [Cluster Mode Overview](https://spark.apache.org/docs/latest/cluster-overview.html):

![Cluster Manager Diagram](https://spark.apache.org/docs/latest/img/cluster-overview.png)

When you are writing Spark code, your code is the "Driver Program" pictured here. Your code needs to instantiate a SparkContext if we want to be able to use the Spark Unstructured API.

### PySpark Stack

Since we are not writing Spark code in Scala, but instead are writing PySpark code in Python, there is some additional architecture to be aware of.

Specifically, all Spark code needs to be able to run on the JVM (Java Virtual Machine), because **PySpark is built on top of Spark's Java API**. PySpark uses the [Py4J](https://www.py4j.org/) library under the hood to accomplish this. 

This is relevant to your development process because:

- Sometimes you will see error messages or warnings related to Java code.
- Many of the function and variable names follow Java naming conventions rather than Python. In particular, you will see many examples of `camelCase` names in places where you would expect `snake_case` Python names.

The architecture including Py4J is something like this (from the [PySpark Internals wiki](https://cwiki.apache.org/confluence/display/SPARK/PySpark+Internals)):

![Data Flow architecture diagram with Py4J](https://i.imgur.com/YlI8AqEl.png)

The driver program launches parallel operations on executor Java Virtual Machines (JVMs). This can occur either locally on a single machine using multiple cores to create parallel processing or across a cluster of computers that are controlled by a master computer. When running locally, "PySparkShell" is the application name. The driver program contains the key instructions for the program and it determines how to best distribute datasets across the cluster and apply operations to those datasets.

The key takeaways for SparkContext are listed below:

- SparkContext is a client of Spark’s execution environment and it acts as the master of the Spark application 
- SparkContext sets up internal services and establishes a connection to a Spark execution environment  
- The driver is the program that creates the SparkContext, connecting to a given Spark Master  

After creation, SparkContext asks the master for some cores to use to do work. The master sets these cores aside and they are used to complete whatever operation they are assigned to do. You can visualize the setup in the figure below:

<img src ="https://github.com/learn-co-curriculum/dsc-sparkcontext/raw/master/images/spark_master_workers.png" width="280">

This image depicts the worker nodes at work. Every worker has 4 cores to work with, and the master allocates tasks to run on certain cores within each worker node.

## Creating a Local SparkContext

While the SparkContext conceptual framework is fairly complex, creating a SparkContext with PySpark is fairly simple. All we need to do is import the relevant class and instantiate it.

### Importing the SparkContext Class

As we can see from the [documentation](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.SparkContext.html), there is an example import statement:

```python
# Import the SparkContext class from the pyspark.context submodule
from pyspark.context import SparkContext
```

Type this code in the cell below and execute the cell.


```python
# Import the SparkContext class from the pyspark.context submodule

```

### Instantiating `sc`

#### Naming Convention

The conventional name for the SparkContext object is `sc`. In fact, in some (Py)Spark environments, there will already be an object in memory called `sc` as soon as the environment is loaded. Therefore unless you have a very specific reason for changing the name, you are strongly encouraged to use the name `sc` to represent the SparkContext.

#### Parameters

In theory you could simply call `SparkContext()` to create your SparkContext, but in practice you should specify values for two parameters: `master` and `appName`.

The `master` parameter is the cluster URL to connect to. If you were using a full-fledged Cluster Manager this URL might be something like `"mesos://host:5050"` but we are just running a local cluster. Therefore we'll specify a `master` value of `"local[*]"`. The `*` means that we are telling Spark to run on all available cores of our machine.

The `appName` parameter is just a label for the application. It's similar to a Python variable name -- just there to help you understand what the code is doing. You can put any string value you like.

#### Codealong

In the cell below, instantiate a variable `sc` using the `SparkContext` class, a `master` of `"local[*]"`, and an `appName` of `"sc practice"`.

```python
# Instantiate sc
sc = SparkContext("local[*]", "sc practice")
```


```python
# Instantiate sc

```

You may see some Java warnings appear below this line of code (or other lines of code). In general you can safely ignore these warnings, although they may provide relevant information for debugging.

#### One SparkContext at a Time

Note that you can only have one SparkContext at a time. If you try to make another one without stopping the first one, you will get an error:


```python
# Bad idea - creating a second SparkContext
try:
    another_sc = SparkContext("local[*]", "double trouble")
except Exception as e:
    print(type(e))
    print(e)
```

## Properties and Methods of SparkContext

Now we have a SparkContext object! Let's investigate it like any other Python object.

### Type

What is the type of our SparkContext?

---

<details>
    <summary>
        <b><u>Click to Reveal Code</u></b>
    </summary>

```python
# Type of sc
type(sc)
```
    
</details>


```python
# Type of sc

```

<details>
    <summary>
        <b><u>Click to Reveal Expected Output</u></b>
    </summary>

```
pyspark.context.SparkContext
```
</details>

### All Attributes

Use Python's `dir` built-in function ([documentation here](https://docs.python.org/3/library/functions.html#dir)) to get a list of all attributes (including methods) accessible through the `sc` object.

---

<details>
    <summary>
        <b><u>Click to Reveal Code</u></b>
    </summary>

```python
# Get a list of all attributes
dir(sc)
```
    
</details>


```python
# Get a list of all attributes

```

<details>
    <summary>
        <b><u>Click to Reveal Expected Output</u></b>
    </summary>

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
 '_assert_on_driver',
 '_batchSize',
 '_callsite',
 '_checkpointFile',
 '_conf',
 '_dictToJavaMap',
 '_do_init',
 '_encryption_enabled',
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
 'getCheckpointDir',
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
 'resources',
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
```
    
</details>

### Python Help

We have a list of attributes, but no explanation of how to use them. Use Python's `help` function ([documentation here](https://docs.python.org/3/library/functions.html#help)) to get an easier-to-read list of all the attributes, including examples, that the `sc` object has.

---

<details>
    <summary>
        <b><u>Click to Reveal Code</u></b>
    </summary>

```python
# Use Python's help function to get information on attributes and methods for sc object
help(sc)
```
    
</details>


```python
# Use Python's help function to get information on attributes and methods for sc object

```

<details>
    <summary>
        <b><u>Click to Reveal Expected Output</u></b>
    </summary>

```
Help on SparkContext in module pyspark.context object:

class SparkContext(builtins.object)
 |  SparkContext(master=None, appName=None, sparkHome=None, pyFiles=None, environment=None, batchSize=0, serializer=PickleSerializer(), conf=None, gateway=None, jsc=None, profiler_cls=<class 'pyspark.profiler.BasicProfiler'>)
 |  
 |  Main entry point for Spark functionality. A SparkContext represents the
 |  connection to a Spark cluster, and can be used to create :class:`RDD` and
 |  broadcast variables on that cluster.
 |  
 |  When you create a new SparkContext, at least the master and app name should
 |  be set, either through the named parameters here or through `conf`.
 |  
 |  Parameters
 |  ----------
 |  master : str, optional
 |      Cluster URL to connect to (e.g. mesos://host:port, spark://host:port, local[4]).
 |  appName : str, optional
 |      A name for your job, to display on the cluster web UI.
 |  sparkHome : str, optional
 |      Location where Spark is installed on cluster nodes.
 |  pyFiles : list, optional
 |      Collection of .zip or .py files to send to the cluster
 |      and add to PYTHONPATH.  These can be paths on the local file
 |      system or HDFS, HTTP, HTTPS, or FTP URLs.
 |  environment : dict, optional
 |      A dictionary of environment variables to set on
 |      worker nodes.
 |  batchSize : int, optional
 |      The number of Python objects represented as a single
 |      Java object. Set 1 to disable batching, 0 to automatically choose
 |      the batch size based on object sizes, or -1 to use an unlimited
 |      batch size
 |  serializer : :class:`pyspark.serializers.Serializer`, optional
 |      The serializer for RDDs.
 |  conf : :py:class:`pyspark.SparkConf`, optional
 |      An object setting Spark properties.
 |  gateway : :py:class:`py4j.java_gateway.JavaGateway`,  optional
 |      Use an existing gateway and JVM, otherwise a new JVM
 |      will be instantiated. This is only used internally.
 |  jsc : :py:class:`py4j.java_gateway.JavaObject`, optional
 |      The JavaSparkContext instance. This is only used internally.
 |  profiler_cls : type, optional
 |      A class of custom Profiler used to do profiling
 |      (default is :class:`pyspark.profiler.BasicProfiler`).
 |  
 |  Notes
 |  -----
 |  Only one :class:`SparkContext` should be active per JVM. You must `stop()`
 |  the active :class:`SparkContext` before creating a new one.
 |  
 |  :class:`SparkContext` instance is not supported to share across multiple
 |  processes out of the box, and PySpark does not guarantee multi-processing execution.
 |  Use threads instead for concurrent processing purpose.
 |  
 |  Examples
 |  --------
 |  >>> from pyspark.context import SparkContext
 |  >>> sc = SparkContext('local', 'test')
 |  >>> sc2 = SparkContext('local', 'test2') # doctest: +IGNORE_EXCEPTION_DETAIL
 |  Traceback (most recent call last):
 |      ...
 |  ValueError: ...
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
 |      Initialize self.  See help(type(self)) for accurate signature.
 |  
 |  __repr__(self)
 |      Return repr(self).
 |  
 |  accumulator(self, value, accum_param=None)
 |      Create an :class:`Accumulator` with the given initial value, using a given
 |      :class:`AccumulatorParam` helper object to define how to add values of the
 |      data type if provided. Default AccumulatorParams are used for integers
 |      and floating-point numbers if you do not provide one. For other types,
 |      a custom AccumulatorParam can be used.
 |  
 |  addFile(self, path, recursive=False)
 |      Add a file to be downloaded with this Spark job on every node.
 |      The `path` passed can be either a local file, a file in HDFS
 |      (or other Hadoop-supported filesystems), or an HTTP, HTTPS or
 |      FTP URI.
 |      
 |      To access the file in Spark jobs, use :meth:`SparkFiles.get` with the
 |      filename to find its download location.
 |      
 |      A directory can be given if the recursive option is set to True.
 |      Currently directories are only supported for Hadoop-supported filesystems.
 |      
 |      Notes
 |      -----
 |      A path can be added only once. Subsequent additions of the same path are ignored.
 |      
 |      Examples
 |      --------
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
 |      SparkContext in the future.  The `path` passed can be either a local
 |      file, a file in HDFS (or other Hadoop-supported filesystems), or an
 |      HTTP, HTTPS or FTP URI.
 |      
 |      Notes
 |      -----
 |      A path can be added only once. Subsequent additions of the same path are ignored.
 |  
 |  binaryFiles(self, path, minPartitions=None)
 |      Read a directory of binary files from HDFS, a local file system
 |      (available on all nodes), or any Hadoop-supported file system URI
 |      as a byte array. Each file is read as a single record and returned
 |      in a key-value pair, where the key is the path of each file, the
 |      value is the content of each file.
 |      
 |      Notes
 |      -----
 |      Small files are preferred, large file is also allowable, but may cause bad performance.
 |  
 |  binaryRecords(self, path, recordLength)
 |      Load data from a flat binary file, assuming each record is a set of numbers
 |      with the specified numerical format (see ByteBuffer), and the number of
 |      bytes per record is constant.
 |      
 |      Parameters
 |      ----------
 |      path : str
 |          Directory to the input data files
 |      recordLength : int
 |          The length at which to split the records
 |  
 |  broadcast(self, value)
 |      Broadcast a read-only variable to the cluster, returning a :class:`Broadcast`
 |      object for reading it in distributed functions. The variable will
 |      be sent to each cluster only once.
 |  
 |  cancelAllJobs(self)
 |      Cancel all jobs that have been scheduled or are running.
 |  
 |  cancelJobGroup(self, groupId)
 |      Cancel active jobs for the specified group. See :meth:`SparkContext.setJobGroup`.
 |      for more information.
 |  
 |  dump_profiles(self, path)
 |      Dump the profile stats into directory `path`
 |  
 |  emptyRDD(self)
 |      Create an RDD that has no partitions or elements.
 |  
 |  getCheckpointDir(self)
 |      Return the directory where RDDs are checkpointed. Returns None if no
 |      checkpoint directory has been set.
 |      
 |      .. versionadded:: 3.1
 |  
 |  getConf(self)
 |  
 |  getLocalProperty(self, key)
 |      Get a local property set in this thread, or null if it is missing. See
 |      :meth:`setLocalProperty`.
 |  
 |  hadoopFile(self, path, inputFormatClass, keyClass, valueClass, keyConverter=None, valueConverter=None, conf=None, batchSize=0)
 |      Read an 'old' Hadoop InputFormat with arbitrary key and value class from HDFS,
 |      a local file system (available on all nodes), or any Hadoop-supported file system URI.
 |      The mechanism is the same as for :py:meth:`SparkContext.sequenceFile`.
 |      
 |      A Hadoop configuration can be passed in as a Python dict. This will be converted into a
 |      Configuration in Java.
 |      
 |      path : str
 |          path to Hadoop file
 |      inputFormatClass : str
 |          fully qualified classname of Hadoop InputFormat
 |          (e.g. "org.apache.hadoop.mapreduce.lib.input.TextInputFormat")
 |      keyClass : str
 |          fully qualified classname of key Writable class (e.g. "org.apache.hadoop.io.Text")
 |      valueClass : str
 |          fully qualified classname of value Writable class
 |          (e.g. "org.apache.hadoop.io.LongWritable")
 |      keyConverter : str, optional
 |          fully qualified name of a function returning key WritableConverter
 |          (None by default)
 |      valueConverter : str, optional
 |          fully qualified name of a function returning value WritableConverter
 |          (None by default)
 |      conf : dict, optional
 |          Hadoop configuration, passed in as a dict (None by default)
 |      batchSize : int, optional
 |          The number of Python objects represented as a single
 |          Java object. (default 0, choose batchSize automatically)
 |  
 |  hadoopRDD(self, inputFormatClass, keyClass, valueClass, keyConverter=None, valueConverter=None, conf=None, batchSize=0)
 |      Read an 'old' Hadoop InputFormat with arbitrary key and value class, from an arbitrary
 |      Hadoop configuration, which is passed in as a Python dict.
 |      This will be converted into a Configuration in Java.
 |      The mechanism is the same as for :py:meth:`SparkContext.sequenceFile`.
 |      
 |      Parameters
 |      ----------
 |      inputFormatClass : str
 |          fully qualified classname of Hadoop InputFormat
 |          (e.g. "org.apache.hadoop.mapreduce.lib.input.TextInputFormat")
 |      keyClass : str
 |          fully qualified classname of key Writable class (e.g. "org.apache.hadoop.io.Text")
 |      valueClass : str
 |          fully qualified classname of value Writable class
 |          (e.g. "org.apache.hadoop.io.LongWritable")
 |      keyConverter : str, optional
 |          fully qualified name of a function returning key WritableConverter
 |          (None by default)
 |      valueConverter : str, optional
 |          fully qualified name of a function returning value WritableConverter
 |          (None by default)
 |      conf : dict, optional
 |          Hadoop configuration, passed in as a dict (None by default)
 |      batchSize : int, optional
 |          The number of Python objects represented as a single
 |          Java object. (default 0, choose batchSize automatically)
 |  
 |  newAPIHadoopFile(self, path, inputFormatClass, keyClass, valueClass, keyConverter=None, valueConverter=None, conf=None, batchSize=0)
 |      Read a 'new API' Hadoop InputFormat with arbitrary key and value class from HDFS,
 |      a local file system (available on all nodes), or any Hadoop-supported file system URI.
 |      The mechanism is the same as for :py:meth:`SparkContext.sequenceFile`.
 |      
 |      A Hadoop configuration can be passed in as a Python dict. This will be converted into a
 |      Configuration in Java
 |      
 |      Parameters
 |      ----------
 |      path : str
 |          path to Hadoop file
 |      inputFormatClass : str
 |          fully qualified classname of Hadoop InputFormat
 |          (e.g. "org.apache.hadoop.mapreduce.lib.input.TextInputFormat")
 |      keyClass : str
 |          fully qualified classname of key Writable class
 |          (e.g. "org.apache.hadoop.io.Text")
 |      valueClass : str
 |          fully qualified classname of value Writable class
 |          (e.g. "org.apache.hadoop.io.LongWritable")
 |      keyConverter : str, optional
 |          fully qualified name of a function returning key WritableConverter
 |          None by default
 |      valueConverter : str, optional
 |          fully qualified name of a function returning value WritableConverter
 |          None by default
 |      conf : dict, optional
 |          Hadoop configuration, passed in as a dict
 |          None by default
 |      batchSize : int, optional
 |          The number of Python objects represented as a single
 |          Java object. (default 0, choose batchSize automatically)
 |  
 |  newAPIHadoopRDD(self, inputFormatClass, keyClass, valueClass, keyConverter=None, valueConverter=None, conf=None, batchSize=0)
 |      Read a 'new API' Hadoop InputFormat with arbitrary key and value class, from an arbitrary
 |      Hadoop configuration, which is passed in as a Python dict.
 |      This will be converted into a Configuration in Java.
 |      The mechanism is the same as for :py:meth:`SparkContext.sequenceFile`.
 |      
 |      Parameters
 |      ----------
 |      inputFormatClass : str
 |          fully qualified classname of Hadoop InputFormat
 |          (e.g. "org.apache.hadoop.mapreduce.lib.input.TextInputFormat")
 |      keyClass : str
 |          fully qualified classname of key Writable class (e.g. "org.apache.hadoop.io.Text")
 |      valueClass : str
 |          fully qualified classname of value Writable class
 |          (e.g. "org.apache.hadoop.io.LongWritable")
 |      keyConverter : str, optional
 |          fully qualified name of a function returning key WritableConverter
 |          (None by default)
 |      valueConverter : str, optional
 |          fully qualified name of a function returning value WritableConverter
 |          (None by default)
 |      conf : dict, optional
 |          Hadoop configuration, passed in as a dict (None by default)
 |      batchSize : int, optional
 |          The number of Python objects represented as a single
 |          Java object. (default 0, choose batchSize automatically)
 |  
 |  parallelize(self, c, numSlices=None)
 |      Distribute a local Python collection to form an RDD. Using range
 |      is recommended if the input represents a range for performance.
 |      
 |      Examples
 |      --------
 |      >>> sc.parallelize([0, 2, 3, 4, 6], 5).glom().collect()
 |      [[0], [2], [3], [4], [6]]
 |      >>> sc.parallelize(range(0, 6, 2), 5).glom().collect()
 |      [[], [0], [], [2], [4]]
 |  
 |  pickleFile(self, name, minPartitions=None)
 |      Load an RDD previously saved using :meth:`RDD.saveAsPickleFile` method.
 |      
 |      Examples
 |      --------
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
 |      Parameters
 |      ----------
 |      start : int
 |          the start value
 |      end : int, optional
 |          the end value (exclusive)
 |      step : int, optional
 |          the incremental step (default: 1)
 |      numSlices : int, optional
 |          the number of partitions of the new RDD
 |      
 |      Returns
 |      -------
 |      :py:class:`pyspark.RDD`
 |          An RDD of int
 |      
 |      Examples
 |      --------
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
 |      Examples
 |      --------
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
 |          4. :class:`PickleSerializer` is used to deserialize pickled objects on the Python side
 |      
 |      Parameters
 |      ----------
 |      path : str
 |          path to sequencefile
 |      keyClass: str, optional
 |          fully qualified classname of key Writable class (e.g. "org.apache.hadoop.io.Text")
 |      valueClass : str, optional
 |          fully qualified classname of value Writable class
 |          (e.g. "org.apache.hadoop.io.LongWritable")
 |      keyConverter : str, optional
 |          fully qualified name of a function returning key WritableConverter
 |      valueConverter : str, optional
 |          fully qualifiedname of a function returning value WritableConverter
 |      minSplits : int, optional
 |          minimum splits in dataset (default min(2, sc.defaultParallelism))
 |      batchSize : int, optional
 |          The number of Python objects represented as a single
 |          Java object. (default 0, choose batchSize automatically)
 |  
 |  setCheckpointDir(self, dirName)
 |      Set the directory under which RDDs are going to be checkpointed. The
 |      directory must be an HDFS path if running on a cluster.
 |  
 |  setJobDescription(self, value)
 |      Set a human readable description of the current job.
 |      
 |      Notes
 |      -----
 |      If you run jobs in parallel, use :class:`pyspark.InheritableThread` for thread
 |      local inheritance, and preventing resource leak.
 |  
 |  setJobGroup(self, groupId, description, interruptOnCancel=False)
 |      Assigns a group ID to all the jobs started by this thread until the group ID is set to a
 |      different value or cleared.
 |      
 |      Often, a unit of execution in an application consists of multiple Spark actions or jobs.
 |      Application programmers can use this method to group all those jobs together and give a
 |      group description. Once set, the Spark web UI will associate such jobs with this group.
 |      
 |      The application can use :meth:`SparkContext.cancelJobGroup` to cancel all
 |      running jobs in this group.
 |      
 |      Notes
 |      -----
 |      If interruptOnCancel is set to true for the job group, then job cancellation will result
 |      in Thread.interrupt() being called on the job's executor threads. This is useful to help
 |      ensure that the tasks are actually stopped in a timely manner, but is off by default due
 |      to HDFS-1208, where HDFS may respond to Thread.interrupt() by marking nodes as dead.
 |      
 |      If you run jobs in parallel, use :class:`pyspark.InheritableThread` for thread
 |      local inheritance, and preventing resource leak.
 |      
 |      Examples
 |      --------
 |      >>> import threading
 |      >>> from time import sleep
 |      >>> from pyspark import InheritableThread
 |      >>> result = "Not Set"
 |      >>> lock = threading.Lock()
 |      >>> def map_func(x):
 |      ...     sleep(100)
 |      ...     raise RuntimeError("Task should have been cancelled")
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
 |      >>> suppress = lock.acquire()
 |      >>> suppress = InheritableThread(target=start_job, args=(10,)).start()
 |      >>> suppress = InheritableThread(target=stop_job).start()
 |      >>> suppress = lock.acquire()
 |      >>> print(result)
 |      Cancelled
 |  
 |  setLocalProperty(self, key, value)
 |      Set a local property that affects jobs submitted from this thread, such as the
 |      Spark fair scheduler pool.
 |      
 |      Notes
 |      -----
 |      If you run jobs in parallel, use :class:`pyspark.InheritableThread` for thread
 |      local inheritance, and preventing resource leak.
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
 |      The text files must be encoded as UTF-8.
 |      
 |      If use_unicode is False, the strings will be kept as `str` (encoding
 |      as `utf-8`), which is faster and smaller than unicode. (Added in
 |      Spark 1.2)
 |      
 |      Examples
 |      --------
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
 |      Examples
 |      --------
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
 |      The text files must be encoded as UTF-8.
 |      
 |      If `use_unicode` is False, the strings will be kept as `str` (encoding
 |      as `utf-8`), which is faster and smaller than unicode. (Added in
 |      Spark 1.2)
 |      
 |      For example, if you have the following files:
 |      
 |      .. code-block:: text
 |      
 |          hdfs://a-hdfs-path/part-00000
 |          hdfs://a-hdfs-path/part-00001
 |          ...
 |          hdfs://a-hdfs-path/part-nnnnn
 |      
 |      Do ``rdd = sparkContext.wholeTextFiles("hdfs://a-hdfs-path")``,
 |      then ``rdd`` contains:
 |      
 |      .. code-block:: text
 |      
 |          (a-hdfs-path/part-00000, its content)
 |          (a-hdfs-path/part-00001, its content)
 |          ...
 |          (a-hdfs-path/part-nnnnn, its content)
 |      
 |      Notes
 |      -----
 |      Small files are preferred, as each file will be loaded fully in memory.
 |      
 |      Examples
 |      --------
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
 |      Parameters
 |      ----------
 |      conf : :py:class:`pyspark.SparkConf`, optional
 |  
 |  setSystemProperty(key, value) from builtins.type
 |      Set a Java system property, such as spark.executor.memory. This must
 |      must be invoked before instantiating SparkContext.
 |  
 |  ----------------------------------------------------------------------
 |  Readonly properties defined here:
 |  
 |  applicationId
 |      A unique identifier for the Spark application.
 |      Its format depends on the scheduler implementation.
 |      
 |      * in case of local spark app something like 'local-1433865536131'
 |      * in case of YARN something like 'application_1433865536131_34483'
 |      
 |      Examples
 |      --------
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
 |  resources
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
 |  Data descriptors defined here:
 |  
 |  __dict__
 |      dictionary for instance variables (if defined)
 |  
 |  __weakref__
 |      list of weak references to the object (if defined)
 |  
 |  ----------------------------------------------------------------------
 |  Data and other attributes defined here:
 |  
 |  PACKAGE_EXTENSIONS = ('.zip', '.egg', '.jar')
```

</details>

### Investigating Specific Attributes

Refer to the [PySpark documentation](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.SparkContext.html) to find the appropriate attributes to answer these questions.

### Spark Version

What version of Spark is the application running?

---

<details>
    <summary>
        <b><u>Click to Reveal Code</u></b>
    </summary>

```python
# Spark version
sc.version
```
    
</details>


```python
# Spark version

```

### Start Time

What time was the Spark Context created?

---

<details>
    <summary>
        <b><u>Click to Reveal Code</u></b>
    </summary>

```python
# Start time
sc.startTime
```
    
</details>


```python
# Start time

```

Note that this is the epoch time so it will appear as a large integer.

### All Configuration Settings

We can access the complete configuration settings (including all defaults) for the current SparkContext by chaining together the `getConf()` method and the `getAll()` method.

---

<details>
    <summary>
        <b><u>Click to Reveal Code</u></b>
    </summary>

```python
# All configuration settings
sc.getConf().getAll()
```
    
</details>


```python
# All configuration settings

```

## Shutting Down the SparkContext

When you are finished using a SparkContext, be sure to call the `stop` method. This will allow you to create another SparkContext in the future.

---

<details>
    <summary>
        <b><u>Click to Reveal Code</u></b>
    </summary>

```python
# Shut down SparkContext
sc.stop()
```
    
</details>


```python
# Shut down SparkContext

```

Once shut down, you can no longer access Spark functionality before starting a new SparkContext. 


```python
try:
    sc.version
except Exception as e:
    print(type(e))
    print(e)
```

## Additional Resources

- [Apache Spark Context](https://data-flair.training/blogs/learn-apache-spark-sparkcontext/)

## Summary

In this codealong, we saw how SparkContext is used as an entry point to Spark applications. We learned how to start a SparkContext, how to list and use some of the attributes and methods in SparkContext and how to shut it down. Students are encouraged to explore other attributes and methods offered by the `sc` object. Some of these, namely creating and transforming datasets as RDDs, will be explored in later labs. 
