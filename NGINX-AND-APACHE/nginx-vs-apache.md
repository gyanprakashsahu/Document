### Apache Vs. Nginx
Main difeerence between apache and nginx is that apache is "process based architecture"
and nginx is "event driven architecture". Let me explain both of these architecture in detail :

###Apache:
We will only discuss below MPM (Multi Processing Module) , while apache has many MPM dependent on plateform and functionalities, but below MPM are the widely used  MPM of apache:

#### MPM Prefork:

ADVANTAGES: This prefork model of apache is non-threaded and is a good model, as it makes each and every connection isolated from each other.So if one connection is is having some issues, the other one is not all effected. By default if no MPM module is specified then apache uses this MPM Prefork as its MPM module.

DISADVANTAGES: In this model a single parent process sits and creates many child processes which wait for requests and serve as the requests arrive. Which means each and every request is served by a seperate process. In other words, we can say its “process per request”. And apache maintains, several number of idle process before the requests arrive. Due to these idle processes waiting for requests, they can serve fast when requests arrive.If you have got a lot number of requests at one time, then you will have lot number of child processes spawned by apache, and which will result in heavy resource utilization, as each process will utilize a certain amount of system memory and cpu resources.

#### MPM WORKER:

This model of apache can serve a large number of requests with less system resources than the prefork model because here a limited number of process will serve, many number of requests.In this model also there is one single parent process, which spawns some child processes. But there is no “process per requests”, but instead “thread per requests”. So the child process will have a certain number of threads inside it. Each child process will have certain “server threads” and certain “idle threads”. Idle threads are waiting for new requests, so there is no time wasted in creating threads when the requests arrive.There is a directive inside apache config file /etc/httpd/conf/httpd.conf called “StartServers” which says how many child process will be there when apache starts.
Child process handles requests with the help of a fixed number of threads inside them which is specified by the argument “ThreadsPerChild” in the config file.

NOTE: In Operating System's thread is a small instance of a process which does some job and exits. Thread is sometimes called a process inside a process.

### NGINX: 
Nginx was mainly made, to solve the c10k problem in apache.
C10k: its a name given to the issue of optimizing the web server software to handle large number of requsts at one time. In the range of 10000 requests at a time, hence the name.

Nginx is an event driven single threaded architecture.(i will come back to event driven later). The main difference this even driven architecture makes is that, a very small number of nginx worker process can serve a very very large number of requests.
Nginx avoids the idea of child processes. All requests are handled by a single thread. And this single thread will handle everything, with the help of something called as event loop. So the thread pops up whenever a new connection, or some thing is required(not wasting resources.).

####COMPARISON: 
1.In apache when a request is being served, either a thread or a process is created which serves the request. Now if one requst needs some data from the database,and files from disk, etc the process waits for that.So some processes in apache just sits and wait for certain task to complete(eating system resources).Suppose a client with a slow internet connection connects to a web server running apache, the apache server retrieves the data from the disk, to serve the client. Now even after serving the client that process will wait until a confirmation is received from that cliet(which will waste that much process resource). But in nginx it is not as i discussed before.

2.In general, Nginx have less components to add more features. But Apache has tons of features and provides lot more functionality than Nginx.

3.Nginx do not support Operating Systems like OpenVMS and IBMi. But Apache supports much wider range of Operating Systems.

4.Since Nginx comes only with core features that are required for a web server, it is lightweight when compared to Apache

5.The performance and scalability of Nginx is not completely dependent on hardware resources, whereas the performance and scalability of the Apache is dependent on underlying hardware resources like memory and CPU.

Like Apache, Nginx has all the features you would expect from a leading Web server:
1.Static file serving.
2.SSL/TLS support.
3.Virtual hosts.
4.Reverse proxying.
5.Load balancing.
6.Compression.
7.Access controls.
8.URL rewriting.
9.Custom logging.
10.Server-side includes.
11.Limited WebDAV.
12.FLV streaming.
13.FastCGI.
