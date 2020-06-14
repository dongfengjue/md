cookie 
------
为什么使用cookie？
http 无状态

------
cookie类型

Cookie总时由用户客户端进行保存的（一般是浏览器），按其存储位置可分为：内存式Cookie（非持久cookie）和硬盘式Cookie（持久cookie）。

------
cookie实现原理
客户端请求服务器后，如果服务器需要记录用户状态，服务器会在响应信息中包含一个Set-Cookie的响应头，客户端会根据这个响应头存储Cookie信息。再次请求服务器时，客户端会在请求信息中包含一个Cookie请求头，而服务器会根据这个请求头进行用户身份、状态等较验。

------
例子：
1. 客户端请求服务器
客户端请求IT笔录网站首页，请求头如下：
GET / HTTP/1.0
HOST: itbilu.com
2. 服务器响应请求
Cookie是一种key=value形式的字符串，服务器需要记录这个客户端请求的状态，因此在响应头中包一个Set-Cookie字段。响应头如下：
HTTP/1.0 200 OK
Set-Cookie: UserID=itbilu; Max-Age=3600; Version=1
Content-type: text/html
……
3. 再次请求时，客户端请求中会包含一个Cookie请求头
客户端会对服务器响应的Set-Cookie头信息进行存储。再次请求时，将会在请求头中包含服务器响应的Cookie信息。请求头如下
GET / HTTP/1.0
HOST: itbilu.com
Cookie: UserID=itbilu


-------------

nginx
------
nginx优点：
开源，功能丰富，稳定性高，性能高

------
反向代理
反向代理（Reverse Proxy）方式是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。

------
负载均衡
Nginx提供的负载均衡策略有2种：内置策略和扩展策略。
内置策略为轮询，加权轮询，Ip hash。
扩展策略，很多了。。。

ip hash 算法
根据hash结果将同一个客户端ip的请求分发给同一台服务器进行处理，可以解决session不共享的问题

------
nginx 配置文件

...              #全局块

events {         #events块
   ...
}

http      #http块
{
    ...   #http全局块
    server        #server块
    { 
        ...       #server全局块
        location [PATTERN]   #location块
        {
            ...
        }
        location [PATTERN] 
        {
            ...
        }
    }
    server
    {
      ...
    }
    ...     #http全局块
}
1、全局块：配置影响nginx全局的指令。一般有运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，允许生成worker process数等。
2、events块：配置影响nginx服务器或与用户的网络连接。有每个进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，开启多个网络连接序列化等。
3、http块：可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。如文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，连接超时时间，单连接请求数等。
4、server块：配置虚拟主机的相关参数，一个http中可以有多个server。比如，端口，主机名称例：
server {
listen   443;
    listen   80;
    server_name www.huize.com;

    ssl on;
    ssl_certificate ssl/huize.com.crt;
    ssl_certificate_key ssl/huize.com.key;
    ssl_session_timeout  5m;
    ssl_protocols  SSLv3 TLSv1;
    ssl_ciphers  HIGH:!ADH:!EXPORT56:RC4+RSA:+MEDIUM;
    ssl_prefer_server_ciphers   on;

    location / {
        proxy_next_upstream error timeout http_503 http_504 http_502;
        proxy_connect_timeout 500s;
        proxy_read_timeout 500s;
        proxy_send_timeout 500s;
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_pass http://www_Backend;
    }
}

5、location块：配置请求的路由，以及各种页面的处理情况。

------########### 每个指令必须有分号结束。#################
#user administrator administrators;  #配置用户或者组，默认为nobody nobody。
#worker_processes 2;  #允许生成的进程数，默认为1
#pid /nginx/pid/nginx.pid;   #指定nginx进程运行文件存放地址
error_log log/error.log debug;  #制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
events {
    accept_mutex on;   #设置网路连接序列化，防止惊群现象发生，默认为on
    multi_accept on;  #设置一个进程是否同时接受多个网络连接，默认为off
    #use epoll;      #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
    worker_connections  1024;    #最大连接数，默认为512
}
http {
    include       mime.types;   #文件扩展名与文件类型映射表
    default_type  application/octet-stream; #默认文件类型，默认为text/plain
    #access_log off; #取消服务日志    
    log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; #自定义格式
    access_log log/access.log myFormat;  #combined为日志格式的默认值
    sendfile on;   #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
    sendfile_max_chunk 100k;  #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
    keepalive_timeout 65;  #连接超时时间，默认为75s，可以在http，server，location块。

    upstream mysvr {   
      server 127.0.0.1:7878;
      server 192.168.10.121:3333 backup;  #热备
    }
    error_page 404 https://www.baidu.com; #错误页
    server {
        keepalive_requests 120; #单连接请求上限次数。
        listen       4545;   #监听端口
        server_name  127.0.0.1;   #监听地址       
        location  ~*^.+$ {       #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
           #root path;  #根目录
           #index vv.txt;  #设置默认页
           proxy_pass  http://mysvr;  #请求转向mysvr 定义的服务器列表
           deny 127.0.0.1;  #拒绝的ip
           allow 172.18.5.54; #允许的ip           
        } 
    }
惊群现象：一个网路连接到来，多个睡眠的进程被同事叫醒，但只有一个进程能获得链接，这样会影响系统性能。

每个指令必须有分号结束。

-------#运行用户
user nobody;
#启动进程,通常设置成和cpu的数量相等
worker_processes  1;

#全局错误日志及PID文件
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

#工作模式及连接数上限
events {
    #epoll是多路复用IO(I/O Multiplexing)中的一种方式,
    #仅用于linux2.6以上内核,可以大大提高nginx的性能
    use   epoll; 

    #单个后台worker process进程的最大并发链接数    
    worker_connections  1024;

    # 并发总数是 worker_processes 和 worker_connections 的乘积
    # 即 max_clients = worker_processes * worker_connections
    # 在设置了反向代理的情况下，max_clients = worker_processes * worker_connections / 4  为什么
    # 为什么上面反向代理要除以4，应该说是一个经验值
    # 根据以上条件，正常情况下的Nginx Server可以应付的最大连接数为：4 * 8000 = 32000
    # worker_connections 值的设置跟物理内存大小有关
    # 因为并发受IO约束，max_clients的值须小于系统可以打开的最大文件数
    # 而系统可以打开的最大文件数和内存大小成正比，一般1GB内存的机器上可以打开的文件数大约是10万左右
    # 我们来看看360M内存的VPS可以打开的文件句柄数是多少：
    # $ cat /proc/sys/fs/file-max
    # 输出 34336
    # 32000 < 34336，即并发连接总数小于系统可以打开的文件句柄总数，这样就在操作系统可以承受的范围之内
    # 所以，worker_connections 的值需根据 worker_processes 进程数目和系统可以打开的最大文件总数进行适当地进行设置
    # 使得并发总数小于操作系统可以打开的最大文件数目
    # 其实质也就是根据主机的物理CPU和内存进行配置
    # 当然，理论上的并发总数可能会和实际有所偏差，因为主机还有其他的工作进程需要消耗系统资源。
    # ulimit -SHn 65535

}


http {
    #设定mime类型,类型由mime.type文件定义
    include    mime.types;
    default_type  application/octet-stream;
    #设定日志格式
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main;

    #sendfile 指令指定 nginx 是否调用 sendfile 函数（zero copy 方式）来输出文件，
    #对于普通应用，必须设为 on,
    #如果用来进行下载等应用磁盘IO重负载应用，可设置为 off，
    #以平衡磁盘与网络I/O处理速度，降低系统的uptime.
    sendfile     on;
    #tcp_nopush     on;

    #连接超时时间
    #keepalive_timeout  0;
    keepalive_timeout  65;
    tcp_nodelay     on;

    #开启gzip压缩
    gzip  on;
    gzip_disable "MSIE [1-6].";

    #设定请求缓冲
    client_header_buffer_size    128k;
    large_client_header_buffers  4 128k;


    #设定虚拟主机配置
    server {
        #侦听80端口
        listen    80;
        #定义使用 www.nginx.cn访问
        server_name  www.nginx.cn;

        #定义服务器的默认网站根目录位置
        root html;

        #设定本虚拟主机的访问日志
        access_log  logs/nginx.access.log  main;

        #默认请求
        location / {
            
            #定义首页索引文件的名称
            index index.php index.html index.htm;   

        }

        # 定义错误提示页面
        error_page   500 502 503 504 /50x.html;
        location = /50x.html {
        }

        #静态文件，nginx自己处理
        location ~ ^/(images|ｊａｖａｓｃｒｉｐｔ|js|css|flash|media|static)/ {
            
            #过期30天，静态文件不怎么更新，过期可以设大一点，
            #如果频繁更新，则可以设置得小一点。
            expires 30d;
        }

        #PHP 脚本请求全部转发到 FastCGI处理. 使用FastCGI默认配置.
        location ~ .php$ {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include fastcgi_params;
        }

        #禁止访问 .htxxx 文件
            location ~ /.ht {
            deny all;
        }

    }
}




jetty 
------
基本架构
Jetty ， Servlet 引擎，架构简单，也是一个可扩展性和灵活的应用服务器。它有一个基本数据模型，这个数据模型就是 Handler（处理器），所有可以被扩展的组件都可以作为一个 Handler，添加到 Server 中，Jetty 就是帮你管理这些 Handler。Jetty 中另外一个比不可少的组件是 Connector，它负责接受客户端的连接请求，并将请求分配给一个处理队列去执行。

/static/madfefb24b06a8087c70b36a54278b806.png


---------

jetty 和 tomcat
------
相同点：
1.      Tomcat和Jetty都是一种Servlet引擎，他们都支持标准的servlet规范和JavaEE的规范。2，热部署，开发模式下，改动方法内部代码，可以不重启服务器，但是改动了方法，改动了配置，需要重启服务器
------
不同点：
1.      架构比较
Jetty的架构比Tomcat的更为简单
Jetty的架构是基于Handler来实现的，主要的扩展功能都可以用Handler来实现，扩展简单。
Tomcat的架构是基于容器设计的，进行扩展是需要了解Tomcat的整体设计结构，不易扩展。
2.      性能比较
Jetty和Tomcat性能方面差异不大
Jetty可以同时处理大量连接而且可以长时间保持连接，适合于web聊天应用等等。
Jetty的架构简单，因此作为服务器，Jetty可以按需加载组件，减少不需要的组件，减少了服务器内存开销，从而提高服务器性能。
Jetty默认采用NIO结束在处理I/O请求上更占优势，在处理静态资源时，性能较高
 
Tomcat适合处理少数非常繁忙的链接，也就是说链接生命周期短的话，Tomcat的总体性能更高。
Tomcat默认采用BIO处理I/O请求，在处理静态资源时，性能较差。
3.      其它比较
Jetty的应用更加快速，修改简单，对新的Servlet规范的支持较好。
Tomcat目前应用比较广泛，对JavaEE和Servlet的支持更加全面，很多特性会直接集成进来。

---------------------------------

数据库隔离机制

                脏读  不可重复读   幻读
Read uncommitted    √   √   √
Read committed      ×   √   √
Repeatable read     ×   ×   √
Serializable        ×   ×   ×


Read uncommitted 读未提交（脏读  不可重复读   幻读）
公司发工资了，领导把5000元打到singo的账号上，但是该事务并未提交，而singo正好去查看账户，发现工资已经到账，是5000元整，非常高兴。可是不幸的是，领导发现发给singo的工资金额不对，是2000元，于是迅速回滚了事务，修改金额后，将事务提交，最后singo实际的工资只有2000元，singo空欢喜一场。
出现上述情况，即我们所说的脏读 ，两个并发的事务，“事务A：领导给singo发工资”、“事务B：singo查询工资账户”，事务B读取了事务A尚未提交的数据。


Read committed 读提交（不可重复读   幻读）
singo拿着工资卡去消费，系统读取到卡里确实有2000元，而此时她的老婆也正好在网上转账，把singo工资卡的2000元转到另一账户，并在singo之前提交了事务，当singo扣款时，系统检查到singo的工资卡已经没有钱，扣款失败，singo十分纳闷，明明卡里有钱，为何

出现上述情况，即我们所说的不可重复读 ，两个并发的事务，“事务A：singo消费”、“事务B：singo的老婆网上转账”，事务A事先读取了数据，事务B紧接了更新了数据，并提交了事务，而事务A再次读取该数据时，数据已经发生了改变。


Repeatable read 重复读（幻读）
当隔离级别设置为Repeatable read时，可以避免不可重复读。当singo拿着工资卡去消费时，一旦系统开始读取工资卡信息（即事务开始），singo的老婆就不可能对该记录进行修改，也就是singo的老婆不能在此时转账。

singo的老婆工作在银行部门，她时常通过银行内部系统查看singo的信用卡消费记录。有一天，她正在查询到singo当月信用卡的总消费金额 （select sum(amount) from transaction where month = 本月）为80元，而singo此时正好在外面胡吃海塞后在收银台买单，消费1000元，即新增了一条1000元的消费记录（insert transaction ... ），并提交了事务，随后singo的老婆将singo当月信用卡消费的明细打印到A4纸上，却发现消费总额为1080元，singo的老婆很诧异，以为出 现了幻觉，幻读就这样产生了。
(注：读提交在读取一条记录时会出现不可重复读，而可重复度通过对事务里面的读写操作加锁，解决了度提交的问题，但是对统计某个范围内的记录数量，还是会产生幻读。)Serializable 序列化
Serializable是最高的事务隔离级别，同时代价也花费最高，性能很低，一般很少使用，在该级别下，事务顺序执行，不仅可以避免脏读、不可重复读，还避免了幻像读。

-----------------------------------


网络osi七层协议
------

应用层， 文件传输，电子邮件，文件服务，虚拟终端 TFTP，HTTP，SNMP，FTP，SMTP，DNS，Telnet

表示层， 数据格式化，代码转换，数据加密 没有协议

会话层， 解除或建立与别的接点的联系 没有协议

传输层， 提供端对端的接口 TCP，UDP

网络层， 为数据包选择路由 IP，ICMP，RIP，OSPF，BGP，IGMP

数据链路层， 传输有地址的帧以及错误检测功能 SLIP，CSLIP，PPP，ARP，RARP，MTU

物理层， 以二进制数据形式在物理媒体上传输数据 ISO2110，IEEE802，IEEE802.2

tcp 三次握手


四次挥手




【注意】中断连接端可以是Client端，也可以是Server端。


-----------------------------------


汇编语言
------
面向处理器程序设计语言，从二进制进阶到了基本的命令


------
助记符，地址符号

在汇编语言中，用助记符（Mnemonics）代替机器指令的操作码，用地址符号（Symbol）或标号（Label）代替指令或操作数的地址。
------

运算指令，

整数和逻辑运算指令
移位指令
位操作指令
。。。

-----------------------------------




