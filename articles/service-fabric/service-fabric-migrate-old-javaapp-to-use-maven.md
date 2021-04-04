---
title: Migrera från Java SDK till maven
description: Uppdatera de äldre Java-programmen som använde Service Fabric Java-SDK:n för att hämta Service Fabric Java-beroenden från Maven. När du har slutfört konfigurationen kan de äldre Java-programmen skapa.
author: rapatchi
ms.topic: conceptual
ms.date: 08/23/2017
ms.custom: devx-track-java
ms.author: rapatchi
ms.openlocfilehash: 3efa51f5632dd5cdc274ea39df5178aa0351a01f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652304"
---
# <a name="update-your-previous-java-service-fabric-application-to-fetch-java-libraries-from-maven"></a>Uppdatera det tidigare Service Fabric Java-programmet för att hämta Java-bibliotek från Maven
Service Fabric Java-binärfiler har flyttats från Service Fabric Java SDK till maven-värd. Du kan använda **mavencentral** för att hämta de senaste Service Fabric Java-beroenden. Den här guiden hjälper dig att uppdatera befintliga Java-program som skapats för Service Fabric Java SDK med hjälp av en Yeoman-mall eller Sol förmörkelse som är kompatibla med den maven-baserade versionen.

## <a name="prerequisites"></a>Förutsättningar

1. Avinstallera först den befintliga Java SDK: n.

   ```bash
   sudo dpkg -r servicefabricsdkjava
   ```

2. Installera senaste Service Fabric CLI enligt instruktionerna [här](service-fabric-cli.md).

3. Se till att du har JDK 1,8 och Gradle installerat för att bygga och arbeta med Service Fabric Java-program. Om de inte har installerats än kan du köra följande för att installera JDK 1.8 (openjdk-8-jdk) och Gradle -

   ```bash
   sudo apt-get install openjdk-8-jdk-headless
   sudo apt-get install gradle
   ```

4. Uppdatera installations-/avinstallationsskripten för programmet så att de använder nya Service Fabric CLI enligt instruktionerna [här](service-fabric-application-lifecycle-sfctl.md). Du kan använda våra komma igång-[exempel](https://github.com/Azure-Samples/service-fabric-java-getting-started) som referens.

>[!TIP]
> När du har avinstallerat Service Fabric Java-SDK:n fungerar inte Yeoman. Följ kraven [här](service-fabric-create-your-first-linux-application-with-java.md) för att få igång Service Fabric Yeoman Java-mallgeneratorn.

## <a name="service-fabric-java-libraries-on-maven"></a>Service Fabric Java-bibliotek på Maven

Service Fabric Java-bibliotek har lagrats i Maven. Du kan lägga till beroendena i ``pom.xml`` eller ``build.gradle`` för dina projekt så att de använder Service Fabric Java-bibliotek från **mavenCentral**.

### <a name="actors"></a>Aktörer

Service Fabric-stöd för tillförlitliga aktörer för ditt program.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>Tjänster

Service Fabric-stöd för tillståndslös tjänst för ditt program.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>Andra

#### <a name="transport"></a>Transport

Transportnivåstöd för Service Fabric Java-program. Du behöver inte uttryckligen lägga till det här beroendet till tillförlitliga aktörer- eller tjänstprogram, om du inte programmerar på transportnivån.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>Fabric-stöd

Systemnivåstöd för Service Fabric, som kommunicerar med ursprunglig Service Fabric-körning. Du behöver inte uttryckligen lägga till det här beroendet till tillförlitliga aktörer- eller tjänstprogram. Det hämtas automatiskt från Maven när du inkluderar de andra beroendena ovan.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```

## <a name="migrating-service-fabric-stateless-service"></a>Migrera tillståndslös Service Fabric-tjänst

Om du vill kunna skapa en befintlig tillståndslös Service Fabric Java-tjänst med hjälp av Service Fabric-beroenden som hämtas från Maven, måste du uppdatera filen ``build.gradle`` i tjänsten. Tidigare såg det ut så här -

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':Interface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
}
.
.
.
task copyDeps <<{
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('libj*.so')
    }
}
```

För att hämta beroendena från Maven skulle **uppdaterade** ``build.gradle`` nu ha motsvarande delar enligt följande -

```gradle
repositories {
        mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':Interface')
    azuresf ('com.microsoft.servicefabric:sf-services:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": mpath)
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
   }
}
.
.
.
task copyDeps <<{
    copy {
        from("lib/")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*')
    }
}
```

I allmänhet kan du titta på något av våra komma igång-exempel för att få en uppfattning om hur byggskriptet skulle se ut för en tillståndslös Service Fabric Java-tjänst. Här är [build.gradle](https://github.com/Azure-Samples/service-fabric-java-getting-started/blob/master/reliable-services-actor-sample/build.gradle) för EchoServer-exemplet.

## <a name="migrating-service-fabric-actor-service"></a>Migrera Service Fabric-aktörstjänst

Om du vill kunna skapa ett befintligt Service Fabric Java-aktörsprogram med hjälp av Service Fabric-beroenden som hämtas från Maven, måste du uppdatera filen ``build.gradle`` i gränssnittspaketet och i tjänstpaketet. Om du har ett TestClient-paket måste du uppdatera det också. Så för aktören ``Myactor`` behöver du uppdatera på följande platser -

```
./Myactor/build.gradle
./MyactorInterface/build.gradle
./MyactorTestClient/build.gradle
```

#### <a name="updating-build-script-for-the-interface-project"></a>Uppdatera byggskript för gränssnittsprojektet

Tidigare såg det ut så här -

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
}
.
.
```

För att hämta beroendena från Maven skulle **uppdaterade** ``build.gradle`` nu ha motsvarande delar enligt följande -

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
```

#### <a name="updating-build-script-for-the-actor-project"></a>Uppdatera byggskript för aktörsprojektet

Tidigare såg det ut så här -

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
      baseName "myactor"
    destinationDir = file('./../myjavaapp/MyactorPkg/Code')
    }
}
.
.
.
task copyDeps<< {
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('libj*.so')
    }
    copy {
        from("../MyactorInterface/out/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
}
```

För att hämta beroendena från Maven skulle **uppdaterade** ``build.gradle`` nu ha motsvarande delar enligt följande -

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": mpath)
    baseName "myactor"
    destinationDir = file('../myjavaapp/MyactorPkg/Code')}
 }
.
.
.
task copyDeps<< {
      copy {
              from("lib/")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*')
      }
      copy {
              from("../MyactorInterface/out/lib")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*.jar')
      }
}
```

#### <a name="updating-build-script-for-the-test-client-project"></a>Uppdatera byggskript för testklientprojektet

Ändringar här liknar de ändringar som beskrivs i föregående avsnitt, det vill säga aktörsprojektet. Tidigare såg Gradle-skriptet ut så här -

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar
{
    manifest {
    attributes(
        'Main-Class': 'reliableactor.test.MyactorTestClient',
        "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    }
    baseName "myactor-test"
    destinationDir = file('out/lib')
}
.
.
.
task copyDeps<< {
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('libj*.so')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

För att hämta beroendena från Maven skulle **uppdaterade** ``build.gradle`` nu ha motsvarande delar enligt följande -

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar
{
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
    attributes(
                'Main-Class': 'reliableactor.test.MyactorTestClient',
                "Class-Path": mpath)
    baseName "myactor-test"
    destinationDir = file('./out/lib')
        }
}
.
.
.
task copyDeps<< {
        copy {
                from("lib/")
                into("./out/lib/lib")
                include('*')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

## <a name="next-steps"></a>Nästa steg

* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med hjälp av Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med Service Fabric-plugin-programmet för Eclipse](service-fabric-get-started-eclipse.md)
* [Interagera med Service Fabric-kluster med Service Fabric CLI](service-fabric-cli.md)
