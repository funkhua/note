

# nginx location

	Syntax:	location [ = | ~ | ~* | ^~ ] uri { ... }
	location @name { ... }
	Default:	—
	Context:	server, location

###  location匹配
- 普通前缀匹配
  - \^\~：匹配以指定字符串开头的uri，普通字符匹配，不是正则匹配   location \^\~/oms-web
  - = ：普通字符精确匹配		location =/
  - None: /oms-web
- 正则匹配
  - ~ ：不区分大小写  		location ~\.(gif|jpg|jpeg)$
  - ~\*：区分大小写	                location ~\* \.(gif|jpg|jpeg)$
- "@" named location
  - 用于内部重定向

###  location匹配优先级
- 普通前缀匹配，按照最长匹配优先，跟在文件中编辑的上下顺序无关；
- 正则匹配，按照在配置文件中自上而下，最先匹配优先，一旦匹配就终止；
- 正则匹配优先于普通前缀匹配，但是让步 = 精确匹配，如果最长前缀匹配位置用修饰符 "^ ~" ,匹配到后，将不再进行正则匹配。 
- @ location 只用于内部
###  总结
- nginx 对给定请求的的location匹配，首先是检查 普通前缀的匹配的配置，选择并记住具有**最长匹配前缀**的配置位置，然后，**按配置文件自上而下** 检查 正则表达式匹配的配置，对正则表达式的搜索终止于第一个匹配，并使用相应的配置，如果没有匹配正则表达式，则使用前面记住的前缀位置的配置。 
- **例外说明：** 
  - 如果最长前缀匹配位置用修饰符 "^ ~" ,匹配到后，将不再进行正则匹配。 
  - 如果“/”请求频繁发生，那么定义“location = /”将加快处理这些请求，因为搜索在第一次比较之后立即终止。 **注意精确匹配不能包含嵌套location**



参考：http://nginx.org/en/docs/http/ngx_http_core_module.html#location
