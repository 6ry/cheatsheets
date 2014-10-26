

http://wiki.nginx.org/Pitfalls
http://www.nginxtips.com/hardening-nginx-ssl-tsl-configuration/
http://blog.martinfjordvald.com/2013/06/debugging-nginx-errors/



`events` block - handle directives that deal with the event-polling nature. Most directives inside this block can be safely ignored, execpt `worker_connections`, which controls the number of connections each worker can handle.


*****


##Nginx Configuration Inheritance Model
<http://blog.martinfjordvald.com/2012/08/understanding-the-nginx-configuration-inheritance-model/>

There are 6 possible contexts in nginx, here in top to bottom order:
    Global.
    Http.
    Server.
    If.
    Location.
        Nested Location.
        If in location.
        limit_except.


When it comes to inheritance behaviour there are four types of configuration directives in nginx:
*   Normal directive – One value per context, for example: “root” or “index”.

*   Array directive – Multiple values per context, for example: “access_log” or “fastcgi_param”.

*   Action directive – Something which does not just configure, for example: “rewrite” or “fastcgi_pass”.
    They are confined to one context and will never inherit downwards, they can
    however be specified in multiple contexts and in some cases will be
    executed for each context. The rewrite directive is an action directive
    that is allowed in server and location context where both contexts might be
    executed.

*   try_files directive.


*****


##Location blocks

####Location Modifer Definitions
Modifier    | Result
--------    | ------
no modifier | match as a **prefix value**. `location /` will match any URI beginning with `/`, while `location /foo` will match any URI beginning with `/foo`.
=           | match as an **exact value**. `location = /foo` will only match the exact URI `/foo` not the URI `/foobar or` even `/foo/`.
~           | match as a **case sensitive regular expression** using the PCRE library.
~*          | match as a **case insensitive regular expression** using the PCRE library.
^~          | match as a **prefix value, which is more important than a regular expression**.

####Location Modifier Execution Order
Modifier    | Specificity
--------    | -----------
=           | This is the most specific modifier possible, as it matches only the exact string. If this location matches, it will be chosen first.
^~          | This modifier is used specifically when you want a prefix match to be more important than a regular expression location. If you have multiple matching locations of this type, the longest match will be used.
~ and ~*    | nginx has no way to decide how specific a regular expression is, so these are matched in the order they are defined. This means that if multiple regular expression locations match, the first one defined will be used.
no modifier | Finally if nothing else matches, a standard prefix match is used. If multiple prefix locations match, the longest match will be used.


*****

##Examples

####Example 1

```
server {
    listen          80;
    server_name     example.com;

    root            /var/www/website;
    index           index.php;

    location / {
        try_files   $uri $uri/ /index.php;
        #           ^^^^ ^^^^^ ^^^^^^^^^^
        #           (1)   (2)     (3)
        #
        #           (1) if a URI does not end in .php but, for instance /contact-us, it will 
        #               try to find a file on the disk using the root directive and the URI,
        #               i.e. /var/www/website/contact-us
        #
        #           (2) if file not found on disk, it tries to search for directory instead and
        #               uses our index directive to find an index file.
        #               i.e. /var/www/website/contact-us/index.php
        #
        #           (3) finally, if both file and directory not found on disk, it rewrites
        #               internally to /index.php and restarts location evaluation.
    }

    location ~ \.php$ {
        #    ^^^^^^^^
        #    a request with a URI ending in .php will be handled by this block
        #
        include         fastcgi.conf;
        fastcgi_pass    127.0.0.1:9000;
    }
}
```

####Example 2

