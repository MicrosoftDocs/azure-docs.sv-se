---
title: Anslutningsprogram för Azure Logic Apps
description: Automatisera arbets flöden med kopplingar för Azure Logic Apps, till exempel inbyggt, hanterat, lokalt, integrations konto, ISE och företags anslutningar
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 06/11/2020
ms.openlocfilehash: 1dd38f0360a4471124497d8357481283cd98383c
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566309"
---
# <a name="connectors-for-azure-logic-apps"></a>Anslutningsprogram för Azure Logic Apps

Anslutningsappar ger snabb åtkomst från Azure Logic Apps till händelser, data och åtgärder i andra appar, tjänster, system, protokoll och plattformar. Genom att använda anslutningsprogram i dina logikappar kan du utöka funktionerna för dina molnappar och lokala appar så att du kan utföra olika åtgärder för de data som du skapar och redan har.

Även om Logic Apps erbjuder [hundratals kopplingar](/connectors), beskriver den här artikeln de *populära och vanligaste* kopplingarna som används av tusentals appar och miljon tals körningar för bearbetning av data och information. Om du vill hitta en fullständig lista över kopplingar och varje kopplings referensinformation, till exempel utlösare, åtgärder och gränser, granskar du kopplings referens sidorna under [kopplings översikt](/connectors). Lär dig även mer om [utlösare och åtgärder](#triggers-actions), [Logic Apps pris modell](../logic-apps/logic-apps-pricing.md)och [Logic Apps pris information](https://azure.microsoft.com/pricing/details/logic-apps/).

> [!TIP]
> Om du vill integrera med en tjänst eller ett API som inte har någon koppling kan du antingen anropa tjänsten via ett protokoll, till exempel HTTP, eller skapa en [anpassad anslutning](#custom).

## <a name="connector-types"></a>Anslutnings typer

Kopplingar är tillgängliga som inbyggda utlösare och åtgärder eller som hanterade anslutningar.

<a name="built-in"></a>

* [**Inbyggt**](#built-ins): inbyggda utlösare och åtgärder är "interna" för att Azure Logic Apps och hjälpa dig att utföra de här uppgifterna för dina Logic Apps:

  * Kör med anpassade och avancerade scheman.

  * Organisera och kontrol lera din Logic Apps-arbets flöde, till exempel slingor och villkor, och även för att arbeta med variabler och data åtgärder.

  * Kommunicera med andra slut punkter.

  * Ta emot och svara på begär Anden.

  * Anropa Azure Functions, Azure API Apps (Web Apps), dina egna API: er som hanteras och publicerats med Azure API Management och kapslade Logic-appar som kan ta emot förfrågningar.

<a name="managed-connectors"></a>

* [**Hanterade anslutningar**](#managed-api-connectors): distribuerade och hanteras av Microsoft, de här anslutningarna tillhandahåller utlösare och åtgärder för att komma åt moln tjänster, lokala system eller både, inklusive Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint med mera. Vissa anslutningar har specifikt stöd för kommunikation mellan företag och företag (B2B) och kräver ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som är länkat till din Logic app. Innan du använder vissa anslutningar måste du först skapa anslutningar, som hanteras av Azure Logic Apps.

  Om du till exempel använder Microsoft BizTalk Server kan dina Logi Kap par ansluta till och kommunicera med dina BizTalk Server med hjälp av den [BizTalk Server lokala anslutningen](#on-premises-connectors). Du kan sedan utöka eller utföra BizTalk-liknande åtgärder i dina Logi Kap par genom att använda [kopplingarna för integrations konton](#integration-account-connectors).

  Anslutningarna klassificeras som antingen standard eller Enterprise. [Företags anslutningar](#enterprise-connectors) ger till gång till företags system som SAP, IBM MQ och IBM 3270 för ytterligare kostnader. För att avgöra om en koppling är standard eller företag, se teknisk information på varje kopplings referens sida under [anslutnings översikt](/connectors).

  Du kan också identifiera anslutningar genom att använda dessa kategorier, även om vissa kopplingar kan korsa flera kategorier. SAP är till exempel en Enterprise Connector och en lokal anslutning:

  | Kategori | Beskrivning |
  |----------|-------------|
  | [**Hanterade anslutningar**](#managed-api-connectors) | Skapa Logi Kap par som använder tjänster som Azure Blob Storage, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online och många fler. |
  | [**Lokala anslutningsappar**](#on-premises-connectors) | När du har installerat och konfigurerat den [lokala datagatewayen][gateway-doc]kan de här anslutningarna hjälpa dina Logi Kap par att komma åt lokala system som SQL Server, SharePoint Server, Oracle DB, fil resurser och andra. |
  | [**Anslutningar för integrationskonton**](#integration-account-connectors) | De här kopplingarna är tillgängliga när du skapar och betalar för ett integrations konto, omvandlar och validerar XML, kodar och avkodar flata filer och bearbetar Business-to-Business (B2B)-meddelanden med AS2-, EDIFACT-och X12-protokoll. |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment-ise"></a>Ansluta från en integrerings tjänst miljö (ISE)

För logi Kap par som behöver direkt åtkomst till resurser i ett virtuellt Azure-nätverk kan du skapa en dedikerad [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) där du kan skapa, distribuera och köra Logi Kap par på dedikerade resurser. När du bläddrar bland de anslutningar som du vill använda för logi Kap par i en ISE visas en **Core** -etikett på inbyggda utlösare och åtgärder, medan **ISE** -etiketten visas på vissa anslutningar.

> [!NOTE]
> Logi Kap par som körs i en ISE och deras kopplingar, oavsett var dessa anslutningar körs, följer en fast pris plan jämfört med den förbruknings bara pris planen. Mer information finns i [Logic Apps pris modell](../logic-apps/logic-apps-pricing.md) och [Logic Apps pris information](https://azure.microsoft.com/pricing/details/logic-apps/).

| Etikett | Exempel | Description |
|-------|---------|-------------|
| **GRUNDLÄGGANDE** | ![Exempel på ISE-anslutning](./media/apis-list/example-core-connector.png) | Inbyggda utlösare och åtgärder med den här etiketten körs i samma ISE som dina Logic Apps. |
| **ISE** | ![Exempel på ISE-anslutning](./media/apis-list/example-ise-connector.png) | Hanterade anslutningar med den här etiketten körs i samma ISE som dina Logic Apps. Om du har ett lokalt system som är anslutet till ett virtuellt Azure-nätverk kan du med hjälp av en ISE direkt komma åt det systemet utan den [lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md). I stället kan du antingen använda systemets **ISE** -koppling om det är tillgängligt, en HTTP-åtgärd eller en [anpassad anslutning](#custom). För lokala system som inte har **ISE** -kopplingar använder du en lokal datagateway. Information om hur du granskar tillgängliga ISE-anslutningar finns i [ISE-anslutningar](#ise-connectors). |
| Ingen etikett | ![Exempel på anslutning för flera innehavare](./media/apis-list/example-multi-tenant-connector.png) | Alla andra anslutningar utan **Core** -eller **ISE** -etiketten, som du kan fortsätta att använda, körs i den globala Logic Apps tjänsten för flera innehavare. |
|||

<a name="built-ins"></a>

## <a name="built-in"></a>Inbyggd

Logic Apps innehåller inbyggda utlösare och åtgärder så att du kan skapa schemabaserade arbets flöden, hjälpa dina Logi Kap par att kommunicera med andra appar och tjänster, styra arbets flödet via dina Logi Kap par och hantera eller manipulera data.

| Name | Beskrivning |
|------|-------------|
| [![Schema för API-ikon ][schedule-icon]<br> **Schedule**][schedule-doc] | – Kör en Logi Kap par på en angiven upprepning, från grundläggande till avancerade scheman med [ **upprepnings** utlösaren][schedule-recurrence-doc]. <br>– Kör en Logi Kap par som måste hantera data i kontinuerliga segment med en [utlösare för **glidnings fönster** ][schedule-sliding-window-doc]. <br>– Pausa din Logic app under en angiven varaktighet med [ **fördröjnings** åtgärden][schedule-delay-doc]. <br>– Pausa din Logi Kap par fram till angivet datum och tid med [ **fördröjningen innan** åtgärden][schedule-delay-until-doc]. |
| [![API-ikon ][batch-icon]<br> **batch**][batch-doc] | – Bearbeta meddelanden i batchar med utlösaren **batch-meddelanden** . <br>– Anropa Logi Kap par som har befintliga batch-utlösare med åtgärden **skicka meddelanden till batch** . |
| [![API ][http-icon]<br> **-ikon http**][http-doc] | Anropa HTTP- eller HTTPS-slutpunkter med utlösare och åtgärder för HTTP. Andra inbyggda HTTP-utlösare och åtgärder inkluderar [http + Swagger][http-swagger-doc] och [http + webhook][http-webhook-doc]. |
| [![API- ][http-request-icon]<br> **Request** ikon][http-request-doc] | – Gör ditt Logic app-anrop från andra appar eller tjänster, Utlös på Event Grid resurs händelser eller Utlös på svar till Azure Security Center aviseringar med utlösaren för **begäran** . <br>– Skicka svar till en app eller tjänst med **svars** åtgärden. |
| [![API-ikon ][azure-api-management-icon]<br> **Azure API <br> Management**][azure-api-management-doc] | Anropa utlösare och åtgärder som definierats av dina egna API: er som du hanterar och publicerar med Azure API Management. |
| [![API-ikon ][azure-app-services-icon]<br> **Azure app <br> tjänster**][azure-app-services-doc] | Anropa Azure API-appar eller webbappar som finns på Azure App Service. Utlösare och åtgärder som definieras av dessa appar visas som alla andra utlösare och åtgärder för första klass när Swagger ingår. |
| [![API-ikon ][azure-logic-apps-icon]<br> **Azure Logic <br> Apps**][nested-logic-app-doc] | Anropa andra Logi Kap par som börjar med **begär** ande utlösare. |
|||

### <a name="run-code-from-logic-apps"></a>Kör kod från Logic Apps

Logic Apps innehåller inbyggda åtgärder för att köra din egen kod i din Logic Apps-arbets flöde:

| Name | Beskrivning |
|------|-------------|
| [![API-ikon ][azure-functions-icon]<br> **Azure Functions**][azure-functions-doc] | Anropa Azure Functions som kör anpassade kod avsnitt (C# eller Node.js) från dina Logic Apps. |
| [![][inline-code-icon]<br>**Infogad kod** för API-ikon][inline-code-doc] | Lägg till och kör kods tycken för JavaScript-kod från dina Logic Apps. |
|||

### <a name="control-workflow"></a>Kontroll arbets flöde

Logic Apps innehåller inbyggda åtgärder för att strukturera och kontrol lera åtgärder i din Logic Apps-arbets flöde:

| Name | Beskrivning |
|------|-------------|
| [![Villkor för inbyggd ikon ][condition-icon]<br> **Condition**][condition-doc] | Utvärdera ett villkor och kör olika åtgärder baserat på om villkoret är sant eller falskt. |
| [![Inbyggd ikon ][for-each-icon]<br> **för varje**][for-each-doc] | Utför samma åtgärder på varje objekt i en matris. |
| [![Inbyggda ikon ][scope-icon]<br> **omfång**][scope-doc] | Gruppera åtgärder i *omfattningar*, som får sin egen status efter att åtgärderna i omfånget har körts. |
| [![Växel för inbyggd ikon ][switch-icon]<br> **Switch**][switch-doc] | Gruppera åtgärder i *fall*, som tilldelas unika värden förutom standard fallet. Kör bara det fall vars tilldelade värde matchar resultatet från ett uttryck, ett objekt eller en token. Kör standard fallet om inga matchningar finns. |
| [![Inbyggd ikon ][terminate-icon]<br> **Avsluta**][terminate-doc] | Stoppa ett aktivt Logic app-arbetsflöde som körs. |
| [![Inbyggd ikon ][until-icon]<br> **tills**][until-doc] | Upprepa åtgärder tills det angivna villkoret är sant eller så har status ändrats. |
|||

### <a name="manage-or-manipulate-data"></a>Hantera eller manipulera data

Logic Apps innehåller inbyggda åtgärder för att arbeta med data utdata och deras format:

| Name | Beskrivning |
|------|-------------|
| [![Inbyggda ikon ][data-operations-icon]<br> **data åtgärder**][data-operations-doc] | Utför åtgärder med data: <p>- **Skriv**: skapa en enstaka utdata från flera indata med olika typer. <br>- **Skapa CSV-tabell**: skapa en kommaavgränsad tabell (CSV) från en matris med JSON-objekt. <br>- **Skapa HTML-tabell**: skapa en HTML-tabell från en matris med JSON-objekt. <br>- **Filtrera matris**: skapa en matris från objekt i en annan matris som uppfyller dina kriterier. <br>- **Anslut**: skapa en sträng från alla objekt i en matris och avgränsa dessa objekt med angiven avgränsare. <br>- **Parsa JSON**: skapa användarvänliga token från egenskaper och deras värden i JSON-innehåll så att du kan använda dessa egenskaper i ditt arbets flöde. <br>- **Välj**: skapa en matris med JSON-objekt genom att transformera objekt eller värden i en annan matris och mappa dessa objekt till angivna egenskaper. |
| ![Inbyggd ikon][date-time-icon]<br>**Datum tid** | Utföra åtgärder med tidsstämplar: <p>- **Lägg till i tid**: Lägg till det angivna antalet enheter i en tidstämpel. <br>- **Konvertera tidszon**: konvertera en tidsstämpel från käll tids zonen till mål tids zonen. <br>- **Aktuell tid**: returnera den aktuella tidsstämpeln som en sträng. <br>- **Hämta framtida tid**: returnera den aktuella tidsstämpeln och de angivna tidsenheterna. <br>- **Hämta föregående tid**: returnera den aktuella tidsstämpeln minus de angivna tidsenheterna. <br>- **Subtrahera från tid**: subtrahera ett antal tidsenheter från en tidsstämpel. |
| [![Inbyggda Icon- ][variables-icon]<br> **variabler**][variables-doc] | Utföra åtgärder med variabler: <p>- **Lägg till matris variabel**: infoga ett värde som det sista objektet i en matris som lagras av en variabel. <br>- **Lägg till i sträng variabel**: infoga ett värde som sista tecken i en sträng som lagras av en variabel. <br>- **Minska variabeln**: minska en variabel med ett konstantvärde. <br>- **Increment-variabel**: öka en variabel med ett konstant värde. <br>- **Initiera variabel**: skapa en variabel och deklarera dess datatyp och initialt värde. <br>- **Ange variabel**: tilldela ett annat värde till en befintlig variabel. |
|||

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>Hanterade anslutningar

Logic Apps tillhandahåller de här populära standard anslutningarna för automatisering av uppgifter, processer och arbets flöden med dessa tjänster eller system:

| Name | Beskrivning |
|------|-------------|
| [![API-ikon ][azure-service-bus-icon]<br> **Azure Service Bus**][azure-service-bus-doc] | Hantera asynkrona meddelanden, sessioner och ämnesprenumerationer med det anslutningsprogram som oftast används i Logic Apps. |
| [![API-ikon ][sql-server-icon]<br> **SQL Server**][sql-server-doc] | Anslut till din SQL Server lokalt eller en Azure SQL Database i molnet så att du kan hantera poster, köra lagrade procedurer eller köra frågor. |
| [![API-ikon ][azure-blob-storage-icon]<br> **Azure Blob <br> Storage**][azure-blob-storage-doc] | Anslut till ditt lagrings konto så att du kan skapa och hantera BLOB-innehåll. |
| [![API-ikon ][office-365-outlook-icon]<br> **Office 365 <br> Outlook**][office-365-outlook-doc] | Anslut till ditt Office 365-e-postkonto så att du kan skapa och hantera e-post, uppgifter, Kalender händelser och möten, kontakter, förfrågningar och mycket annat. |
| [![API ][sftp-ssh-icon]<br> **-ikon SFTP – SSH**][sftp-ssh-doc] | Anslut till SFTP-servrar som du kan komma åt från Internet med hjälp av SSH så att du kan arbeta med dina filer och mappar. |
| [![API-ikon ][sharepoint-online-icon]<br> **SharePoint <br> online**][sharepoint-online-doc] | Anslut till SharePoint Online så att du kan hantera filer, bilagor, mappar med mera. |
| [![API-ikon ][azure-queues-icon]<br> **Azure- <br> köer**][azure-queues-doc] | Anslut till ditt Azure Storage-konto så att du kan skapa och hantera köer och meddelanden. |
| [![API ][ftp-icon]<br> **-ikon FTP**][ftp-doc] | Anslut till FTP-servrar som du kan komma åt från Internet så att du kan arbeta med dina filer och mappar. |
| [![API-ikon ][file-system-icon]<br> **fil <br> system**][file-system-doc] | Anslut till din lokala fil resurs så att du kan skapa och hantera filer. |
| [![API-ikon ][azure-event-hubs-icon]<br> **Azure-Event Hubs**][azure-event-hubs-doc] | Använda och publicera händelser via en Event Hub. Du kan till exempel hämta utdata från din logikapp med Event Hubs och sedan skicka dem till en leverantör av realtidsanalys. |
| [![API-ikon ][azure-event-grid-icon]<br> **Azure Event** <br> **Grid**][azure-event-grid-doc] | Övervaka händelser som publicerats av en Event Grid, till exempel när Azure-resurser eller resurser från tredje part ändras. |
| [![API-ikon ][salesforce-icon]<br> **Salesforce**][salesforce-doc] | Anslut till ditt Salesforce-konto så att du kan skapa och hantera objekt, till exempel poster, jobb, objekt med mera. |
|||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Lokala anslutningsappar

Här följer några vanliga standard anslutningar som Logic Apps ger åtkomst till data och resurser i lokala system. Innan du kan skapa en anslutning till ett lokalt system måste du först [Ladda ned, installera och konfigurera en lokal datagateway][gateway-doc]. Den här gatewayen tillhandahåller en säker kommunikations kanal utan att behöva konfigurera den nödvändiga nätverks infrastrukturen.

:::row:::
    :::column:::
        [![API-ikon ][biztalk-server-icon]<br> **BizTalk** <br> **Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][file-system-icon]<br> **fil <br> system**][file-system-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][ibm-db2-icon]<br> **IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][ibm-informix-icon]<br> **IBM** <br> **Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API-ikon ][mysql-icon]<br> **MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][oracle-db-icon]<br> **Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][postgre-sql-icon]<br> **postgresql**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][sharepoint-server-icon]<br> **SharePoint <br> Server**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API-ikon ][sql-server-icon]<br> **SQL <br> Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][teradata-icon]<br> **Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Anslutningar för integrationskonton

Logic Apps tillhandahåller standard anslutningar för att skapa B2B-lösningar (Business-to-Business) med dina Logi Kap par när du skapar och betalar för ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), som är tillgängligt via Enterprise-INTEGRATIONSPAKET (EIP) i Azure. Med det här kontot kan du skapa och lagra B2B-artefakter som handels partner, avtal, kartor, scheman, certifikat och så vidare. Om du vill använda dessa artefakter associerar du dina Logi Kap par med ditt integrations konto. Om du för närvarande använder BizTalk Server kan dessa anslutningar verka bekant redan.

:::row:::
    :::column:::
        [![API-ikon ][as2-icon]<br> **AS2- <br> avkodning**][as2-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][as2-icon]<br> **AS2- <br> kodning**][as2-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][edifact-icon]<br> **EDIFACT- <br> avkodning**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][edifact-icon]<br> **EDIFACT- <br> kodning**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API-ikon ][flat-file-decode-icon]<br> **flat fil- <br> avkodning**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][flat-file-encode-icon]<br> **flat fil <br> kodning**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][integration-account-icon]<br> **integrations <br> konto**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][liquid-icon]<br> **flytande** <br> **transformeringar**][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API-ikon ][x12-icon]<br> **X12- <br> avkodning**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][x12-icon]<br> **X12- <br> kodning**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![][xml-transform-icon]<br>**XML-** <br> **transformeringar** för API-ikon][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![API ][xml-validate-icon]<br> **-ikon XML- <br> verifiering**][xml-validate-doc]
    :::column-end:::
:::row-end:::

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Enterprise-anslutningsappar

Logic Apps tillhandahåller de här företags anslutningarna för åtkomst till företags system, till exempel SAP och IBM MQ:

:::row:::
    :::column:::
        [![API-ikon ][ibm-3270-icon]<br> **IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][ibm-mq-icon]<br> **IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][sap-icon]<br> **SAP**][sap-connector-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>ISE-anslutningar

För logi Kap par som du skapar och kör i en dedikerad [integrerings tjänst miljö (ISE)](#integration-service-environment)identifierar Logic App Designer inbyggda utlösare och åtgärder som körs i din ISE med hjälp av **Core** -etiketten. Hanterade anslutningar som körs i en ISE visar **ISE** -etiketten, medan anslutningar som körs i den globala Logic Apps tjänsten för flera innehavare inte visar någon etikett. Den här listan visar de anslutningar som för närvarande har ISE-versioner:

:::row:::
    :::column:::
        [![API-ikon ][as2-icon]<br> **AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][azure-automation-icon]<br> **Azure <br> Automation**][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][azure-blob-storage-icon]<br> **Azure Blob <br> Storage**][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][azure-cosmos-db-icon]<br> **Azure Cosmos <br> db**][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API-ikon ][azure-event-hubs-icon]<br> **Azure Event <br> Hub**][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][azure-event-grid-icon]<br> **Azure Event <br> Grid**][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][azure-file-storage-icon]<br> **Azure File <br> Storage**][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][azure-key-vault-icon]<br> **Azure Key <br> Vault**][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API-ikon ][azure-monitor-logs-icon]<br> **Azure Monitor <br> loggar**][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![API ][azure-service-bus-icon]<br> **-ikon Azure Service <br> Bus**][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][azure-sql-data-warehouse-icon]<br> **Azure SQL data <br> Warehouse**][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][azure-table-storage-icon]<br> **Azure Table <br> Storage**][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API-ikon ][azure-queues-icon]<br> **Azure- <br> köer**][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][edifact-icon]<br> **EDIFACT**][edifact-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][file-system-icon]<br> **fil <br> system**][file-system-doc]
    :::column-end:::
    :::column:::
        [![API ][ftp-icon]<br> **-ikon FTP**][ftp-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API-ikon ][ibm-3270-icon]<br> **IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][ibm-db2-icon]<br> **IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][ibm-mq-icon]<br> **IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][sap-icon]<br> **SAP**][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API ][sftp-ssh-icon]<br> **-ikon SFTP – SSH**][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![API ][smtp-icon]<br> **-ikon SMTP**][smtp-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][sql-server-icon]<br> **SQL <br> Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][x12-icon]<br> **X12**][x12-doc]
    :::column-end:::
:::row-end:::

Mer information finns i de här ämnena:

* [Åtkomst till Azures virtuella nätverks resurser från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Prissättningsmodell för Logic Apps](../logic-apps/logic-apps-pricing.md)
* [Ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>Utlösare och åtgärds typer

Kopplingar kan tillhandahålla *utlösare*, *åtgärder*eller både och. En *utlösare* är det första steget i alla logiska appar, vanligt vis genom att ange den händelse som utlöser utlösaren och börjar köra din Logic app. FTP-anslutningen har till exempel en utlösare som startar din Logic app "när en fil läggs till eller ändras". Vissa utlösare söker regelbundet efter den angivna händelsen eller data och utlöses sedan när de identifierar den angivna händelsen eller informationen. Andra utlösare väntar men utlöses direkt när en enskild händelse inträffar eller när nya data är tillgängliga. Utlösare skickas också tillsammans med alla data som krävs till din Logic app. Din Logic app kan läsa och använda dessa data i hela arbets flödet. Office 365 Outlook Connector har till exempel en utlösare, "när ett nytt e-postmeddelande kommer", som kan skicka innehållet från e-postmeddelandet till din Logic app-arbetsflöde.

När en utlösare aktive ras skapar Azure Logic Apps en instans av din Logic app och börjar köra *åtgärderna* i din Logic app-arbetsflöde. Åtgärder är de steg som följer utlösaren och utför uppgifter i din Logic app-arbetsflöde. Du kan till exempel skapa en Logic-app som hämtar kund information från en SQL-databas och bearbetar dessa data i senare åtgärder.

Här är de allmänna typerna av utlösare som Azure Logic Apps tillhandahåller:

* *Upprepnings utlösare*: den här utlösaren körs enligt ett angivet schema och är inte nära kopplad till en viss tjänst eller ett visst system.

* *Avsöknings utlösare*: den här utlösaren avsöker regelbundet en specifik tjänst eller ett system baserat på det angivna schemat, söker efter nya data eller om en specifik händelse har inträffat. Om nya data är tillgängliga eller om den här händelsen har inträffat skapar utlösaren och kör en ny instans av din Logic app, som nu kan använda de data som skickas som indata.

* *Push-utlösare*: den här utlösaren väntar och lyssnar efter nya data eller för att en händelse ska inträffa. När nya data är tillgängliga eller när händelsen inträffar skapar utlösaren och kör en ny instans av din Logic app, som nu kan använda de data som skickas som indata.

<a name="connections"></a>

## <a name="connector-configuration"></a>Anslutnings konfiguration

Varje kopplings utlösare och åtgärder tillhandahåller egna egenskaper som du kan konfigurera. Många anslutningar kräver också att du först skapar en *anslutning* till mål tjänsten eller systemet och anger autentiseringsuppgifter för autentisering eller annan konfigurations information innan du kan använda en utlösare eller åtgärd i din Logic app. Innan du kan komma åt och arbeta med ditt Office 365 Outlook-e-postkonto måste du till exempel auktorisera en anslutning till det kontot.

För kopplingar som använder Azure Active Directory (Azure AD) OAuth, skapar en anslutning en inloggning till tjänsten, till exempel Office 365, Salesforce eller GitHub, där din åtkomsttoken är [krypterad](../security/fundamentals/encryption-overview.md) och lagras säkert i ett Azure Secret Store. Andra anslutningar, till exempel FTP och SQL, kräver en anslutning som har konfigurations information, till exempel Server adressen, användar namnet och lösen ordet. Dessa anslutnings konfigurations uppgifter krypteras och lagras på ett säkert sätt. Läs mer om [kryptering i Azure](../security/fundamentals/encryption-overview.md).

Anslutningarna kan komma åt mål tjänsten eller systemet så länge tjänsten eller systemet tillåter det. För tjänster som använder Azure AD OAuth-anslutningar, till exempel Office 365 och Dynamics, Azure Logic Apps uppdaterar åtkomsttoken på obestämd tid. Andra tjänster kan ha gränser för hur länge Azure Logic Apps kan använda en token utan att uppdatera. I allmänhet är vissa åtgärder ogiltiga för alla åtkomsttoken, till exempel att ändra ditt lösen ord.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Anpassade API: er och anslutningar

Om du vill anropa API: er som kör anpassad kod eller som inte är tillgängliga som kopplingar kan du utöka Logic Apps-plattformen genom att [skapa anpassade API Apps](../logic-apps/logic-apps-create-api-app.md). Du kan också [skapa anpassade anslutningar](../logic-apps/custom-connector-overview.md) för *alla* rest-eller SOAP-baserade API: er som gör dessa API: er tillgängliga för alla Logic-appar i din Azure-prenumeration. Om du vill göra anpassade API Apps eller anslutningar offentliga för alla som ska användas i Azure kan du [skicka in anslutningar för Microsoft-certifiering](/connectors/custom-connectors/submit-certification).

> [!NOTE]
> Logic Apps som du distribuerar och kör i en [integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) kan komma åt resurser direkt i ett virtuellt Azure-nätverk. Om du har anpassade anslutningar som kräver den lokala datagatewayen och du har skapat dessa anslutningar utanför en ISE kan Logic Apps i en ISE också använda dessa anslutningar.
>
> Anpassade anslutningar som skapats i en ISE fungerar inte med den lokala datagatewayen. Dessa anslutningar kan dock direkt komma åt lokala data källor som är anslutna till ett virtuellt Azure-nätverk som är värd för ISE. Därför behöver Logic Apps i en ISE förmodligen inte datagatewayen när de kommunicerar med dessa resurser.
>
> Mer information om hur du skapar ISEs finns i [ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="block-connections"></a>

## <a name="block-creating-connections"></a>Blockera skapande av anslutningar

Om din organisation inte tillåter att du ansluter till vissa resurser genom att använda deras anslutningar i Azure Logic Apps kan du [blockera möjligheten att skapa anslutningar](../logic-apps/block-connections-connectors.md) för vissa anslutningar i Logic app-arbetsflöden genom att använda [Azure policy](../governance/policy/overview.md). Mer information finns i [blockera anslutningar som skapats av vissa anslutningar i Azure Logic Apps](../logic-apps/block-connections-connectors.md).

## <a name="get-ready-for-deployment"></a>Gör dig redo för distribution

Även om du skapar anslutningar inifrån en Logic app, är anslutningarna separata Azure-resurser med sina egna resurs definitioner. Om du vill granska dessa resurs definitioner för anslutnings resurser kan du antingen [Granska resurs gruppen för din Logi Kap par genom att använda Azure Portal](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#view-resource-definitions) eller [Ladda ned din Logic app från Azure till Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md), vilket är det enklaste sättet att skapa en giltig app-mall för parametriserade som är redo för distribution.

## <a name="next-steps"></a>Nästa steg

* Visa [hela anslutnings listan](/connectors)
* [Skapa din första logiska app](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Skapa anpassade anslutningar för Logic Apps](/connectors/custom-connectors/)
* [Skapa anpassade API:er för logikappar](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Anslut till datakällor lokalt från logikappar med lokala datagatewayer"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Skapa en Azure API Management-tjänstinstans för att hantera och publicera dina API: er"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Integrera logikappar med App Service API Apps"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrera logikappar med Azure Functions"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Bearbeta meddelanden i grupper eller som batchar"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Utvärdera ett villkor och kör olika åtgärder baserat på om villkoret är sant eller falskt"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Utför samma åtgärder på varje objekt i en matris"
[http-doc]: ./connectors-native-http.md "Anropa HTTP-eller HTTPS-slutpunkter från dina Logic Apps"
[http-request-doc]: ./connectors-native-reqres.md "Ta emot HTTP-begäranden i dina Logic Apps"
[http-response-doc]: ./connectors-native-reqres.md "Svara på HTTP-begäranden från dina Logic Apps"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Ring upp REST-slutpunkter från dina Logic Apps"
[http-webhook-doc]: ./connectors-native-webhook.md "Vänta på vissa händelser från HTTP-eller HTTPS-slutpunkter"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Lägg till och kör kods tycken för JavaScript-kod från dina Logic Apps"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrera logikappar med kapslade arbetsflöden"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Välja och filtrera matriser med frågeåtgärden"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Köra Logi Kap par baserade på ett schema"
[schedule-delay-doc]: ./connectors-native-delay.md "Fördröjning vid körning av nästa åtgärd"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Fördröjning vid körning av nästa åtgärd"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Köra Logic Apps enligt ett återkommande schema"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Kör Logi Kap par som behöver hantera data i intilliggande segment"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organisera åtgärder i grupper som får sin egen status efter att åtgärderna i gruppen har körts"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organisera åtgärder i fall, som tilldelas unika värden. Kör bara det fall vars värde matchar resultatet från ett uttryck, ett objekt eller en token. Om inga matchningar finns kör du standard fallet"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Stoppa eller Avbryt ett aktivt pågående arbets flöde för din Logic app"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Upprepa åtgärder tills det angivna villkoret är sant eller så har status ändrats"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Utföra data åtgärder, till exempel för att filtrera matriser eller skapa CSV-och HTML-tabeller"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Utföra åtgärder med variabler, till exempel initiera, ange, öka, minska och lägga till i sträng eller mat ris variabel"

<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "Skapa och hantera automatiserings jobb för molnet och den lokala infrastrukturen"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Hantera filer i blobcontainern med Azure Blob Storage Connector"
[azure-cosmos-db-doc]: /connectors/documentdb/ "Anslut till Azure Cosmos DB så att du kan komma åt dokument och lagrade procedurer"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Övervaka händelser som publicerats av en Event Grid, till exempel när Azure-resurser eller resurser från tredje part ändras"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Anslut till Azure Event Hubs så att du kan ta emot och skicka händelser mellan Logic Apps och Event Hubs"
[azure-file-storage-doc]: /connectors/azurefile/ "Anslut till ditt Azure Storage-konto så att du kan skapa, uppdatera, hämta och ta bort filer"
[azure-key-vault-doc]: /connectors/keyvault/ "Anslut till din Azure Key Vault så att du kan hantera dina hemligheter och nycklar"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Kör frågor mot Azure Monitor loggar i Log Analytics arbets ytor och Application Insights komponenter"
[azure-queues-doc]: /connectors/azurequeues/ "Anslut till ditt Azure Storage-konto så att du kan skapa och hantera köer och meddelanden"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Skicka meddelanden från Service Bus-köer och Service Bus-ämnen och ta emot meddelanden från Service Bus-köer och Service Bus-prenumerationer"
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "Anslut till Azure SQL Data Warehouse så att du kan visa dina data"
[azure-table-storage-doc]: /connectors/azuretables/ "Anslut till ditt Azure Storage-konto så att du kan skapa, uppdatera och fråga tabeller med mera"
[biztalk-server-doc]: /connectors/biztalk/ "Anslut till din BizTalk Server så att du kan köra BizTalk-baserade program sida vid sida med Azure Logic Apps"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Ansluta till ett lokalt filsystem"
[ftp-doc]: ./connectors-create-api-ftp.md "Ansluta till en FTP-/FTPS-server för FTP-aktiviteter, till exempel överföring, hämtning och borttagning av filer och mycket mer"
[github-doc]: ./connectors-create-api-github.md "Ansluta till GitHub och spåra problem"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Ansluter till Google Calendar och kan hantera kalendern"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Anslut till Google Sheets så att du kan ändra dina blad"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Ansluter till Google tasks så att du kan hantera dina uppgifter"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Ansluta till 3270-appar i IBM-stordatorer"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Anslut till IBM DB2 i molnet eller lokalt. Uppdatera en rad, hämta en tabell med mera"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Anslut till Informix i molnet eller lokalt. Läsa en rad, lista tabeller med mera"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Ansluta till IBM MQ lokalt eller i Azure för att skicka och ta emot meddelanden"
[instagram-doc]: ./connectors-create-api-instagram.md "Anslut till Instagram. Utlösa eller agera på händelser"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Ansluta till Mandrill för kommunikation"
[mysql-doc]: /connectors/mysql/ "Anslut till din lokala MySQL-databas så att du kan läsa och skriva data"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Anslut till ditt Office 365-konto så att du kan skicka och ta emot e-post, hantera din kalender och dina kontakter med mera"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Anslut till din personliga Microsoft OneDrive så att du kan ladda upp, ta bort, lista filer och mycket mer"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Anslut till ditt företag Microsoft OneDrive så att du kan ladda upp, ta bort, lista dina filer med mera"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Anslut till en Oracle-databas så att du kan lägga till, infoga och ta bort rader med mera"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Anslut till din Outlook-postlåda så att du kan hantera din e-post, kalendrar, kontakter med mera"
[postgre-sql-doc]: /connectors/postgresql/ "Anslut till PostgreSQL-databasen så att du kan läsa data från tabeller"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Anslut till ditt Salesforce-konto. Hantera konton, leads, affärs möjligheter och mycket mer"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Ansluta till ett lokalt SAP-system"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Anslut till SendGrid. Skicka e-post och hantera mottagar listor"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Anslut till ditt SFTP-konto med hjälp av SSH. Ladda upp, Hämta, ta bort filer och mycket mer"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "Anslut till en lokal SharePoint-Server. Hantera dokument, list objekt och mer"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "Anslut till SharePoint Online. Hantera dokument, list objekt och mer"
[slack-doc]: ./connectors-create-api-slack.md "Ansluta till slack och skicka meddelanden till slack-kanaler"
[smtp-doc]: ./connectors-create-api-smtp.md "Ansluta till en SMTP-server och skicka e-post med bifogade filer"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Ansluter till SparkPost för kommunikation"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Anslut till Azure SQL Database eller SQL Server. Skapa, uppdatera, hämta och ta bort poster i en SQL Database-tabell"
[teradata-doc]: /connectors/teradata/ "Anslut till Teradata-databasen för att läsa data från tabeller"
[twilio-doc]: ./connectors-create-api-twilio.md "Anslut till Twilio. Skicka och hämta meddelanden, Hämta tillgängliga nummer, hantera inkommande telefonnummer och mycket mer"
[youtube-doc]: ./connectors-create-api-youtube.md "Anslut till YouTube. Hantera dina videor och kanaler"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Koda och avkoda meddelanden som använder AS2-protokollet"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Koda och avkoda meddelanden som använder EDIFACT-protokollet"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Avkoda meddelanden som använder EDIFACT-protokollet"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Koda meddelanden som använder EDIFACT-protokollet"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Lär dig mer om platt företags integrerings fil"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Lär dig mer om platt företags integrerings fil"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Hantera metadata för integrations konto artefakter"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Transformera JSON med flytande mallar"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Koda och avkoda meddelanden som använder X12-protokollet"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Avkoda meddelanden som använder X12-protokollet"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Koda meddelanden som använder X12-protokollet"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Transformera XML-meddelanden"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Validera XML-meddelanden"
