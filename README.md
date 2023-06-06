# MavenRepository

## Creating Authentication File

Create `settings.xml` under the .m2 directory and enter the following information:
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

If you don't want to manage the Github account information in the local Maven repository, create `.m2/settings.xml` under the project root and enter the authentication information mentioned above.
In this case, add `.m2` to the .gitignore file to prevent pushing the .m2 directory under the project root to GitHub.


## Editing Pom

### Referencing the Github Account
Add `<properties>` to use the above github account:
```
<properties>
  <github.global.server>github</github.global.server>
</properties>
```

### Repository Specification for Deployment
Repository Specification for Deployment
Add the repository that Maven should reference during deployment to `<distributionManagement>`.
You can enter any name for `<id>`, but it needs to match the repository specified in the `maven-deploy-plugin` below.
You can enter any information for `<name>` as desired.
`${project.build.directory}` represents the target folder, and it indicates the `mvn-repo` folder within it as the Github repository.

```
<distributionManagement>
  <repository>
    <id>internal.repo</id>
    <name>{YOUR_PROJECT_NAME} github repository</name>
    <url>file://${project.build.directory}/mvn-repo</url>
  </repository>
</distributionManagement>
```

### Specify deliverables for deployment

Add the following plugin to generate artifacts to deploy to the repository specified in `<distributionManagement>`.
`<altDeploymentRepository>` consists of `id::layout::url`, where id is the id declared in `distributionManagement`, layout is the layout of the repository and url is the repository address.
When the deploy command is executed, the artifacts are generated in the file hierarchy of `target/mvn-repo`.

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
    ...
  </plugins>
</build>
```

### 
Add the `site-maven-plugin` to specify detailed information about the repository to be deployed.
The details of each tag are shown in the table below:

|Tag|Description|
|---|---|
|message|Message for committing to git|
|noJekyll|Whether to create a .nojekyll file|
|merge|Whether to overwrite existing artifacts|
|outputDirectory|The directory where artifacts are output （target/mvn-repo）|
|repositoryName|Repository name （MavenRepository）|
|repositoryOwner|Username of the repository（glycoinfo）|
|branch|Branch name（master）|
|includes|Specification of files to process|

```
<build>
  <plugins>
  ...
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
        <repositoryName>MavenRepository</repositoryName>
        <repositoryOwner>glycoinfo</repositoryOwner>
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

## Deploy
* Maven Local Repository

```
mvn deploy
```

* .m2 Directory in the project root
```
mvn -B -s .m2/settings.xml deploy
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

### GlycanBuilder2 (1.20.1)
```
<dependencies>
  ...
  <dependency>
  　　　　<groupId>org.eurocarbdb.glycanbuilder</groupId>
    <artifactId>glycanbuilder2</artifactId>
    <version>1.20.1</version>
  </dependency>
  ...
</dependencies>
```
