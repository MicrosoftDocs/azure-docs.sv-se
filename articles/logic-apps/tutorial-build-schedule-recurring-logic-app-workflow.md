---
title: Skapa schemabaserade arbetsflöden för automatisering med Azure
description: Självstudie – Skapa ett schemabaserat, återkommande automatiseringsarbetsflöde som integreras mellan molntjänster med hjälp av Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: 0dab2f49043f3a59dd4f06633e3f0228fc39a424
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792101"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>Självstudie: Skapa schemabaserade och återkommande automatiseringsarbetsflöden med Azure Logic Apps

Den här självstudien visar hur du skapar [en exempellogikapp](../logic-apps/logic-apps-overview.md) som automatiserar ett arbetsflöde som körs enligt ett återkommande schema. Mer specifikt kontrollerar den här exempellogiken restiden, inklusive trafiken, mellan två platser och körs varje veckodag. Om tiden överskrider en viss gräns skickar logikappen ett e-postmeddelande som innehåller restiden och den extra tid som krävs för att komma fram till målet. Arbetsflödet innehåller olika steg som börjar med en schemabaserad utlösare följt av en Bing Maps-åtgärd, en åtgärd för dataåtgärder, en kontrollflödesåtgärd och en e-postaviseringsåtgärd.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en tom logikapp.
> * Lägg till en upprepningsutlösare som anger schemat för logikappen.
> * Lägg till en Bing Maps-åtgärd som hämtar restiden för en rutt.
> * Lägg till en åtgärd som skapar en variabel, konverterar restiden från sekunder till minuter och lagrar resultatet i variabeln.
> * Lägger till ett villkor som jämför restiden med en angiven tidsgräns.
> * Lägg till en åtgärd som skickar dig ett e-postmeddelande om restiden överskrider gränsen.

När du är klar ser logikappen ut som det här arbetsflödet på en hög nivå:

![Skärmbild som visar den övergripande översikten för ett exempel på ett logikapparbetsflöde.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett e-postkonto från en e-postleverantör som stöds av Logic Apps, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](/connectors/). Den här snabbstarten använder Office 365 Outlook med ett arbets- eller skolkonto. Om du använder ett annat e-postkonto förblir de allmänna stegen desamma, men användargränssnittet kan skilja sig något.

  > [!IMPORTANT]
  > Om du vill använda Gmail-anslutningsappen kan endast G Suite-företagskonton använda den här anslutningsappen utan begränsning i logikappar. Om du har ett Gmail-konsumentkonto kan du använda den här anslutningsappen med endast specifika Google-godkända tjänster, eller så kan du skapa en Google-klientapp som ska användas för autentisering med [Gmail-anslutningsappen.](/connectors/gmail/#authentication-and-bring-your-own-application) Mer information finns i [Datasäkerhets- och sekretesspolicyer för Google-anslutningsappar i Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* För att kunna hämta restiden för en resväg behöver du en åtkomstnyckel för Bing Maps-API:t. Hämta nyckeln genom att följa anvisningarna för [hur du hämtar en Bing Maps-nyckel](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key).

* Om logikappen behöver kommunicera via en brandvägg som begränsar trafiken till specifika  IP-adresser måste brandväggen tillåta åtkomst för både [inkommande](logic-apps-limits-and-config.md#inbound) och utgående [IP-adresser](logic-apps-limits-and-config.md#outbound) som används av Logic Apps-tjänsten eller körningen i Azure-regionen där logikappen finns. Om logikappen även använder hanterade anslutningsappar, till exempel Office 365 Outlook-anslutningsappen eller SQL-anslutningsappen, eller använder anpassade [anslutningsappar](/connectors/custom-connectors/)måste brandväggen även tillåta åtkomst för alla utgående [IP-adresser](logic-apps-limits-and-config.md#outbound) för hanterade anslutningsappar i logikappens Azure-region. [](../connectors/managed.md) 

## <a name="create-your-logic-app"></a>Skapa en logikapp

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto. På Startsidan för Azure väljer du **Skapa en resurs.**

1. På Azure Marketplace väljer du **Integration**  >  **Logic App**.

   ![Skärmbild som visar Azure Marketplace med "Integration" och "Logic App" valt.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. I fönstret **Logikapp** anger du den information som beskrivs här om den logikapp som du vill skapa.

   ![Skärmbild som visar skapandefönstret för logikappen och den information som ska tillhandahållas för den nya logikappen.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Prenumeration** | <*Azure-subscription-name*> | Namnet på din Azure-prenumeration. I det här exemplet används `Pay-As-You-Go`. |
   | **Resursgrupp** | LA-TravelTime-RG | Namnet på [Azure-resursgruppen](../azure-resource-manager/management/overview.md), som används för att organisera relaterade resurser. I det här exemplet skapas en ny resursgrupp med namnet `LA-TravelTime-RG` . |
   | **Namn** | LA-TravelTime | Logikappens namn, som endast får innehålla bokstäver, siffror, bindestreck ( `-` ), understreck ( `_` ), parenteser ( `(` , ), och punkter ( `)` `.` ). I det här exemplet används `LA-TravelTime`. |
   | **Plats** | USA, västra | Den region där logikappens information ska lagras. I det här exemplet används `West US`. |
   | **Log Analytics** | Av | Behåll inställningen **Av** för diagnostisk loggning. |
   ||||

1. När du är klar väljer du **Granska + skapa**. När Azure har verifierat informationen om logikappen väljer du **Skapa**.

1. När Azure har distribuerat din app väljer **du Gå till resurs**.

   Azure öppnar fönstret Logic Apps mallval som visar en introduktionsvideo, vanliga utlösare och mallmönster för logikapp.

1. Rulla nedåt förbi videoavsnitten och vanliga utlösare till **avsnittet** Mallar och välj **Tom logikapp.**

   ![Skärmbild som visar Logic Apps mallval med "Tom logikapp" markerat.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Lägg sedan till [upprepningsutlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts), som kör arbetsflödet baserat på ett angivet schema. Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när nya data uppfyller ett särskilt villkor. Mer information finns i [Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Lägg till upprepningsutlösaren

1. I sökrutan Logic Apps Designer anger du `recurrence` och väljer utlösaren med namnet **Recurrence (Upprepning).**

   ![Skärmbild som visar sökrutan Logic Apps Designer som innehåller söktermen "upprepning" och i listan "Utlösare" visas utlösaren "Upprepning" markerad.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. På formen **Upprepning** väljer du **ellipsen** (**...**) och väljer sedan Byt **namn på**. Byt namn på utlösaren med den här beskrivningen: `Check travel time every weekday morning`

   ![Skärmbild som visar den valda ellipsknappen, listan Inställningar öppnas och kommandot "Byt namn" har valts.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. I utlösaren ändrar du dessa egenskaper enligt beskrivningen och visas här.

   ![Skärmbild som visar ändringarna i utlösarens intervall och frekvens.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Intervall** | Yes | 1 | Antalet intervaller som ska förflyta mellan kontrollerna |
   | **Frekvens** | Yes | Vecka | Den tidsenhet som används för upprepningen |
   |||||

1. Under **Intervall** och **Frekvens öppnar** du listan Lägg till ny **parameter** och väljer de här egenskaperna som ska läggas till i utlösaren.

   * **Dessa dagar**
   * **Vid dessa timmar**
   * **Vid dessa minuter**

   ![Skärmbild som visar den öppna listan "Lägg till ny parameter" och de valda egenskaperna: "På dessa dagar", "Vid dessa timmar" och "Vid dessa minuter".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Ange nu värdena för de ytterligare egenskaperna som visas och beskrivs här.

   ![Skärmbild som visar ytterligare egenskaper som är inställda på värdena enligt beskrivningen i följande tabell.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Dessa dagar** | Måndag,Tisdag,Onsdag,Torsdag,Fredag | Den här inställningen är endast tillgänglig när du ställer **in Frekvens** på **Vecka**. |
   | **Vid dessa timmar** | 7,8,9 | Den här inställningen är endast tillgänglig när du ställer **in Frekvens** på **Vecka** eller **Dag.** För den här upprepningen väljer du dygnets timmar. Det här exemplet körs på `7` `8` markeringarna , och `9` -hour. |
   | **Vid dessa minuter** | 0,15,30,45 | Den här inställningen är endast tillgänglig när du ställer **in Frekvens** på **Vecka** eller **Dag.** För den här upprepningen väljer du minuter på dagen. Det här exemplet börjar vid nolltimmemarkeringen och körs var 15:e minut. |
   ||||

   Den här utlösaren utlöses varje kvart under vardagar med start kl. 7:00 och slut kl. 9:45. Upprepningsschemat visas i rutan **Förhandsgranskning**. Mer information finns i [Schemalägga aktiviteter och arbetsflöden](../connectors/connectors-native-recurrence.md) och [Arbetsflödesåtgärder och utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Om du vill dölja utlösarinformationen för tillfället döljer du figuren genom att klicka inuti figurens namnlist.

   ![Skärmbild som visar den komprimerade utlösarformen.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Spara logikappen. Välj Spara i **designerverktygsfältet.**

Logikappen är nu live i Azure Portal men gör inget annat än att utlösa baserat på det angivna schemat. Därför ska vi lägga till en åtgärd som svarar när utlösaren utlöses.

## <a name="get-the-travel-time-for-a-route"></a>Hämta restiden för en resväg

Nu när du har en utlösare lägger du till en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) som hämtar restiden mellan två platser. Logic Apps tillhandahåller en anslutningsapp för Bing Maps-API:t så att du lätt kan hämta den här informationen. Innan du börjar kontrollerar du att du har en Bing Maps API-nyckel (beskrivs i förutsättningarna för den här kursen).

1. I Logikappdesignern går du till upprepningsutlösaren och väljer **Nytt steg.**

1. Under **Välj en åtgärd** väljer du **Standard.** I sökrutan anger du `bing maps` och väljer åtgärden Get route **(Hämta väg).**

   ![Skärmbild som visar listan "Välj en åtgärd" filtrerad efter "bing maps"-åtgärder och åtgärden "Hämta väg" vald.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Om du inte har en Bing Maps-anslutning uppmanas du att skapa en anslutning. Ange anslutningsinformationen enligt beskrivningen och välj sedan **Skapa**.

   ![Skärmbild som visar rutan Bing Maps-anslutning med det angivna anslutningsnamnet och API-nyckeln för Bing Maps.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Anslutningsnamn** | Yes | BingMapsConnection | Ange ett namn på anslutningen. I det här exemplet används `BingMapsConnection`. |
   | **API-nyckel** | Yes | <*Bing-Maps-API-nyckel*> | Ange Bing Maps API-nyckeln som du tidigare fick. Om du inte har en Bing Maps-nyckel tar du reda på [hur du hämtar en nyckel](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key). |
   |||||

1. Byt namn på åtgärden med den här beskrivningen: `Get route and travel time with traffic` .

1. I åtgärden öppnar du listan **Lägg till ny parameter** och väljer dessa egenskaper.

   * **Optimera**
   * **Avståndsenhet**
   * **Travel mode** (Färdsätt)

   ![Skärmbild som visar "Hämta väg..." med egenskaperna "Optimera", "Avståndsenhet" och "Reseläge" valda.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Ange nu värdena för de egenskaper som visas och beskrivs här.

   ![Skärmbild som visar ytterligare egenskapsvärden för åtgärden "Hämta väg".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Waypoint 1** (Platsmarkör 1) | Yes | <*startplats*> | Vägens ursprung. Det här exemplet anger ett exempel på en startadress. |
   | **Waypoint 2** (Platsmarkör 2) | Yes | <*slutplats*> | Vägens mål. Det här exemplet anger en exempelmåladress. |
   | **Optimera** | No | timeWithTraffic | En parameter för att optimera färdvägen, till exempel avstånd, restid med aktuell trafik med mera. Välj parametervärdet **timeWithTraffic**. |
   | **Avståndsenhet** | No | <*vad du föredrar*> | Avståndsenhet för din resväg. I det här **exemplet används Mile** som enhet. |
   | **Travel mode** (Färdsätt) | No | Driving (Bil) | Färdsättet för din resväg. Välj **Körläge.** |
   |||||

   Mer information om dessa parametrar och värden finns i [Beräkna en väg](/bingmaps/rest-services/routes/calculate-a-route).

1. Välj Spara i **designerverktygsfältet.**

Skapa sedan en variabel så att du kan omvandla och lagra den aktuella restiden i minuter i stället för sekunder. På så sätt behöver du inte upprepa omvandlingen och det blir enklare att använda värdet i senare steg. 

## <a name="create-a-variable-to-store-travel-time"></a>Skapa en variabel för att lagra restiden

Ibland kanske du vill köra åtgärder på data i arbetsflödet och sedan använda resultatet i senare åtgärder. Om du vill spara resultaten så att du enkelt kan återanvända eller referera till dem kan du skapa variabler som lagrar dessa resultat efter bearbetningen. Du kan skapa variabler endast på den översta nivån i din logikapp.

Som standard returnerar **åtgärden Hämta** resväg den aktuella restiden med trafik i sekunder från egenskapen Travel Duration **Traffic (Restid för** trafik). Genom att omvandla och lagra det här värdet i minuter i stället för sekunder blir det enklare att använda värdet senare utan att du behöver omvandla det på nytt.

1. Välj Nytt steg under åtgärden **Hämta väg** i **designern.**

1. Under **Välj en åtgärd** väljer du **Inbyggd**. I sökrutan anger du `variables` och väljer åtgärden Med namnet Initiera **variabeln**.

   ![Skärmbild som visar åtgärden Initiera variabel vald.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Byt namn på åtgärden med den här beskrivningen: `Create variable to store travel time`

1. Ange den här informationen för variabeln som visas i den här tabellen och i stegen nedan i tabellen:

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Namn** | Yes | travelTime | Namnet på variabeln. I det här exemplet används `travelTime`. |
   | **Typ** | Yes | Integer | Datatypen för variabeln |
   | **Värde** | No | Ett uttryck som konverterar den aktuella restiden från sekunder till minuter (se stegen i den här tabellen). | Det inledande värdet för variabeln |
   |||||

   1. Om du vill skapa uttrycket för **egenskapen Värde** klickar du i rutan så att listan med dynamiskt innehåll visas. Om det behövs kan du utvidga webbläsaren tills den dynamiska listan visas. I listan med dynamiskt innehåll väljer du **Uttryck**, som visar uttrycksredigeraren.

      ![Skärmbild som visar åtgärden "Initiera variabel" med markören inuti egenskapen "Value" som öppnar listan med dynamiskt innehåll.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Listan med dynamiskt innehåll visar utdata från tidigare åtgärder som är tillgängliga för dig att välja som indata för efterföljande åtgärder i arbetsflödet. Listan med dynamiskt innehåll innehåller en uttrycksredigerare som du kan använda för att välja funktioner som utför åtgärder i uttrycket. Den här uttrycksredigeraren är bara tillgänglig i listan med dynamiskt innehåll.

   1. Ange det här uttrycket i uttrycksredigeraren: `div(,60)`

      ![Skärmbild som visar uttrycksredigeraren med uttrycket "div(,60)" angivet.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. I uttrycket placerar du markören mellan vänsterparentesen (**(**) och kommatecken (**,**) och väljer **Dynamiskt innehåll**.

      ![Skärmbild som visar var du placerar markören i uttrycket "div(,60)" med "Dynamiskt innehåll" markerat.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. I listan med dynamiskt innehåll under väljer du egenskapsvärdet Travel **Duration Traffic (Restidstrafik).**

      ![Skärmbild som visar egenskapsvärdet "Travel Duration Traffic" (Restidstrafik) valt.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. När egenskapsvärdet har matchas inuti uttrycket väljer du **OK**.

      ![Skärmbild som visar knappen "OK" som vald.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Värdeegenskapen visas nu så som det visas här: 

      ![Skärmbild som visar egenskapen "Value" med det lösta uttrycket.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Spara logikappen.

Lägg sedan till ett villkor som kontrollerar om den aktuella restiden är större än en specifik gräns.

## <a name="compare-the-travel-time-with-limit"></a>Jämför restiden med gränsen

1. Under åtgärden **Skapa variabel för att lagra restid** väljer du Nytt **steg.**

1. Under **Välj en åtgärd** väljer du **Inbyggd**. Skriv `condition` i sökrutan. och från åtgärdslistan väljer du åtgärden med namnet **Villkor**.

   ![Skärmbild som visar den valda åtgärden "Villkor"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Byt namn på villkoret med den här beskrivningen: `If travel time exceeds limit`

1. Skapa ett villkor som kontrollerar om egenskapen **travelTime** överskrider den angivna gränsen enligt beskrivningen och visas här:

   1. I villkoret, på villkorets vänstra sida, klickar du i rutan **Välj ett** värde.

   1. I listan med dynamiskt innehåll som visas under **Variabler väljer du** egenskapen **travelTime**.

      ![Skärmbild som visar rutan "Välj ett värde" på villkorets vänstra sida med den dynamiska innehållslistan öppen och egenskapen "travelTime" markerad.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. I den mellersta jämförelserutan väljer du operatorn med namnet **är större än**.

   1. Ange den här gränsen i rutan Välj ett värde **på villkorets** högra sida: `15`

      När du är klar ser villkoret ut som i det här exemplet:

      ![Skärmbild som visar det färdiga villkoret för att jämföra restiden med den angivna gränsen.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Spara logikappen.

Lägg sedan till åtgärden som ska köras när restiden överskrider din gräns.

## <a name="send-email-when-limit-exceeded"></a>Skicka ett e-postmeddelande när gränsen överskrids

Lägg nu till en åtgärd som skickar e-post när restiden överskrider din gräns. E-postmeddelandet innehåller den aktuella restiden och den extra restid som krävs för den angivna resvägen.

1. I villkorets **true-gren** väljer du Lägg **till en åtgärd**.

1. Under **Välj en åtgärd** väljer du **Standard.** Skriv `send email` i sökrutan. Listan returnerar många resultat, så för att hjälpa dig att filtrera listan väljer du först den e-postanslutning som du vill använda.

   Om du till exempel har ett Outlook-e-postkonto väljer du anslutningsappen för din kontotyp:

   * För Azure arbets- eller skolkonto väljer du **Office 365 Outlook**.
   * För personliga Microsoft-konton väljer du **Outlook.com**.

   Det här exemplet fortsätter genom att välja Office 365 Outlook.

   ![Skärmbild som visar "Välj en åtgärdslista" med standardkategorin och office 365 Outlook-anslutningsappen vald.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. När anslutningsappens åtgärder visas väljer du den åtgärd som skickar e-post, till exempel:

   ![Skärmbild som visar åtgärden "Skicka ett e-postmeddelande" markerad.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Om du inte redan har en anslutning loggar du in och autentiserar åtkomsten till ditt e-postkonto när du uppmanas att göra det.

   Azure Logic Apps skapar en anslutning till ditt e-postkonto.

1. Byt namn på åtgärden med den här beskrivningen: `Send email with travel time`

1. För **egenskapen Till** anger du mottagarens e-postadress. I testsyfte kan du använda din e-postadress.

1. För egenskapen **Ämne** anger du e-postmeddelandets ämne och inkluderar variabeln **travelTime** genom att följa dessa steg:

   1. Ange texten `Current travel time (minutes):` med ett avslutande blanksteg. Håll markören i rutan **Ämne** så att listan med dynamiskt innehåll förblir öppen.

   1. I listan med dynamiskt innehåll går du till **rubriken Variabler och** väljer Visa **mer** så att variabeln **travelTime** visas.

      ![Skärmbild som visar listan med dynamiskt innehåll med avsnittet "Variabler" och "Se mer" markerat.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > Den dynamiska innehållslistan visar inte automatiskt variabeln **travelTime** eftersom egenskapen **Subject** förväntar sig ett strängvärde, medan **travelTime är** ett heltalsvärde.

      ![Skärmbild som visar listan med dynamiskt innehåll med variabeln "travelTime" vald.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. För egenskapen **Brödtext** anger du innehållet för e-postmeddelandets brödtext genom att följa dessa steg:

   1. Ange texten `Add extra travel time (minutes):` med ett avslutande blanksteg. Håll markören i rutan **Brödtext** så att listan med dynamiskt innehåll förblir öppen.

   1. I listan med dynamiskt innehåll väljer du **Uttryck**, som visar uttrycksredigeraren.

      ![Skärmbild som visar listan med dynamiskt innehåll med "Uttryck" valt.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. I uttrycksredigeraren anger du `sub(,15)` så att du kan beräkna antalet minuter som överskrider din gräns: 

      ![Skärmbild som visar uttrycksredigeraren med uttrycket "sub(,15)" angivet.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. I uttrycket placerar du markören mellan den vänstra parentesen (**(**) och kommatecken (**,**) och väljer **Dynamiskt innehåll**.

      ![Skärmbild som visar var du placerar markören i uttrycket "sub(,15)" med "Dynamiskt innehåll" markerat.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. Välj **travelTime** (Restid) under **Variables** (Variabler).

      ![Skärmbild som visar listan med dynamiskt innehåll med variabeln "travelTime" vald.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. När egenskapen har lösts i uttrycket väljer du **OK**.

      ![Skärmbild som visar listan med dynamiskt innehåll och "OK" valt.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Egenskapen **Brödtext** visas nu så som det visas här:

      ![Skärmbild som visar listan med dynamiskt innehåll med uttrycket löst i e-poståtgärdens egenskap "Body".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Spara logikappen.

Testa och kör sedan logikappen, som nu ser ut ungefär som i det här exemplet:

![Skärmbild som visar det färdiga logikapparbetsflödet](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Kör logikappen

Starta logikappen manuellt genom att välja Kör i **designerverktygsfältet.**

* Om den aktuella restiden ligger under din gräns gör logikappen inget annat och väntar eller nästa intervall innan den kontrollerar igen. 

* Om den aktuella restiden överskrider din gräns får du ett e-postmeddelande med den aktuella restiden och antalet minuter över din gräns. Här är ett exempel på ett e-postmeddelande som logikappen skickar:

  ![Skärmbild som visar ett exempel på ett e-postmeddelande som rapporterar den aktuella restiden och den extra restiden som överskrider den angivna gränsen.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > Om du inte får e-post kan du titta i mappen Skräppost. Ditt skräppostfilter kan dirigera om dessa typer av e-post. Om du är osäker på om din logikapp kördes korrekt kan du läsa informationen om att [felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulerar! Du har nu skapat och kört en logikapp med en återkommande åtgärd enligt ett schema. 

Om du vill skapa andra logikappar som använder **upprepningsutlösaren** kan du titta på de här mallarna som är tillgängliga när du har skapat en logikapp:

* Få dagliga påminnelser skickade till dig.
* Ta bort gamla Azure-blobar.
* Lägg till ett meddelande i en Azure Storage-kö.

## <a name="clean-up-resources"></a>Rensa resurser

Logikappen fortsätter att köras tills du inaktiverar eller tar bort appen. När du inte längre behöver exempellogikappen tar du bort resursgruppen som innehåller logikappen och relaterade resurser.

1. I Azure Portal sökrutan anger du namnet på den resursgrupp som du skapade. Välj resursgruppen under **Resursgrupper** i resultatet.

   I det här exemplet skapades resursgruppen med namnet `LA-TravelTime-RG` .

   ![Skärmbild som visar Azure-sökrutan med "la-travel-time-rg" angivet och **LA-TravelTime-RG** valt.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > Om azure-startsidan visar resursgruppen under **Senaste resurser** kan du välja gruppen från startsidan.

1. På resursgruppmenyn kontrollerar du att **Översikt** har valts. I verktygsfältet **i** fönstret Översikt väljer du Ta **bort resursgrupp.**

   ![Skärmbild som visar resursgruppens "Översikt"-fönster och i fönstrets verktygsfält är "Ta bort resursgrupp" markerat.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. I bekräftelsefönstret som visas anger du resursgruppens namn och väljer Ta **bort.**

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en logikapp som kontrollerar trafik baserat på ett angivet schema (på veckodagar) och vidtar åtgärder (skickar ett e-postmeddelande) när restiden överskrider en angiven gräns. Nu kan du lära dig hur du skapar en logikapp som skickar begäranden om distributionslista för godkännande genom att integrera Azure-tjänster, Microsoft-tjänster och andra SaaS-appar (Programvara som en tjänst).

> [!div class="nextstepaction"]
> [Hantera begäranden om distributionslista](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
