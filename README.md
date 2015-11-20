# Hadoop Tutorial

## Mappers
A mapper program takes a line of input, processes it in some way, and prints out a line of output that will be sent to the reducer.

The output must be in the format: `key \t value`

Here is a simple Mapper program that helps you count how many times a word appears in a document:
https://github.com/gwDistSys15/dist-sys-exercises/blob/master/lec-10/hadoop/wordcount.py

## Reducers
The reducers will get the output lines produced by the Mappers.  Hadoop guarantees that all outputs with the same key will be sent to the same Reducer.  That reducer will also get all of those Key/Value pairs in order (i.e., all matching keys will be consecutive).

The reducer takes a line of output and can produce any output it likes.  The outputs of the reducers are the final outputs of the program. Typically a reducer reads in many lines of input (e.g., all that have the same key) and then produces a single line of output.

Here is a simple Reducer program that adds up the counts for all matching keys (thus it pairs well with the word count example above):
https://github.com/gwDistSys15/dist-sys-exercises/blob/master/lec-10/hadoop/reduce.py

## Running locally
To run your programs locally, simply use:
```
cat tweets-small | python tweets.py | sort | python reduce.py
```

## Uploading your program to the cloud
To upload your file to the cloud use this command:
```
s3cmd put SOMEFILE s3://gwacm15/YOURNAME/
```
You will need to upload your mapper file before you can run it on the cloud. We will use a cloud provided reduce function.

## Using Elastic Map Reduce

  - Log into https://gwcloud.signin.aws.amazon.com/console with the username and password listed on the slides.
  - Click *EMR* under the Analytics group
  - Click on the GW Dist Sys EMR cluster with the green circle
  - Expand the Steps section at the bottom of the page. This lists all the Hadoop programs scheduled to run on the cluster.
  - Click Add Step and change the Step Type to Streaming Program
  - Use the settings:
      - Mapper: `s3://gwdistsys-twitter/tweet.py`
      - Input: `s3://gwdistsys-twitter/input`
      - Output: `s3://gwacm15/YOURNAME/UNIQUENAME/`
      - Reducer: `aggregate`
      - Arguments: `-cacheFile s3://gwdistsys-twitter/movclass.p#movclass.p`

This will schedule the cluster to run the simple sentiment analysis program on all the tweets.

To see the results of the computation you need to use Amazon's S3 storage service.  Find S3 under the Services menu and navigate to the folder you used as output.  There will be a set of files with names like "part-0001". These are the outputs produced by your reducer nodes.
