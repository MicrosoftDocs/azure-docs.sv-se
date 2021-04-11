---
title: Haveri beredskap för integrations konton
description: Konfigurera integrations konton och B2B-artefakter med haveri beredskap mellan regioner i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/10/2017
ms.openlocfilehash: 09fb738a499d6016c3aead0d33436f034bc5d339
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728410"
---
# <a name="set-up-cross-region-disaster-recovery-for-integration-accounts-in-azure-logic-apps"></a>Konfigurera katastrof återställning mellan regioner för integrations konton i Azure Logic Apps

B2B-arbetsbelastningar innefattar pengar transaktioner som order och fakturor. Under en katastrof händelse är det viktigt för en verksamhet att snabbt återställas för att möta service avtal på företags nivå som överenskommits med sina partner. Den här artikeln visar hur du skapar en verksamhets kontinuitets plan för B2B-arbetsbelastningar. 

* Beredskap för haveri beredskap 
* Redundansväxla till sekundär region under en katastrof händelse 
* Återgå till primär region efter en katastrof händelse

## <a name="disaster-recovery-readiness"></a>Beredskap för haveri beredskap  

1. Identifiera en sekundär region och skapa ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i den sekundära regionen.

2. Lägg till partners, scheman och avtal för de nödvändiga meddelande flöden där körnings statusen måste replikeras till integrations kontot för sekundär region.

   > [!TIP]
   > Se till att det finns en konsekvens i integrerings kontots artefakts namngivnings konvention i flera regioner. 

3. Om du vill hämta körnings statusen från den primära regionen skapar du en Logic-app i den sekundära regionen. 

   Den här Logic-appen bör ha en *utlösare* och en *åtgärd*. 
   Utlösaren ska ansluta till integrations kontot för den primära regionen och åtgärden bör ansluta till integrations kontot för den sekundära regionen. 
   Baserat på tidsintervallet avsöker utlösaren den primära regionens körnings status tabell och hämtar de nya posterna, om sådana finns. Åtgärden uppdaterar dem till integrations kontot för den sekundära regionen. 
   Detta hjälper till att få stegvis körnings status från primär region till sekundär region.

4. Affärs kontinuitet i Logic Apps integrations kontot har utformats för att stödja baserat på B2B-protokoll – X12, AS2 och EDIFACT. Om du vill hitta detaljerade steg väljer du respektive länkar.

5. Rekommendationen är att distribuera alla primära region resurser i en sekundär region. 

   Primära region resurser omfattar Azure SQL Database eller Azure Cosmos DB Azure Service Bus och Azure Event Hubs som används för meddelande hantering, Azure-API Management och Azure Logic Apps-funktionen i Azure App Service.   

6. Upprätta en anslutning från en primär region till en sekundär region. Om du vill hämta körnings statusen från en primär region skapar du en logisk app i en sekundär region. 

   Logic-appen bör ha en utlösare och en åtgärd. 
   Utlösaren ska ansluta till ett integrations konto för den primära regionen. 
   Åtgärden bör ansluta till ett integrations konto för sekundär region. 
   Baserat på tidsintervallet avsöker utlösaren den primära regionens körnings status tabell och hämtar de nya posterna, om sådana finns. 
   Åtgärden uppdaterar dem till ett integrations konto för sekundär region. 
   Den här processen hjälper till att få stegvis körnings status från den primära regionen till den sekundära regionen.

Affärs kontinuitet i ett Logic Apps integrations konto ger stöd baserat på B2B-protokollen X12, AS2 och EDIFACT. Detaljerade anvisningar om hur du använder X12 och AS2 finns i [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) och [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) i den här artikeln.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Redundansväxla till en sekundär region under en katastrof händelse

När den primära regionen inte är tillgänglig för affärs kontinuitet under en katastrof händelse, dirigerar du trafiken till den sekundära regionen. En sekundär region hjälper en verksamhet att snabbt återställa funktioner för att möta de återställnings-/RTO som överenskommits av deras partner. Det minimerar också ansträngningarna att redundansväxla från en region till en annan region. 

Det finns en förväntad svars tid när kontroll nummer kopieras från en primär region till en sekundär region. För att undvika att skicka dubbletter av genererade kontroll nummer till partner under en katastrof händelse, är rekommendationen att öka kontroll numren i de sekundära region avtalen med hjälp av [PowerShell-cmdletar](/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Återgå till en primär region efter katastrof händelse

Följ dessa steg om du vill återgå till en primär region när den är tillgänglig:

1. Sluta ta emot meddelanden från partner i den sekundära regionen.  

2. Öka de genererade kontroll numren för alla de primära region avtalen med hjälp av [PowerShell-cmdletar](/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn).  

3. Dirigera trafik från den sekundära regionen till den primära regionen.

4. Kontrol lera att Logic-appen som skapats i den sekundära regionen för att hämta körnings status från den primära regionen är aktive rad.

## <a name="x12"></a>X12 

Affärs kontinuitet för EDI X12-dokument baseras på kontroll nummer:

> [!TIP]
> Du kan också använda [snabb starts mal len X12](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) för att skapa Logic Apps. Du måste skapa primära och sekundära integrations konton för att kunna använda mallen. Mallen hjälper till att skapa två Logic Apps, en för mottagna kontroll nummer och en annan för genererade kontroll nummer. Respektive utlösare och åtgärder skapas i Logi Kap par och ansluter utlösaren till det primära integrations kontot och åtgärden till det sekundära integrations kontot.

**Förutsättningar**

Om du vill aktivera haveri beredskap för inkommande meddelanden väljer du de duplicerade kontroll inställningarna i X12-avtalets mottagnings inställningar.

![Välj duplicera kontroll inställningar](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Skapa en [logisk app](../logic-apps/quickstart-create-first-logic-app-workflow.md) i en sekundär region.    

2. Sök på **X12** och välj **X12 – när ett kontroll nummer ändras**.   

   ![Sök efter X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Utlösaren efterfrågar att du upprättar en anslutning till ett integrations konto. 
   Utlösaren ska vara ansluten till ett integrations konto för den primära regionen.

3. Ange ett anslutnings namn, Välj ditt *primära region integrations konto* i listan och välj **skapa**.   

   ![Skärm bild som visar var du anger ett namn på anslutningen och väljer ditt integrations konto för den primära regionen. ](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. Inställningen **datetime för start av kontroll numret** är valfri. **Frekvensen** kan anges till **dag**, **timme**, **minut** eller **sekund** med ett intervall.   

   ![Skärm bild som visar inställningen DateTime för att starta kontroll nummerens synkronisering.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Välj **Nytt steg** > **Lägg till en åtgärd**.

   ![Skärm bild som visar knappen Nytt steg och alternativet Lägg till en åtgärd.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Sök på **X12** och välj **X12 – Lägg till eller uppdatera kontroll nummer**.   

   ![Lägg till eller uppdatera kontroll nummer](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Om du vill ansluta en åtgärd till ett integrations konto för sekundär region väljer du **ändra anslutning**  >  **Lägg till ny anslutning** för att visa en lista över tillgängliga integrations konton. Ange ett anslutnings namn, Välj ditt *integrerings konto för sekundär region* i listan och välj **skapa**. 

   ![Skärm bild som visar var du kan lägga till ett namn för integrations kontot för sekundär region.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Växla till rå data genom att klicka på ikonen i det övre högra hörnet.

   ![Skärm bild som visar ikonen som ska väljas för att växla till RAW.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Välj brödtext från väljaren för dynamiskt innehåll och spara Logic-appen.

   ![Fält för dynamiskt innehåll](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Baserat på tidsintervallet avsöker utlösaren den primära regionen mottagen kontroll nummer tabell och hämtar de nya posterna. 
   Åtgärden uppdaterar posterna i integrations kontot för den sekundära regionen. 
   Om det inte finns några uppdateringar visas utlösarens status som **överhoppad**.   

   ![Kontroll nummer tabell](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Baserat på tidsintervallet replikeras den stegvisa körnings statusen från en primär region till en sekundär region. När den primära regionen inte är tillgänglig under en katastrof händelse, dirigerar du trafiken till den sekundära regionen för affärs kontinuitet. 

## <a name="edifact"></a>EDIFACT 

Affärs kontinuitet för EDI EDIFACT-dokument baseras på kontroll nummer.

**Förutsättningar**

Om du vill aktivera haveri beredskap för inkommande meddelanden väljer du de duplicerade kontroll inställningarna i EDIFACT-avtalets mottagnings inställningar.

![Skärm bild som visar de duplicerade kontroll inställningarna i EDIFACT-avtalets mottagnings inställningar.](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Skapa en [logisk app](../logic-apps/quickstart-create-first-logic-app-workflow.md) i en sekundär region.    

2. Sök på **EDIFACT** och välj **EDIFACT – när ett kontroll nummer ändras**.

   ![Sök efter EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Utlösaren efterfrågar att du upprättar en anslutning till ett integrations konto. 
   Utlösaren ska vara ansluten till ett integrations konto för den primära regionen. 

3. Ange ett anslutnings namn, Välj ditt *primära region integrations konto* i listan och välj **skapa**.    

   ![Namn på integrations konto för primär region](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. Inställningen **datetime för start av kontroll numret** är valfri. **Frekvensen** kan anges till **dag**, **timme**, **minut** eller **sekund** med ett intervall.    

   ![Skärm bild som visar inställningarna för datum/tid och frekvens.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Välj **Nytt steg** > **Lägg till en åtgärd**.    

   ![Skärm bild som visar var du väljer Lägg till en åtgärd.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Sök på **EDIFACT** och välj **EDIFACT – Lägg till eller uppdatera kontroll nummer**.   

   ![Skärm bild som visar var du kan lägga till eller uppdatera kontroll nummer.](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Om du vill ansluta en åtgärd till ett integrations konto för sekundär region väljer du **ändra anslutning**  >  **Lägg till ny anslutning** för att visa en lista över tillgängliga integrations konton. Ange ett anslutnings namn, Välj ditt *integrerings konto för sekundär region* i listan och välj **skapa**.

   ![Skapa ett namn på integrations konto för sekundär region.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Växla till rå data genom att klicka på ikonen i det övre högra hörnet.

   ![Skärm bild som visar ikonen för att välja när du vill växla till rå data.](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Välj brödtext från väljaren för dynamiskt innehåll och spara Logic-appen.   

   ![Skärm bild som visar den dynamiska innehålls väljaren där du kan välja brödtext från.](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Baserat på tidsintervallet avsöker utlösaren den primära regionen mottagen kontroll nummer tabell och hämtar de nya posterna.
   Åtgärden uppdaterar posterna till integrations kontot för den sekundära regionen. 
   Om det inte finns några uppdateringar visas utlösarens status som **överhoppad**.

   ![Kontroll nummer tabell](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Baserat på tidsintervallet replikeras den stegvisa körnings statusen från en primär region till en sekundär region. När den primära regionen inte är tillgänglig under en katastrof händelse, dirigerar du trafiken till den sekundära regionen för affärs kontinuitet. 

## <a name="as2"></a>AS2 

Affärs kontinuitet för dokument som använder AS2-protokollet baseras på meddelande-ID och MIC-värde.

> [!TIP]
> Du kan också använda [snabb starts mal len AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) för att skapa Logic Apps. Du måste skapa primära och sekundära integrations konton för att kunna använda mallen. Mallen hjälper till att skapa en logisk app som har en utlösare och en åtgärd. Logic-appen skapar en anslutning från en utlösare till ett primärt integrations konto och en åtgärd till ett sekundärt integrations konto.

1. Skapa en [Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md) i den sekundära regionen.  

2. Sök på **AS2** och välj **AS2 – när ett MIC-värde skapas**.   

   ![Sök efter AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   En utlösare efterfrågar att du upprättar en anslutning till ett integrations konto. 
   Utlösaren ska vara ansluten till ett integrations konto för den primära regionen. 
   
3. Ange ett anslutnings namn, Välj ditt *primära region integrations konto* i listan och välj **skapa**.

   ![Skärm bild som visar var du anger ett anslutnings namn när ett MIC-värde skapas.](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. Inställningen **datetime för start av mikrofon värde** är valfri. **Frekvensen** kan anges till **dag**, **timme**, **minut** eller **sekund** med ett intervall.   

   ![DateTime och frekvens](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Välj **Nytt steg** > **Lägg till en åtgärd**.  

   ![Nytt steg, Lägg till en åtgärd](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Sök på **AS2** och välj **AS2 – Lägg till eller uppdatera MIC-innehåll**.  

   ![Tillägg eller uppdatering av MIC](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Om du vill ansluta en åtgärd till ett sekundärt integrations konto väljer du **ändra anslutning**  >  **Lägg till ny anslutning** för att visa en lista över tillgängliga integrations konton. Ange ett anslutnings namn, Välj ditt *integrerings konto för sekundär region* i listan och välj **skapa**.

   ![Namn på integrations konto för sekundär region](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Växla till rå data genom att klicka på ikonen i det övre högra hörnet.

   ![Växla till rå data](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Välj brödtext från väljaren för dynamiskt innehåll och spara Logic-appen.   

   ![Dynamiskt innehåll](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Baserat på tidsintervallet avsöker utlösaren den primära region tabellen och hämtar de nya posterna. Åtgärden uppdaterar dem till integrations kontot för den sekundära regionen. 
   Om det inte finns några uppdateringar visas utlösarens status som **överhoppad**.  

   ![Primär region tabell](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Baserat på tidsintervallet replikeras den stegvisa körnings statusen från den primära regionen till den sekundära regionen. När den primära regionen inte är tillgänglig under en katastrof händelse, dirigerar du trafiken till den sekundära regionen för affärs kontinuitet. 

## <a name="next-steps"></a>Nästa steg

[Övervaka B2B-meddelanden med Azure Monitor-loggar](../logic-apps/monitor-b2b-messages-log-analytics.md)
