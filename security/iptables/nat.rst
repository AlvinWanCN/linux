############################################
NAT (Network Address Translation)
############################################



.. contents::


linux系统下允许包转发
`````````````````````````

临时开启
---------------
.. code-block:: bash

    echo 1 > /proc/sys/net/ipv4/ip_forward

永久开启
---------------

.. code-block::

    echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
    sysctl -p


将本地所有tcp端口请求转发到目标IP地址上
````````````````````````````````````````

这里我们本服务器IP地址是192.168.127.83， 目标服务器是一台vmware esxi，IP地址是192.168.127.60

进行如下设置后，就可以通过访问192.168.127.83来访问到我们的vmware esxi了。

.. code-block:: bash

    iptables -t nat -I PREROUTING -d 192.168.127.83 -p tcp -j DNAT --to-destination 192.168.127.60
    iptables -t nat -I POSTROUTING -s 192.168.127.0/24 -p tcp -j SNAT --to-source 192.168.127.83


本地端口转发为目标服务器器指定端口
`````````````````````````````````


转发一个80端口
-----------------

将本地192.168.38.1端口上的80转发到192.168.127.51的80上。

.. code-block:: bash

 iptables -t nat -I PREROUTING -d 192.168.38.1 -p tcp --dport 80 -j DNAT --to-destination 192.168.127.51:80

上面这条规则配置了如何过去转发本地80到目标服务器，但是数据回来之后还要伪装修改一下才能返回给客户端，需要还需要添加一条。
所有来自192.168.38.0网段的对于目标服务器192.168.127.51的tcp端口为80的请求，都伪装成本服务器
如果使用-s -d -p --dport -o 之类的参数，就是默认对所有都开放。不指定网段，不指定端口，那么所有通过该服务器装发出去的对所有端口的请求，都会变成该服务器发出的请求。

.. code-block:: bash

    iptables -t nat -I POSTROUTING -s 192.168.38.0/24 -d 192.168.127.51 -p tcp --dport 80 -j MASQUERADE

或者可以用下面的命令，将-j MASQUERADE换成--to-source 192.168.127.1，效果是一样的，只是指定了ip。 这两条命令用其中一条就可以了，

.. code-block:: bash

    iptables -t nat -I POSTROUTING -s 192.168.38.0/24 -d 192.168.127.51 -p tcp --dport 80 -j SNAT --to-source 192.168.127.1

转发vmware esxi的三个端口
-------------------------------


本地服务器IP 192.168.127.74， 目标服务器IP 192.168.127.60， 目标服务器是vmware esxi 服务器，我们需要转发三个端口。

.. code-block:: bash

    iptables -t nat -I PREROUTING -d 192.168.127.74 -p tcp  --dport 902 -j DNAT --to-destination 192.168.127.60:902
    iptables -t nat -I PREROUTING -d 192.168.127.74 -p tcp  --dport 80 -j DNAT --to-destination 192.168.127.60:80
    iptables -t nat -I PREROUTING -d 192.168.127.74 -p tcp  --dport 443 -j DNAT --to-destination 192.168.127.60:443

    iptables -t nat -I POSTROUTING -s 192.168.127.0/24 -p tcp -j SNAT --to-source 192.168.127.74

然后就可以通过访问192.168.127.74来访问到192.168.127.60的esxi服务了。


本地端口转发到本地其他端口
``````````````````````````````````

将80端口转发到8080
---------------------------

.. code-block:: bash

    iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080
