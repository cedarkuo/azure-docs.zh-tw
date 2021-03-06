---
title: 管理 JAR 相依性-Azure HDInsight
description: 本文討論管理 HDInsight 應用程式的 JAVA 封存（JAR）相依性的最佳作法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77135729"
---
# <a name="jar-dependency-management-best-practices"></a>JAR 相依性管理最佳作法

安裝在 HDInsight 叢集上的元件會相依于協力廠商程式庫。 通常，這些內建元件會參考 Guava 之類的通用模組特定版本。 當您提交具有相依性的應用程式時，可能會導致相同模組的不同版本之間發生衝突。 如果您先在路徑類型中參考的元件版本，內建元件可能會因為版本不相容而擲回例外狀況。 不過，如果內建元件會先將其相依性插入至路徑，您的應用程式可能`NoSuchMethod`會擲回類似的錯誤。

為避免版本衝突，請考慮為應用程式相依性加上陰影。

## <a name="what-does-package-shading-mean"></a>封裝陰影的意義為何？
[網底] 提供了一種方式來包含及重新命名相依性。 它會重新放置類別，並重寫受影響的位元組程式碼和資源，以建立相依性的私用複本。

## <a name="how-to-shade-a-package"></a>如何為封裝加上陰影？

### <a name="use-uber-jar"></a>使用 uber-jar
Uber 是單一的 jar 檔案，其中包含應用程式 jar 和其相依性。 Uber 中的相依性預設不會加上陰影。 在某些情況下，如果其他元件或應用程式參考這些程式庫的不同版本，這可能會導致版本衝突。 若要避免這種情況，您可以建立 Uber Jar 檔案，其中有部分（或所有）的相依性已加上陰影。

### <a name="shade-package-using-maven"></a>使用 Maven 為封裝加上陰影
Maven 可以建立以 JAVA 和 Scala 撰寫的應用程式。 Maven-陰影-外掛程式可協助您輕鬆建立陰影的 uber jar。

下列範例顯示`pom.xml`已更新的檔案，可使用 maven-陰影-外掛程式來為封裝加上陰影。  XML 區段`<relocation>…</relocation>`會藉由移動對應`com.google.guava`的 JAR `com.google.shaded.guava`檔案專案並重寫受影響的位元組程式碼，將類別從封裝移至封裝中。

變更`pom.xml`之後，您可以執行`mvn package`來建立陰影的 uber jar。

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>使用 SBT 為封裝加上陰影
SBT 也是 Scala 和 JAVA 的組建工具。 SBT 沒有如 maven-陰影-外掛程式的陰影外掛程式。 您可以修改`build.sbt`檔案來為封裝加上陰影。 

例如，若要為`com.google.guava`網底，您可以將下列命令新增至`build.sbt`檔案：

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

接著，您可以`sbt clean`執行`sbt assembly`和以建立陰影的 jar 檔案。 

## <a name="next-steps"></a>後續步驟

* [使用 HDInsight IntelliJ 工具](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [為 IntelliJ 中的 Spark 建立 Scala Maven 應用程式](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
