---
title: Cordova生成安卓源码无法运行
categories: Android
tags: android
---

> 说明: cordova编译生成的android源码在Android Studio中运行, 无法找到
```
Gradle 'android' project refresh failed
Error:No installed build tools found. Install the Android build tools version 19.1.0 or higher.
```
解决方式一:
1. 全局搜索  availableBuildToolsVersions = getAvailableBuildTools()
![修改前的照片](https://img-blog.csdn.net/20180921102547408?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
修改为
```
availableBuildToolsVersions  = ['21.0.0','20.0.0','19.1.0','19.0.3'] as String[]
```
![修改后的源码截图](https://img-blog.csdn.net/20180921102905659?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
分析:

```

String doFindLatestInstalledBuildTools(String minBuildToolsVersion) {
    def availableBuildToolsVersions
    try {
    	// TODO: 获得可用的 build-tools
        availableBuildToolsVersions  = getAvailableBuildTools()
    } catch (e) {
        println "An exception occurred while trying to find the Android build tools."
        throw e
    }
    if (availableBuildToolsVersions.length > 0) {
        def highestBuildToolsVersion = availableBuildToolsVersions[0]
        if (compareVersions(highestBuildToolsVersion, minBuildToolsVersion) < 0) {
            throw new RuntimeException(
                "No usable Android build tools found. Highest installed version is " +
                highestBuildToolsVersion + "; minimum version required is " +
                minBuildToolsVersion + ".")
        }
        highestBuildToolsVersion
    } else {
    	// TODO 上面报错正是这抛出的异常, 为什么会走这呢, 因为没找到build-tools的版本
        throw new RuntimeException(
            "No installed build tools found. Install the Android build tools version " +
            minBuildToolsVersion + " or higher.")
    }
}

/**
 * 获取可用的build-tool
 * @return
 */
String[] getAvailableBuildTools() {
    def buildToolsDir = new File(getAndroidSdkDir(), "build-tools")
    buildToolsDir.list()
        .findAll { it ==~ /[0-9.]+/ } 
        .sort { a, b -> compareVersions(b, a) }
}

/**
 * 获取SDK位置
 * @return
 */
String getAndroidSdkDir() {
    def rootDir = project.rootDir
    def androidSdkDir = null
    String envVar = System.getenv("ANDROID_HOME")
    def localProperties = new File(rootDir, 'local.properties')
    String systemProperty = System.getProperty("android.home")
    if (envVar != null) {
        androidSdkDir = envVar
    } else if (localProperties.exists()) {
        Properties properties = new Properties()
        localProperties.withInputStream { instr ->
            properties.load(instr)
        }
        def sdkDirProp = properties.getProperty('sdk.dir')
        if (sdkDirProp != null) {
            androidSdkDir = sdkDirProp
        } else {
            sdkDirProp = properties.getProperty('android.dir')
            if (sdkDirProp != null) {
                androidSdkDir = (new File(rootDir, sdkDirProp)).getAbsolutePath()
            }
        }
    }
    if (androidSdkDir == null && systemProperty != null) {
        androidSdkDir = systemProperty
    }
    if (androidSdkDir == null) {
        throw new RuntimeException(
            "Unable to determine Android SDK directory.")
    }
    androidSdkDir
}

```



