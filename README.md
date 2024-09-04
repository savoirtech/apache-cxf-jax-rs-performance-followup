# Apache CXF SOAP Performance Follow Up

In our last
[post](https://github.com/savoirtech/apache-cxf-jax-rs-performance), we
recounted our journey to processing ~1.6 Billion JAX-RS calls in an
eight-hour period. This lead to several readers reaching out with
suggestions for tweaks to the scenarios run, or entirely new
configurations. In this post we seek to provide some answers.

<figure>
<img src="./assets/images/CXFLab.png" alt="CXFLab" />
</figure>

## Brief Recap

After several iterations we found that we could reach peak throughput
using the following Client and Server side settings:

| Parameter | Setting |
|----|----|
| MAVEN_OPTS | -Xms32m -Xmx8192m |
| file handle ulimit | /etc/security/limits.conf hard & soft limits increased to 655350 |
| ip_local_port_range | sysctl -w net.ipv4.ip_local_port_range="15000 64000" to allow 49K connections. |

PPC64LE Server:

``` bash
$mvn -Pserver -Dhost=0.0.0.0 -Dprotocol=http
```

x86 Client:

``` bash
$mvn -Pclient -Dhost=192.168.50.154 -Dprotocol=http -Doperation=get -Dthreads=48 -Dtime=28800
```

This resulted in:

``` bash
=============Overall Test Result============
Overall Throughput: get 1092.5105255070816 (invocations/sec)
Overall AVG. response time: 0.9153229892552811 (ms)
1.510322517E9 (invocations), running 1382432.921 (sec)
============================================
```

That’s ?? invocations in eight hours.

# Can you try Java 21 again with Java 17’s Heap & Tuning?

Sure!

This resulted in:

``` bash
=============Overall Test Result============

============================================
```

## How does that compare to our last Java 21 test?

In our prior lab test we achieved ?? calls in eight-hours, this run
provided ?? - a delta improvement of ?? invocations.
