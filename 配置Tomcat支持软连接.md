

#配置tomcat对软连接的支持。
 
###tomcat默认不支持软连接，需要为Context 增加allowLinking="true"。



####Tomcat 4：
	Xml代码：
	<Context path="/test" docBase="/usr/local/pjt/images">  
  		<Resources className="org.apache.naming.resources.FileDirContext" allowLinking="true"/>  
	</Context> 

####Tomcat 7/6/5：   
	Xml代码：
	<Context path="/test" docBase="/usr/local/pjt/images" allowLinking="true"/>  
 

####Tomcat 8：
	Xml代码
    <Context path="/test" docBase="/usr/local/pjt/images">  
      <Resources allowLinking="true"/> 
    </Context>  
 