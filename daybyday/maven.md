# Maven

maven2 和 maven3 有不兼容的地方，maven3可以向后兼容。

## maven archetype 模板工程

> [http://maven.apache.org/archetype/maven-archetype-plugin/](http://maven.apache.org/archetype/maven-archetype-plugin/)

```bash
cd ${project.root.dir} # 进入要创建模板的 project
mvn archetype:create-from-project
cd target/generated-sources/archetype
mvn -Dmaven.test.skip=true clean install  # 安装道本地仓库

# 可以先进行更新，本地archetype存放在：${user.home}/.m2/archetype-catalog.xml中
#mvn archetype:update-local-catalog

mvn archetype:generate -DarchetypeCatalog=local # 由模板创建工程
```

## Q&A

### 构建过程中的异常 PluginContainerException

> [PluginContainerException](https://cwiki.apache.org/confluence/display/MAVEN/PluginContainerException)

在构建过程中，老是报出`Build Failure`，其实可以很简单的根据最下面`[Help 1]`就可以解决的，我倒腾了半天。**谨记** 多关注系统返回信息。 这还有个原因，内网的MAVEN仓库的包可能有问题，也或许是下载的时候网络出的问题，下载的包不完整，导致构建的时候出现 class miss

```text
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 2.653 s
[INFO] Finished at: 2015-10-23T10:00:12+08:00
[INFO] Final Memory: 12M/150M
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:2.17:test (default-test) on project gs-rest-service: Execution default-test of goal org.apache.maven.plugins:maven-surefire-plugin:2.17:test failed: Unable to load the mojo 'test' in the plugin 'org.apache.maven.plugins:maven-surefire-plugin:2.17'. A required class is missing: org/apache/maven/plugin/surefire/SurefireReportParameters
[ERROR] -----------------------------------------------------
[ERROR] realm =    plugin>org.apache.maven.plugins:maven-surefire-plugin:2.17
[ERROR] strategy = org.codehaus.plexus.classworlds.strategy.SelfFirstStrategy
[ERROR] urls[0] = file:/home/liyunpeng/.m2/repository/org/apache/maven/plugins/maven-surefire-plugin/2.17/maven-surefire-plugin-2.17.jar
[ERROR] urls[1] = file:/home/liyunpeng/.m2/repository/org/apache/maven/surefire/maven-surefire-common/2.17/maven-surefire-common-2.17.jar
[ERROR] urls[2] = file:/home/liyunpeng/.m2/repository/org/apache/maven/surefire/surefire-booter/2.17/surefire-booter-2.17.jar
[ERROR] urls[3] = file:/home/liyunpeng/.m2/repository/org/codehaus/plexus/plexus-utils/1.5.1/plexus-utils-1.5.1.jar
[ERROR] urls[4] = file:/home/liyunpeng/.m2/repository/org/apache/maven/reporting/maven-reporting-api/2.0.9/maven-reporting-api-2.0.9.jar
[ERROR] urls[5] = file:/home/liyunpeng/.m2/repository/org/apache/commons/commons-lang3/3.1/commons-lang3-3.1.jar
[ERROR] urls[6] = file:/home/liyunpeng/.m2/repository/org/apache/maven/surefire/surefire-api/2.17/surefire-api-2.17.jar
[ERROR] urls[7] = file:/home/liyunpeng/.m2/repository/org/apache/maven/plugin-tools/maven-plugin-annotations/3.2/maven-plugin-annotations-3.2.jar
[ERROR] Number of foreign imports: 1
[ERROR] import: Entry[import  from realm ClassRealm[maven.api, parent: null]]
[ERROR]
[ERROR] -----------------------------------------------------: org.apache.maven.plugin.surefire.SurefireReportParameters
[ERROR] -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/PluginContainerException
```

