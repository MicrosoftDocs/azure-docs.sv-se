---
title: Lägga till JVM-arg-Azure Monitor Application Insights för Java
description: Så här lägger du till JVM-arg som aktiverar Azure Monitor Application Insights för Java
ms.topic: conceptual
ms.date: 04/16/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 21465305e635cba7d8d4f912bcca2b0306b294e9
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233703"
---
# <a name="adding-the-jvm-arg---azure-monitor-application-insights-for-java"></a>Lägga till JVM-arg-Azure Monitor Application Insights för Java



## <a name="azure-environments"></a>Azure-miljöer

Konfigurera [app Services](../../app-service/configure-language-java.md#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Lägg till JVM `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` -arg någonstans före `-jar` , till exempel:

```
java -javaagent:path/to/applicationinsights-agent-3.0.1.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>Våren Boot via Docker start punkt

Om du använder formuläret *ledn* lägger du till parametern `"-javaagent:path/to/applicationinsights-agent-3.0.1.jar"` i parameter listan någonstans före `"-jar"` parametern, till exempel:

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.0.1.jar", "-jar", "<myapp.jar>"]
```

Om du använder *Shell* -formuläret lägger du till JVM-arg `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` någonstans före `-jar` , till exempel:

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.0.1.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat installerat via `apt-get` eller `yum`

Om du har installerat Tomcat via `apt-get` eller `yum` , bör du ha en-fil `/etc/tomcat8/tomcat8.conf` .  Lägg till den här raden i slutet av filen:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.1.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat installerat via hämta och zippa upp

Om du har installerat Tomcat via hämta och zippa upp [https://tomcat.apache.org](https://tomcat.apache.org) , bör du ha en-fil `<tomcat>/bin/catalina.sh` .  Skapa en ny fil i samma katalog med namnet `<tomcat>/bin/setenv.sh` med följande innehåll:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.1.jar"
```

Om filen `<tomcat>/bin/setenv.sh` redan finns ändrar du filen och lägger till `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` i `CATALINA_OPTS` .


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Köra Tomcat från kommando raden

Leta upp filen `<tomcat>/bin/catalina.bat` .  Skapa en ny fil i samma katalog med namnet `<tomcat>/bin/setenv.bat` med följande innehåll:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.1.jar
```

Citationstecken behövs inte, men om du vill inkludera dem är rätt placering:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.1.jar"
```

Om filen `<tomcat>/bin/setenv.bat` redan finns ändrar du bara filen och lägger till `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` i `CATALINA_OPTS` .

### <a name="running-tomcat-as-a-windows-service"></a>Köra Tomcat som en Windows-tjänst

Leta upp filen `<tomcat>/bin/tomcat8w.exe` .  Kör den körbara filen och Lägg till `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` på `Java Options` `Java` fliken.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Fristående server

Lägg till i `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` den befintliga `JAVA_OPTS` miljö variabeln i filen `JBOSS_HOME/bin/standalone.conf` (Linux) eller `JBOSS_HOME/bin/standalone.conf.bat` (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.1.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Domän Server

Lägg till i `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` den befintliga `jvm-options` i `JBOSS_HOME/domain/configuration/host.xml` :

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.1.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

Om du kör flera hanterade servrar på en enda värd måste du lägga till i `applicationinsights.agent.id` `system-properties` för varje `server` :

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

Det angivna `applicationinsights.agent.id` värdet måste vara unikt. Den används för att skapa en under katalog under katalogen applicationinsights, eftersom varje JVM-process behöver sin egen lokala applicationinsights config och den lokala applicationinsights-logg filen. Om du rapporterar till Central insamlaren `applicationinsights.properties` delas filen av de flera hanterade servrarna, och därför krävs det att du `applicationinsights.agent.id` åsidosätter `agent.id` inställningen i den delade filen. `applicationinsights.agent.rollup.id` kan anges på samma sätt i serverns `system-properties` om du behöver åsidosätta `agent.rollup.id` inställningen per hanterad server.


## <a name="jetty-9"></a>Jetty 9

Lägg till dessa rader i `start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.1.jar
```


## <a name="payara-5"></a>Payara 5

Lägg till i `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` den befintliga `jvm-options` i `glassfish/domains/domain1/config/domain.xml` :

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.1.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

Öppna hanterings konsolen gå till **servrar > WebSphere program servrar > program servrar**, Välj lämpliga program servrar och klicka på: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
I "allmänna JVM-argument" lägger du till följande:
```
-javaagent:path/to/applicationinsights-agent-3.0.1.jar
```
Efter det sparar och startar du om program servern.


## <a name="openliberty-18"></a>Openfrihet 18

Skapa en ny fil `jvm.options` i Server katalogen (till exempel `<openliberty>/usr/servers/defaultServer` ) och Lägg till den här raden:
```
-javaagent:path/to/applicationinsights-agent-3.0.1.jar
```
