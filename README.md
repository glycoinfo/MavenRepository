# MavenRepository

## 認証用ファイルの作成

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
この場合、.gitignoreに`.m2`を追加し、プロジェクト直下の.m2ディレクトリをgithubへプッシュしないようにする

## Pomの編集

### githubアカウントの参照
上記のgithubアカウントを使用するために`<properties>`を追加する
```
<properties>
  <github.global.server>github</github.global.server>
</properties>
```

### デプロイ時のリポジトリ指定
デプロイの実行時にMavenが参照するリポジトリを`<distributionManagement>`に追加する\
`<id>`は好きなように名前の入力が可能だが、下記の`maven-deploy-plugin`で指定するリポジトリと統一する必要がある\
`<name>`は開発者が好きな情報を入力することが可能である\
`${project.build.directory}`はtargetフォルダを示し、その中の`mvn-repo`というフォルダをgithubリポジトリとして扱うことを示す
```
<distributionManagement>
  <repository>
    <id>internal.repo</id>
    <name>{YOUR_PROJECT_NAME} github repository</name>
    <url>file://${project.build.directory}/mvn-repo</url>
  </repository>
</distributionManagement>
```

### デプロイ用の成果物指定
以下のプラグインを追加して、`<distributionManagement>`で指定したリポジトリへデプロイする成果物を生成する\
`<altDeploymentRepository>`は`id::layout::url`で構成され、idは`distributionManagement`で宣言したid、layoutはリポジトリのレイアウト、URLはリポジトリのアドレスを示す\
デプロイコマンドを実行すると、`target/mvn-repo`のファイル階層で成果物が生成される
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
`site-maven-plugin`を追加して、デプロイするリポジトリの詳細情報を指定する\
個々のタグの詳細を下表に示す
|Tag|Description|
|---|---|
|message|gitにコミットする際のメッセージ|
|noJekyll|.nojekyllファイル作成の有無|
|merge|既存の成果物への上書きの有無|
|outputDirectory|成果物が出力されているディレクトリ（target/mvn-repo）|
|repositoryName|リポジトリ名（MavenRepository）|
|repositoryOwner|リポジトリのユーザ名（glycoinfo）|
|branch|ブランチ名（master）|
|includes|処理対象とするファイルの指定|
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
