---
title: poi-tl生成Word
categories: Java
tags: java, poi-tl
date: 2018-09-01 19:55:54
---

*  文档: http://deepoove.com/poi-tl/

* 添加依赖
1.  poi 版本必须大于3.14(我用的是3.17)
2. ooxml 版本必须大于1.2(我用的是1.3)
<!-- more -->
```
 <!-- 模版引擎用到的jar包 -->
    <dependency>
      <groupId>org.apache.poi</groupId>
      <artifactId>poi</artifactId>
      <version>3.17</version>
    </dependency>
    
    <dependency>
      <groupId>com.deepoove</groupId>
      <artifactId>poi-tl</artifactId>
      <version>1.3.1</version>
    </dependency>

    <dependency>
      <groupId>org.apache.poi</groupId>
      <artifactId>poi-ooxml</artifactId>
      <version>3.17</version>
    </dependency>

    <dependency>
      <groupId>org.apache.poi</groupId>
      <artifactId>poi-scratchpad</artifactId>
      <version>3.17</version>
    </dependency>

    <dependency>
      <groupId>org.apache.poi</groupId>
      <artifactId>ooxml-schemas</artifactId>
      <version>1.3</version>
    </dependency>
```

注意: 你可能遇到以下几个问题
* poi版本过低：使用 poi 3.14 以上 
```
Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/poi/POIXMLTypeLoader
	at org.openxmlformats.schemas.wordprocessingml.x2006.main.DocumentDocument$Factory.parse(Unknown Source)
	at org.apache.poi.xwpf.usermodel.XWPFDocument.onDocumentRead(XWPFDocument.java:136)
	at org.apache.poi.POIXMLDocument.load(POIXMLDocument.java:166)
	at org.apache.poi.xwpf.usermodel.XWPFDocument.<init>(XWPFDocument.java:125)
	at com.deepoove.poi.NiceXWPFDocument.<init>(NiceXWPFDocument.java:85)
	at com.deepoove.poi.XWPFTemplate.compile(XWPFTemplate.java:129)
	at com.deepoove.poi.XWPFTemplate.compile(XWPFTemplate.java:110)
	at com.deepoove.poi.XWPFTemplate.compile(XWPFTemplate.java:78)
	at com.deepoove.poi.XWPFTemplate.compile(XWPFTemplate.java:74)
	at core.JingzhouDayReportDaemon.saveData2Word(JingzhouDayReportDaemon.java:187)
	at core.JingzhouDayReportDaemon.run(JingzhouDayReportDaemon.java:45)
	at core.JingzhouDayReportDaemon.main(JingzhouDayReportDaemon.java:289)
Caused by: java.lang.ClassNotFoundException: org.apache.poi.POIXMLTypeLoader
	at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:331)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
	... 12 more
```

```
/-- Encapsulated exception ------------\
java.lang.NoClassDefFoundError: org/apache/poi/UnsupportedFileFormatException
	at java.lang.ClassLoader.defineClass1(Native Method)
	at java.lang.ClassLoader.defineClass(ClassLoader.java:763)
	at java.security.SecureClassLoader.defineClass(SecureClassLoader.java:142)
	at org.apache.catalina.loader.WebappClassLoaderBase.findClassInternal(WebappClassLoaderBase.java:3205)
	at org.apache.catalina.loader.WebappClassLoaderBase.findClass(WebappClassLoaderBase.java:1373)
	at org.apache.catalina.loader.WebappClassLoaderBase.loadClass(WebappClassLoaderBase.java:1861)
	at org.apache.catalina.loader.WebappClassLoaderBase.loadClass(WebappClassLoaderBase.java:1735)
	at java.lang.ClassLoader.defineClass1(Native Method)
	at java.lang.ClassLoader.defineClass(ClassLoader.java:763)
	at java.security.SecureClassLoader.defineClass(SecureClassLoader.java:142)
	at org.apache.catalina.loader.WebappClassLoaderBase.findClassInternal(WebappClassLoaderBase.java:3205)
	at org.apache.catalina.loader.WebappClassLoaderBase.findClass(WebappClassLoaderBase.java:1373)
	at org.apache.catalina.loader.WebappClassLoaderBase.loadClass(WebappClassLoaderBase.java:1861)
	at org.apache.catalina.loader.WebappClassLoaderBase.loadClass(WebappClassLoaderBase.java:1735)
	at org.apache.poi.openxml4j.opc.OPCPackage.open(OPCPackage.java:324)
	at org.apache.poi.util.PackageHelper.open(PackageHelper.java:37)
	at org.apache.poi.xwpf.usermodel.XWPFDocument.<init>(XWPFDocument.java:116)
	at com.deepoove.poi.NiceXWPFDocument.<init>(NiceXWPFDocument.java:85)
	at com.deepoove.poi.XWPFTemplate.compile(XWPFTemplate.java:129)
	at com.deepoove.poi.XWPFTemplate.compile(XWPFTemplate.java:110)
	at com.deepoove.poi.XWPFTemplate.compile(XWPFTemplate.java:78)
	at com.deepoove.poi.XWPFTemplate.compile(XWPFTemplate.java:74)
	at dao.analysis.daytimeReport.DaytimeReportDAO.saveData2Word(DaytimeReportDAO.java:702)
	at action.v3.Analysis.DayTimeReportAction.queryDayReport(DayTimeReportAction.java:369)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at ognl.OgnlRuntime.invokeMethod(OgnlRuntime.java:871)
	at ognl.OgnlRuntime.callAppropriateMethod(OgnlRuntime.java:1294)
	at ognl.ObjectMethodAccessor.callMethod(ObjectMethodAccessor.java:68)
	at com.opensymphony.xwork2.ognl.accessor.XWorkMethodAccessor.callMethodWithDebugInfo(XWorkMethodAccessor.java:117)
	at com.opensymphony.xwork2.ognl.accessor.XWorkMethodAccessor.callMethod(XWorkMethodAccessor.java:108)
	at ognl.OgnlRuntime.callMethod(OgnlRuntime.java:1370)
	at ognl.ASTMethod.getValueBody(ASTMethod.java:91)
	at ognl.SimpleNode.evaluateGetValueBody(SimpleNode.java:212)
	at ognl.SimpleNode.getValue(SimpleNode.java:258)
	at ognl.Ognl.getValue(Ognl.java:467)
	at ognl.Ognl.getValue(Ognl.java:431)
	at com.opensymphony.xwork2.ognl.OgnlUtil$3.execute(OgnlUtil.java:352)
	at com.opensymphony.xwork2.ognl.OgnlUtil.compileAndExecuteMethod(OgnlUtil.java:404)
	at com.opensymphony.xwork2.ognl.OgnlUtil.callMethod(OgnlUtil.java:350)
	at com.opensymphony.xwork2.DefaultActionInvocation.invokeAction(DefaultActionInvocation.java:430)
	at com.opensymphony.xwork2.DefaultActionInvocation.invokeActionOnly(DefaultActionInvocation.java:290)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:251)
	at org.apache.struts2.interceptor.DeprecationInterceptor.intercept(DeprecationInterceptor.java:41)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at org.apache.struts2.interceptor.debugging.DebuggingInterceptor.intercept(DebuggingInterceptor.java:256)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at com.opensymphony.xwork2.interceptor.DefaultWorkflowInterceptor.doIntercept(DefaultWorkflowInterceptor.java:168)
	at com.opensymphony.xwork2.interceptor.MethodFilterInterceptor.intercept(MethodFilterInterceptor.java:98)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at com.opensymphony.xwork2.validator.ValidationInterceptor.doIntercept(ValidationInterceptor.java:265)
	at org.apache.struts2.interceptor.validation.AnnotationValidationInterceptor.doIntercept(AnnotationValidationInterceptor.java:76)
	at com.opensymphony.xwork2.interceptor.MethodFilterInterceptor.intercept(MethodFilterInterceptor.java:98)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at com.opensymphony.xwork2.interceptor.ConversionErrorInterceptor.intercept(ConversionErrorInterceptor.java:138)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at com.opensymphony.xwork2.interceptor.ParametersInterceptor.doIntercept(ParametersInterceptor.java:229)
	at com.opensymphony.xwork2.interceptor.MethodFilterInterceptor.intercept(MethodFilterInterceptor.java:98)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at com.opensymphony.xwork2.interceptor.ParametersInterceptor.doIntercept(ParametersInterceptor.java:229)
	at com.opensymphony.xwork2.interceptor.MethodFilterInterceptor.intercept(MethodFilterInterceptor.java:98)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at com.opensymphony.xwork2.interceptor.StaticParametersInterceptor.intercept(StaticParametersInterceptor.java:191)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at org.apache.struts2.interceptor.MultiselectInterceptor.intercept(MultiselectInterceptor.java:73)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at org.apache.struts2.interceptor.DateTextFieldInterceptor.intercept(DateTextFieldInterceptor.java:125)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at org.apache.struts2.interceptor.CheckboxInterceptor.intercept(CheckboxInterceptor.java:91)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at org.apache.struts2.interceptor.FileUploadInterceptor.intercept(FileUploadInterceptor.java:253)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at com.opensymphony.xwork2.interceptor.ModelDrivenInterceptor.intercept(ModelDrivenInterceptor.java:100)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at com.opensymphony.xwork2.interceptor.ScopedModelDrivenInterceptor.intercept(ScopedModelDrivenInterceptor.java:141)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at com.opensymphony.xwork2.interceptor.ChainingInterceptor.intercept(ChainingInterceptor.java:145)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at com.opensymphony.xwork2.interceptor.PrepareInterceptor.doIntercept(PrepareInterceptor.java:171)
	at com.opensymphony.xwork2.interceptor.MethodFilterInterceptor.intercept(MethodFilterInterceptor.java:98)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at com.opensymphony.xwork2.interceptor.I18nInterceptor.intercept(I18nInterceptor.java:140)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at org.apache.struts2.interceptor.ServletConfigInterceptor.intercept(ServletConfigInterceptor.java:164)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at com.opensymphony.xwork2.interceptor.AliasInterceptor.intercept(AliasInterceptor.java:193)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at com.opensymphony.xwork2.interceptor.ExceptionMappingInterceptor.intercept(ExceptionMappingInterceptor.java:189)
	at com.opensymphony.xwork2.DefaultActionInvocation.invoke(DefaultActionInvocation.java:245)
	at org.apache.struts2.impl.StrutsActionProxy.execute(StrutsActionProxy.java:54)
	at org.apache.struts2.dispatcher.Dispatcher.serviceAction(Dispatcher.java:575)
	at org.apache.struts2.dispatcher.ng.ExecuteOperations.executeAction(ExecuteOperations.java:81)
	at org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter.doFilter(StrutsPrepareAndExecuteFilter.java:99)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:241)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:208)
	at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:218)
	at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:110)
	at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:506)
	at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:169)
	at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:103)
	at org.apache.catalina.valves.AccessLogValve.invoke(AccessLogValve.java:962)
	at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:116)
	at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:452)
	at org.apache.coyote.http11.AbstractHttp11Processor.process(AbstractHttp11Processor.java:1087)
	at org.apache.coyote.AbstractProtocol$AbstractConnectionHandler.process(AbstractProtocol.java:637)
	at org.apache.tomcat.util.net.AprEndpoint$SocketProcessor.doRun(AprEndpoint.java:2536)
	at org.apache.tomcat.util.net.AprEndpoint$SocketProcessor.run(AprEndpoint.java:2525)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
	at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
	at java.lang.Thread.run(Thread.java:745)
Caused by: java.lang.ClassNotFoundException: org.apache.poi.UnsupportedFileFormatException
	at org.apache.catalina.loader.WebappClassLoaderBase.loadClass(WebappClassLoaderBase.java:1892)
	at org.apache.catalina.loader.WebappClassLoaderBase.loadClass(WebappClassLoaderBase.java:1735)
	... 113 more
```

* 参考文档: https://blog.csdn.net/moshowgame/article/details/81702029
