---
title: RosettaNet-meddelanden för B2B-integrering
description: Exchange RosettaNet-meddelanden i Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: f02cbdc7ca8822c5fcc91b106856d7f8f547536b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91565113"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Exchange RosettaNet-meddelanden för B2B Enterprise-integration i Azure Logic Apps

[RosettaNet](https://resources.gs1us.org) är ett ideellt konsortium som har upprättat standard processer för att dela företags information. Dessa standarder används ofta för att tillhandahålla kedjas processer och är förbrett i halvledar-, elektronik-och logistik branschen. RosettaNet-konsortiet skapar och underhåller PIPs (partner Interface processs), som tillhandahåller vanliga definitioner för affärs processer för alla RosettaNet meddelande utbyten. RosettaNet baseras på XML och definierar rikt linjer för meddelanden, gränssnitt för affärs processer och implementerings ramverk för kommunikation mellan företag.

I [Azure Logic Apps](../logic-apps/logic-apps-overview.md)hjälper RosettaNet-anslutaren dig att skapa integrerings lösningar som stöder RosettaNet-standarder. Kopplingen baseras på RosettaNet implementation Framework (RNIF) version 2.0.01. RNIF är ett öppet ramverk för nätverks program som gör det möjligt för affärs partner att samar beta med att köra RosettaNet PIPs. Det här ramverket definierar meddelande strukturen, behovet av bekräftelser, Multipurpose Internet Mail Extensions (MIME)-kodning och den digitala signaturen.

Mer specifikt ger kopplingen dessa funktioner:

* Koda eller ta emot RosettaNet-meddelanden.
* Avkoda eller skicka RosettaNet-meddelanden.
* Vänta på svar och generering av aviseringar om ett haveri meddelande.

För dessa funktioner stöder Connector alla PIPs som definieras av RNIF 2.0.01. Kommunikation med partnern kan vara synkron eller asynkron.

## <a name="rosettanet-concepts"></a>RosettaNet-begrepp

Här följer några begrepp och termer som är unika för RosettaNet-specifikationen och är viktiga när du skapar RosettaNet-baserade integrationer:

* **–**

  RosettaNet-organisationen skapar och underhåller PIPs (partner Interface processs), som tillhandahåller vanliga definitioner för affärs processer för alla RosettaNet meddelande utbyten. Varje PIP-specifikation innehåller en dokument typ definitions fil (DTD) och ett rikt linje dokument för meddelande. DTD-filen definierar tjänst-innehålls meddelande strukturen. Dokumentet med meddelande rikt linjer, som är en HTML-fil som kan läsas, anger begränsningar på element nivå. Tillsammans ger de här filerna en fullständig definition av affärs processen.

   PIPs kategoriseras av en affärs funktion på hög nivå, ett kluster och en underordnad funktion, eller ett segment. Exempel: "3A4" är PIP för inköps order, medan "3" är funktionen för order hantering och "3A" är underfunktionen offert & Order Entry. Mer information finns på RosettaNet- [webbplatsen](https://resources.gs1us.org).

* **Åtgärd**

  Som en del av en PIP är åtgärds meddelanden affärs meddelanden som utbyts mellan partner.

* **Signal**

   Som en del av en PIP är signal meddelanden bekräftelser som skickas som svar på åtgärds meddelanden.

* **Enkel åtgärd och dubbel åtgärd**

  För en enda åtgärd-kärna är det enda svaret ett bekräftelse meddelande. För en PIP med dubbel åtgärd tar initieraren emot ett svarsmeddelande och svarar med en bekräftelse förutom meddelande flödet med en åtgärd.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration ännu kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) för att lagra ditt avtal och andra B2B-artefakter. Det här integrations kontot måste vara associerat med din Azure-prenumeration.

* Minst två [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) som har definierats i ditt integrations konto och kon figurer ATS med kvalificeraren "duns" under **företags identiteter**

* En PIP-process konfiguration, som krävs för att skicka eller ta emot RosettaNet-meddelanden, i ditt integrations konto. Process konfigurationen lagrar alla egenskaper för PIP-konfigurationen. Du kan sedan referera till den här konfigurationen när du skapar ett avtal med partnern. Om du vill skapa en PIP process-konfiguration i ditt integrations konto, se [Lägg till pip process konfiguration](#add-pip).

* Valfria [certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md) för att kryptera, dekryptera eller signera meddelanden som du överför till integrations kontot. Certifikat krävs endast om du använder signering eller kryptering.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Lägg till konfiguration av PIP process

Följ dessa steg om du vill lägga till en PIP-process konfiguration till ditt integrations konto:

1. Leta upp och öppna ditt integrations konto i [Azure Portal](https://portal.azure.com).

1. I fönstret **Översikt** väljer du panelen **RosettaNet pip** .

   ![Välj RosettaNet panel](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. Under **ROSETTANET pip** väljer du **Lägg till**. Ange din PIP-information.

   ![Lägg till RosettaNet PIP-information](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn** | Ja | Ditt PIP-namn |
   | **PIP-kod** | Ja | PIP-tresiffrig kod. Mer information finns i [RosettaNet PIPs](/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **PIP-version** | Ja | PIP-versions numret, som är tillgängligt baserat på din valda PIP kod |
   ||||

   Mer information om dessa PIP-egenskaper finns på [RosettaNet-webbplatsen](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. När du är klar väljer du **OK**, vilket skapar pip-konfigurationen.

1. Om du vill visa eller redigera process konfigurationen väljer du PIP och väljer **Redigera som JSON**.

   Alla process konfigurations inställningar kommer från PIP-specifikationerna. Logic Apps fyller i de flesta av inställningarna med standardvärdena som används oftast för dessa egenskaper.

   ![Redigera RosettaNet PIP-konfiguration](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Bekräfta att inställningarna motsvarar värdena i den aktuella PIP specifikationen och uppfyller dina affärs behov. Vid behov kan du uppdatera värdena i JSON och spara ändringarna.

## <a name="create-rosettanet-agreement"></a>Skapa RosettaNet-avtal

1. Leta upp och öppna ditt integrations konto i [Azure Portal](https://portal.azure.com), om det inte redan är öppet.

1. I **översikts** fönstret väljer du **avtals** panelen.

   ![Välj avtals panel](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. Välj **Lägg till** under **avtal**. Ange din avtals information.

   ![Lägg till avtals information](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn** | Ja | Namnet på avtalet |
   | **Avtals typ** | Ja | Välj **RosettaNet**. |
   | **Värd partner** | Ja | Ett avtal kräver både värd-och gäst partner. Värd partnern representerar den organisation som konfigurerar avtalet. |
   | **Värd identitet** | Ja | En identifierare för värd partnern |
   | **Gäst partner** | Ja | Ett avtal kräver både värd-och gäst partner. Gäst partnern representerar den organisation som gör affärer med värd partnern. |
   | **Gäst identitet** | Ja | En identifierare för gäst partnern |
   | **Ta emot inställningar** | Det varierar | Dessa egenskaper gäller för alla meddelanden som tas emot av värd partnern |
   | **Skicka inställningar** | Det varierar | Dessa egenskaper gäller för alla meddelanden som skickas av värd partnern |  
   | **RosettaNet PIP-referenser** | Ja | PIP-referenser för avtalet. Alla RosettaNet-meddelanden kräver PIP-konfigurationer. |
   ||||

1. Om du vill konfigurera ditt avtal för att ta emot inkommande meddelanden från gäst partner väljer du **ta emot inställningar**.

   ![Ta emot inställningar](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Om du vill aktivera signering eller kryptering för inkommande meddelanden, under **meddelanden**, ska du välja meddelande som ska **vara signerat** eller **meddelandet bör vara krypterat** .

      | Egenskap | Krävs | Beskrivning |
      |----------|----------|-------------|
      | **Meddelandet ska vara signerat** | Inga | Signera inkommande meddelanden med det valda certifikatet. |
      | **Certifikat** | Ja, om signering är aktiverat | Certifikatet som ska användas för signering |
      | **Aktivera meddelande kryptering** | Inga | Kryptera inkommande meddelanden med det valda certifikatet. |
      | **Certifikat** | Ja, om kryptering är aktiverat | Certifikatet som ska användas för kryptering |
      ||||

   1. Under varje val väljer du respektive [certifikat](./logic-apps-enterprise-integration-certificates.md), som du tidigare lagt till i ditt integrations konto, som ska användas för signering eller kryptering.

1. Om du vill konfigurera ditt avtal för att skicka meddelanden till gäst partner väljer du **Skicka inställningar**.

   ![Skicka inställningar](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Om du vill aktivera signering eller kryptering för utgående meddelanden går du till **meddelanden** och väljer **Aktivera meddelande signering** eller **Aktivera meddelande kryptering** . Under varje val väljer du den respektive algoritm och det [certifikat](./logic-apps-enterprise-integration-certificates.md)som du tidigare lagt till i ditt integrations konto för att använda för signering eller kryptering.

      | Egenskap | Krävs | Beskrivning |
      |----------|----------|-------------|
      | **Aktivera meddelande signering** | Inga | Signera utgående meddelanden med den valda signeringsalgoritmen och certifikat. |
      | **Signeringsalgoritm** | Ja, om signering är aktiverat | Signeringsalgoritmen som ska användas, baserat på det valda certifikatet |
      | **Certifikat** | Ja, om signering är aktiverat | Certifikatet som ska användas för signering |
      | **Aktivera meddelande kryptering** | Inga | Kryptera utgående med den valda krypteringsalgoritmen och certifikatet. |
      | **Krypteringsalgoritm** | Ja, om kryptering är aktiverat | Krypteringsalgoritmen som ska användas, baserat på det valda certifikatet |
      | **Certifikat** | Ja, om kryptering är aktiverat | Certifikatet som ska användas för kryptering |
      ||||

   1. Under **slut punkter** anger du de obligatoriska URL: er som ska användas för att skicka åtgärds meddelanden och bekräftelser.

      | Egenskap | Krävs | Beskrivning |
      |----------|----------|-------------|
      | **Åtgärds-URL** |  Ja | Den URL som ska användas för att skicka åtgärds meddelanden. URL: en är ett obligatoriskt fält för både synkrona och asynkrona meddelanden. |
      | **Bekräftelse-URL** | Ja | Den URL som ska användas för att skicka bekräftelse meddelanden. URL: en är ett obligatoriskt fält för asynkrona meddelanden. |
      ||||

1. Om du vill konfigurera ditt avtal med RosettaNet PIP-referenser för partner väljer du **ROSETTANET pip-referenser**. Under **pip namn** väljer du namnet på din tidigare skapade pip.

   ![PIP-referenser](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   Ditt val fyller i de återstående egenskaperna, som baseras på PIP som du ställer in i ditt integrations konto. Om det behövs kan du ändra **pip-rollen**.

   ![Vald PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

När du har slutfört de här stegen är du redo att skicka eller ta emot RosettaNet-meddelanden.

## <a name="rosettanet-templates"></a>RosettaNet-mallar

För att påskynda utvecklingen och rekommendera integrations mönster kan du använda Logic app templates för att avkoda och koda RosettaNet-meddelanden. När du skapar en Logic-app kan du välja från mallgalleriet i Logic App Designer. Du kan också hitta de här mallarna i [GitHub-lagringsplatsen för Azure Logic Apps](https://github.com/Azure/logicapps).

![RosettaNet-mallar](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Ta emot eller avkoda RosettaNet-meddelanden

1. [Skapa en tom Logic-app](quickstart-create-first-logic-app-workflow.md).

1. [Länka ditt integrations konto](logic-apps-enterprise-integration-create-integration-account.md#link-account) till din Logic app.

1. Innan du kan lägga till en åtgärd för att avkoda RosettaNet-meddelandet måste du lägga till en utlösare för att starta din Logic app, till exempel en begäran-utlösare.

1. När du har lagt till utlösaren väljer du **nytt steg**.

   ![Lägg till begär ande utlösare](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. I sökrutan anger du "RosettaNet" och väljer den här åtgärden: **RosettaNet-avkodning**

   ![Sök efter och Välj åtgärden "RosettaNet avkoda"](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Ange informationen för åtgärdens egenskaper:

   ![Skärm bild som visar var du anger information om åtgärdens egenskaper.](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Meddelande** | Ja | RosettaNet-meddelandet att avkoda  |
   | **Sidhuvuden** | Ja | HTTP-huvuden som innehåller värdena för-versionen, som är RNIF-versionen och svars typen, som anger kommunikations typen mellan partnerna och kan vara synkron eller asynkron |
   | **Role** | Ja | Rollen som värd partner i PIP |
   ||||

   Från RosettaNet avkodnings åtgärd inkluderar utdata, tillsammans med andra egenskaper, **utgående signal**, som du kan välja att koda och returnera tillbaka till partnern, eller vidta andra åtgärder på dessa utdata.

## <a name="send-or-encode-rosettanet-messages"></a>Skicka eller koda RosettaNet-meddelanden

1. [Skapa en tom Logic-app](quickstart-create-first-logic-app-workflow.md).

1. [Länka ditt integrations konto](logic-apps-enterprise-integration-create-integration-account.md#link-account) till din Logic app.

1. Innan du kan lägga till en åtgärd för att koda RosettaNet-meddelandet måste du lägga till en utlösare för att starta din Logic app, till exempel en begäran-utlösare.

1. När du har lagt till utlösaren väljer du **nytt steg**.

   ![Lägg till begär ande utlösare](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. I rutan Sök anger du "RosettaNet" och väljer den här åtgärden: **RosettaNet-kodning**

   ![Sök efter och Välj åtgärden "RosettaNet-kodning"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Ange informationen för åtgärdens egenskaper:

   ![Ange åtgärds information](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Meddelande** | Ja | RosettaNet-meddelandet som ska kodas  |
   | **Värd partner** | Ja | Värd partner namnet |
   | **Gäst partner** | Ja | Namnet på gäst partnern |
   | **PIP-kod** | Ja | PIP-koden |
   | **PIP-version** | Ja | PIP-versionen |  
   | **PIP-instans identitet** | Ja | Den unika identifieraren för det här PIP-meddelandet |  
   | **Meddelandetyp** | Ja | Typ av meddelande som ska kodas |  
   | **Role** | Ja | Värd partnerns roll |
   ||||

   Det kodade meddelandet är nu klart att skickas till partnern.

1. För att skicka det kodade meddelandet använder det här exemplet **http** -åtgärden, som byter namn till "http-Send kodat meddelande till partner".

   ![HTTP-åtgärd för att skicka RosettaNet-meddelande](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   Enligt RosettaNet-standarder betraktas affärs transaktionerna som fullständiga när alla steg som har definierats av PIP har slutförts.

1. När värden skickar det kodade meddelandet till partner väntar värden på signalen och bekräftelsen. För att utföra den här uppgiften lägger du till åtgärden **RosettaNet vänta på svar** .

   ![Lägg till åtgärden "RosettaNet väntar på svar"](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   Den varaktighet som ska användas för väntan och antalet återförsök baseras på PIP-konfigurationen i ditt integrations konto. Om svaret inte tas emot genererar den här åtgärden ett meddelande om fel. Om du vill hantera återförsök ska du alltid ange **koda** och **vänta på svars** åtgärder **i en-** loop.

   ![Tills loop med RosettaNet-åtgärder](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du verifierar, transformerar och andra meddelande åtgärder med [Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
