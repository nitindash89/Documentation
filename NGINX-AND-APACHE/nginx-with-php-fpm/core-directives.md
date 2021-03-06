####accept_mutex:
```
Syntax:	accept_mutex on | off;
Default:	
accept_mutex on;
Context:	events
```
If accept_mutex is enabled, worker processes will accept new connections by turn. Otherwise, all worker processes will be notified about new connections, and if volume of new connections is low, some of the worker processes may just waste system resources.

#### accept_mutex_delay:
```
Syntax:	accept_mutex_delay time;
Default:	
accept_mutex_delay 500ms;
Context:	events
```
If accept_mutex is enabled, specifies the maximum time during which a worker process will try to restart accepting new connections if another worker process is currently accepting new connections.

####daemon:
```
Syntax:	daemon on | off;
Default:	
daemon on;
Context:	main
```
Determines whether nginx should become a daemon. Mainly used during development.

#### debug_connection:
Enables debugging log for selected client connections. Other connections will use logging level set by the error_log directive. Debugged connections are specified by IPv4 or IPv6 (1.3.0, 1.2.1) address or network. A connection may also be specified using a hostname. For connections using UNIX-domain sockets (1.3.0, 1.2.1), debugging log is enabled by the “unix:” parameter.
```
events {
    debug_connection 127.0.0.1;
    debug_connection localhost;
    debug_connection 192.0.2.0/24;
    debug_connection ::1;
    debug_connection 2001:0db8::/32;
    debug_connection unix:;
    ...
}
```
For this directive to work, nginx needs to be built with --with-debug, see “A debugging log”.

#### debug_points
```
Syntax:	debug_points abort | stop;
Default:	—
Context:	main
This directive is used for debugging.
```
When internal error is detected, e.g. the leak of sockets on restart of working processes, enabling debug_points leads to a core file creation (abort) or to stopping of a process (stop) for further analysis using a system debugger.

#### error_log:
```
Syntax:	error_log file | stderr | syslog:server=address[,parameter=value] | memory:size [debug | info | notice | warn | error | crit | alert | emerg];
Default:	
error_log logs/error.log error;
Context:	main, http, mail, stream, server, location
```
Configures logging. Several logs can be specified on the same level (1.5.2).
The first parameter defines a file that will store the log. The special value stderr selects the standard error file. Logging to syslog can be configured by specifying the “syslog:” prefix. Logging to a cyclic memory buffer can be configured by specifying the “memory:” prefix and buffer size, and is generally used for debugging (1.7.11).

The second parameter determines the level of logging. Log levels above are listed in the order of increasing severity. Setting a certain log level will cause all messages of the specified and more severe log levels to be logged. For example, the default level error will cause error, crit, alert, and emerg messages to be logged. If this parameter is omitted then error is used.

For debug logging to work, nginx needs to be built with --with-debug, see “A debugging log”.
The directive can be specified on the stream level starting from version 1.7.11.
The directive can be specified on the mail level starting from version 1.9.0.

####env:
```
Syntax:	env variable[=value];
Default:	
env TZ;
Context:	main
```
By default, nginx removes all environment variables inherited from its parent process except the TZ variable. This directive allows preserving some of the inherited variables, changing their values, or creating new environment variables. These variables are then:

inherited during a live upgrade of an executable file;
used by the ngx_http_perl_module module;
used by worker processes. One should bear in mind that controlling system libraries in this way is not always possible as it is common for libraries to check variables only during initialization, well before they can be set using this directive. An exception from this is an above mentioned live upgrade of an executable file.
The TZ variable is always inherited and available to the ngx_http_perl_module module, unless it is configured explicitly.

Usage example:
```
env MALLOC_OPTIONS;
env PERL5LIB=/data/site/modules;
env OPENSSL_ALLOW_PROXY_CERTS=1;
The NGINX environment variable is used internally by nginx and should not be set directly by the user.
```

####events:
```
Syntax:	events { ... }
Default:	—
Context:	main
```
Provides the configuration file context in which the directives that affect connection processing are specified.

####include:
```
Syntax:	include file | mask;
Default:	—
Context:	any
```
Includes another file, or files matching the specified mask, into configuration. Included files should consist of syntactically correct directives and blocks.

Usage example:
```
include mime.types;
include vhosts/*.conf;
```

#### lock_file:
```
Syntax:	lock_file file;
Default:	
lock_file logs/nginx.lock;
Context:	main
```
nginx uses the locking mechanism to implement accept_mutex and serialize access to shared memory. On most systems the locks are implemented using atomic operations, and this directive is ignored. On other systems the “lock file” mechanism is used. This directive specifies a prefix for the names of lock files.

####master_process
```
Syntax:	master_process on | off;
Default:	
master_process on;
Context:	main
```
Determines whether worker processes are started. This directive is intended for nginx developers.

####multi_accept:
```
Syntax:	multi_accept on | off;
Default:	
multi_accept off;
Context:	events
```
If multi_accept is disabled, a worker process will accept one new connection at a time. Otherwise, a worker process will accept all new connections at a time.

The directive is ignored if kqueue connection processing method is used, because it reports the number of new connections waiting to be accepted.

####pcre_jit:
```
Syntax:	pcre_jit on | off;
Default:	
pcre_jit off;
Context:	main
```
This directive appeared in version 1.1.12.
Enables or disables the use of “just-in-time compilation” (PCRE JIT) for the regular expressions known by the time of configuration parsing.

PCRE JIT can speed up processing of regular expressions significantly.

The JIT is available in PCRE libraries starting from version 8.20 built with the --enable-jit configuration parameter. When the PCRE library is built with nginx (--with-pcre=), the JIT support is enabled via the --with-pcre-jit configuration parameter.

####pid:
```
Syntax:	pid file;
Default:	
pid nginx.pid;
Context:	main
```
Defines a file that will store the process ID of the main process.

####ssl_engine:
```
Syntax:	ssl_engine device;
Default:	—
Context:	main
```
Defines the name of the hardware SSL accelerator.

#### thread_pool:
```
Syntax:	thread_pool name threads=number [max_queue=number];
Default:	
thread_pool default threads=32 max_queue=65536;
Context:	main
```
This directive appeared in version 1.7.11.
Defines named thread pools used for multi-threaded reading and sending of files without blocking worker processes.

The threads parameter defines the number of threads in the pool.

In the event that all threads in the pool are busy, a new task will wait in the queue. The max_queue parameter limits the number of tasks allowed to be waiting in the queue. By default, up to 65536 tasks can wait in the queue. When the queue overflows, the task is completed with an error.

####timer_resolution:
```
Syntax:	timer_resolution interval;
Default:	—
Context:	main
```
Reduces timer resolution in worker processes, thus reducing the number of gettimeofday() system calls made. By default, gettimeofday() is called each time a kernel event is received. With reduced resolution, gettimeofday() is only called once per specified interval.

Example:

timer_resolution 100ms;

Internal implementation of the interval depends on the method used:

the EVFILT_TIMER filter if kqueue is used;
timer_create() if eventport is used;
setitimer() otherwise.

####use:
```
Syntax:	use method;
Default:	—
Context:	events
```
Specifies the connection processing method to use. There is normally no need to specify it explicitly, because nginx will by default use the most efficient method.

#### Syntax:	user user [group];
```
Default:	
user nobody nobody;
Context:	main
```
Defines user and group credentials used by worker processes. If group is omitted, a group whose name equals that of user is used.

####worker_aio_requests:
```
Syntax:	worker_aio_requests number;
Default:	
worker_aio_requests 32;
Context:	events
```
This directive appeared in versions 1.1.4 and 1.0.7.
When using aio with the epoll connection processing method, sets the maximum number of outstanding asynchronous I/O operations for a single worker process.

####worker_connection:
```
Syntax:	worker_connections number;
Default:	
worker_connections 512;
Context:	events
```
Sets the maximum number of simultaneous connections that can be opened by a worker process.

It should be kept in mind that this number includes all connections (e.g. connections with proxied servers, among others), not only connections with clients. Another consideration is that the actual number of simultaneous connections cannot exceed the current limit on the maximum number of open files, which can be changed by worker_rlimit_nofile.

#### worker_cpu_affinity:
```
Syntax:	worker_cpu_affinity cpumask ...;
Default:	—
Context:	main
```
Binds worker processes to the sets of CPUs. Each CPU set is represented by a bitmask of allowed CPUs. There should be a separate set defined for each of the worker processes. By default, worker processes are not bound to any specific CPUs.

For example,
```
worker_processes    4;
worker_cpu_affinity 0001 0010 0100 1000;
binds each worker process to a separate CPU, while
```
```
worker_processes    2;
worker_cpu_affinity 0101 1010;
```
binds the first worker process to CPU0/CPU2, and the second worker process to CPU1/CPU3. The second example is suitable for hyper-threading.

The directive is only available on FreeBSD and Linux.

####worker_priority:
```
Syntax:	worker_priority number;
Default:	
worker_priority 0;
Context:	main
```
Defines the scheduling priority for worker processes like it is done by the nice command: a negative number means higher priority. Allowed range normally varies from -20 to 20.

Example:
```
worker_priority -10;
```
####worker_processes:

Syntax:	worker_processes number | auto;
```
Default:	
worker_processes 1;
Context:	main
Defines the number of worker processes.
```
The optimal value depends on many factors including (but not limited to) the number of CPU cores, the number of hard disk drives that store data, and load pattern. When one is in doubt, setting it to the number of available CPU cores would be a good start (the value “auto” will try to autodetect it).

####worker_rlimit_core:
```
Syntax:	worker_rlimit_core size;
Default:	—
Context:	main
```
Changes the limit on the largest size of a core file (RLIMIT_CORE) for worker processes. Used to increase the limit without restarting the main process.

####worker_rlimit_nofile:
```
Syntax:	worker_rlimit_nofile number;
Default:	—
Context:	main
```
Changes the limit on the maximum number of open files (RLIMIT_NOFILE) for worker processes. Used to increase the limit without restarting the main process.

####working_directory:
```
Syntax:	working_directory directory;
Default:	—
Context:	main
```
Defines the current working directory for a worker process. It is primarily used when writing a core-file, in which case a worker process should have write permission for the specified directory.
