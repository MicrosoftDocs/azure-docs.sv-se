---
title: Skapa automatiseringsuppgifter för att hantera och övervaka Azure-resurser
description: Konfigurera automatiserade uppgifter som hjälper dig att hantera Azure-resurser och övervaka kostnader genom att skapa arbetsflöden som körs Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/05/2021
ms.openlocfilehash: 0a98f9e4b108d2498fa19bc0b041f9d52272c7d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774944"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Hantera Azure-resurser och övervaka kostnader genom att skapa automatiseringsuppgifter (förhandsversion)

> [!IMPORTANT]
> Den här funktionen är tillgänglig som en offentlig förhandsversion utan servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

För att hjälpa dig att hantera [Azure-resurser](../azure-resource-manager/management/overview.md#terminology) enklare kan du skapa automatiserade hanteringsuppgifter för en specifik resurs eller resursgrupp med hjälp av automatiseringsaktivitetsmallar, som varierar i tillgänglighet baserat på resurstypen. För ett [Azure Storage-konto kan du till](../storage/common/storage-account-overview.md)exempel konfigurera en automationsaktivitet som skickar månadskostnaden för det lagringskontot. För en [virtuell Azure-dator](https://azure.microsoft.com/services/virtual-machines/)kan du skapa en automatiseringsuppgift som aktiverar eller inaktiverar den virtuella datorn enligt ett fördefinierat schema.

I bakgrunden är en automatiseringsuppgift i själva verket ett arbetsflöde som körs [på Azure Logic Apps-tjänsten](../logic-apps/logic-apps-overview.md) och debiteras med samma [priser](https://azure.microsoft.com/pricing/details/logic-apps/) och [prismodell.](../logic-apps/logic-apps-pricing.md) När du har skapat uppgiften kan du visa och redigera det underliggande arbetsflödet genom att öppna uppgiften i Logikappdesignern. När en uppgift har körts klart kan du granska status, historik, indata och utdata för varje körning.

Här är de tillgängliga uppgiftsmallarna i den här förhandsversionen:

| Resurstyp | Mallar för Automation-uppgifter |
|---------------|---------------------------|
| Azure-resursgrupper | **När resursen tas bort** |
| Alla Azure-resurser | **Skicka månadskostnad för resurs** |
| Virtuella Azure-datorer | Dessutom: <p>- **Stänga av virtuell dator** <br>- **Starta virtuell dator** |
| Azure Storage-konton | Dessutom: <p>- **Ta bort gamla blobar** |
| Azure Cosmos DB | Dessutom <p>- **Skicka frågeresultat via e-post** |
|||

Den här artikeln visar hur du utför följande uppgifter:

* [Skapa en automatiseringsuppgift](#create-automation-task) för en specifik Azure-resurs.

* [Granska en uppgifts historik](#review-task-history), som innehåller körningsstatus, indata, utdata och annan historisk information.

* [Redigera uppgiften så](#edit-task) att du kan uppdatera uppgiften eller anpassa aktivitetens underliggande arbetsflöde i Logikappdesignern.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>Hur skiljer sig automatiseringsuppgifter från Azure Automation?

För närvarande kan du bara skapa en automatiseringsaktivitet på [resursnivå,](../logic-apps/logic-apps-overview.md) visa aktivitetens körningshistorik och redigera aktivitetens underliggande logikapparbetsflöde, som drivs av Azure Logic Apps-tjänsten. Automatiseringsuppgifter är enklare och enklare än [att Azure Automation](../automation/automation-intro.md).

Som jämförelse är Azure Automation en molnbaserad automatiserings- och konfigurationstjänst som stöder konsekvent hantering i dina Azure- och icke-Azure-miljöer. Tjänsten omfattar [processautomatisering för](../automation/automation-intro.md#process-automation) orkestrering av processer med hjälp av [runbooks, konfigurationshantering med ändringsspårning](../automation/automation-runbook-execution.md)och inventering, uppdateringshantering, delade funktioner och heterogena funktioner. [](../automation/change-tracking/overview.md) Automatisering ger dig fullständig kontroll under distribution, drift och inaktivering av arbetsbelastningar och resurser.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Den Azure-resurs som du vill hantera. I den här artikeln används ett Azure Storage-konto som exempel.

* Ett Office 365-konto om du vill följa med i exemplet, som skickar dig e-post med hjälp av Office 365 Outlook.

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>Skapa en automatiseringsuppgift

1. I [Azure Portal](https://portal.azure.com)du den resurs som du vill hantera.

1. På resursmenyn bläddrar du till **avsnittet Automation** och väljer **Uppgifter**

   ![Skärmbild som visar Azure Portal och en resursmeny för lagringskontot där menyalternativet "Uppgifter" är markerat i avsnittet "Automation".](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. I fönstret **Uppgifter** väljer du Lägg **till** så att du kan välja en uppgiftsmall.

   ![Skärmbild som visar fönstret "Uppgifter" för lagringskontot där verktygsfältet har "Lägg till" markerat](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. I fönstret **Lägg till en** uppgift under Välj en **mall** väljer du mallen för den uppgift som du vill skapa. Om nästa sida inte visas väljer du **Nästa: Autentisering**.

   Det här exemplet fortsätter genom att välja **mallen Skicka månadskostnad för** resursaktivitet.

   ![Skärmbild som visar valen "Skicka månadskostnad för resurs" och "Nästa: Autentisering"](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. Under **Autentisering** går du till **avsnittet** Anslutningar och väljer **Skapa** för varje anslutning som visas i uppgiften så att du kan ange autentiseringsuppgifter för alla anslutningar. Anslutningstyperna i varje aktivitet varierar beroende på aktivitet.

   Det här exemplet visar bara en av de anslutningar som krävs av den här aktiviteten.

   ![Skärmbild som visar det valda alternativet "Skapa" för Azure Resource Manager anslutningen](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. När du uppmanas till det loggar du in med dina autentiseringsuppgifter för Azure-kontot.

   ![Skärmbild som visar valet "Logga in"](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   Varje autentiserad anslutning ser ut ungefär som i det här exemplet:

   ![Skärmbild som visar att anslutningen har skapats](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. När du har autentiserat alla anslutningar **väljer du Nästa:** Konfiguration om nästa sida inte visas.

1. Under **Konfiguration** anger du ett namn på aktiviteten och eventuell annan information som krävs för uppgiften. När du är färdig väljer du **Skapa**.

   > [!NOTE]
   > Du kan inte ändra aktivitetsnamnet när du har skapat det, så tänk på ett namn som fortfarande gäller om du [redigerar det underliggande arbetsflödet](#edit-task-workflow). Ändringar som du gör i det underliggande arbetsflödet gäller endast för den uppgift som du har skapat, inte uppgiftsmallen.
   >
   > Om du till exempel ger aktiviteten namnet , men senare redigerar det underliggande arbetsflödet så att det körs varje vecka, kan du inte ändra `Send monthly cost` aktivitetens namn till `Send weekly cost` .

   Uppgifter som skickar e-postaviseringar kräver en e-postadress.

   ![Skärmbild som visar nödvändig information för den valda uppgiften](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   Den uppgift som du skapade, som är live och körs automatiskt, visas nu i **listan Automation-uppgifter.**

   ![Skärmbild som visar listan med automatiseringsuppgifter](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > Om uppgiften inte visas omedelbart kan du prova att uppdatera uppgiftslistan eller vänta lite innan du uppdaterar. Välj Uppdatera i **verktygsfältet.**

   När den valda uppgiften körs får du ett e-postmeddelande som ser ut som i det här exemplet:

   ![Skärmbild som visar e-postaviseringar som skickats av uppgift](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Granska aktivitetshistorik

Följ dessa steg om du vill visa en aktivitets historik över körningar tillsammans med deras status, indata, utdata och annan information:

1. I [Azure Portal](https://portal.azure.com)du den resurs som har den aktivitetshistorik som du vill granska.

1. På resursens meny går du till **Inställningar och** väljer **Automation-uppgifter.**

1. I uppgiftslistan hittar du den uppgift som du vill granska. I kolumnen Körningar **för aktiviteten** väljer du **Visa**.

   ![Skärmbild som visar en uppgift och det valda alternativet "Visa"](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   Fönstret **Körningshistorik** visar alla körningar för uppgiften tillsammans med deras status, starttider, identifierare och körningstid.

   ![Skärmbild som visar en uppgifts körningar, deras status och annan information](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   Här visas möjliga statusar för en körning:

   | Status | Beskrivning |
   |--------|-------------|
   | **Annullerat** | Aktiviteten avbröts när den kördes. |
   | **Misslyckades** | Uppgiften har minst en misslyckad åtgärd, men det fanns inga efterföljande åtgärder för att hantera felet. |
   | **Körs** | Aktiviteten körs för närvarande. |
   | **Lyckades** | Alla åtgärder lyckades. En uppgift kan fortfarande slutföras om en åtgärd misslyckades, men det fanns en efterföljande åtgärd för att hantera felet. |
   | **Väntar** | Körningen har inte startat än och har pausats eftersom en tidigare instans av aktiviteten fortfarande körs. |
   |||

   Mer information finns i Granska [körningshistorik](../logic-apps/monitor-logic-apps.md#review-runs-history)

1. Om du vill visa statusar och annan information för varje steg i en körning väljer du den körningen.

   Fönstret **Logikappskörning** öppnas och visar det underliggande arbetsflöde som kördes.

   * Ett arbetsflöde börjar alltid med en [*utlösare*](../connectors/apis-list.md#triggers). För den här uppgiften börjar arbetsflödet med [ **upprepningsutlösaren**](../connectors/connectors-native-recurrence.md).

   * Varje steg visar status och körningens varaktighet. Steg med varaktighet på 0 sekunder tog mindre än 1 sekund att köra.

   ![Skärmbild som visar varje steg i körningen, statusen och körningens varaktighet](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. Om du vill granska indata och utdata för varje steg väljer du steget, som expanderar.

   Det här exemplet visar indata för upprepningsutlösaren, som inte har några utdata eftersom utlösaren endast anger när arbetsflödet körs och inte ger några utdata för efterföljande åtgärder som ska bearbetas.

   ![Skärmbild som visar den expanderade utlösaren och indata](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   Åtgärden Skicka ett **e-postmeddelande har** däremot indata från tidigare åtgärder i arbetsflödet och utdata.

   ![Skärmbild som visar en utökad åtgärd, indata och utdata](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Information om hur du kan skapa egna automatiserade arbetsflöden så att du kan integrera appar, data, tjänster och system, förutom automationsuppgifter för Azure-resurser, finns i [Snabbstart: Skapa](../logic-apps/quickstart-create-first-logic-app-workflow.md)ditt första integreringsarbetsflöde med hjälp av Azure Logic Apps – Azure Portal .

<a name="edit-task"></a>

## <a name="edit-the-task"></a>Redigera uppgiften

Om du vill ändra en uppgift har du följande alternativ:

* [Redigera aktiviteten "infogade"](#edit-task-inline) så att du kan ändra aktivitetens egenskaper, till exempel anslutningsinformation eller konfigurationsinformation, till exempel din e-postadress.

* [Redigera aktivitetens underliggande arbetsflöde i](#edit-task-workflow) Logikappdesignern.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>Redigera den infogade aktiviteten

1. I [Azure Portal](https://portal.azure.com)du den resurs som har den uppgift som du vill uppdatera.

1. På resursens meny går du till **Automation och** väljer **Uppgifter.**

1. I uppgiftslistan hittar du den uppgift som du vill uppdatera. Öppna aktivitetens ellipsmeny (**...**) och välj **Redigera på rad.**

   ![Skärmbild som visar den öppna ellipsmenyn och det valda alternativet "Redigera på rad"](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   Fliken Autentisering visas **som** standard och visar de befintliga anslutningarna.

1. Om du vill lägga till nya autentiseringsuppgifter eller välja olika befintliga autentiseringsuppgifter för en anslutning öppnar du anslutningens ellipsmeny (**...**) och väljer antingen **Lägg** till ny anslutning eller, om det är tillgängligt, andra autentiseringsuppgifter.

   ![Skärmbild som visar fliken Autentisering, befintliga anslutningar och den valda ellipsmenyn](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. Om du vill uppdatera andra aktivitetsegenskaper väljer **du Nästa: Konfiguration.**

   För uppgiften i det här exemplet är e-postadressen den enda egenskapen som är tillgänglig för redigering.

   ![Skärmbild som visar fliken Konfiguration](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. När du är klar väljer du **Spara**.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>Redigera aktivitetens underliggande arbetsflöde

När du ändrar det underliggande arbetsflödet för en automationsaktivitet påverkar ändringarna bara den aktivitetsinstans som du skapade och inte mallen som skapar uppgiften. När du har gjort och sparat ändringarna kanske namnet som du angav för den ursprungliga uppgiften inte längre beskriver uppgiften korrekt, så du kan behöva återskapa uppgiften med ett annat namn.

> [!TIP]
> Det är bästa praxis att klona det underliggande arbetsflödet så att du kan redigera den kopierade versionen i stället. På så sätt kan du göra och testa ändringarna på kopian medan den ursprungliga automatiseringsuppgiften fortsätter att fungera och köras utan att störa eller bryta befintliga funktioner. När du har slutfört ändringarna och är nöjd med att den nya versionen körs kan du inaktivera eller ta bort den ursprungliga automationsaktiviteten och använda den klonade versionen för automationsaktiviteten. Följande steg innehåller information om hur du klonar arbetsflödet.

1. I [Azure Portal](https://portal.azure.com)du den resurs som har den uppgift som du vill uppdatera.

1. På resursens meny går du till **Automation och** väljer **Uppgifter.**

1. I uppgiftslistan hittar du den uppgift som du vill uppdatera. Öppna uppgiftens ellipsmeny (**...**) och välj **Öppna i Logic Apps**.

   ![Skärmbild som visar den öppna ellipsmenyn och det valda alternativet "Öppna i Logic Apps"](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   Aktivitetens underliggande arbetsflöde öppnas i Azure Logic Apps-tjänsten och  visar fönstret Översikt där du kan visa samma körningshistorik som är tillgänglig för aktiviteten.

   ![Skärmbild som visar uppgiften i Azure Logic Apps med fönstret Översikt valt](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. Om du vill öppna det underliggande arbetsflödet i Logikappdesignern går du till logikappens meny och väljer **Logikappdesigner.**

   ![Skärmbild som visar menyalternativet "Logikappdesigner" markerat och designytan med det underliggande arbetsflödet](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   Nu kan du redigera egenskaperna för arbetsflödets utlösare och åtgärder samt redigera utlösaren och åtgärderna som definierar själva arbetsflödet. Som bästa praxis bör du dock följa stegen för att klona arbetsflödet så att du kan göra ändringarna på en kopia medan det ursprungliga arbetsflödet fortsätter att fungera och köras.

1. Följ dessa steg om du vill klona arbetsflödet och redigera den kopierade versionen i stället:

   1. På logikappens arbetsflödesmeny väljer du **Översikt.**

   1. I verktygsfältet i översiktsfönstret väljer du **Klona**.

   1. Ange ett nytt namn för det kopierade **logikapparbetsflödet** under Namn i fönstret för att skapa logikappen.

      Förutom **Logic App Status** är de andra egenskaperna inte tillgängliga för redigering. 
      
   1. Under **Status för logikapp** väljer **du** Inaktiverad så att det klonade arbetsflödet inte körs medan du gör dina ändringar. Du kan aktivera arbetsflödet när du är redo att testa ändringarna.

   1. När Azure har etablerat det klonade arbetsflödet kan du söka efter och öppna arbetsflödet i Logic App Designer.

1. Om du vill visa egenskaperna för utlösaren eller en åtgärd expanderar du utlösaren eller åtgärden.

   Du kan till exempel ändra upprepningsutlösaren så att den körs varje vecka i stället för varje månad.

   ![Skärmbild som visar den expanderade upprepningsutlösaren med listan Frekvens öppen för att visa tillgängliga frekvensalternativ](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   Mer information om upprepningsutlösaren finns i [Skapa, schemalägga och köra återkommande uppgifter och arbetsflöden med upprepningsutlösaren](../connectors/connectors-native-recurrence.md). Mer information om andra utlösare och åtgärder som du kan använda finns i [Anslutningsappar för Azure Logic Apps](../connectors/apis-list.md).

1. Spara ändringarna genom att välja Spara i **designerverktygsfältet.**

   ![Skärmbild som visar designerverktygsfältet och det valda kommandot "Spara"](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. Om du vill testa och köra det uppdaterade arbetsflödet går du till designerverktygsfältet och väljer **Kör**.

   När körningen är klar visar designern körningsinformationen för arbetsflödet.

   ![Skärmbild som visar arbetsflödets körningsinformation i designern](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. Om du vill inaktivera arbetsflödet så att aktiviteten inte fortsätter att köras kan du gå [till Hantera logikappar i Azure Portal](../logic-apps/manage-logic-apps-with-azure-portal.md).

## <a name="provide-feedback"></a>Ge feedback

Vi vill gärna höra från dig! Om du vill rapportera buggar, ge feedback eller ställa frågor om den här förhandsversionen kan [du kontakta Azure Logic Apps team](mailto:logicappspm@microsoft.com).

## <a name="next-steps"></a>Nästa steg

* [Hantera logikappar i Azure Portal](../logic-apps/manage-logic-apps-with-azure-portal.md)
