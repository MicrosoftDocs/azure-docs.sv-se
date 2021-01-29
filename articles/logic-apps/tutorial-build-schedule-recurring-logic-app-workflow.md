---
title: Bygg schemabaserade automatiserings arbets flöden med Azure
description: Självstudie – Skapa ett schema baserat, återkommande Automation-arbetsflöde som integrerar över moln tjänster med hjälp av Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2020
ms.openlocfilehash: 95275e68d0c7674caf4dd2b20f5586db5193fd03
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054067"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>Självstudie: skapa schemabaserade och återkommande automatiserings arbets flöden med Azure Logic Apps

Den här självstudien visar hur du skapar en exempel- [Logic-app](../logic-apps/logic-apps-overview.md) som automatiserar ett arbets flöde som körs enligt ett återkommande schema. Mer specifikt kontrollerar den här exempel Logic app res tiden, inklusive trafiken, mellan två platser och körs varje morgon morgon. Om tiden överskrider en angiven gräns skickar Logic-appen ett e-postmeddelande som innehåller res tiden och den extra tid som krävs för att komma till målet. Arbets flödet innehåller olika steg som börjar med en schema-baserad utlösare som följs av en Bing Maps-åtgärd, en åtgärd för data åtgärder, en kontroll flödes åtgärd och en åtgärd för e-postaviseringar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en tom logikapp.
> * Lägg till en upprepnings utlösare som anger schemat för din Logic app.
> * Lägg till en Bing Maps-åtgärd som hämtar res tiden för en väg.
> * Lägg till en åtgärd som skapar en variabel, konverterar res tiden från sekunder till minuter och lagrar resultatet i variabeln.
> * Lägger till ett villkor som jämför restiden med en angiven tidsgräns.
> * Lägg till en åtgärd som skickar e-post om res tiden överskrider gränsen.

När du är klar ser logikappen ut som det här arbetsflödet på en hög nivå:

![Skärm bild som visar översikt på hög nivå för ett exempel på ett Logic app-arbetsflöde.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett e-postkonto från en e-postleverantör som stöds av Logic Apps, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](/connectors/). I den här snabb starten används Office 365 Outlook med ett arbets-eller skol konto. Om du använder ett annat e-postkonto förblir de allmänna stegen desamma, men användar gränssnittet kan skilja sig något.

  > [!IMPORTANT]
  > Om du vill använda Gmail Connector kan endast företags konton i G-Suite använda den här anslutningen utan begränsning i Logic Apps. Om du har ett Gmail-konto kan du använda den här anslutningen med endast vissa Google-godkända tjänster, eller så kan du [skapa en Google-klient som används för autentisering med din Gmail-anslutning](/connectors/gmail/#authentication-and-bring-your-own-application). Mer information finns i [principer för data säkerhet och sekretess för Google Connectors i Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* För att kunna hämta restiden för en resväg behöver du en åtkomstnyckel för Bing Maps-API:t. Hämta nyckeln genom att följa anvisningarna för [hur du hämtar en Bing Maps-nyckel](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key).

* Om din Logi Kap par behöver kommunicera via en brand vägg som begränsar trafik till vissa IP-adresser, måste brand väggen tillåta åtkomst  för både [inkommande](logic-apps-limits-and-config.md#inbound) och [utgående](logic-apps-limits-and-config.md#outbound) ip-adresser som används av Logic Apps tjänst eller körning i den Azure-region där din Logic app finns. Om din Logic app även använder [hanterade anslutningar](../connectors/apis-list.md#managed-api-connectors), till exempel Office 365 Outlook Connector eller SQL-anslutning, eller använder [anpassade anslutningar](/connectors/custom-connectors/), måste brand väggen också tillåta åtkomst för *alla* [utgående IP-adresser för hanterad anslutning](logic-apps-limits-and-config.md#outbound) i din Logic app Azure-region.

## <a name="create-your-logic-app"></a>Skapa en logikapp

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto. På Azures start sida väljer du **skapa en resurs**.

1. På menyn för Azure Marketplace väljer du **integrering**  >  **Logic app**.

   ![Skärm bild som visar menyn för Azure Marketplace med "integration" och "Logic app" valt.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. I fönstret **Logic app** anger du den information som beskrivs här om den Logic-app som du vill skapa.

   ![Skärm bild som visar fönstret för att skapa logiska appar och den information som ska tillhandahållas för den nya Logic-appen.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Prenumeration** | <*Azure-prenumeration-namn*> | Namnet på din Azure-prenumeration. I det här exemplet används `Pay-As-You-Go`. |
   | **Resursgrupp** | LA-TravelTime-RG | Namnet på Azure- [resurs gruppen](../azure-resource-manager/management/overview.md)som används för att organisera relaterade resurser. I det här exemplet skapas en ny resurs grupp med namnet `LA-TravelTime-RG` . |
   | **Namn** | LA-TravelTime | Din Logic Apps namn, som endast får innehålla bokstäver, siffror, bindestreck ( `-` ), under streck ( `_` ), parenteser ( `(` , `)` ) och punkter ( `.` ). I det här exemplet används `LA-TravelTime`. |
   | **Plats** | USA, västra | Den region där du vill lagra information om din Logic Apps. I det här exemplet används `West US`. |
   | **Log Analytics** | Av | Behåll inställningen **Av** för diagnostisk loggning. |
   ||||

1. När du är klar väljer du **Granska + skapa**. När Azure har verifierat informationen om din Logic app väljer du **skapa**.

1. När Azure har distribuerat din app väljer **du gå till resurs**.

   Azure öppnar fönstret Välj mall för val av Logic Apps som visar en introduktions video, vanliga utlösare och mönster för Logic app-mallar.

1. Rulla nedåt i avsnitten video och vanliga utlösare i avsnittet **mallar** och välj **Tom Logic app**.

   ![Skärm bild som visar fönstret för val av mall för Logic Apps med "Tom Logic app" markerat.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Lägg sedan till upprepnings [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts)som kör arbets flödet baserat på ett angivet schema. Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när nya data uppfyller ett särskilt villkor. Mer information finns i [Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Lägg till upprepnings utlösaren

1. I sökrutan i Logic Apps designer anger du `recurrence` och väljer utlösaren med namnet **upprepning**.

   ![Skärm bild som visar den Logic Apps designer-sökrutan som innehåller Sök termen "upprepning" och i listan "utlösare" visas den valda upprepnings utlösaren.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Välj knappen med **tre punkter** (**...**) i formen **upprepning** och välj sedan **Byt namn**. Byt namn på utlösaren med den här beskrivningen: `Check travel time every weekday morning`

   ![Skärm bild som visar knappen ovaler markerad, listan "Inställningar" är öppen och kommandot "Byt namn" har marker ATS.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. I utlösaren ändrar du egenskaperna enligt beskrivningen och visas här.

   ![Skärm bild som visar ändringar av utlösarens intervall och frekvens.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Intervall** | Ja | 1 | Antalet intervaller som ska förflyta mellan kontrollerna |
   | **Frekvens** | Ja | Vecka | Den tidsenhet som används för upprepningen |
   |||||

1. Under **intervall** och **frekvens**, öppna listan **Lägg till ny parameter** och välj de egenskaper som ska läggas till i utlösaren.

   * **Dessa dagar**
   * **Vid dessa timmar**
   * **Vid dessa minuter**

   ![Skärm bild som visar listan "Lägg till ny parameter" och de valda egenskaperna: "på dessa dagar", "vid dessa timmar" och "på dessa minuter".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Ange värdena för de ytterligare egenskaperna enligt vad som visas och beskrivs här.

   ![Skärm bild som visar ytterligare egenskaper som anges till värdena enligt beskrivningen i följande tabell.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Dessa dagar** | Måndag,Tisdag,Onsdag,Torsdag,Fredag | Den här inställningen är endast tillgänglig när du anger **frekvensen** till **vecka**. |
   | **Vid dessa timmar** | 7,8,9 | Den här inställningen är endast tillgänglig när du anger **frekvensen** till **vecka** eller **dag**. För den här upprepningen väljer du vecko timmarna. Det här exemplet körs vid `7` `8` tecknen, och `9` -timmen. |
   | **Vid dessa minuter** | 0,15,30,45 | Den här inställningen är endast tillgänglig när du anger **frekvensen** till **vecka** eller **dag**. För den här upprepningen väljer du minuter på dagen. Det här exemplet börjar vid noll-timmarsformat och körs var 15: e minut. |
   ||||

   Den här utlösaren utlöses varje kvart under vardagar med start kl. 7:00 och slut kl. 9:45. Upprepningsschemat visas i rutan **Förhandsgranskning**. Mer information finns i [Schemalägga aktiviteter och arbetsflöden](../connectors/connectors-native-recurrence.md) och [Arbetsflödesåtgärder och utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Om du vill dölja utlösarens information för tillfället kan du dölja formen genom att klicka inuti formens namn List.

   ![Skärm bild som visar formen komprimerad utlösare.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

Din Logi Kap par är nu Live i Azure Portal men gör inte något annat än utlösare baserat på det angivna schemat. Därför ska vi lägga till en åtgärd som svarar när utlösaren utlöses.

## <a name="get-the-travel-time-for-a-route"></a>Hämta restiden för en resväg

Nu när du har en utlösare lägger du till en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) som hämtar restiden mellan två platser. Logic Apps tillhandahåller en anslutningsapp för Bing Maps-API:t så att du lätt kan hämta den här informationen. Innan du börjar kontrollerar du att du har en Bing Maps API-nyckel (beskrivs i förutsättningarna för den här kursen).

1. I Logic Apps designer väljer du **nytt steg** under upprepnings utlösaren.

1. Under **Välj en åtgärd** väljer du **standard**. I rutan Sök anger du `bing maps` och väljer åtgärden **Hämta väg**.

   ![Skärm bild som visar listan "Välj en åtgärd" som filtrerats efter "Bing Maps"-åtgärder och åtgärden "Hämta väg" vald.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Om du inte har en Bing Maps-anslutning uppmanas du att skapa en anslutning. Ange anslutnings informationen som du ser och se och välj sedan **skapa**.

   ![Skärm bild som visar anslutnings rutan Bing Maps med det angivna anslutnings namnet och API-nyckeln Bing Maps.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Anslutnings namn** | Ja | BingMapsConnection | Ange ett namn på anslutningen. I det här exemplet används `BingMapsConnection`. |
   | **API-nyckel** | Ja | <*Bing-Maps-API-nyckel*> | Ange Bing Maps-API-nyckeln som du har fått tidigare. Om du inte har en Bing Maps-nyckel tar du reda på [hur du hämtar en nyckel](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key). |
   |||||

1. Byt namn på åtgärden med den här beskrivningen: `Get route and travel time with traffic` .

1. Öppna **listan Lägg till ny parameter** i åtgärden och välj dessa egenskaper.

   * **Optimera**
   * **Avståndsenhet**
   * **Travel mode** (Färdsätt)

   ![Skärm bild som visar "Hämta väg..." åtgärd med egenskaperna "optimera", "avstånds enhet" och "rese läge" valda.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Ange värdena för egenskaperna som visas och beskrivs här.

   ![Skärm bild som visar ytterligare egenskaps värden för åtgärden "Hämta väg".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Waypoint 1** (Platsmarkör 1) | Ja | <*Start-location*> | Vägens ursprung. I det här exemplet anges ett exempel på en start adress. |
   | **Waypoint 2** (Platsmarkör 2) | Ja | <*slut punkt*> | Vägens mål. I det här exemplet anges ett exempel på en mål adress. |
   | **Optimera** | Inga | timeWithTraffic | En parameter för att optimera färdvägen, till exempel avstånd, restid med aktuell trafik med mera. Välj parameter värde, **timeWithTraffic**. |
   | **Avståndsenhet** | Inga | <*dina inställningar*> | Avståndsenhet för din resväg. I det här exemplet används **mil** som enhet. |
   | **Travel mode** (Färdsätt) | Inga | Driving (Bil) | Färdsättet för din resväg. Välj **körläge** . |
   |||||

   Mer information om dessa parametrar och värden finns i [Beräkna en väg](/bingmaps/rest-services/routes/calculate-a-route).

1. I verktygsfältet designer väljer du **Spara**.

Skapa sedan en variabel så att du kan omvandla och lagra den aktuella restiden i minuter i stället för sekunder. På så sätt behöver du inte upprepa omvandlingen och det blir enklare att använda värdet i senare steg. 

## <a name="create-a-variable-to-store-travel-time"></a>Skapa en variabel för att lagra res tiden

Ibland kanske du vill köra åtgärder på data i arbets flödet och sedan använda resultatet i senare åtgärder. Om du vill spara dessa resultat så att du enkelt kan återanvända eller referera till dem kan du skapa variabler som lagrar dessa resultat efter bearbetning. Du kan skapa variabler endast på den översta nivån i din logikapp.

Som standard returnerar åtgärden **Hämta väg** den aktuella res tiden med trafik i sekunder från egenskapen **res varaktighets trafik** . Genom att omvandla och lagra det här värdet i minuter i stället för sekunder blir det enklare att använda värdet senare utan att du behöver omvandla det på nytt.

1. I designern, under åtgärden **Hämta väg** , väljer du **nytt steg**.

1. Under **Välj en åtgärd** väljer du **inbyggt**. I rutan Sök anger du `variables` och väljer åtgärden **initiera variabel**.

   ![Skärm bild som visar åtgärden initiera variabel vald.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Byt namn på åtgärden med den här beskrivningen: `Create variable to store travel time`

1. Ange den här informationen för din variabel enligt tabellen och i stegen nedan:

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Namn** | Ja | travelTime | Namnet på variabeln. I det här exemplet används `travelTime`. |
   | **Typ** | Ja | Integer | Datatypen för variabeln |
   | **Värde** | Inga | Ett uttryck som konverterar den aktuella res tiden från sekunder till minuter (se stegen under den här tabellen). | Det inledande värdet för variabeln |
   |||||

   1. Skapa uttrycket för egenskapen **Value** genom att klicka i rutan så att listan med dynamiskt innehåll visas. Om det behövs kan du bredda webbläsaren tills den dynamiska listan visas. I listan med dynamiskt innehåll väljer du **uttryck** som visar uttrycks redigeraren.

      ![Skärm bild som visar åtgärden "initiera variabel" med markören i egenskapen "value", som öppnar listan med dynamiskt innehåll.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      I listan med dynamiskt innehåll visas utdata från tidigare åtgärder som du kan välja som indata till efterföljande åtgärder i ditt arbets flöde. Listan med dynamiskt innehåll innehåller en uttrycks redigerare som du kan använda för att välja funktioner som utför åtgärder i ditt uttryck. Den här uttrycks redigeraren är endast tillgänglig i listan med dynamiskt innehåll.

   1. Ange det här uttrycket i uttrycksredigeraren: `div(,60)`

      ![Skärm bild som visar uttrycks redigeraren med uttrycket "div (, 60)" angivet.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Placera markören mellan den vänstra parentesen (**(**) och kommatecknet (**,**) i uttrycket och välj **dynamiskt innehåll**.

      ![Skärm bild som visar var du placerar markören i uttrycket "div (, 60)" med "dynamiskt innehåll" markerat.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. I listan med dynamiskt innehåll under Välj egenskap svärdet **res varaktighets trafik**.

      ![Skärm bild som visar egenskap svärdet "res varaktighets trafik" vald.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. När egenskap svärdet har matchats inuti uttrycket väljer du **OK**.

      ![Skärm bild som visar knappen "OK" som vald.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Egenskapen **Value** visas nu som visas här:

      ![Skärm bild som visar egenskapen "värde" med matchat uttryck.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Spara logikappen.

Lägg sedan till ett villkor som kontrollerar om den aktuella restiden är större än en specifik gräns.

## <a name="compare-the-travel-time-with-limit"></a>Jämför res tiden med gränsen

1. Under åtgärden **skapa variabel för att lagra res tiden** väljer du **nytt steg**.

1. Under **Välj en åtgärd** väljer du **inbyggt**. Skriv `condition` i sökrutan. och Välj åtgärden med namnet **villkor** i listan åtgärder.

   ![Skärm bild som visar åtgärden "villkor" vald](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Byt namn på villkoret med den här beskrivningen: `If travel time exceeds limit`

1. Bygg ett villkor som kontrollerar om egenskap svärdet **travelTime** överskrider din angivna gräns enligt beskrivningen och visas här:

   1. Klicka i rutan **Välj ett värde** i villkoret till vänster.

   1. Från listan med dynamiskt innehåll som visas under **variabler** väljer du egenskapen med namnet **travelTime**.

      ![Skärm bild som visar rutan "Välj ett värde" på villkorets vänstra sida med den dynamiska innehålls listan öppen och egenskapen "travelTime" har marker ATS.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. I rutan mellan jämförelser väljer du den Operator **som heter är större än**.

   1. Ange följande gräns i rutan **Välj ett värde** på villkorets högra sida: `15`

      När du är klar ser villkoret ut som i det här exemplet:

      ![Skärm bild som visar det färdiga villkoret för att jämföra res tiden med den angivna gränsen.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Spara logikappen.

Lägg sedan till åtgärden som ska köras när res tiden överskrider din gräns.

## <a name="send-email-when-limit-exceeded"></a>Skicka ett e-postmeddelande när gränsen överskrids

Lägg nu till en åtgärd som skickar e-post när res tiden överskrider din gräns. E-postmeddelandet innehåller den aktuella restiden och den extra restid som krävs för den angivna resvägen.

1. I villkorets **sanna** gren väljer du **Lägg till en åtgärd**.

1. Under **Välj en åtgärd** väljer du **standard**. Skriv `send email` i sökrutan. Listan returnerar många resultat så att du kan filtrera listan genom att först välja den e-postkoppling som du vill använda.

   Om du till exempel har ett Outlook-e-postkonto väljer du anslutnings typen för din kontotyp:

   * För Azure arbets- eller skolkonto väljer du **Office 365 Outlook**.
   * För personliga Microsoft-konton väljer du **Outlook.com**.

   Det här exemplet fortsätter genom att välja Office 365 Outlook.

   ![Skärm bild som visar "Välj en åtgärds lista" med "standard"-kategorin och "Office 365 Outlook"-kopplingen har marker ATS.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. När kopplingens åtgärder visas väljer du den åtgärd som skickar e-post, till exempel:

   ![Skärm bild som visar åtgärden "Skicka ett e-postmeddelande" vald.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Om du inte redan har en anslutning loggar du in och autentiserar åtkomsten till ditt e-postkonto när du uppmanas till det.

   Azure Logic Apps skapar en anslutning till ditt e-postkonto.

1. Byt namn på åtgärden med den här beskrivningen: `Send email with travel time`

1. Ange mottagarens e-postadress för egenskapen **till** . I test syfte kan du använda din e-postadress.

1. För egenskapen **ämne** anger du e-postmeddelandets ämne och inkluderar **travelTime** -variabeln genom att följa dessa steg:

   1. Ange texten `Current travel time (minutes):` med ett avslutande blanksteg. Behåll markören i rutan **ämne** så att listan över dynamiskt innehåll förblir öppen.

   1. I den dynamiska innehålls listan i rubriken **variabler** väljer du **Visa mer** så att variabeln med namnet **travelTime** visas.

      ![Skärm bild som visar listan med dynamiska innehåll med avsnittet "variabler" och "Se fler" markerat.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > Den dynamiska innehålls listan visar inte automatiskt **travelTime** -variabeln eftersom egenskapen **subject** förväntar sig ett sträng värde, medan **travelTime** är ett heltals värde.

      ![Skärm bild som visar listan med dynamiskt innehåll med variabeln "travelTime" vald.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. För egenskapen **Body** anger du innehållet för e-postmeddelandets brödtext genom att följa dessa steg:

   1. Ange texten `Add extra travel time (minutes):` med ett avslutande blanksteg. Behåll markören i **text** rutan så att listan över dynamiskt innehåll förblir öppen.

   1. I listan med dynamiskt innehåll väljer du **uttryck** som visar uttrycks redigeraren.

      ![Skärm bild som visar listan med dynamiskt innehåll med uttrycket "Expression" markerat.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. I uttrycks redigeraren anger `sub(,15)` du så att du kan beräkna antalet minuter som överskrider gränsen: 

      ![Skärm bild som visar uttrycks redigeraren med uttrycket "sub (, 15)" angiven.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Placera markören mellan den vänstra parentesen (**(**) och kommatecknet (**,**) i uttrycket och välj **dynamiskt innehåll**.

      ![Skärm bild som visar var du placerar markören i uttrycket "sub (, 15)" med "dynamiskt innehåll" markerat.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. Välj **travelTime** (Restid) under **Variables** (Variabler).

      ![Skärm bild som visar listan med dynamiskt innehåll där variabeln "travelTime" har marker ATS.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. När egenskapen har matchats inuti uttrycket väljer du **OK**.

      ![Skärm bild som visar listan med dynamiskt innehåll och "OK" vald.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Egenskapen **Body** visas nu som visas här:

      ![Skärm bild som visar listan med dynamiskt innehåll med uttrycket löst i e-poståtgärdens egenskap "Body".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Spara logikappen.

Testa sedan och kör din Logi Kap par som nu ser ut ungefär som i det här exemplet:

![Skärm bild som visar det färdiga exempel på Logic app-arbetsflöde](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Kör logikappen

Om du vill starta din Logic app manuellt går du till verktygsfältet i designern och väljer **Kör**.

* Om den aktuella res tiden ligger under gränsen, gör din Logic app inget annat och väntar eller nästa intervall innan du kontrollerar igen. 

* Om den aktuella res tiden överskrider din gräns får du ett e-postmeddelande med den aktuella res tiden och antalet minuter över din gräns. Här är ett exempel på ett e-postmeddelande som logikappen skickar:

  ![Skärm bild som visar ett exempel på ett e-postmeddelande som rapporterar den aktuella res tiden och den extra res tid som överskrider den angivna gränsen.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > Om du inte får e-post kan du titta i mappen Skräppost. Ditt skräppostfilter kan dirigera om dessa typer av e-post. Om du är osäker på om din logikapp kördes korrekt kan du läsa informationen om att [felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulerar! Du har nu skapat och kört en logikapp med en återkommande åtgärd enligt ett schema. 

Om du vill skapa andra Logi Kap par som använder **upprepnings** utlösaren kan du kolla in de här mallarna som är tillgängliga när du har skapat en Logic app:

* Få dagliga påminnelser skickade till dig.
* Ta bort gamla Azure-blobar.
* Lägg till ett meddelande i en Azure Storage-kö.

## <a name="clean-up-resources"></a>Rensa resurser

Din Logic app fortsätter att köras tills du inaktiverar eller tar bort appen. När du inte längre behöver appen exempel logik tar du bort resurs gruppen som innehåller din Logic app och relaterade resurser.

1. I sökrutan Azure Portal anger du namnet på resurs gruppen som du skapade. Välj resurs gruppen under **resurs grupper** i resultaten.

   I det här exemplet skapades resurs gruppen med namnet `LA-TravelTime-RG` .

   ![Skärm bild som visar Azure Search-rutan med "La-Travel-Time-RG" angiven och * * LA-TravelTime-RG * * valt.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > Om Azures start sida visar resurs gruppen under de **senaste resurserna** kan du välja gruppen från start sidan.

1. På menyn resurs grupp kontrollerar du att **Översikt** är markerat. I verktygsfältet i **översikts** fönstret väljer du **ta bort resurs grupp**.

   ![Skärm bild som visar resurs gruppens översikts fönster och i fönstrets verktygsfält, "ta bort resurs grupp" är markerat.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. I bekräftelse fönstret som visas anger du resurs gruppens namn och väljer **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en Logic-app som kontrollerar trafik baserat på ett angivet schema (på vardags morgon) och vidtar åtgärder (skickar ett e-postmeddelande) när res tiden överskrider en angiven gräns. Nu kan du lära dig hur du skapar en Logi Kap par som skickar begär Anden om att godkännas genom att integrera Azure-tjänster, Microsoft-tjänster och andra SaaS-appar (program vara som en tjänst).

> [!div class="nextstepaction"]
> [Hantera begäranden om distributionslista](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
