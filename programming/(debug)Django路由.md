#Django路由

###第一种: 单一路由映射

	from django.conf.urls import url
	from django.contrib import admin
	from web.views import index

	urlpatterns = [
   		url(r'^admin/', admin.site.urls),
    	url(r'^index/', index),
	]

###正则匹配








全局的urls做分发

	url(r'^web/', include('web.urls'))