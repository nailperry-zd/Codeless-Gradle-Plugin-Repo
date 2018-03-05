# Codeless-Gradle-Plugin-Repo

该仓库为[LazierTracker](https://github.com/nailperry-zd/LazierTracker)工程的`buildsrc`插件对应的远程maven仓库

## 发布插件到github远程maven仓库

参考链接：

[Android Studio 将github作为远程maven仓库](http://blog.csdn.net/leilba/article/details/49367271#comments)
    
### 1. 打开插件源码`buildsrc`的`build.gradle`

	apply plugin: 'maven'
	uploadArchives{
	    repositories.mavenDeployer {
	        // maven savePath
	        def deployPath = file('/Users/nailperry/Desktop/Gradle_Plugin_Repo')
	        repository(url:"file://${deployPath.absolutePath}")
	        pom.project{
	            groupId "com.codeless"
	            artifactId "codeless-gradle-plugin"
	            version "0.0.1"
	        }
	    }
	}

这里面groupId可以填包名，artifactId可以填module名称，version是当前module的版本，可以填1.0 （这三个参数都可以自己决定，这个最后影响的是调用这个远程库的时候compile里面所要填写的值，比如按照图片的参数来的话，最后使用的时候得写 `classpath 'com.codeless:codeless-gradle-plugin:0.0.1'`）

### 2. 然后打开终端，cd到该gradle文件所在的目录，运行输入命令：
    
    gradle uploadArchives

### 3. 发布插件到github远程maven仓库

本地文件夹`/Users/nailperry/Desktop/Gradle_Plugin_Repo`即为一个本地`maven`仓库，将该本地`maven`仓库上传到`github`即得到一个远程`maven`仓库。

## 集成github远程maven仓库中的插件

### 1. Gradle集成

`project`的`build.grade`中添加`maven`地址和`classpath`

    buildscript {
    repositories {
        jcenter()
        maven {
            url "https://raw.githubusercontent.com/nailperry-zd/Codeless-Gradle-Plugin-Repo/master"
        }
    }
    dependencies {
        ...
        classpath 'com.codeless:codeless-gradle-plugin:0.0.1'
        ...
    }
}

`app`的`build.grade`中添加
	
	// 引用远程maven仓库的插件
    apply plugin: 'com.codeless'

### 2. 自定义参数

`app`的`build.grade`中添加如下代码，各配置项的含义请参考英文注释

```
codelessConfig {
    //this will determine the name of this plugin transform, no practical use.
    pluginName = 'myPluginTest'
    //turn this on to make it print help content, default value is true
    showHelp = true
    //this flag will decide whether the log of the modifying process be printed or not, default value is false
    keepQuiet = false
    //this is a kit feature of the plugin, set it true to see the time consume of this build
    watchTimeConsume = false

    //this is the most important part, 3rd party JAR packages that want our plugin to inject;
    //our plugin will inject package defined in 'AndroidManifest.xml' and 'butterknife.internal.butterknife.internal.DebouncingOnClickListener' by default.
    //structure is like ['butterknife.internal','com.a.c'], type is HashSet<String>.
    //You can also specify the name of the class;
    //example: ['com.xxx.xxx.BaseFragment']
    targetPackages = []
}
```

