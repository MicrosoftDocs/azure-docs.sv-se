---
title: 'Snabb start: din första Java-fråga'
description: I den här snabb starten följer du stegen för att aktivera resurs diagram maven-paket för Java och köra din första fråga.
ms.date: 03/30/2021
ms.topic: quickstart
ms.custom: devx-track-java
ms.openlocfilehash: 97c04cb8b8180034bdc5109446c79deb56e457c9
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223974"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-java"></a>Snabb start: kör din första resurs diagram fråga med Java

Det första steget för att använda Azure Resource Graph är att kontrol lera att nödvändiga maven-paket för Java är installerade. Den här snabb starten vägleder dig genom processen att lägga till maven-paket i Java-installationen.

I slutet av den här processen har du lagt till maven-paketen i Java-installationen och kör din första resurs diagram fråga.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- Kontrol lera att den senaste versionen av Azure CLI är installerad (minst **2.21.0**). Om den inte har installerats än kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

  > [!NOTE]
  > Azure CLI krävs för att aktivera Azure SDK för Java för att använda **CLI-baserad autentisering** i följande exempel. Information om andra alternativ finns i [klient biblioteket för Azure Identity för Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity).

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), version
  8.

- [Apache maven](https://maven.apache.org/), version 3,6 eller senare.

## <a name="create-the-resource-graph-project"></a>Skapa resurs diagrams projektet

Om du vill aktivera Java för att fråga Azure Resource Graph skapar och konfigurerar du ett nytt program med Maven och installerar de nödvändiga maven-paketen.

1. Initiera ett nytt Java-program med namnet "argQuery" med en [maven-archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html):

   ```cmd
   mvn -B archetype:generate -DarchetypeGroupId="org.apache.maven.archetypes" -DgroupId="com.Fabrikam" -DartifactId="argQuery"
   ```

1. Ändra kataloger till den nya projektmappen `argQuery` och öppna `pom.xml` i din favorit redigerare. Lägg till följande `<dependency>` noder under den befintliga `<dependencies>` noden:

   ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.2.4</version>
    </dependency>
    <dependency>
        <groupId>com.azure.resourcemanager</groupId>
        <artifactId>azure-resourcemanager-resourcegraph</artifactId>
        <version>1.0.0-beta.1</version>
    </dependency>
   ```

1. I `pom.xml` filen lägger du till följande `<properties>` nod under `<project>` noden bas för att uppdatera käll-och mål versionerna:

   ```xml
   <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. I `pom.xml` filen lägger du till följande `<build>` nod under `<project>` noden bas för att konfigurera målet och huvud klassen för projektet som ska köras.

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.codehaus.mojo</groupId>
               <artifactId>exec-maven-plugin</artifactId>
               <version>1.2.1</version>
               <executions>
                   <execution>
                       <goals>
                           <goal>java</goal>
                       </goals>
                   </execution>
               </executions>
               <configuration>
                   <mainClass>com.Fabrikam.App</mainClass>
               </configuration>
           </plugin>
       </plugins>
   </build>
   ```

1. Ersätt standardvärdet `App.java` i `\argQuery\src\main\java\com\Fabrikam` med följande kod och spara den uppdaterade filen:

   ```java
   package com.Fabrikam;
   
   import java.util.Arrays;
   import java.util.List;
   import com.azure.core.management.AzureEnvironment;
   import com.azure.core.management.profile.AzureProfile;
   import com.azure.identity.DefaultAzureCredentialBuilder;
   import com.azure.resourcemanager.resourcegraph.ResourceGraphManager;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequest;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequestOptions;
   import com.azure.resourcemanager.resourcegraph.models.QueryResponse;
   import com.azure.resourcemanager.resourcegraph.models.ResultFormat;
   
   public class App
   {
       public static void main( String[] args )
       {
           List<String> listSubscriptionIds = Arrays.asList(args[0]);
           String strQuery = args[1];
   
           ResourceGraphManager manager = ResourceGraphManager.authenticate(new DefaultAzureCredentialBuilder().build(), new AzureProfile(AzureEnvironment.AZURE));
   
           QueryRequest queryRequest = new QueryRequest()
               .withSubscriptions(listSubscriptionIds)
               .withQuery(strQuery);
           
           QueryResponse response = manager.resourceProviders().resources(queryRequest);
   
           System.out.println("Records: " + response.totalRecords());
           System.out.println("Data:\n" + response.data());
       }
   }
   ```

1. Bygg `argQuery` konsol programmet:

   ```bash
   mvn package
   ```

## <a name="run-your-first-resource-graph-query"></a>Köra din första Resource Graph-fråga

När Java-konsolen har skapats är det dags att testa en enkel resurs diagram fråga. Frågan returnerar de fem första Azure-resurserna med **namn** och **resurs typ** för varje resurs.

I varje anrop till finns `argQuery` det variabler som du måste ersätta med dina egna värden:

- `{subscriptionId}` – Ersätt med ditt prenumerations-ID
- `{query}` – Ersätt med din Azures resurs diagram fråga

1. Använd Azure CLI för att autentisera med `az login` .

1. Ändra kataloger till `argQuery` projektmappen som du skapade med föregående `mvn -B archetype:generate` kommando.

1. Kör din första Azure Resource Graph-fråga med Maven för att kompilera konsol programmet och skicka argumenten. `exec.args`Egenskapen identifierar argument efter blank steg. För att identifiera frågan som ett enda argument, radbryts vi med enkla citat tecken ( `'` ).

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5'"
   ```

   > [!NOTE]
   > Eftersom den här frågan inte tillhandahåller en sorterings modifierare, till exempel `order by` att köra den här frågan flera gånger, är det troligt att en annan uppsättning resurser skapas per begäran.

1. Ändra argumentet till `argQuery.exe` och ändra frågan till `order by` egenskapen **namn** :

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5 | order by name asc'"
   ```

   > [!NOTE]
   > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Den här kommando ordningen begränsar först frågeresultaten och beställer dem.

1. Ändra den sista parametern till `argQuery.exe` och ändra frågan till först `order by` egenskapen **namn** och sedan `limit` till de fem främsta resultaten:

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | order by name asc | limit 5'"
   ```

När den slutliga frågan körs flera gånger, förutsatt att ingenting i din miljö ändras, är resultatet som returneras konsekvent och beställt av egenskapen **namn** , men fortfarande begränsat till de fem främsta resultaten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort Java-konsol programmet och installerade paket kan du göra det genom att ta bort `argQuery` projektmappen.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat ett Java-konsolprogram med de nödvändiga resurs diagram paketen och kör din första fråga. Om du vill veta mer om det här resurs språket fortsätter du till sidan information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)