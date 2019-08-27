# Guacamole远程桌面显示
一: 安装Guacamole服务端
二: 配置Guacamole服务端
	2.1: 默认身份验证方法: guacamole所有的用户和连接是从user-mapping.xml文件中读取和存储
	该方法用于guacamole小型部署和一种相对简单的方式验证guacamole是否设置正确
	2.2: 主要配置
	GUACAMOLE_HOME目录--默认位于该目录/etc/guacamole/ 和其文件夹之下的guacamole.properties文件
	/etc/guacamole/
		|
		|------extensions            所有的Guacamole扩展的安装位置, Guacamole会在启动的时候加载所有的.jar文件
		|------guacamole.properties  Guacamole.war.1.0.0的主要配置文件, 文件中的属性指示Guacamole如何连接guacd, 配置已安装的身份验证
		|------lib                   扩展程序所需的库目录, 如数据库驱动扩展需要数据库驱动库, 将放在这个目录
		|------user-mapping.xml      Guacamole系统中所有的用户和连接配置
		|------logback.xml           Guacamole的logback日志系统记录配置文件
		|------guacd.conf            Guacd服务的主要配置, 绑定主机和端口, ssl加密等 --> (似乎替换了guacamole.properties文件功能)
	2.3: 可重写GUACAMOLE_HOME文件夹的位置
		2.3.1: 在项目的主目录创建一个名为 .guacamole的文件夹, 目录存在, 自动为GUACAMOLE_HOME文件夹
		2.3.2: 使用环境变量设置GUACAMOLE_HOME的路径
		2.3.3: 系统属性guacamole.home指定备用目录的完整路径
	2.4: guacamole.properties -- 是Guacamole或Guacamole扩展读取的所有配置属性的公共位置
	文件是可选的, 如果默认值不足或者是为其他扩展提供配置, 则以下字段属性必须有值
		api-session-timeout
			尽管不活动，允许Guacamole会话（身份验证令牌）保持有效的时间（以分钟为单位）。如果省略，Guacamole会话将在60分钟不活动后到期。
		available-languages
			逗号分隔的语言键白名单，允许在Guacamole界面中作为显示语言选择
		enable-environment-properties
			如果设置为“true”，Guacamole将首先评估其环境以获取任何给定配置属性的值，然后再使用指定的值guacamole.properties或回退到默认值。通过启用此选项，您可以使用环境变量轻松覆盖任何其他配置属性。
		guacd-hostname
			Guacamole代理守护进程（guacd）正在监听的主机。如果省略，Guacamole将假设guacd正在侦听localhost。
		guacd-port
			Guacamole代理守护进程（guacd）正在侦听的端口。如果省略，Guacamole将假设guacd正在侦听端口4822。
		guacd-ssl
			如果设置为“true”，Guacamole将需要在Web应用程序和guacd之间进行SSL/TLS加密。默认情况下，Web应用程序和guacd之间的通信将不加密。请注意，如果启用此选项，则还必须配置 guacd以通过命令行选项使用SSL。这些选项记录在guacd的联机帮助页中。您将需要SSL证书和私钥。
	2.5: RealVNC和及其派生的TigerVNC 与 Guacamole兼容性好
三: Guacamole的一些命令
	查看guacd的日志  tail -f ./messages |grep guacd


四: Guacamole-js
	1. Class: Client
	var guac = new Guacamole.Client( new Guacamole.HTTPTunnel("/portal/guacd/tunnel") );
	var params = { hostname:"192.168.3.168", port:"5901", password:"123456" };
	guac.connect(JSON.stringify(params));

	2. Guacamole需要的连接字符串
	var connnetParams = "GUAC_ID=" + "自定义"
                + "&GUAC_WIDTH=" + Math.floor(optimal_width * 0.5)
                + "&GUAC_HEIGHT=" + Math.floor(optimal_height * 0.5)
                + "&GUAC_TYPE=c"
                + "&GUAC_AUDIO=audio/L8"
                + "&GUAC_AUDIO=audio/L16"
                + "&GUAC_IMAGE=image/jpeg"
                + "&GUAC_IMAGE=image/png"
                + "&GUAC_IMAGE=image/webp"
                + "&GUAC_DPI=" + Math.floor(optimal_dpi)
    3. 