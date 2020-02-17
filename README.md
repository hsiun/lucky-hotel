### 项目概述
这个代码是一个培训课程的，这个项目是一个传统的Java Web Application（我这里传统的意思是没有使用Maven，直接运行在tomcat上的应用）。本来想把它改造成Maven的项目，直接使用maven的tomcat plugin来运行，后来想想还没有在Idea上实践过这种传统的Web项目，于是保留了它原来的样子。

### 技术概述
在技术上是使用spring+springMVC+mybaits+EasyUI+jQuery+Ajax。Spring作为bean容器，并将Mybaits作为bean组件交给Spring管理。Spring MVC作为前端控制器，提供Web应用的Controller。Mybatis管理做数据库管理。浏览器端的数据展示使用的是JSP+JSTL的技术，EasyUI作为展示框架，JQuery做数据交互用。

### 项目结构
src目录下的是java源代码，src下有两个文件夹，config配置文件，spring，springmvc，mybatis分别放在不同的目录下。spring，springmvc的配置文件通过web应用的web.xml文件加载，mybatis通过spring的配置文件加载，经过这一系列的步骤完成整个web应用的初始化。另一个是java代码，这个项目的业务逻辑比较简单，就是一些简单的增删改查操作。WebContent目录下是web应用相关的目录，resources下的是静态资源。WEB-INF下的web.xml就是web application的目录。views方的是前端相关jsp页面。mysql目录存放的是数据库初始化脚本，可用于Mysql8。


### 技术点
后台采用常见的分层设计，包含controller-service-dao三层，由于业务简单，这里没有值得一提的。interceptor是拦截器，在controller之前拦截http请求，执行相关的操作，这里执行的是登录相关拦截操作的。

```java
public class LoginInterceptor implements HandlerInterceptor {

	@Override
	public void afterCompletion(HttpServletRequest arg0,
			HttpServletResponse arg1, Object arg2, Exception arg3)
			throws Exception {
		// TODO Auto-generated method stub

	}

	@Override
	public void postHandle(HttpServletRequest arg0, HttpServletResponse arg1,
			Object arg2, ModelAndView arg3) throws Exception {
		// TODO Auto-generated method stub

	}

	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
			Object arg2) throws Exception {
		// TODO Auto-generated method stub
		String requestURI = request.getRequestURI();
		Object admin = request.getSession().getAttribute("account");
		if(admin == null){
			//表示未登录或者登录失效
			System.out.println("链接"+requestURI+"进入拦截器！");
			String header = request.getHeader("X-Requested-With");
			//判断是否是ajax请求
			if("XMLHttpRequest".equals(header)){
				//表示是ajax请求
				Map<String, String> ret = new HashMap<String, String>();
				ret.put("type", "error");
				ret.put("msg", "登录会话超时或还未登录，请重新登录!");
				response.getWriter().write(JSONObject.fromObject(ret).toString());
				return false;
			}
			//表示是普通链接跳转，直接重定向到登录页面
			response.sendRedirect(request.getServletContext().getContextPath() + "/home/login");
			return false;
		}
		
		return true;
	}
}
```

`HandlerInterceptor`是spring的一个扩展点，通过实现这个接口可以达到在http请求处理之前对其进行拦截的作用，spring用很多这样的扩展点，这真是spring扩展性好的表现。


controller层通过ModelAndView对象将数据传输到前端。前端采用jsp模版技术，jstl标签进行数据填充。


### IDEA导入

直接file -> new -> project from existing sources... 然后一直下一步就可以了

项目导入主要注意两点
project struct -> Artifacts -> Web Application Deployed配置相关Artifacts

右上角锤子的图标，edit configuration，在弹出的Run/Debug Configurations对话框中配置运行命令，tab页Deployment中配置前一步添加的artifacts。Deployment也还可以配置Application context，这里配置为/lucky_hotel，不这么配置的话会导致房型图片无法显示。


