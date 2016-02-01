---
layout: post
title: nginx work module
date: '2016-02-01 10:35:00 +0800'
categories: articles
---

> **the article is from**:
>
> [https://www.quora.com/How-can-nginx-handle-concurrent-requests-with-a-single-worker-process](https://www.quora.com/How-can-nginx-handle-concurrent-requests-with-a-single-worker-process)

nginx uses multiplexing and event notifications heavily, and dedicates specific tasks to separate processes. Connections are processed in a highly efficient run-loop in a limited number of single-threaded processes called workers. Within each worker nginx can handle many thousands of concurrent connections and requests per second.

<img class="img-responsive" src="{{ site.baseurl }}/downloads/posts/nginx/nginx-structure.png"     alt="nginx structure">

- Master: Monitor workers, respawn when a worker dies.Handle signals and notify workers
- Worker:Process client requests.Handle connections Get cmd from master.Worker
processes accept new requests from a shared "listen" socket and execute a highly
efficient run-looinside each worker to process thousands of connections per worker.
There's no specialized arbitration odistribution of connections to the workers in
nginx; this work is done by the OS kernel mechanisms. Upostartup, an initial set
of listening sockets is created. workers then continuously accept, read from and
write to the sockets while processing HTTP requests and responses.

<img class="img-responsive" src="{{ site.baseurl }}/downloads/posts/nginx/nginx-workflow.png"     alt="nginx workflow">

The run-loop is the most complicated part of the nginx worker code. It includes comprehensive inner calls and relies heavily on the idea of asynchronous task handling. Asynchronous operations are implemented through modularity, event notifications, extensive use of callback functions and fine-tuned timers. Overall, the key principle is to be as non-blocking as possible. The only situation where nginx can still block is when there's not enough disk storage performance for a worker process.

Because nginx does not fork a process or thread per connection, memory usage is very conservative and extremely efficient in the vast majority of cases. nginx conserves CPU cycles as well because there's no ongoing create-destroy pattern for processes or threads. What nginx does is check the state of the network and storage, initialize new connections, add them to the run-loop, and process asynchronously until completion, at which point the connection is deallocated and removed from the run-loop. Combined with the careful use of syscalls and an accurate implementation of supporting interfaces like pool and slab memory allocators, nginx typically achieves moderate-to-low CPU usage even under extreme workloads.
