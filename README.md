OVERVIEW
=======

nginx-x-rid-header is a small module which adds a request-scoped id (uuid) variable that can be used to correlate frontend logging/activity with backend logging/activity.

Supports platforms w/e2fsprogs & libuuid.

CREDITS
======

Brian Long  (<mailto:newobj@gmail.com>, <mailto:brian@dotspots.com>, <http://newobj.net>)

Kevin Smith (<mailto:k@opscode.com>, <http://www.opscode.com>)

USAGE
=====

1) Add `--add-module=../git/nginx-x-rid-header` to your nginx configure command. On Linux and other platforms which do not bundle e2fsprogs, you should also add `--with-ld-opt=-luuid` to the configure command.
 a) On Open{Indiana|Solaris},, install e2fsprogs from SFE (http://staticdev.uk.openindiana.org:10002).  It is installed a bit deeper in the hierarchy, so build with "C_INCLUDE_PATH=/usr/include/ext2fs/ LD_LIBRARY_PATH=/usr/lib/ext2fs make" to get the right path.

2) You now have access to a `$request_id` variable. Suggested use:

    log_format main  '$remote_addr - $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent" "$http_x_forwarded_for" - $connection $request_time $upstream_cache_status rid:$request_id';
    server {
        listen       80;
        server_name  example.com;
        location / {
            proxy_set_header x-exampledotcom-rid $request_id;
            proxy_pass   http://localhost:8080;
        }
    }

3) On your backend (8080), you can pull the request header `x-exampledotcom-rid`, and log it or tie it to whatever you may like. This makes it really easy to correlate backend exceptions or instrumentation with frontend http request logs.
