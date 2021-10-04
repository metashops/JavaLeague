#### TCP协议

TCP 协议（传输控制协议）是面向连接的协议，是可靠传输的，所以在连接前必须建立TCP连接，也就是“三次握手”

#### UDP协议

UDP是不具有可靠性的数据报协议

三次握手



#### InetAddress类

相关方法：

获取本机InetAddress对象getLocalHost

根据指定主机名/域名获取IP地址对象getByName

获取InetAddress对象的主机名getHostName

获取InetAddress对象的地址getHostAddress

```java
public class API_ {
    public static void main(String[] args) throws UnknownHostException {
        // 1、获取本机InetAddress对象
        InetAddress localHost = InetAddress.getLocalHost();
        System.out.println(localHost);
        // 2、根据指定主机名/域名获取IP地址对象getByName
        //InetAddress byName = InetAddress.getByName("adpdfles-MP");
        //3、根据域名获取IP地址对象getByName,如：www.baidu.com
        InetAddress byName1 = InetAddress.getByName("www.baidu.com");
        System.out.println(byName1);
        //4、通过InetAddress对象获取对应地址
        String hostAddress = byName1.getHostAddress();
        System.out.println(hostAddress);
        //5、InetAddress对象获取对应主机名/或域名
        String hostName = byName1.getHostName();
        System.out.println(hostName);

    }
}
```



#### HTTP 协议

#### 案例：

A主机发送数据——》B主机，中间会发生什么事情？



