#System auditing and benchmarking

##Prerequisites
1. Fully onfigured system

##System performance benchmarks
###UnixBench
1. Download the UnixBench application
https://code.google.com/p/byte-unixbench/downloads/detail?name=UnixBench5.1.3.tgz&can=2&q=
2. Copy the file from your laptop to the RPi
`scp "/Users/brianmcmillan/Documents/Projects/System Auditing/UnixBench/UnixBench5.1.3.tar" pi@192.168.0.11:~`
`scp foobar.txt your_username@remotehost.edu:/some/remote/directory

3. Connect to the RPi
`ssh pi@192.168.0.11`
4. Open the source code directory
`cd ~/ && ls`
5. Untar the compressed file and open the directory
`tar -xvf UnixBench5.1.3.tar && cd UnixBench`
6. Compile UnixBench
`make`
7. Run the program
`./Run`

Returns:
```
   #    #  #    #  #  #    #          #####   ######  #    #   ####   #    #
   #    #  ##   #  #   #  #           #    #  #       ##   #  #    #  #    #
   #    #  # #  #  #    ##            #####   #####   # #  #  #       ######
   #    #  #  # #  #    ##            #    #  #       #  # #  #       #    #
   #    #  #   ##  #   #  #           #    #  #       #   ##  #    #  #    #
    ####   #    #  #  #    #          #####   ######  #    #   ####   #    #

   Version 5.1.3                      Based on the Byte Magazine Unix Benchmark

   Multi-CPU version                  Version 5 revisions by Ian Smith,
                                      Sunnyvale, CA, USA
   January 13, 2011                   johantheghost at yahoo period com
...
1 x Dhrystone 2 using register variables  1 2 3 4 5 6 7 8 9 10
1 x Double-Precision Whetstone  1 2 3 4 5 6 7 8 9 10
...
4 x Shell Scripts (1 concurrent)  1 2 3
4 x Shell Scripts (8 concurrent)  1 2 3

========================================================================
   BYTE UNIX Benchmarks (Version 5.1.3)

   System: j8twmxx: GNU/Linux
   OS: GNU/Linux -- 4.1.7-v7+ -- #817 SMP PREEMPT Sat Sep 19 15:32:00 BST 2015
   Machine: armv7l (unknown)
   Language: en_US.utf8 (charmap="UTF-8", collate="UTF-8")
   CPU 0: ARMv7 Processor rev 5 (v7l) (0.0 bogomips)
   CPU 1: ARMv7 Processor rev 5 (v7l) (0.0 bogomips)
   CPU 2: ARMv7 Processor rev 5 (v7l) (0.0 bogomips)
   CPU 3: ARMv7 Processor rev 5 (v7l) (0.0 bogomips)
   17:06:53 up 56 min,  2 users,  load average: 0.31, 0.09, 0.07; runlevel 3

------------------------------------------------------------------------
Benchmark Run: Tue Nov 24 2015 17:06:53 - 17:34:48
4 CPUs in system; running 1 parallel copy of tests

Dhrystone 2 using register variables        3013841.6 lps   (10.0 s, 7 samples)
Double-Precision Whetstone                      436.1 MWIPS (9.9 s, 7 samples)
Execl Throughput                                338.4 lps   (29.5 s, 2 samples)
File Copy 1024 bufsize 2000 maxblocks         73356.0 KBps  (30.0 s, 2 samples)
File Copy 256 bufsize 500 maxblocks           20992.0 KBps  (30.0 s, 2 samples)
File Copy 4096 bufsize 8000 maxblocks        192285.0 KBps  (30.0 s, 2 samples)
Pipe Throughput                              188332.0 lps   (10.0 s, 7 samples)
Pipe-based Context Switching                  34787.6 lps   (10.0 s, 7 samples)
Process Creation                               1234.5 lps   (30.0 s, 2 samples)
Shell Scripts (1 concurrent)                   1117.7 lpm   (60.0 s, 2 samples)
Shell Scripts (8 concurrent)                    312.2 lpm   (60.1 s, 2 samples)
System Call Overhead                         400872.9 lps   (10.0 s, 7 samples)

System Benchmarks Index Values               BASELINE       RESULT    INDEX
Dhrystone 2 using register variables         116700.0    3013841.6    258.3
Double-Precision Whetstone                       55.0        436.1     79.3
Execl Throughput                                 43.0        338.4     78.7
File Copy 1024 bufsize 2000 maxblocks          3960.0      73356.0    185.2
File Copy 256 bufsize 500 maxblocks            1655.0      20992.0    126.8
File Copy 4096 bufsize 8000 maxblocks          5800.0     192285.0    331.5
Pipe Throughput                               12440.0     188332.0    151.4
Pipe-based Context Switching                   4000.0      34787.6     87.0
Process Creation                                126.0       1234.5     98.0
Shell Scripts (1 concurrent)                     42.4       1117.7    263.6
Shell Scripts (8 concurrent)                      6.0        312.2    520.4
System Call Overhead                          15000.0     400872.9    267.2
                                                                   ========
System Benchmarks Index Score                                         170.3

------------------------------------------------------------------------
Benchmark Run: Tue Nov 24 2015 17:34:48 - 18:02:47
4 CPUs in system; running 4 parallel copies of tests

Dhrystone 2 using register variables       12039415.0 lps   (10.0 s, 7 samples)
Double-Precision Whetstone                     1743.0 MWIPS (9.9 s, 7 samples)
Execl Throughput                               1241.7 lps   (30.0 s, 2 samples)
File Copy 1024 bufsize 2000 maxblocks        116159.0 KBps  (30.0 s, 2 samples)
File Copy 256 bufsize 500 maxblocks           32548.6 KBps  (30.0 s, 2 samples)
File Copy 4096 bufsize 8000 maxblocks        314502.5 KBps  (30.0 s, 2 samples)
Pipe Throughput                              746887.8 lps   (10.0 s, 7 samples)
Pipe-based Context Switching                 130128.4 lps   (10.0 s, 7 samples)
Process Creation                               2641.9 lps   (30.0 s, 2 samples)
Shell Scripts (1 concurrent)                   2486.0 lpm   (60.1 s, 2 samples)
Shell Scripts (8 concurrent)                    329.4 lpm   (60.5 s, 2 samples)
System Call Overhead                        1539930.7 lps   (10.0 s, 7 samples)

System Benchmarks Index Values               BASELINE       RESULT    INDEX
Dhrystone 2 using register variables         116700.0   12039415.0   1031.7
Double-Precision Whetstone                       55.0       1743.0    316.9
Execl Throughput                                 43.0       1241.7    288.8
File Copy 1024 bufsize 2000 maxblocks          3960.0     116159.0    293.3
File Copy 256 bufsize 500 maxblocks            1655.0      32548.6    196.7
File Copy 4096 bufsize 8000 maxblocks          5800.0     314502.5    542.2
Pipe Throughput                               12440.0     746887.8    600.4
Pipe-based Context Switching                   4000.0     130128.4    325.3
Process Creation                                126.0       2641.9    209.7
Shell Scripts (1 concurrent)                     42.4       2486.0    586.3
Shell Scripts (8 concurrent)                      6.0        329.4    549.0
System Call Overhead                          15000.0    1539930.7   1026.6
                                                                   ========
System Benchmarks Index Score                                         430.0
```
###Remove UnixBench

##Configuration auditing
https://www.stigviewer.com/stig/red_hat_enterprise_linux_6/
###Lynis
https://cisofy.com/lynis/
https://cisofy.com/documentation/lynis/get-started/
http://blog.technotesdesk.com/auditing-linux-machine-with-lynis-auditing-tool/

http://blog.technotesdesk.com/auditing-linux-machine-with-lynis-auditing-tool/


###Shell scripts
See my alpine docker audit script
https://gist.github.com/brianmcmillan/5a12e44f5a5e0d640707