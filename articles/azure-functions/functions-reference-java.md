---
title: Referens för Java-utvecklare för Azure Functions
description: Lär dig hur du utvecklar funktioner med Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 1ffbd760ae75605d75652b29d379420d6946aa8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96326462"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java Developer Guide

Den här guiden innehåller detaljerad information som hjälper dig att utveckla Azure Functions med Java.

Som Java-utvecklare, om du är nybörjare på Azure Functions, bör du först läsa någon av följande artiklar:

| Komma igång | Begrepp| 
| -- | -- |  
| <ul><li>[Java-funktion med Visual Studio Code](./create-first-function-vs-code-java.md)</li><li>[Java/maven-funktion med Terminal/kommando-prompt](./create-first-function-cli-java.md)</li><li>[Java-funktion med Gradle](functions-create-first-java-gradle.md)</li><li>[Java-funktion med Sol förmörkelse](functions-create-maven-eclipse.md)</li><li>[Java-funktion med IntelliJ idé](functions-create-maven-intellij.md)</li></ul> | <ul><li>[Utvecklarguide](functions-reference.md)</li><li>[Värdalternativ](functions-scale.md)</li><li>[Prestanda &nbsp; överväganden](functions-best-practices.md)</li></ul> |

## <a name="java-function-basics"></a>Grundläggande om Java-funktioner

En Java-funktion är en `public` metod, dekorerad med anteckningen `@FunctionName` . Den här metoden definierar posten för en Java-funktion och måste vara unik i ett visst paket. Paketet kan ha flera klasser med flera offentliga metoder som är kommenterade med `@FunctionName` . Ett enda paket distribueras till en Function-app i Azure. När du kör i Azure tillhandahåller Function-appen distributions-, körnings-och hanterings kontexten för dina enskilda Java-funktioner.

## <a name="programming-model"></a>Programmeringsmodell 

Begreppen [utlösare och bindningar](functions-triggers-bindings.md) är grundläggande för Azure Functions. Utlösare startar körningen av koden. Bindningar ger dig ett sätt att skicka data till och returnera data från en funktion utan att behöva skriva anpassad kod för data åtkomst.

## <a name="create-java-functions"></a>Skapa Java-funktioner

För att göra det enklare att skapa Java-funktioner finns det maven verktyg och archetypes som använder fördefinierade Java-mallar för att skapa projekt med en speciell funktions utlösare.    

### <a name="maven-based-tooling"></a>Maven verktyg

Följande utvecklings miljöer har Azure Functions verktyg som du kan använda för att skapa Java-funktions projekt: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

I artikel länkarna ovan visas hur du skapar dina första funktioner med hjälp av en valfri IDE. 

### <a name="project-scaffolding"></a>Project-ramverk

Om du föredrar kommando rads utveckling från terminalen är det enklaste sättet att Autogenerera Java-baserade funktions projekt att använda `Apache Maven` archetypes. Java maven-archetype för Azure Functions publiceras _i följande_:_artifactId_: [com. Microsoft. Azure: Azure-Functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

Följande kommando genererar ett nytt Java-funktions projekt med hjälp av den här archetype:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

---

För att komma igång med den här archetype, se [Java-snabb](./create-first-function-cli-java.md)starten.

## <a name="folder-structure"></a>Mappstruktur

Här är mappstrukturen för ett Azure Functions Java-projekt:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

Du kan använda en delad [host.jspå](functions-host-json.md) en fil för att konfigurera Function-appen. Varje funktion har sin egen kod fil (. Java) och bindnings konfigurations fil (function.jspå).

Du kan använda mer än en funktion i ett projekt. Undvik att placera dina funktioner i separata jar v7. `FunctionApp`I mål katalogen är vad som distribueras till din Function-app i Azure.

## <a name="triggers-and-annotations"></a>Utlösare och anteckningar

 Funktioner anropas av en utlösare, till exempel en HTTP-begäran, en timer eller en uppdatering av data. Din funktion måste bearbeta den utlösaren och alla andra indata för att skapa en eller flera utdata.

Använd de Java-anteckningar som ingår i [com. Microsoft. Azure. Azure. functions. Annotation. *-](/java/api/com.microsoft.azure.functions.annotation) paketet för att binda indata och utdata till dina metoder. Mer information finns i [referens dokumenten för Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Du måste konfigurera ett Azure Storage konto i [local.settings.jsför](./functions-run-local.md#local-settings-file) att köra Azure Blob Storage, Azure Queue Storage eller Azure Table Storage-utlösare lokalt.

Exempel:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {HttpMethod.POST},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Här är det genererade `function.json` som motsvarar [Azure-Functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="java-versions"></a>Java-versioner

Den version av Java som används när du skapade Function-appen där funktioner körs i Azure anges i pom.xml-filen. Maven-archetype genererar för närvarande en pom.xml för Java 8, som du kan ändra innan du publicerar. Java-versionen i pom.xml måste matcha den version som du har utvecklat lokalt och testat din app på. 

### <a name="supported-versions"></a>Versioner som stöds

I följande tabell visas aktuella Java-versioner som stöds för varje huvud version av Functions-körningen, efter operativ system:

| Funktions version | Java-versioner (Windows) | Java-versioner (Linux) |
| ----- | ----- | --- |
| 3.x | 11 <br/>8 | 11 <br/>8 |
| 2x | 8 | saknas |

Om du inte anger en Java-version för distributionen kommer maven archetype att standardvärdet Java 8 under distributionen till Azure.

### <a name="specify-the-deployment-version"></a>Ange distributions version

Du kan kontrol lera vilken version av Java som är mål för maven-archetype med hjälp av `-DjavaVersion` parametern. Värdet för den här parametern kan vara antingen `8` eller `11` . 

Maven-archetype genererar en pom.xml som är riktad mot den angivna Java-versionen. Följande element i pom.xml visar vilken Java-version som ska användas:

| Element |  Java 8-värde | Java 11-värde | Beskrivning |
| ---- | ---- | ---- | --- |
| **`Java.version`** | 1.8 | 11 | Den version av Java som används av maven-compiler-plugin-programmet. |
| **`JavaVersion`** | 8 | 11 | Java-version som körs av Function-appen i Azure. |

I följande exempel visas inställningarna för Java 8 i relevanta avsnitt i pom.xml-filen:

#### `Java.version`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="12-19" highlight="14":::

#### `JavaVersion`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="80":::

> [!IMPORTANT]
> Du måste ha variabeln JAVA_HOME-miljövariabeln korrekt angiven till JDK-katalogen som används vid kompilering av kod med hjälp av Maven. Kontrol lera att JDK-versionen är minst lika hög som `Java.version` inställningen. 

### <a name="specify-the-deployment-os"></a>Ange distributions-OS

Med maven kan du också ange det operativ system som din Function-App körs på i Azure. Använd- `os` elementet för att välja operativ system. 

| Element |  Windows | Linux | Docker |
| ---- | ---- | ---- | --- |
| **`os`** | windows | Linux | Docker |

I följande exempel visas operativ system inställningen i `runtime` avsnittet i pom.xml-filen:

:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="79":::
 
## <a name="jdk-runtime-availability-and-support"></a>Tillgänglighet och support för JDK-körning 

För lokal utveckling av Java Functions-appar laddar du ned och använder lämpligt [Azul Zulu Enterprise för Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java-JDKs från [Azul-system](https://www.azul.com/downloads/azure-only/zulu/). Azure Functions använder en Java JDK-körning i Azul när du distribuerar din Function-app till molnet.

[Azure-support](https://azure.microsoft.com/support/) för problem med JDKs-och Function-appar är tillgänglig med ett [kvalificerat support](https://azure.microsoft.com/support/plans/)avtal.

## <a name="customize-jvm"></a>Anpassa JVM

Med funktioner kan du anpassa den Java Virtual Machine (JVM) som används för att köra Java-funktioner. [Följande JVM-alternativ](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) används som standard:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Du kan ange ytterligare argument i en app-inställning med namnet `JAVA_OPTS` . Du kan lägga till appinställningar till din Function-app distribuerad till Azure i Azure Portal eller Azure CLI.

> [!IMPORTANT]  
> I förbruknings planen måste du också lägga till inställningen WEBSITE_USE_PLACEHOLDER med värdet 0 för att anpassningen ska fungera. Den här inställningen ökar kall start tider för Java functions.

### <a name="azure-portal"></a>Azure Portal

I [Azure Portal](https://portal.azure.com)använder du [fliken program inställningar](functions-how-to-use-azure-function-app-settings.md#settings) för att lägga till `JAVA_OPTS` inställningen.

### <a name="azure-cli"></a>Azure CLI

Du kan använda kommandot [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) för att ange `JAVA_OPTS` , som i följande exempel:

# <a name="consumption-plan"></a>[Förbrukningsplan](#tab/consumption/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --settings "JAVA_OPTS=-Djava.awt.headless=true" \
    "WEBSITE_USE_PLACEHOLDER=0" \
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="consumption-plan"></a>[Förbrukningsplan](#tab/consumption/cmd)

```azurecli-interactive
az functionapp config appsettings set ^
    --settings "JAVA_OPTS=-Djava.awt.headless=true" ^
    "WEBSITE_USE_PLACEHOLDER=0" ^
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="dedicated-plan--premium-plan"></a>[Dedikerad plan/Premium-plan](#tab/dedicated+premium/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --settings "JAVA_OPTS=-Djava.awt.headless=true" \
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="dedicated-plan--premium-plan"></a>[Dedikerad plan/Premium-plan](#tab/dedicated+premium/cmd)

```azurecli-interactive
az functionapp config appsettings set ^
    --settings "JAVA_OPTS=-Djava.awt.headless=true" ^
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

---

Det här exemplet aktiverar konsol löst läge. Ersätt `<APP_NAME>` med namnet på din Function-app och `<RESOURCE_GROUP>` med resurs gruppen. 

## <a name="third-party-libraries"></a>Bibliotek från tredje part 

Azure Functions stöder användningen av bibliotek från tredje part. Som standard paketeras alla beroenden som anges i projekt `pom.xml` filen automatiskt under [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) målet. För bibliotek som inte har angetts som beroenden i `pom.xml` filen placerar du dem i en `lib` katalog i funktionens rot Katalog. Beroenden som placeras i `lib` katalogen läggs till i system klass inläsaren vid körning.

`com.microsoft.azure.functions:azure-functions-java-library`Beroendet anges i classpath som standard och behöver inte tas med i `lib` katalogen. [Azure-Functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) lägger också till beroenden som anges [här](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) i classpath.

## <a name="data-type-support"></a>Data typs stöd

Du kan använda vanliga gamla Java-objekt (Pojo), typer som definieras i `azure-functions-java-library` eller primitiva data typer, till exempel sträng och heltal som ska bindas till indata-eller utgående bindningar.

### <a name="pojos"></a>Pojo

För att konvertera indata till POJO, använder [Azure-Functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) [Gson](https://github.com/google/gson) -biblioteket. POJO-typer som används som indata till funktioner ska vara `public` .

### <a name="binary-data"></a>Binära data

Binda binära indata eller utdata till `byte[]` , genom att ange `dataType` fältet i function.jspå `binary` :

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Om du förväntar dig null-värden använder du `Optional<T>` .

## <a name="bindings"></a>Bindningar

Bindningar för indata och utdata ger ett deklarativ sätt att ansluta till data i din kod. En funktion kan ha flera bindningar för indata och utdata.

### <a name="input-binding-example"></a>Exempel på indatamängds bindning

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { HttpMethod.PUT }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData,
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Du anropar den här funktionen med en HTTP-begäran. 
- HTTP-begärans nytto Last skickas som en `String` för argumentet `inputReq` .
- En post hämtas från Table Storage och skickas som `TestInputData` argumentet `inputData` .

Om du vill ta emot en batch med indata kan du binda till `String[]` ,, `POJO[]` `List<String>` eller `List<POJO>` .

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Den här funktionen utlöses när det finns nya data i den konfigurerade händelsehubben. Eftersom `cardinality` är inställt på `MANY` , tar funktionen emot en batch med meddelanden från händelsehubben. `EventData` från Event Hub konverteras till `TestEventData` för att köra funktionen.

### <a name="output-binding-example"></a>Exempel på utgående bindning

Du kan binda en utgående bindning till returvärdet med hjälp av `$return` . 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Om det finns flera utgående bindningar använder du returvärdet för bara en av dem.

Om du vill skicka flera utmatnings värden använder du `OutputBinding<T>` definierade i `azure-functions-java-library` paketet. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

Du anropar den här funktionen på en HttpRequest. Den skriver flera värden till Queue Storage.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage och HttpResponseMessage

 Dessa definieras i `azure-functions-java-library` . De är hjälp typer för att arbeta med HttpTrigger-funktioner.

| Specialiserad typ      |       Mål        | Typisk användning                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-utlösare     | Hämtar metod, rubriker eller frågor |
| `HttpResponseMessage` | HTTP-utgående bindning | Returnerar annan status än 200   |

## <a name="metadata"></a>Metadata

Några utlösare skickar [Utlös ande metadata](./functions-triggers-bindings.md) tillsammans med indata. Du kan använda anteckningen `@BindingName` för att binda till Utlös ande metadata.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
I föregående exempel `queryValue` är bindningen till frågesträngparametern i URL: en för `name` http-begäran `http://{example.host}/api/metadata?name=test` . Här är ett annat exempel som visar hur du binder till `Id` från kö-utlösarens metadata.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> Namnet som anges i anteckningen måste matcha metadata-egenskapen.

## <a name="execution-context"></a>Körningskontext

`ExecutionContext`, som definieras i `azure-functions-java-library` , innehåller hjälp metoder för att kommunicera med Functions-körningen. Mer information finns i [referens artikeln för ExecutionContext](/java/api/com.microsoft.azure.functions.executioncontext).

### <a name="logger"></a>Loggar

Använd `getLogger` , definierad i `ExecutionContext` , för att skriva loggar från funktions kod.

Exempel:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Visa loggar och spåra

Du kan använda Azure CLI för att strömma Java STDOUT-och stderr-loggning, samt annan program loggning. 

Så här konfigurerar du din Function-app för att skriva program loggning med hjälp av Azure CLI:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

---

Om du vill strömma logga utdata för din Function-app med hjälp av Azure CLI öppnar du en ny kommando tolk, bash eller terminalserversession och anger följande kommando:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

---

Kommandot [AZ webapp log pilslut](/cli/azure/webapp/log) har alternativ för att filtrera utdata med hjälp av `--provider` alternativet. 

Om du vill hämta loggfilerna som en enda ZIP-fil med hjälp av Azure CLI öppnar du en ny kommando tolk, bash eller terminalserversession och anger följande kommando:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Du måste ha aktiverat fil system loggning i Azure Portal eller Azure CLI innan du kör det här kommandot.

## <a name="environment-variables"></a>Miljövariabler

I funktioner visas [appinställningar](functions-app-settings.md), till exempel tjänst anslutnings strängar, som miljövariabler under körningen. Du kan komma åt de här inställningarna med hjälp av `System.getenv("AzureWebJobsStorage")` .

I följande exempel hämtas [program inställningen](functions-how-to-use-azure-function-app-settings.md#settings)med nyckeln med namnet `myAppSetting` :

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> Värdet för AppSetting FUNCTIONS_EXTENSION_VERSION ska vara ~ 2 eller ~ 3 för en optimerad kall start upplevelse.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Functions Java-utveckling finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)
* Lokal utveckling och fel sökning med [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md)och [Sol förmörkelse](functions-create-maven-eclipse.md)
* [Fjärrfelsökning Java Functions med Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven-plugin-program för Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Effektivisera skapandet av funktioner genom `azure-functions:add` målet och Förbered en mellanlagringsplats för distribution av [zip-filer](deployment-zip-push.md).
