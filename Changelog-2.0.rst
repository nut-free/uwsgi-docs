uWSGI 2.0
=========

更新日志 [20131230]

重要改动
*****************

已确定删除动态选项，以及broken_plugins目录

错误修复和改进
*************************

- 改进日志循环
- 不依赖unix信号在harakiri期间打印请求状态
- 为uid和gid添加魔术变量
- 各种Lua修复
- Riccardo Magliocchetti贡献的大量coverity管理（coverity-governed）的错误修复

新特性
********

--attach-daemon2
^^^^^^^^^^^^^^^^

这是一个用于配置外部守护进程的基于键值的选项。

已更新文档： :doc:`AttachingDaemons`

Linux setns()支持
^^^^^^^^^^^^^^^^^^^^^

uWSGI 1.9-2.0中的最大的改进之一是对Linux名字空间的完全支持。

这最后的补丁添加了对setns()系统调用的支持。

该系统调用允许一个进程“附加”到一个正在运行中的名字空间。

uWSGI实例可以通过unix socket公开它们的名字空间文件描述符 (基本上，它们是/proc/self/ns中的文件)。

外部实例连接到那个unix socket，然后自动进入映射到名字空间。

要以“名字空间服务器模式”生成一个实例，则使用 ``--setns-socket <addr>`` 选项

.. code-block:: sh

   uwsgi --setns-socket /var/run/ns.socket --unshare net,ipc,uts ...
   
   
要附加，则简单使用 ``--setns <addr>``


.. code-block:: sh

   uwsgi --setns /var/run/ns.socket ...
   
已更新文档： :doc:`Namespaces`

"私有"钩子
^^^^^^^^^^^^^^^

当uWSGI运行你的钩子时，它冗长打印整个钩子动作信息。这在某些场景（例如，当你作为root用户运行初始阶段，并且允许非特权用户访问日志时）下会是一个安全问题。

在你的动作前面加上前缀'!'会抑制完全日志记录：

.. code-block:: ini

   [uwsgi]
   hook-asap = !exec:my_secret_command

yajl库 (JSON解析器) 支持
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

直到现在，uWSGI只支持把jansson作为managing .js配置文件所需的json解析器。

你现在可以使用yajl库 (centos中可用) 来作为替代的JSON解析器 (将会自动检测)

Perl spooler支持
^^^^^^^^^^^^^^^^^^^^

perl/PSGI插件现在可以被用作一个spooler服务器：

.. code-block:: pl

   uwsgi::spooler(sub {
        my $args = shift;
        print Dumper($args);
        return -2;
   });


客户端部分仍旧缺失，因为我们需要修复一些内部API问题。

预期在2.0.1完成 ;)

网关可以移除特权
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

网关 (例如http router, sslrouter, rawrouter, forkptyrouter ...)现在可以由master独立移除特权。

目前，只有http/https/spdy路由器公开了新的选项 (``--http-uid/--http-gid``)

Subscriptions-governed SNI上下文
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

订阅系统现在支持3个额外的键 (你可以用--subscribe2选项来设置它们):

``sni_key``

``sni_cert``

``sni_ca``

它们所有都接收一个到相关ssl文件的路径。

看看： :doc:`SNI`


可用性
************

uWSGI 2.0已于20131230发布，你可以从这里下载：

http://projects.unbit.it/downloads/uwsgi-2.0.tar.gz
