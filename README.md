####dpdk-nginx
--------------
dpdk-nginx fork from official nginx-1.9.5, and run on the dpdk user space TCP/IP stack(NETDP). For detail function, please refer to nginx official website(http://nginx.org/).

####build and install
--------------
*  Download latest dpdk version from [dpdk website](http://dpdk.org/)
```
$ make config T=x86_64-native-linuxapp-gcc
$ make install T=x86_64-native-linuxapp-gcc
$ export RTE_SDK=/home/mytest/dpdk
$ export RTE_TARGET=x86_64-native-linuxapp-gcc
```
*  Build dpdk and opendp following the [opendp wiki](https://github.com/opendp/dpdk-odp/wiki/Compile-APP-with-netdp) 
```
$ git clone https://github.com/opendp/dpdk-odp.git
$ export RTE_ODP=/home/mytest/dpdk-odp
$ make
$ sudo ./build/opendp -c 0x1 -n 1  -- -p 0x1 --config="(0,0,0)"
EAL: Detected lcore 0 as core 0 on socket 0
EAL: Detected lcore 1 as core 1 on socket 0
EAL: Support maximum 128 logical core(s) by configuration.
EAL: Detected 2 lcore(s)
EAL: VFIO modules not all loaded, skip VFIO support...
EAL: Setting up physically contiguous memory...
EAL: Ask a virtual area of 0x400000 bytes
EAL: Virtual area found at 0x7fdf90c00000 (size = 0x400000)
EAL: Ask a virtual area of 0x15400000 bytes
```
*  Download dpdk-nginx, build dpdk-nginx

```
$ git clone https://github.com/opendp/dpdk-nginx.git
$ ./configure  --with-http_dav_module
$ make
$ make install   # default install dir is /usr/local/nginx
```
####Testing
--------------
Notes：These test run on VM, they are function testing, not performance testing.
*  Startup netdp TCP/IP stack
```
$ sudo ./build/opendp -c 0x1 -n 1  -- -p 0x1 --config="(0,0,0)"
EAL: Detected lcore 0 as core 0 on socket 0
EAL: Detected lcore 1 as core 1 on socket 0
EAL: Support maximum 128 logical core(s) by configuration.
EAL: Detected 2 lcore(s)
EAL: VFIO modules not all loaded, skip VFIO support...
EAL: Setting up physically contiguous memory...
...
```
*  startup nginx
```
$ sudo ./objs/nginx
EAL: Detected lcore 0 as core 0 on socket 0
EAL: Detected lcore 1 as core 1 on socket 0
....
2015/11/09 19:04:37 [notice] 1812#0: OS: Linux 3.16.0-30-generic
2015/11/09 19:04:37 [notice] 1812#0: getrlimit(RLIMIT_NOFILE): 1024:4096
```
*  Test http connection by ab tool
```
CPU:Intel(R) Xeon(R) CPU E5-2430 0 @ 2.20GHz.
NIC:Intel Corporation 82576 Gigabit Network Connection (rev 01) 
OPENDP run on a lcore.

root@h163:~# ab -n 30000 -c 500 2.2.2.2:80/
This is ApacheBench, Version 2.3 <$Revision: 1528965 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 2.2.2.2 (be patient)
Completed 3000 requests
Completed 6000 requests
Completed 9000 requests
Completed 12000 requests
Completed 15000 requests
Completed 18000 requests
Completed 21000 requests
Completed 24000 requests
Completed 27000 requests
Completed 30000 requests
Finished 30000 requests


Server Software:        nginx/1.9.5
Server Hostname:        2.2.2.2
Server Port:            80

Document Path:          /
Document Length:        612 bytes

Concurrency Level:      500
Time taken for tests:   0.965 seconds
Complete requests:      30000
Failed requests:        0
Total transferred:      25320000 bytes
HTML transferred:       18360000 bytes
Requests per second:    31092.43 [#/sec] (mean)
Time per request:       16.081 [ms] (mean)
Time per request:       0.032 [ms] (mean, across all concurrent requests)
Transfer rate:          25626.97 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    2   1.1      1       7
Processing:     2   14   5.1     14      30
Waiting:        2   14   5.0     14      29
Total:          4   16   4.7     16      30

Percentage of the requests served within a certain time (ms)
  50%     16
  66%     18
  75%     19
  80%     20
  90%     22
  95%     24
  98%     25
  99%     27
 100%     30 (longest request)

```
*  Test file download by wget tool
```
CPU:Intel(R) Xeon(R) CPU E5-2430 0 @ 2.20GHz.
NIC:Intel Corporation 82576 Gigabit Network Connection (rev 01) 
OPENDP run on a lcore.

root@h163:~# wget http://2.2.2.2/nginx_big_data
--2016-01-02 20:58:24--  http://2.2.2.2/nginx_big_data
Connecting to 2.2.2.2:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 44046102 (42M) [application/octet-stream]
Saving to: ‘nginx_big_data.1’

100%[=====================================>] 44,046,102   111MB/s   in 0.4s

2016-01-02 20:58:24 (111 MB/s) - ‘nginx_big_data.1’ saved [44046102/44046102]

root@h163:~#
```
####Notes
* netdp tcp stack support reuseport, so can enable nginx reuseport feature, multi nginx can listen on same port.

####Support
-------
For free support, please use netdp team mail list at zimeiw@163.com, or QQ Group:86883521, or https://dpdk-odp.slack.com.
