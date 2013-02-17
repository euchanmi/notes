# JVM Options Complete Reference:

http://www.pingtimeout.fr/2012/05/jvm-options-complete-reference.html

## To monitor JVM:

   * jconsole

## To get the stack trace of java process:

   * jstack
   * prints Java stack traces of Java threads for a given Java process or core file or a remote debug server.

## To get the dump out of JVM:

   * jmap -dump:file=name pid
   * Then, load the dump file using either VisualVM or jhat

## To check the JVM heap memory usage:

   * jmap pid or jmap -heap pid
   * This will print heap memory usage.

## To get the JVM settings:

   * run "jps" command to get the pid of running java apps.
   * run "jinfo <pid>". This will print the detailed info of the JVM settings. 

## JVM Options:

-Xmsinitial java heap size-Xmxmaximum java heap size-Xmnthe size of the heap for the young generation


-Xssthe stack size for each thread

It is good practice with server-side Java applications like Resin to set the minimum -Xms and maximum -Xmx heap sizes to the same value.

Heap size does not determine the amount of memory your process usesIf you monitor your java process with an OS tool like top or taskmanager, you may see the amount of memory you use exceed the amount you have specified for -Xmx. -Xmx limits the java heap size, java will allocate memory for other things, including a stack for each thread. It is not unusual for the total memory consumption of the VM to exceed the value of -Xmx. 


## Garbage Collection: Serial vs. Parallel vs. Concurrent-Mark-Sweepwhat's the difference between the serial, parallel and CMS (Concurrent-Mark-Sweep) collectors?
 
first of all, let's take a look which collectors operate on the young generation and which on the tenured (old) generation:
 

   * the following collectors operate on the young generation:
-XX:+UseSerialGC
-XX:+UseParallelGC
-XX:+UseParNewGC
 

   * the following collectors operate on the old generation:
-XX:+UseParallelOldGC
-XX:+UseConcMarkSweepGC
 

   * what's the difference between the Serial and the Parallel collector?
both the serial and parallel collectors cause a stop-the-world during the GC.
so what's the difference between them?
a serial collector is a default copying collector which uses only one GC thread for the GC operation, while a parallel collector uses multiple GC threads for the GC operation.
 

   * what's the difference between the Parallel and the CMS collector?
the CMS performs the following steps (all made by only one GC thread):
- initial mark
- concurrent marking
- remark
- concurrent sweeping
 
there are two differences between a parallel and a CMS collectors:
1) the parallel uses multiple GC threads, while the CMS uses only one.
2) the parallel is a 'stop-the-world' collector, while the CMS stops the world only during the initial mark and remark phases.
during the concurrent marking and sweeping phases, the CMS thread runs along with the application's threads.
 

   * if you wish to combine both parallelism and concurrency in your GC, you can use the following:
-XX:UserParNewGC for the new generation (multiple GC threads)
-XX:+UseConcMarkSweepGC for the old generation (one GC thread, freezes the JVM only during the initial mark and remark phases)


