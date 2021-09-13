1、下载

2、解压

3、配置环境

（1）没ZSH

```
vim ~/.bash_profile
#添加
# MAVEN
# maven
export PATH=$PATH:/Users/apple/myroot/soft/apache-maven-3.8.1/bin

# 然后
source ~/.bash_profile
```

（2）有ZSH

```
vim ~/.zshrc
#添加
# MAVEN
export M2_HOM=/Users/apple/myroot/soft/apache-maven-3.8.1
export PATH=$PATH:$M2_HOME/bin

# 然后
source ~/.zshrc
```

4、配置阿里云镜像

```
<mirrors>
      <mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>central</mirrorOf>
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
      </mirror>
  </mirrors>
 
  <profiles>
         <profile>
              <id>jdk-1.8</id>
              <activation>
                <activeByDefault>true</activeByDefault>
                <jdk>1.8</jdk>
              </activation>
              <properties>
                <maven.compiler.source>1.8</maven.compiler.source>
                <maven.compiler.target>1.8</maven.compiler.target>
                <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
              </properties>
         </profile>
  </profiles>
```

