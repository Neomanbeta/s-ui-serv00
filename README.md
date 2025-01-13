# s-ui-serv00
s-ui serv00 编译版

面板先开两个tcp端口（88888,99999），一个udp端口（77777），配置完成后，在实现trojan+argo | Hysteria 2 | Tuic 三个协议的情况下，只占用一个udp端口，两个TCP端口会空出来。

CF后台创建好两条通道，记下token备用，举例：通道1给面板使用，比如设置成s15sui.你的域名.us.kg内容是http://[::1]:88888 通道2给trojan+argo使用比如设置成s15tr.你的域名.us.kg内容是http://127.0.0.2:88888 备用

上传s-sui-freebsd-amd64-1.2.0-beta.2.zip到serv00解压，进入解压后的s-ui文件夹，chmod +x s-ui提权

然后 wget https://cloudflared.bowring.uk/binaries/cloudflared-freebsd-2024.11.1.7z 下载argo到当前目录。

用你喜欢的命令把cloudflared-freebsd-2024.11.1.7z里面的argo二进制文件解压缩出来，并且改名成argo

nohup ./argo tunnel --no-autoupdate run --token 你的token > /dev/null 2>&1 &  启动argo

openssl ecparam -genkey -name prime256v1 -out private.key 回车

openssl req -new -x509 -days 36500 -key private.key -out cert.crt -subj "/CN=www.bing.com" 回车

生成hy2需要使用的证书和密钥

./s-ui setting -port 88888 -subPort 99999 启动面板

浏览器打开s15.serv00.com:88888登录面板，默认的用户名和密码是admin

登录以后打开设置，修改 界面的地址为::1端口为88888  订阅的地址为127.0.0.1 端口为88888 然后点击保存，点击重启面板，这时候你就会打不开面板了。

现在用浏览器打开https://s15sui.你的域名.us.kg/app/就又能打开面板了，登录，打开面板里的TLS设置，添加，起个名字，比如udp，SNI添www.bing.com，证书文件路径和私钥文件路径指向之前生成的两个问题件

比如/home/你的用户名/s-ui/cert.crt和/home/你的用户名/s-ui/private.key

接下来s-ui必要的基础设置我就不赘述了，不会自己去看教程，看YouTube视频去学，我只说后面重要的。

在入站管理里面创建一个hy2，一个tuic，一个trojan节点

这里要注意的是trojan的节点(也可以是vless也可以是vmess，选trojan是为了照顾iPhone用户)的地址添127.0.0.2，端口88888

hy2的地址添你所在服务器三个入口里面没被封的第一个ip，tuic的地址添你所在服务器三个入口里面没被封的第二个ip，端口添77777，切记这两个ip不能添相同的，因为是复用端口。

不知道你用的服务器三个入口哪个通哪个不通，就去看老王的监控页面 https://status.eooce.com

现在说生成的链接导入客户端的事情，首先我没怎么用过s-ui，所以我不知道这是s-ui就这么设计的，还是说是bug，它里面生成的二维码和链接都是无法直接用的，因为每个节点都是只监听在指定的ip，它不会识别。

所以这就是为什么把订阅的地址指向127.0.0.1的原因，因为链接都不能直接用，所以订阅也没用。

trojan的链接复制到客户端，比如nekobox，编辑这个节点，在HOST和SNI里填入前面CF里创建的通道地址s15tr.你的域名.us.kg，地址改成优选域名或者优选ip，端口改成443，保存。

hy2的链接复制到客户端，编辑这个节点，SNI里面填入www.bing.com 勾选不检查服务器证书，保存。

tuic的链接复制到客户端，编辑这个节点，SNI里面填入www.bing.com 勾选不检查服务器证书，保存。

这样就完成了，在trojan+argo | Hysteria 2 | Tuic 三个协议的情况下，只占用了一个udp端口，服务器还剩下两个端口，你可以去干别的玩。

至于保活，推荐外部保活，方式形式多样，自行发挥吧，完结撒花。

PS：我自己是不推荐用任何面板的，所以后续s-ui更新，我不能保证我还会继续编译。
