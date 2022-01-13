# MavenRepository

## Pomの編集
```
<properties>
  <github.global.server>github</github.global.server>
</properties>
```

```
<distributionManagement>
  <repository>
    <id>internal.repo</id>
    <name>{YOUR_PROJECT_NAME} github repository</name>
    <url>file://${project.build.directory}/mvn-repo</url>
  </repository>
</distributionManagement>
```

```
<build>
  <plugins>
    <plugin>
      <artifactId>maven-deploy-plugin</artifactId>
      <version>2.8.2</version>
      <configuration>
        <altDeploymentRepository>internal.repo::default::file://${project.build.directory}/mvn-repo</altDeploymentRepository>
      </configuration>
    </plugin>
      
    <plugin>
      <groupId>com.github.github</groupId>
      <artifactId>site-maven-plugin</artifactId>
      <version>0.12</version>
      <configuration>
        <message>Maven artifacts for ${project.version}</message>
        <noJekyll>true</noJekyll>
        <merge>true</merge>
        <outputDirectory>${project.build.directory}/mvn-repo</outputDirectory>
        <branch>refs/heads/master</branch>
        <includes><include>**/*</include></includes>
        <repositoryName>{MAVEN_REPOSITORY_NAME}</repositoryName>
        <repositoryOwner>{ACCOUNT_NAME}</repositoryOwner>
      </configuration>
      <executions>
        <execution>
          <goals>
            <goal>site</goal>
          </goals>
          <phase>deploy</phase>
        </execution>
      </executions>
    </plugin>
    ...
  </plugins>
</build>
```

## 認証用ファイルの作成

* Mavenローカルリポジトリ\
.m2の直下に`settings.xml`を作成して以下の情報を入力する
```
<settings xmlns="http://maven.Apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.Apache.org/SETTINGS/1.0.0
                      http://maven.Apache.org/xsd/settings-1.0.0.xsd">
    <servers>
        <server>
            <id>github</id>
            <username>{GITHUB_USER_NAME}</username>
            <password>{GITHUB_ACCESS_TOKEN}</password>
        </server>
    </servers>
</settings>
```

MavenのローカルリポジトリでGithubのアカウント情報を管理したくない場合は、プロジェクト直下に`.m2/settings.xml`を作成して上記の認証情報を入力する\
**.gitignoreに.m2/と記載しておくことを強くお勧めします**

## Deploy
* Mavenローカルリポジトリ
```
$ mvn deploy
```

* プロジェクト直下の.m2ディレクトリ
```
$ mvn -B -s .m2/settings.xml deploy
```

|Option|Description|
| -- | -- |
| -s |Alternate path for the user settings file|
| -B |Run in non-interactive (batch) mode| 

## Use library
```
<project ...>
  <repositories>
    ...
    <repository>
      <id>github</id>
      <name>my github repository</name>
      <url>https://raw.githubusercontent.com/glycoinfo/MavenRepository/master/</url>
    </repository>
   ...
  </repositories>
  ...
</project>
```

### MolecularFramework (0.1-b37-SNAPSHOT)
```
<dependencies>
  ...
  <dependency>
    <groupId>org.eurocarbdb</groupId>
　  　　　<artifactId>MolecularFramework</artifactId>
    <version>0.1-b37-SNAPSHOT</version>
  </dependency>
  ...
</dependencies>
```

### GlycanBuilder2 (1.15.0)
```
<dependencies>
  ...
  <dependency>
  　　　　<groupId>org.eurocarbdb.glycanbuilder</groupId>
    <artifactId>glycanbuilder2</artifactId>
    <version>1.15.0</version>
  </dependency>
  ...
</dependencies>
```
