

配置tomcat对软连接的支持。

tomcat默认不支持软连接，需要为Context 增加allowLinking="true" 



#### Tomcat 4：

- Xml代码：

  ```
  <Context path="/test" docBase="/data/www/pjt/images">  
      <Resources className="org.apache.naming.resources.FileDirContext" allowLinking="true"/>  
  </Context> 
  ```

#### Tomcat 7/6/5： 
- Xml代码：

  ```
  <Context path="/test" docBase="/data/www/pjt/images" allowLinking="true"/> 
  ```

- 参考链接：

  ```
  https://tomcat.apache.org/tomcat-7.0-doc/config/context.html
  https://tomcat.apache.org/tomcat-6.0-doc/config/context.html
  ```

#### Tomcat 8/9：

- Xml代码

  ```
  <Context path="/test" docBase="/data/www/pjt/images">  
      <Resources allowLinking="true"/> 
  </Context>  
  ```

- 参考链接：

  ```
  https://tomcat.apache.org/tomcat-8.0-doc/config/resources.html
  https://tomcat.apache.org/tomcat-9.0-doc/config/resources.html
  
  If the value of this flag is true, symlinks will be allowed inside the web application, pointing to resources inside or outside the web application base path. If not specified, the default value of the flag is false.
  
  NOTE: This flag MUST NOT be set to true on the Windows platform (or any other OS which does not have a case sensitive filesystem), as it will disable case sensitivity checks, allowing JSP source code disclosure, among other security problems.
  ```
