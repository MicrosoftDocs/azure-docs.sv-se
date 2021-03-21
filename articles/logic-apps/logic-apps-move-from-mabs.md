---
title: Migrera appar från BizTalk Services till Azure Logic Apps
description: Hur du flyttar dina appar och lösningar från Microsoft Azure BizTalk Services (MABS) till Azure Logic Apps
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: 6c07ab4b18c017bd29723d2640129b8e67374e3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023660"
---
# <a name="migrate-your-apps-and-solutions-from-biztalk-services-to-azure-logic-apps"></a>Migrera appar och lösningar från BizTalk Services till Azure Logic Apps

Microsoft Azure BizTalk Services (MABS) tas ur bruk. Följ anvisningarna i den här artikeln om du vill flytta MABS-integrerings lösningar till [Azure Logic Apps](../logic-apps/logic-apps-overview.md). 

## <a name="introduction"></a>Introduktion

BizTalk Services består av två under tjänster:

* Microsoft BizTalk Services Hybridanslutningar
* EAI och EDI-brygga-baserad integrering

[Azure App Service hybridanslutningar](../app-service/app-service-hybrid-connections.md) ersätter BizTalk Services hybridanslutningar. Azure Hybridanslutningar är tillgängligt med Azure App Service via Azure Portal. Den här tjänsten innehåller en Hybridanslutningshanteraren så att du kan hantera befintliga BizTalk Services hybrid anslutningar och även nya hybrid anslutningar som du skapar i portalen. 

[Logic Apps](../logic-apps/logic-apps-overview.md) ersätter EAI-och EDI-brygga-baserad integrering med alla funktioner i BizTalk Services med mera. Med den här tjänsten kan du snabbt och enkelt bygga komplexa integrerings lösningar i en webbläsare eller med Visual Studio.

I den här tabellen mappas BizTalk Services-funktioner till Logic Apps.

| BizTalk Services   | Logic Apps            | Syfte                      |
| ------------------ | --------------------- | ---------------------------- |
| Anslutning          | Anslutning             | Skicka och ta emot data   |
| Bridge             | Logikapp             | Pipeline-processor           |
| Validera fas     | XML-validerings åtgärd | Verifiera ett XML-dokument mot ett schema | 
| Utöka steg       | Datatoken           | Befordra egenskaper till meddelanden eller för routnings beslut |
| Transformerings steg    | Transformerings åtgärd      | Konvertera XML-meddelanden från ett format till ett annat |
| Avkodnings steg       | Flat fil-avkodnings åtgärd | Konvertera från flat fil till XML |
| Koda steg       | Encode-åtgärd för flat fil | Konvertera från XML till flat fil |
| Meddelande kontroll  | Azure Functions eller API Apps | Kör anpassad kod i integreringarna |
| Flödes åtgärd       | Villkor eller växel | Dirigera meddelanden till en av de angivna anslutningarna |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk Services artefakter

BizTalk Services har flera typer av artefakter. 

## <a name="connectors"></a>Anslutningsappar

Med hjälp av BizTalk Services kopplingar kan du skicka och ta emot data, inklusive tvåvägs bryggor som möjliggör HTTP-baserade förfrågningar/svar-interaktioner. Logic Apps använder samma terminologi och innehåller hundratals kopplingar som har samma syfte genom att ansluta till en mängd olika tekniker och tjänster. Till exempel är kopplingarna tillgängliga för Cloud SaaS-och PaaS-tjänster, till exempel OneDrive, Office365, Dynamics CRM och fler, plus lokala system via den lokala datagatewayen som ersätter BizTalk Adapter service för BizTalk Services. Källor i BizTalk Services är begränsade till FTP, SFTP och Service Bus kö-eller ämnes prenumeration.

![Diagram som visar BizTalk Services flödet.](media/logic-apps-move-from-mabs/sources.png)

Som standard har varje bro en HTTP-slutpunkt som är konfigurerad med körnings adressen och de relativa adress egenskaperna för bryggan. Använd [fråge-och svars](../connectors/connectors-native-reqres.md) åtgärderna för att uppnå samma resultat med Logic Apps.

## <a name="xml-processing-and-bridges"></a>XML-bearbetning och bryggor

I BizTalk Services är en brygga likvärdig med en bearbetnings pipeline. En brygga kan ta emot data från en koppling, göra en del arbete med data och skicka resultatet till ett annat system. Logic Apps är detsamma genom att stödja samma pipeline-baserade interaktions mönster som BizTalk Services och även tillhandahålla andra integrations mönster. [XML Request-Reply Bridge](/previous-versions/azure/hh689781(v=azure.100)) i BIZTALK Services kallas VETER pipeline, som består av steg som utför dessa uppgifter:

* (V) validera
* (E) utöka
* (T) transformera
* (E) utöka
* (R) väg

Den här bilden visar hur bearbetning delas mellan begäran och svar, som ger kontroll över begäran och svars Sök vägar separat, till exempel genom att använda olika kartor för varje sökväg:

![Skärm bild som visar hur bearbetningen delas mellan begäran och svara.](media/logic-apps-move-from-mabs/xml-request-reply.png)

En XML One-Way Bridge lägger också till avkoda och koda steg i början och slutet av bearbetningen. Pass-Throughs bryggan innehåller en enda utökande fas.

### <a name="message-processing-decoding-and-encoding"></a>Meddelande bearbetning, avkodning och kodning

I BizTalk Services kan du ta emot olika typer av XML-meddelanden och bestämma det matchande schemat för det mottagna meddelandet. Det här arbetet utförs i stadiet *meddelande typer* i pipeline för mottagnings bearbetning. Avkodnings steget använder sedan den identifierade meddelande typen för att avkoda meddelandet med det angivna schemat. Om schemat är ett flat-filschema konverterar det här steget den inkommande flata filen till XML. 

Logic Apps innehåller liknande funktioner. Du får en platt fil över olika protokoll med hjälp av de olika anslutnings utlösarna (fil system, FTP, HTTP och så vidare) och använder den [flata fil avkodnings](../logic-apps/logic-apps-enterprise-integration-flatfile.md) åtgärden för att konvertera inkommande data till XML. Du kan flytta befintliga planiska filscheman direkt till Logic Apps utan några ändringar och sedan ladda upp scheman till ditt integrations konto.

### <a name="validation"></a>Validering

När inkommande data har konverterats till XML (eller om XML-meddelandet togs emot), körs verifieringen för att avgöra om meddelandet följer ditt XSD-schema. Om du vill utföra den här uppgiften i Logic Apps använder du åtgärden [XML-verifiering](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) . Du kan använda samma scheman från BizTalk Services utan några ändringar.

### <a name="transform-messages"></a>Transformera meddelanden

I BizTalk Services konverterar Transform-steget ett XML-baserat meddelande format till ett annat. Detta arbete görs genom att använda en karta med hjälp av den TRFM-baserade mapper. I Logic Apps är processen liknande. Transformations åtgärden kör en karta från ditt integrations konto. Den största skillnaden är att Maps i Logic Apps är i XSLT-format. XSLT innehåller möjlighet att återanvända befintlig XSLT som du redan har, inklusive kartor som skapats för BizTalk Server som innehåller functoids. 

### <a name="routing-rules"></a>Dirigeringsregler

BizTalk Services fattar ett Dirigerings beslut där slut punkten eller anslutningen skickar inkommande meddelanden eller data. Möjligheten att välja från förkonfigurerade slut punkter är möjlig med hjälp av filter alternativet för Routning:

![Skärm bild som visar alternativet routing filter.](media/logic-apps-move-from-mabs/route-filter.png)

I BizTalk Services, om det *bara finns två alternativ, är det* bästa sättet att konvertera vägvals filter i BizTalk Services. Använd en **växel** om det finns fler än två.

Logic Apps innehåller avancerade Logic-funktioner plus avancerat kontroll flöde och routning med [villkors uttryck](../logic-apps/logic-apps-control-flow-conditional-statement.md) och [switch-instruktioner](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Utöka

I BizTalk Services bearbetning lägger det utökar-steget till egenskaper till den meddelande kontext som är associerad med mottagna data. Du kan till exempel befordra en egenskap som ska användas för routning från en databas sökning eller genom att extrahera ett värde med ett XPath-uttryck. Logic Apps ger åtkomst till alla sammanhangsbaserade data utdata från föregående åtgärder, vilket gör det enkelt att replikera samma beteende. Med hjälp av `Get Row` åtgärden SQL-anslutning returnerar du till exempel data från en SQL Server databas och använder data i en besluts åtgärd för routning. På samma sätt är egenskaper för inkommande Service Bus köade meddelanden med en utlösare adresser bara, och XPath med hjälp av definitions språk uttryck för XPath-arbetsflöden.

### <a name="run-custom-code"></a>Kör anpassad kod

Med BizTalk Services kan du [köra anpassad kod](/previous-versions/azure/dn232389(v=azure.100)) som överförs i dina egna sammansättningar. Den här funktionen implementeras av [IMessageInspector]() -gränssnittet. Varje steg i Bridge innehåller två egenskaper (vid retur-kontrollen och på avslutnings kontroll) som tillhandahåller .NET-typen som implementerar det här gränssnittet. Med anpassad kod kan du utföra mer komplex bearbetning av data och du kan återanvända befintlig kod i sammansättningar som utför vanliga affärs logik. 

Logic Apps erbjuder två huvudsakliga sätt att köra anpassad kod: Azure Functions och API Apps. Azure Functions kan skapas och anropas från Logic Apps. Se [lägga till och köra anpassad kod för](../logic-apps/logic-apps-azure-functions.md)Logi Kap par via Azure Functions. Använd API Apps, en del av Azure App Service, för att skapa egna utlösare och åtgärder. Lär dig mer om hur [du skapar en anpassad API som ska användas med Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Om du har anpassad kod i sammansättningar som du anropar från BizTalk Services kan du antingen flytta koden till Azure Functions eller skapa anpassade API: er med API Apps, beroende på vad du implementerar. Om du till exempel har kod som omsluter en annan tjänst för vilken Logic Apps inte har en koppling, skapar du en API-app och använder de åtgärder som din API-app tillhandahåller i din Logic app. Om du har hjälp funktioner eller bibliotek är Azure Functions förmodligen den bästa anpassningen.

### <a name="edi-processing-and-trading-partner-management"></a>EDI-bearbetning och hantering av handels partner

BizTalk Services och Logic Apps innehåller EDI-och B2B-bearbetning med stöd för AS2 (Applicability Statement 2), X12 och EDIFACT. I BizTalk Services skapar du EDI-bryggor och skapar eller hanterar handels partner och avtal i den dedikerade spårnings-och hanterings portalen.
I Logic Apps får du den här funktionen genom [Enterprise-integrationspaket (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). EIP tillhandahåller [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) och B2B-åtgärder för EDI-och B2B-bearbetning. Du kan också använda ett integrations konto för att skapa och hantera [handels partner](../logic-apps/logic-apps-enterprise-integration-partners.md) och [avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md). När du har skapat ett integrations konto kan du länka en eller flera Logic Apps till kontot. Du kan sedan använda B2B-åtgärder för att få åtkomst till information om handels partner från din Logic app. Följande åtgärder tillhandahålls:

* AS2-kodning
* AS2-avkodning
* X12-kodning
* X12-avkodning
* EDIFACT-kodning
* EDIFACT-avkodning

Till skillnad från BizTalk Services är de här åtgärderna fristående från transport protokollen. När du skapar dina Logi Kap par har du mer flexibilitet i vilka anslutningar du använder för att skicka och ta emot data. Du kan till exempel ta emot X12-filer som bifogade filer från e-post och sedan bearbeta dessa filer i en Logic app. 

## <a name="manage-and-monitor"></a>Hantera och övervaka

I BizTalk Services kan en särskild Portal tillhandahålla spårnings funktioner för att övervaka och felsöka problem. Logic Apps tillhandahåller omfattande spårnings-och övervakningsfunktioner för att övervaka Logi Kap par [i Azure Portal](../logic-apps/monitor-logic-apps.md)och innehåller en mobilapp för att hålla ett öga på sakernas gång när du är på språng.

## <a name="high-availability"></a>Hög tillgänglighet

För hög tillgänglighet (HA) i BizTalk Services kan du dela bearbetnings belastningen genom att använda mer än en instans i en speciell region. Logic Apps tillhandahåller i region HA utan extra kostnad. 

I BizTalk Services kräver haveri beredskap utanför regionen för B2B-bearbetning en säkerhets kopierings-och återställnings process. För affärs kontinuitet ger Logic Apps över flera regioner aktiva/passiva [Dr-funktioner](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md), vilket gör att du kan synkronisera B2B-data mellan integrations konton i olika regioner.

## <a name="next-steps"></a>Nästa steg

* [Vad är Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md), eller kom snabbt igång med en [färdig mall](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Visa alla tillgängliga anslutningar](../connectors/apis-list.md) som du kan använda i Logic Apps