# Troubleshooting

## Tests don't pass or have timeouts

If you used `reStart` to start a local job server, be sure it's stopped using `reStop` first.

Also, try adding `-Dakka.test.timefactor=X` to `SBT_OPTS` before launching sbt, where X is a number greater than 1.  This scales out the Akka TestKit timeouts by a factor X.

## Requests are timing out

or you get `akka.pattern.AskTimeoutException`.

send timeout param along with your request (in secs). eg below.

```
http://devsparkcluster.cloudapp.net/jobs?appName=job-server-tests&classPath=spark.jobserver.WordCountExample&sync=true&timeout=20
```

## Job server won't start / cannot bind to 0.0.0.0:8090

Check that another process isn't already using that port.  If it is, you may want to start it on another port:

    reStart --- -Dspark.jobserver.port=2020

## Job Server Doesn't Connect to Spark Cluster

Finally, I got the problem solved. There are two problems in my configuration:

1. the version of spark cluster is 1.1 but the spark version in job server machine is 1.0.2
after upgrading spark to 1.1 in job server machine, jobs can be submitted to spark cluster (can show in spark UI) but cannot be executed.
2. the spark machines need to know the host name of job server machine
after this fixed, I can run jobs submitted from a remote job server successfully.

(Thanks to @pcliu)

## I want to run job-server on Windows

1. Create directory `C:\Hadoop\bin`
2. Download `http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe` and place it in `C:\Hadoop\bin`
3. Set environment variable HADOOP_HOME (either in a .bat script or within OS properties)  `HADOOP_HOME=C:\Hadoop`
4. Start spark-job-server in a shell that has the HADOOP_HOME environment set.
5. Submit the WordCountExample Job.

(Thanks to Javier Delgadillo)

## Akka Deadletters / Workers disconnect from Job Server

Most likely a networking issue. Try using IP addresses instead of DNS.  (happens in AWS)
