# TestMavenRepository

[注意] 個々のブランチをmasterや他のブランチにマージしないでください 

## Use library
```
<project ...>
  <repositories>
    ...
    <repository>
      <id>github</id>
      <name>my github repository</name>
      <url>https://raw.githubusercontent.com/{USER_NAME}/{REPOSITORY_NAME}/{BRANCH_NAME}/</url>
    </repository>
   ...
  </repositories>
  ...
</project>
```

### MolecularFramework (0.1-b37-SNAPSHOT)
```
<repositories>
  ...
  <repository>
    <id>github</id>
    <name>my github repository</name>
    <url>https://raw.githubusercontent.com/{USER_NAME}/{REPOSITORY_NAME}/MolecularFramework-repo/</url>
  </repository>
  ...
</repositories>
```

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
<repositories>
  ...
  <repository>
    <id>github</id>
    <name>my github repository</name>
    <url>https://raw.githubusercontent.com/{USER_NAME}/{REPOSITORY_NAME}/GlycanBuilder2-repo/</url>
  </repository>
  ...
</repositories>
```

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
