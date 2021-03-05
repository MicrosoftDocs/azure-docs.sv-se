---
title: 'Självstudie: utveckla en Java-webbapp med Azure Cosmos DB och SQL API'
description: 'Självstudie: den här självstudien för Java-webbprogram visar hur du använder Azure Cosmos DB och SQL API för att lagra och få åtkomst till data från ett Java-program som finns på Azure Websites.'
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 930594d2c1af198aba3dae936cd0ce8fcf91c336
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214124"
---
# <a name="tutorial-build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Självstudie: bygga ett Java-webbprogram med Azure Cosmos DB och SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Den här självstudiekursen om webbprogram i Java beskriver hur du kan använda tjänsten [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) för att lagra och komma åt data från ett Java-program i Azure App Service Web Apps. I den här artikeln lär du dig att:

* Skapa ett enkelt JSP-program (JavaServer Pages) i Eclipse.
* Arbeta med Azure Cosmos DB-tjänsten med hjälp av [Azure Cosmos DB Java SDK](https://github.com/Azure/azure-documentdb-java).

I den här självstudien om Java visar vi hur du skapar en webbaserad aktivitetshanteringsapp där du kan skapa, hämta och markera aktiviteter som slutförda, enligt bilden nedan. Alla aktiviteter i att göra-listan lagras som JSON-dokument i Azure Cosmos DB.

:::image type="content" source="./media/sql-api-java-application/image1.png" alt-text="Java-app med att göra-lista":::

> [!TIP]
> Den här självstudien om apputveckling förutsätter att du har tidigare erfarenhet av Java. Om du inte har använt Java eller de [verktyg som krävs](#Prerequisites)tidigare, rekommenderar vi att du hämtar det fullständiga [göra]- https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app) projektet från GitHub och skapar det med hjälp av [anvisningarna i slutet av den här artikeln](#GetProject). När du har skapat det kan du läsa den här artikeln för information om koden i projektets sammanhang.  
>

## <a name="prerequisites-for-this-java-web-application-tutorial"></a><a id="Prerequisites"></a>Förhandskrav för den här självstudien för Java-webbprogram

Innan du påbörjar den här självstudien om apputveckling måste du ha följande:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Java Development Kit (JDK) 7+](/java/azure/jdk/).
* [Eclipse IDE för Java EE-utvecklare.](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [En Azure-webbplats med en aktiverad Java-körningsmiljö (t.ex. Tomcat eller Jetty).](../app-service/quickstart-java.md)

Om du installerar de här verktygen för första gången ger coreservlets.com en genom gång av installations processen i avsnittet snabb start i [självstudierna: installerar TomCat7 och använder den med Sol förmörkelse](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html) -artikeln.

## <a name="create-an-azure-cosmos-db-account"></a><a id="CreateDB"></a>Skapa ett Azure Cosmos DB-konto

Vi ska börja med att skapa ett Azure Cosmos DB-konto. Om du redan har ett konto eller om du använder Azure Cosmos DB-emulatorn för den här kursen kan du gå vidare till [Steg 2: Skapa Java JSP-programmet](#CreateJSP).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a name="create-the-java-jsp-application"></a><a id="CreateJSP"></a>Skapa Java JSP-programmet

Så här skapar du JSP-appen:

1. Först börjar vi med att skapa ett Java-projekt. Starta Eclipse och klicka på **Arkiv**, **Nytt** och slutligen **Dynamiskt webbprojekt**. Om du inte ser **dynamiskt webb projekt** som visas som ett tillgängligt projekt gör du följande: Klicka på **Arkiv**, klicka på **nytt**, klicka på **projekt**..., expandera **webb**, klicka på **dynamiskt webb projekt** och klicka på **Nästa**.
   
    :::image type="content" source="./media/sql-api-java-application/image10.png" alt-text="JSP Java-apputveckling":::

1. Ange ett projektnamn i rutan **Projektnamn** och välj eventuellt ett värde (t.ex. Apache Tomcat v7.0) i rullgardinsmenyn **Körningsmål** och klicka sedan på **Slutför**. När du väljer ett mål för körning kan du köra projektet lokalt genom Eclipse.

1. Expandera projektet i Eclipse i vyn Projektutforskaren. Högerklicka på **Webbinnehåll**, klicka på **Ny** och sedan på **JSP-fil**.

1. I dialogrutan **Ny JSP-fil** namnger du filen **index.jsp**. Behåll den överordnade mappen som **Webbinnehåll**, så som visas i nedanstående bild, och klicka sedan på **Nästa**.
   
    :::image type="content" source="./media/sql-api-java-application/image11.png" alt-text="Skapa en ny JSP-fil – självstudie om Java-webbapp":::

1. I dialogrutan **Välj JSP-mall** väljer du i den här självstudien **Ny JSP-fil (html)** och klickar sedan på **Slutför**.

1. När *index.jsp* -filen öppnas i Sol förmörkelse lägger du till text att visa **Hello World!** i det befintliga `<body>`-elementet när index.jsp-filen öppnas i Eclipse. Det uppdaterade `<body>`-innehållet bör likna följande kod:

   ```html
   <body>
     <% out.println("Hello World!"); %>
   </body>
   ```

1. Spara filen *index.jsp* .

1. Om du anger ett mål för körning i steg 2 kan du klicka på **Projekt** och **Kör** för att köra JSP-appen lokalt:

   :::image type="content" source="./media/sql-api-java-application/image12.png" alt-text="Hello World – självstudie om Java-app":::

## <a name="install-the-sql-java-sdk"></a><a id="InstallSDK"></a>Installera SQL Java SDK

Det enklaste sättet att hämta SQL Java SDK och dess beroenden är via [Apache Maven](https://maven.apache.org/). För att göra detta måste du konvertera projektet till ett Maven-projekt med hjälp av följande steg:

1. Högerklicka på ditt projekt i Projektutforskaren, klicka på **Konfigurera** och sedan på **Konvertera till Maven-projekt**.

1. I fönstret **Skapa ny Pom** accepterar du standardvärdena och klickar på **Slutför**.

1. Öppna filen pom.xml i **Projektutforskaren**.

1. På fliken **Beroenden** i panelen **Beroenden** klickar du på **Lägg till**.

1. I fönstret **Välj beroende** gör du följande:
   
   * I rutan **grupp-ID** anger du `com.azure` .
   * I rutan **artefakt-ID** anger du `azure-cosmos` .
   * I rutan **version** anger du `4.11.0` .
  
   Du kan också lägga till beroendet XML för grupp-ID och artefakt-ID direkt till *pom.xml* -filen:

   ```xml
   <dependency>
     <groupId>com.azure</groupId>
     <artifactId>azure-cosmos</artifactId>
     <version>4.11.0</version>
   </dependency>
   ```

1. Klicka på **OK** och maven kommer att installera SQL Java SDK eller spara pom.xml-filen.

## <a name="use-the-azure-cosmos-db-service-in-your-java-application"></a><a id="UseService"></a>Använda tjänsten Azure Cosmos DB i ditt Java-program

Nu ska vi lägga till modeller, vyer och kontrollanter i ditt webb program.

### <a name="add-a-model"></a> Lägga till en modell

Först ska vi definiera en modell i en ny fil *TodoItem. java*. `TodoItem`Klassen definierar schemat för ett objekt tillsammans med get-och set-metoderna:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/model/TodoItem.java":::

### <a name="add-the-data-access-objectdao-classes"></a>Lägg till data åtkomst objekts klasser (DAO)

Skapa ett data åtkomst objekt (DAO) för att skapa en sammanfattning av hur objekt ska Azure Cosmos DB. För att kunna spara ToDo-objekt till en samling måste klienten veta vilken databas och samling de ska sparas i (refererade med självlänkar). Det är vanligen bäst att cachelagra databasen och samlingen när det är möjligt, för att undvika ytterligare rundor till databasen.

1. Om du vill anropa tjänsten Azure Cosmos DB måste du instansiera ett nytt `cosmosClient` objekt. I allmänhet är det bäst att återanvända `cosmosClient` objektet i stället för att skapa en ny klient för varje efterföljande begäran. Du kan återanvända klienten genom att definiera den i- `cosmosClientFactory` klassen. Uppdatera värden och MASTER_KEY värden som du sparade i [steg 1](#CreateDB). Ersätt HOST-variabeln med med din URI och ersätt MASTER_KEY med din primära nyckel. Använd följande kod för att skapa `CosmosClientFactory` klassen i filen *CosmosClientFactory. java* :

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/CosmosClientFactory.java":::

1. Skapa en ny *TodoDao. java* -fil och Lägg till `TodoDao` klassen för att skapa, uppdatera, läsa och ta bort att göra-objekt:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/TodoDao.java":::

1. Skapa en ny *MockDao. java* -fil och Lägg till `MockDao` klassen, implementerar den här klassen `TodoDao` klassen för att utföra CRUD-åtgärder på objekten:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/MockDao.java":::

1. Skapa en ny *DocDbDao. java* -fil och Lägg till- `DocDbDao` klassen. Den här klassen definierar kod för att bevara TodoItems i behållaren, hämtar din databas och samling, om den finns, eller skapa en ny om den inte finns. I det här exemplet används [Gson](https://code.google.com/p/google-gson/) för att serialisera och deserialisera TodoItem vanliga gamla Java-objekt (Pojo) till JSON-dokument. För att kunna spara ToDo-objekt till en samling måste klienten veta vilken databas och samling de ska sparas i (refererade med självlänkar). Den här klassen definierar också hjälp funktionen för att hämta dokumenten med ett annat attribut (t. ex. "ID") i stället för själv länk. Du kan använda hjälp metoden för att hämta ett TodoItem JSON-dokument efter ID och sedan deserialisera det till en POJO.

   Du kan också använda `cosmosClient` klient objekt för att hämta en samling eller lista över TodoItems med en SQL-fråga. Slutligen definierar du DELETE-metoden för att ta bort en TodoItem från listan. Följande kod visar innehållet i `DocDbDao` klassen:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/DocDbDao.java":::

1. Skapa sedan en ny *TodoDaoFactory. java* -fil och Lägg till `TodoDaoFactory` klassen som skapar ett nytt DocDbDao-objekt:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/TodoDaoFactory.java":::

### <a name="add-a-controller"></a>Lägga till en styrenhet

Lägg till *TodoItemController* -kontrollanten i programmet. I det här projektet generar vi konstruktorn, get-metoder, set-metoder och ett verktyg via [Project Lombok](https://projectlombok.org/). Du kan också skriva koden manuellt eller låta IDE-generera den.:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/controller/TodoItemController.java":::

### <a name="create-a-servlet"></a>Skapa en servlet

Skapa sedan en servlet för att dirigera HTTP-förfrågningar till kontrollanten. Skapa filen *ApiServlet. java* och definiera följande kod under den:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/ApiServlet.java":::

## <a name="wire-the-rest-of-the-of-java-app-together"></a><a id="Wire"></a>Dra samman resten av Java-appen

Nu när vi har slutfört de roliga bitarna är allt det som återstår att bygga ett snabb användar gränssnitt och ansluta det till ditt DAO.

1. Du behöver ett webb användar gränssnitt för att visa för användaren. Nu ska vi skriva om *index.jsp* vi skapade tidigare med följande kod:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/index.jsp":::

1. Skriv slutligen vissa JavaScript-skript på klient sidan för att koppla ihop webb användar gränssnittet och servlet.

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/assets/todo.js":::

1. Nu återstår bara att testa appen. Kör appen lokalt och lägg till några Todo-objekt genom att fylla i namn och kategori och klicka på **Lägg till aktivitet**. När objektet visas kan du uppdatera om det är slutfört genom att växla till kryss rutan och klicka på **Uppdatera aktiviteter**.

## <a name="deploy-your-java-application-to-azure-web-sites"></a><a id="Deploy"></a>Distribuera Java-programmet till Azure Web Sites

Med Azure Web Sites är det enkelt att distribuera Java-appar. Allt du behöver göra är att exportera appen som en WAR-fil och antingen ladda upp den via källkontrollen (t.ex. Git) eller FTP.

1. Du exporterar appen som en WAR-fil genom att högerklicka på projektet i **Projektutforskaren**, klicka på **Exportera** och sedan på **WAR-fil**.

1. I fönstret **WAR-export** gör du så här:
   
   * I rutan webb projekt anger du Azure-Cosmos-Java-Sample.
   * Välj en plats där WAR-filen ska sparas i rutan Destination.
   * Klicka på **Finish**.

1. Nu när du har en WAR-fil laddar du bara upp den till katalogen **Webbappar** på Azure Web Sites. Anvisningar som beskriver hur du laddar upp filen finns i [Add a Java application to Azure App Service Web Apps](../app-service/quickstart-java.md) (Lägga till ett Java-program i Azure App Service Web Apps). När WAR-filen har överförts till webapps-katalogen, kommer körnings miljön att upptäcka att du har lagt till den och läser in den automatiskt.

1. Du kan visa den färdiga produkten genom att gå till `http://YOUR\_SITE\_NAME.azurewebsites.net/azure-cosmos-java-sample/` och börja lägga till aktiviteter!

## <a name="get-the-project-from-github"></a><a id="GetProject"></a>Hämta projektet från GitHub

Alla exempel i den här självstudien finns i projektet [Todo](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app) på GitHub. Om du vill importera Todo-projektet till Eclipse ska du se till att du har de program och resurser som anges i avsnittet [Förutsättningar](#Prerequisites) och sedan göra följande:

1. Installera [Project Lombok](https://projectlombok.org/). Lombok används för att generera konstruktorer, get-metoder och set-metoder i projektet. När du har laddat ned filen lombok.jar installerar du den genom att dubbelklicka på filen eller från kommandoraden.

1. Om Eclipse är öppet startar du om det för att läsa in Lombok.

1. I **Arkiv**-menyn i Eclipse klickar du på **Importera**.

1. I fönstret **Importera** klickar du på **Git**, **Projekt från Git** och **Nästa**.

1. På skärmen **Välj lagerkälla** klickar du på **Klona URI**.

1. Skriv https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app i rutan **URI** på skärmen **Source Git Repository** (Git-källagringsplats) och klicka på **Nästa**.

1. På skärmen **Val av gren** kontrollerar du att **main** är markerat och klickar sedan på **Nästa**.

1. På skärmen **Lokal destination** klickar du på **Bläddra** och väljer en mapp dit lagret kan kopieras och klickar sedan på **Nästa**.

1. På skärmen **Välj en guide för importprojekt** kontrollerar du att **Importera befintliga projekt** är markerat och klickar sedan på **Nästa**.

1. På skärmen **Importera projekt** avmarkerar du projektet **DocumentDB** och klickar sedan på **Slutför**. Azure DocumentDB-projektet innehåller Azure Cosmos DB Java SDK, som vi ska lägga till som ett beroende i stället.

1. I **Project Explorer** navigerar du till Azure-Cosmos-Java-sample\src\com.Microsoft.Azure.Cosmos.Sample.dao\DocumentClientFactory.Java och ersätter värden och MASTER_KEY värden med URI: n och primär nyckeln för ditt Azure Cosmos DB-konto och sparar sedan filen. Mer information finns i [steg 1. Skapa ett Azure Cosmos Database-konto](#CreateDB).

1. I **Project Explorer** högerklickar du på **Azure-Cosmos-Java-Sample**, klickar på **Bygg sökväg** och klickar sedan på **Konfigurera build-sökväg**.

1. På skärmen **Java byggsökväg**, i den högra panelen, väljer du fliken **Bibliotek** och klickar sedan på **Lägg till externa JAR:er**. Navigera till platsen där filen lombok.jar finns. Klicka på **Öppna** och sedan på **OK**.

1. Öppna fönstret **Egenskaper** genom steg 12 och klicka sedan på **Körningsmål** i den vänstra panelen.

1. På skärmen **Körningsmål** klickar du på **Nytt**, väljer **Apache Tomcat v7.0** och klickar sedan på **OK**.

1. Öppna fönstret **Egenskaper** som i steg 12 och klicka sedan på **Projektfasetter** i den vänstra panelen.

1. På skärmen **Projektfasetter** väljer du **Dynamisk webbmodul** och **Java**, och klickar sedan på **OK**.

1. På fliken **Servrar** längst ned på skärmen högerklickar du på **Tomcat v7.0-server på localhost** och klickar sedan på **Lägg till och ta bort**.

1. I fönstret **Lägg till och ta bort** flyttar **du Azure-Cosmos-Java-Sample** till den **konfigurerade** rutan och klickar sedan på **Slutför**.

1. Högerklicka på **Tomcat v7.0 Server at localhost** (Tomcat v7.0-server på localhost) på fliken **Server** och klicka sedan på **Starta om**.

1. Gå till `http://localhost:8080/azure-cosmos-java-sample/` i en webbläsare och börja lägga till aktiviteter. Om du har ändrat portarnas standardvärden ändrar du 8080 till värdet du har valt.

1. Information om hur du distribuerar projektet till en Azure-webbplats finns i [steg 6. Distribuera ditt program till Azure Web Sites](#Deploy).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bygg ett node.js-program med Azure Cosmos DB](sql-api-nodejs-application.md)