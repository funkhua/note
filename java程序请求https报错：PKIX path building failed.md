### java程序请求https报错：PKIX path building failed


	javax.net.ssl.SSLHandshakeException: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
		at sun.security.ssl.Alerts.getSSLException(Unknown Source)
		at sun.security.ssl.SSLSocketImpl.fatal(Unknown Source)
		at sun.security.ssl.Handshaker.fatalSE(Unknown Source)
		at sun.security.ssl.Handshaker.fatalSE(Unknown Source)
		at sun.security.ssl.ClientHandshaker.serverCertificate(Unknown Source)
		at sun.security.ssl.ClientHandshaker.processMessage(Unknown Source)
		at sun.security.ssl.Handshaker.processLoop(Unknown Source)
		at sun.security.ssl.Handshaker.process_record(Unknown Source)
		at sun.security.ssl.SSLSocketImpl.readRecord(Unknown Source)
		at sun.security.ssl.SSLSocketImpl.performInitialHandshake(Unknown Source)
		at sun.security.ssl.SSLSocketImpl.startHandshake(Unknown Source)
		at sun.security.ssl.SSLSocketImpl.startHandshake(Unknown Source)
		at sun.net.www.protocol.https.HttpsClient.afterConnect(Unknown Source)
		at sun.net.www.protocol.https.AbstractDelegateHttpsURLConnection.connect(Unknown Source)
		at sun.net.www.protocol.http.HttpURLConnection.getOutputStream(Unknown Source)
		at sun.net.www.protocol.https.HttpsURLConnectionImpl.getOutputStream(Unknown Source)
		at com.sim.util.Http2Client.post(Http2Client.java:37)
		at com.sim.task.OrderStatusTask.excute(OrderStatusTask.java:97)
		at sun.reflect.GeneratedMethodAccessor58.invoke(Unknown Source)
		at sun.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source)
		at java.lang.reflect.Method.invoke(Unknown Source)
		at org.springframework.util.MethodInvoker.invoke(MethodInvoker.java:269)
		at org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean$MethodInvokingJob.executeInternal(MethodInvokingJobDetailFactoryBean.java:257)
		at org.springframework.scheduling.quartz.QuartzJobBean.execute(QuartzJobBean.java:75)
		at org.quartz.core.JobRunShell.run(JobRunShell.java:202)
		at org.quartz.simpl.SimpleThreadPool$WorkerThread.run(SimpleThreadPool.java:573)
	Caused by: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
		at sun.security.validator.PKIXValidator.doBuild(Unknown Source)
		at sun.security.validator.PKIXValidator.engineValidate(Unknown Source)
		at sun.security.validator.Validator.validate(Unknown Source)
		at sun.security.ssl.X509TrustManagerImpl.validate(Unknown Source)
		at sun.security.ssl.X509TrustManagerImpl.checkTrusted(Unknown Source)
		at sun.security.ssl.X509TrustManagerImpl.checkServerTrusted(Unknown Source)
		... 22 more
	Caused by: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
		at sun.security.provider.certpath.SunCertPathBuilder.engineBuild(Unknown Source)
		at java.security.cert.CertPathBuilder.build(Unknown Source)
		... 28 more

### 之前一直稳定访问，合作伙伴，换了https证书，坑啊! 估计是java没有识别根证书颁发机构(CA)的SSL证书。用浏览器访问https是可以的。

### 解决方法：将证书添加到java cacerts文件中，让程序接受它。

1. 打开谷歌浏览器，访问程序调用的https的地址。
2. 下载证书。
	1. ![](https://i.imgur.com/uE94KKa.png)
	2. 选择--“详细信息”--“复制到文件”，依次 下一步，将证书保存下来。
3. 通过keytool工具将证书导入java cacerts文件中。
	1. java的cacerts一般放在 /PATH/jre/lib/security/cacerts
	2. 备份 # sudo cp /PATH/jre/lib/security/cacerts /PATH/jre/lib/security/cacerts.bak.DATE
	3. keytool -import -alias TEST -keystore /opt/jre/lib/security/cacerts -file /home/roger/test.crt
	4. 这时候提示 “Enter keystore password”： **默认密码为：changeit**
	5. 问你是否导入记得回答 **yes**
4. 重启JVM，问题解决。
