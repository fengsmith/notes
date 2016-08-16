Host is not allowed to connect to this MySQL server 和 access denied for user 'root'@'localhost' 和忘记 MYSQL 密码的解决办法。

昨天，我让同事连我的数据库，连不上，报错：

    Host is not allowed to connect to this MySQL server
    
这个错误之前也见过，意思是说我的 MySQL 服务器不允许这台主机连接。需要修改 MySQL 数据库中的数据库名称为 mysql 的数据库中的 user 表。可以增加一条记录只允许某个主机用某个用户连接。也可以修改一条记录允许所有的主机用某一个用户连接。
可以把图中红框中的 127.0.0.1 修改为 % 就可以了。原来的意思是只允许 root 用户在本机连接 MySQL 数据库服务器，修改为 % 的意思是所有的主机都可以用 root 用户连接 MySQL 服务器了。

我只是记得要修改 mysql 数据库中的 user 表中的 host 字段，我昨天把 127.0.0.1 错误地修改为 :: ，结果导致了所有的主机都不能访问 MySQL 数据库服务器了。因为没有一台主机的名字是 :: 。我为什么会错误地改成这样呢，我看了下其他的数据中的 host 字段中有 ::1 这样的值，我错误地以为 ::1 表示允许所有的主机连接。而我在修改的时候却少加了个 1 ，导致了所有的主机包括本地都不能连接数据库服务器。
那么 ::1 到底表示的是什么呢？后来查了 ::1 是 ip v6 下的一种表示方式， 和 localhost 127.0.0.1 也是一样的意思，表示的是本地主机。

    Host is not allowed to connect to this MySQL server
     
这是是因为权限的问题，忘记了用户名和密码也可以通过同样的方法解决。
我是在 my.ini 文件中增加了一行跳过权限检查的设置就好了：

    skip-grant-tables
    
说一下 my.ini 文件的位置吧，我的系统是 win7 64位，my.ini 文件位于：
         
    C:\ProgramData\MySQL\MySQL Server 5.7\my.ini
    
还有一个文件名叫 my-default.ini ，我最开始找到了这个文件，没找到 my.ini 文件，我用的数据库版本是 5.7 以为 5.7 的 my-default.ini 文件就是 my.ini 文件。结果添加了跳过权限检查的设置仍然不能连接数据库服务器。后来
后来终于找到了 my.ini 文件，增加了跳过权限设置重启服务之后终于可以建立数据库连接了。

再说一下在 my.ini 文件的哪个位置加这一行设置，在 my.ini 文件中会有一行：

    [mysqld]
    
找到这一行内容，在这一行内容的下面加：
    
    skip-grant-tables

这一行设置，然后重启 MySQL 的服务就可以连接到数据库的服务器了。我试了下加了这个设置之后，只需要知道 MySQL 服务器的 ip 地址就能连接到服务器了。不需要知道 MySQL 服务器的用户名和密码，在连接的时候用户名和密码字段为空或为任意的值都能成功的连接到数据库服务器。
如果是忘记用户名和密码的话，可以建立连接之后再做相应的操作，比如修改密码或新增数据库用户等。最后再把 my.ini 文件新加的那一行设置删除了，重启 MySQL 服务就生效了。
    

    

    





     