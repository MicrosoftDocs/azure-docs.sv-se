---
title: Skapa automatiserings uppgifter för att hantera och övervaka Azure-resurser
description: Konfigurera automatiserade uppgifter som hjälper dig att hantera Azure-resurser och övervaka kostnader genom att skapa arbets flöden som körs på Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 8180fe8554e5fff83e4caef8c245839518649ca1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719057"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Hantera Azure-resurser och övervaka kostnader genom att skapa automatiserings uppgifter (för hands version)

> [!IMPORTANT]
> Den här funktionen är tillgänglig som en offentlig förhandsversion utan servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

För att hjälpa dig att hantera [Azure-resurser](../azure-resource-manager/management/overview.md#terminology) enklare kan du skapa automatiserade hanterings uppgifter för en speciell resurs eller resurs grupp med hjälp av mallar för automatisering, som varierar i tillgänglighet baserat på resurs typen. För ett [Azure Storage-konto](../storage/common/storage-account-overview.md)kan du till exempel konfigurera en automatiserings uppgift som skickar dig månads kostnaden för det lagrings kontot. För en [virtuell Azure-dator](https://azure.microsoft.com/services/virtual-machines/)kan du skapa en automatiserings uppgift som aktiverar eller inaktiverar den virtuella datorn enligt ett fördefinierat schema.

I bakgrunden är en automatiserings uppgift i själva verket ett arbets flöde som körs på den [Azure Logic Apps](../logic-apps/logic-apps-overview.md) tjänsten och faktureras med samma [pris sättnings](https://azure.microsoft.com/pricing/details/logic-apps/) priser och [pris sättnings modell](../logic-apps/logic-apps-pricing.md). När du har skapat uppgiften kan du Visa och redigera det underliggande arbets flödet genom att öppna uppgiften i Logic App Designer. När en aktivitet har slutfört minst en körning kan du granska status, historik, indata och utdata för varje körning.

Här är tillgängliga uppgiftsmallar i den här för hands versionen:

| Resurstyp | Uppgiftsmallar för automatisering |
|---------------|---------------------------|
| Azure-resursgrupper | **När resursen tas bort** |
| Alla Azure-resurser | **Skicka månatlig kostnad för resurs** |
| Virtuella Azure-datorer | Dessutom: <p>- **Stäng av virtuell dator** <br>- **Starta virtuell dator** |
| Azure Storage-konton | Dessutom: <p>- **Ta bort gamla blobbar** |
| Azure Cosmos DB | Dessutom <p>- **Skicka frågeresultat via e-post** |
|||

Den här artikeln visar hur du utför följande uppgifter:

* [Skapa en automatiserings uppgift](#create-automation-task) för en angiven Azure-resurs.

* [Granska aktivitetens historik](#review-task-history), som innehåller körnings status, indata, utdata och annan historisk information.

* [Redigera uppgiften](#edit-task) så att du kan uppdatera uppgiften, eller anpassa aktivitetens underliggande arbets flöde i Logic Apps designer.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>Hur skiljer sig automatiserings uppgifterna från Azure Automation?

För närvarande kan du bara skapa en automatiserings uppgift på resurs nivå, visa aktivitetens körnings historik och redigera aktivitetens underliggande Logic app-arbetsflöde, som drivs av [Azure Logic Apps](../logic-apps/logic-apps-overview.md) tjänsten. Automatiserings aktiviteter är mer grundläggande och lätta än [Azure Automation](../automation/automation-intro.md).

I jämförelse är Azure Automation en molnbaserad automatiserings-och konfigurations tjänst som stöder konsekvent hantering i dina Azure-och icke-Azure-miljöer. Tjänsten omfattar [process automatisering för att dirigera processer](../automation/automation-intro.md#process-automation) med hjälp av [Runbooks](../automation/automation-runbook-execution.md), konfigurations hantering med [ändrings spårning och inventering](../automation/change-tracking/overview.md), uppdaterings hantering, delade funktioner och heterogena funktioner. Automation ger dig fullständig kontroll under distribution, drift och inaktive ring av arbets belastningar och resurser.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Den Azure-resurs som du vill hantera. I den här artikeln används ett Azure Storage-konto som exempel.

* Ett Office 365-konto om du vill följa med exemplet, som skickar e-post med hjälp av Office 365 Outlook.

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>Skapa en automatiserings uppgift

1. Leta upp den resurs som du vill hantera i [Azure Portal](https://portal.azure.com).

1. I menyn resurs bläddrar du till avsnittet **Automation** och väljer **aktiviteter**

   ![Skärm bild som visar den Azure Portal och en resurs meny för lagrings konton där meny alternativet "uppgifter" har marker ATS för "Automation"-avsnittet.](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. I fönstret **uppgifter** väljer du **Lägg till** så att du kan välja en uppgiftsmall.

   ![Skärm bild som visar fönstret med lagrings kontots aktiviteter där verktygsfältet har valt Lägg till](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. I fönstret **Lägg till en åtgärd** väljer du mallen för den aktivitet som du vill skapa under **Välj en mall**. Om nästa sida inte visas väljer du **Nästa: autentisering**.

   Det här exemplet fortsätter genom att välja den **Skicka månads kostnaden för resurs** aktivitets mal len.

   ![Skärm bild som visar valen, "skicka månatlig kostnad för resursen" och "Nästa: autentisering"](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. Under **autentisering** i avsnittet **anslutningar** väljer du **skapa** för varje anslutning som visas i aktiviteten så att du kan ange autentiseringsuppgifter för alla anslutningar. Typerna av anslutningar i varje aktivitet varierar beroende på aktiviteten.

   I det här exemplet visas endast en av de anslutningar som krävs för den här aktiviteten.

   ![Skärm bild som visar det valda alternativet "skapa" för Azure Resource Manager anslutningen](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. När du uppmanas till det loggar du in med dina autentiseringsuppgifter för Azure-kontot.

   ![Skärm bild som visar valet, "logga in"](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   Varje lyckad autentiserad anslutning ser ut ungefär som i det här exemplet:

   ![Skärm bild som visar att anslutningen har skapats](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. När du har autentiserat alla anslutningar väljer du **Nästa: konfiguration** om nästa sida inte visas.

1. Under **konfiguration** anger du ett namn för uppgiften och annan information som krävs för uppgiften. När du är färdig väljer du **Skapa**.

   > [!NOTE]
   > Du kan inte ändra aktivitets namnet när det har skapats, så Överväg ett namn som fortfarande gäller om du [redigerar det underliggande arbets flödet](#edit-task-workflow). Ändringar som du gör i det underliggande arbets flödet gäller endast för den uppgift som du har skapat, inte uppgifts mal len.
   >
   > Om du till exempel namnger aktiviteten `Send monthly cost` , men senare redigerar det underliggande arbets flödet för att köras varje vecka, kan du inte ändra aktivitetens namn till `Send weekly cost` .

   Uppgifter som skickar e-postaviseringar kräver en e-postadress.

   ![Skärm bild som visar nödvändig information för den valda aktiviteten](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   Uppgiften som du skapade, som aktive ras automatiskt och körs, visas nu i listan **Automation-uppgifter** .

   ![Skärm bild som visar listan med automatiserings uppgifter](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > Om aktiviteten inte visas direkt kan du försöka med att uppdatera uppgifts listan eller vänta lite innan du uppdaterar. I verktygsfältet väljer du **Uppdatera**.

   När den markerade aktiviteten har körts får du ett e-postmeddelande som ser ut som i det här exemplet:

   ![Skärm bild som visar e-postavisering som skickats av uppgift](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Granska uppgifts historik

Följ dessa steg om du vill visa en aktivitets historik för körningar tillsammans med deras status, indata, utdata och annan information:

1. Leta upp den resurs som innehåller den aktivitets historik som du vill granska i [Azure Portal](https://portal.azure.com).

1. Välj **automatiserings uppgifter** på resurs menyn under **Inställningar**.

1. Leta upp den uppgift som du vill granska i listan uppgifter. I den aktivitetens **körnings** kolumn väljer du **Visa**.

   ![Skärm bild som visar en uppgift och valt visnings alternativ](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   I rutan **körnings historik** visas alla körningar för aktiviteten tillsammans med deras status, start tid, identifierare och körnings varaktighet.

   ![Skärm bild som visar en aktivitets körningar, deras status och annan information](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   Här kan du Visa status för en körning:

   | Status | Beskrivning |
   |--------|-------------|
   | **Avbröts** | Uppgiften avbröts under körning. |
   | **Misslyckades** | Aktiviteten har minst en misslyckad åtgärd, men inga efterföljande åtgärder fanns för att hantera felen. |
   | **Körs** | Aktiviteten körs för närvarande. |
   | **Brutit** | Alla åtgärder har genomförts. En uppgift kan fortfarande slutföras om en åtgärd Miss lyckas, men en efterföljande åtgärd fanns för att hantera felet. |
   | **Väntar** | Körningen har inte startat ännu och pausats eftersom en tidigare instans av aktiviteten fortfarande körs. |
   |||

   Mer information finns i [Översikt över körnings historik](../logic-apps/monitor-logic-apps.md#review-runs-history)

1. Om du vill visa status och annan information för varje steg i en körning väljer du den kör.

   **Körnings fönstret för Logic app** öppnas och visar det underliggande arbets flödet som kördes.

   * Ett arbets flöde börjar alltid med en [*utlösare*](../connectors/apis-list.md#triggers-actions). För den här uppgiften börjar arbets flödet med [ **upprepnings** utlösaren](../connectors/connectors-native-recurrence.md).

   * I varje steg visas dess status och körnings tid. Steg som har 0-sekund varaktighet tog mindre än 1 sekund att köra.

   ![Skärm bild som visar varje steg i körnings-, status-och körnings tid](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. Om du vill granska indata och utdata för varje steg väljer du steget, som expanderas.

   I det här exemplet visas indata för upprepnings utlösaren, som inte har några utdata eftersom utlösaren endast anger när arbets flödet körs och ger inga utdata för efterföljande åtgärder att bearbeta.

   ![Skärm bild som visar utökad utlösare och indata](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   Åtgärden **skicka en e-post** har däremot indata från tidigare åtgärder i arbets flödet och utdata.

   ![Skärm bild som visar en utökad åtgärd, indata och utdata](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Information om hur du kan skapa egna automatiserade arbets flöden så att du kan integrera appar, data, tjänster och system som skiljer sig från kontexten för automatiserings aktiviteter för Azure-resurser finns i [snabb start: skapa ditt första integrations arbets flöde med hjälp av Azure Logic Apps-Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="edit-task"></a>

## <a name="edit-the-task"></a>Redigera uppgiften

Om du vill ändra en uppgift kan du välja mellan följande alternativ:

* [Redigera uppgiften "infogad"](#edit-task-inline) så att du kan ändra aktivitetens egenskaper, t. ex. anslutnings information eller konfigurations information, till exempel din e-postadress.

* [Redigera aktivitetens underliggande arbets flöde](#edit-task-workflow) i Logic Apps designer.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>Redigera aktivitets infogat

1. Leta upp den resurs som innehåller den uppgift som du vill uppdatera i [Azure Portal](https://portal.azure.com).

1. På resurs menyn under **automatisering** väljer du **uppgifter**.

1. Leta upp den uppgift som du vill uppdatera i listan uppgifter. Öppna menyn för aktivitets ellipser (**...**) och välj **Redigera i rad**.

   ![Skärm bild som visar menyn öppna ellipser och det valda alternativet "redigera i rad"](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   Fliken **autentisering** visas som standard och visar befintliga anslutningar.

1. Om du vill lägga till nya autentiseringsuppgifter för autentisering eller välja olika befintliga autentiseringsuppgifter för en anslutning öppnar du menyn för anslutningens ellipser (**...**) och väljer antingen **Lägg till ny anslutning** eller om den är tillgänglig, olika autentiseringsuppgifter för autentisering.

   ![Skärm bild som visar fliken autentisering, befintliga anslutningar och den valda ellips-menyn](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. Om du vill uppdatera andra aktivitets egenskaper väljer du **Nästa: konfiguration**.

   För uppgiften i det här exemplet är den enda egenskapen som är tillgänglig för redigering e-postadressen.

   ![Skärm bild som visar fliken konfiguration](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. När du är klar väljer du **Spara**.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>Redigera aktivitetens underliggande arbets flöde

När du ändrar det underliggande arbets flödet för en automatiserings uppgift påverkar dina ändringar endast aktivitets instansen som du skapade och inte mallen som skapar aktiviteten. När du har gjort och sparat ändringarna kanske det namn som du har angett för den ursprungliga aktiviteten inte längre beskriver uppgiften längre, så du kan behöva återskapa uppgiften med ett annat namn.

> [!TIP]
> Vi rekommenderar att du klonar det underliggande arbets flödet så att du kan redigera den kopierade versionen i stället. På så sätt kan du göra och testa dina ändringar på kopian medan den ursprungliga automatiserings aktiviteten fortsätter att fungera och köras utan att riskera avbrott eller bryta befintliga funktioner. När du är klar med ändringarna och är nöjd med att den nya versionen körs kan du inaktivera eller ta bort den ursprungliga automatiserings aktiviteten och använda den klonade versionen för automatiserings uppgiften. Följande steg innehåller information om hur du klonar ditt arbets flöde.

1. Leta upp den resurs som innehåller den uppgift som du vill uppdatera i [Azure Portal](https://portal.azure.com).

1. På resurs menyn under **automatisering** väljer du **uppgifter**.

1. Leta upp den uppgift som du vill uppdatera i listan uppgifter. Öppna menyn för aktivitets ellipser (**...**) och välj **Öppna i Logic Apps**.

   ![Skärm bild som visar menyn öppna ellipser och det valda alternativet, "öppna i Logic Apps"](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   Aktivitetens underliggande arbets flöde öppnas i Azure Logic Apps tjänsten och visar **översikts** fönstret där du kan visa samma körnings historik som är tillgänglig för aktiviteten.

   ![Skärm bild som visar uppgiften i Azure Logic Apps Visa med översikts fönstret markerat](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. Öppna det underliggande arbets flödet i Logic Apps designer genom att välja **Logic Apps designer** på menyn Logic Apps.

   ![Skärm bild som visar meny alternativet "Logic App Designer" valt och design ytan med det underliggande arbets flödet](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   Du kan nu redigera egenskaperna för arbets flödets utlösare och åtgärder samt redigera utlösaren och åtgärderna som definierar själva arbets flödet. Men i bästa praxis följer du stegen för att klona arbets flödet så att du kan göra dina ändringar på en kopia medan det ursprungliga arbets flödet fortsätter att fungera och köras.

1. Följ dessa steg om du vill klona arbets flödet och redigera den kopierade versionen i stället:

   1. På menyn Logic app-arbetsflöde väljer du **Översikt**.

   1. I verktygsfältet i översikts fönstret väljer du **klona**.

   1. I fönstret för att skapa logiska appar, under **namn**, anger du ett nytt namn för ditt kopierade Logic app-arbetsflöde.

      Förutom för **Logic app-status** är de andra egenskaperna inte tillgängliga för redigering. 
      
   1. Under **Logic app-status** väljer du **inaktive rad** så att det klonade arbets flödet inte körs medan du gör dina ändringar. Du kan aktivera arbets flödet när du är redo att testa dina ändringar.

   1. När Azure har slutfört etableringen av ditt klonade arbets flöde kan du söka efter och öppna det arbets flödet i Logic Apps designer.

1. Om du vill visa egenskaperna för utlösaren eller en åtgärd expanderar du utlösaren eller åtgärden.

   Du kan till exempel ändra upprepnings utlösaren så att den körs varje vecka i stället för varje månad.

   ![Skärm bild som visar den utökade upprepnings utlösaren med frekvens listan öppen för att visa tillgängliga frekvens alternativ](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   Mer information om upprepnings utlösaren finns i [skapa, schemalägga och köra återkommande uppgifter och arbets flöden med upprepnings utlösaren](../connectors/connectors-native-recurrence.md). Mer information om andra utlösare och åtgärder som du kan använda finns i [kopplingar för Azure Logic Apps](../connectors/apis-list.md).

1. Spara ändringarna genom att välja **Spara** i verktygsfältet designer.

   ![Skärm bild som visar verktygsfältet designer och det valda kommandot "Spara"](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. Om du vill testa och köra det uppdaterade arbets flödet väljer du **Kör** i verktygsfältet designer.

   När körningen är klar visar designern arbets flödets körnings information.

   ![Skärm bild som visar arbets flödets körnings information i designern](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. Om du vill inaktivera arbets flödet så att aktiviteten inte fortsätter att köras, se [Hantera Logic Apps i Azure Portal](../logic-apps/manage-logic-apps-with-azure-portal.md).

## <a name="provide-feedback"></a>Ge feedback

Vi vill gärna höra från dig! [Kontakta Azure Logic Apps-teamet](mailto:logicappspm@microsoft.com)om du vill rapportera buggar, ge feedback eller ställa frågor om den här förhands gransknings funktionen.

## <a name="next-steps"></a>Nästa steg

* [Hantera Logic Apps i Azure Portal](../logic-apps/manage-logic-apps-with-azure-portal.md)
