---
title: Azure snabb start – kör ditt första batch-jobb i Azure Portal
description: Lär dig hur du använder Azure Portal för att skapa ett batch-konto, en pool med Compute-noder och ett jobb som kör grundläggande aktiviteter i poolen.
ms.topic: quickstart
ms.date: 08/17/2020
ms.custom: mvc
ms.openlocfilehash: cf65a681764a848f8132ec44b8ba313ef1a83235
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511379"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Snabbstart: Kör ditt första Batch-jobb på Azure-portalen

Kom igång med Azure Batch genom att använda Azure Portal för att skapa ett batch-konto, en pool med datornoder (virtuella datorer) och ett jobb som kör aktiviteter i poolen. När du har slutfört den här snabb starten förstår du viktiga begrepp i batch-tjänsten och är redo att testa batch med mer realistiska arbets belastningar i större skala.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-batch-account"></a>Skapa ett Batch-konto

Följ dessa steg om du vill skapa ett Batch-konto som ska användas som exempel för testning. Du behöver ett Batch-konto för att skapa pooler och jobb. Som du ser här kan du länka ett Azure-lagringskonto till Batch-kontot. Även om det inte krävs för den här snabbstarten, är lagringskontot användbart för att distribuera program och lagra indata och utdata för de flesta verkliga arbetsbelastningarna.

1. I [Azure Portal](https://portal.azure.com)väljer du **skapa en resurs**  >  **Compute**  >  **Batch-tjänst**. 

   :::image type="content" source="media/quick-create-portal/marketplace-batch.png" alt-text="Skärm bild av batch-tjänsten på Azure Marketplace.":::

1. I fältet **resurs grupp** väljer du **Skapa nytt** och anger ett namn för resurs gruppen.

1. Ange ett värde för **konto namn**. Det här namnet måste vara unikt inom den valda Azure- **platsen** . Det får bara innehålla gemena bokstäver och siffror, och det måste vara mellan 3-24 tecken.

1. Under **lagrings konto**väljer du ett befintligt lagrings konto eller skapar ett nytt.

1. Ändra inga andra inställningar. Välj **Granska + skapa**och välj **skapa** för att skapa batch-kontot.

När meddelandet **distributionen lyckades** visas går du till det batch-konto som du skapade.

## <a name="create-a-pool-of-compute-nodes"></a>Skapa en pool med beräkningsnoder

Nu när du har ett Batch-konto kan du skapa en exempelpool med Windows beräkningsnoder för testning. Poolen för det här snabb exemplet består av två noder som kör en Windows Server 2019-avbildning från Azure Marketplace.

1. Välj **pooler**  >  **Lägg till**i batch-kontot.

1. Ange ett **Pool-ID** som kallas *mypool*.

1. Vid **Operativsystem** väljer du följande inställningar (du kan utforska andra alternativ).
  
   |Inställning  |Värde  |
   |---------|---------|
   |**Avbildnings typ**|Marketplace|
   |**Publisher**     |Microsoft Windows Server|
   |**Erbjudande**     |Windows Server|
   |**SKU**     |2019-Data Center-Core – smalldisk|

1. Rulla nedåt för att ange inställningar för **Nodstorlek** och **Skala**. Föreslagen nodstorlek erbjuder en bra balans mellan prestanda och kostnad för det här snabba exemplet.
  
   |Inställning  |Värde  |
   |---------|---------|
   |**Nodprisnivå**     |Standard a1|
   |**Reserverade målnoder**     |2|

1. Behåll standardinställningarna för återstående inställningar och välj **OK** för att skapa poolen.

Batch skapar poolen omedelbart, men det tar några minuter att allokera och starta beräkningsnoderna. Under denna tid visar poolens **Allokeringstillstånd** som **Ändrar storlek**. Du kan gå vidare och skapa ett jobb och aktiviteter medan poolen ändrar storlek.

Efter några minuter ändras tilldelnings statusen till **stadigt**och noderna börjar. Om du vill kontrol lera nodernas status väljer du poolen och väljer sedan **noder**. När en nods tillstånd anges som **Inaktiv** är noden redo att köra aktiviteter.

## <a name="create-a-job"></a>Skapa ett jobb

Nu när du har en pool ska du skapa ett jobb att köra på den. Ett Batch-jobb är en logisk grupp för en eller flera aktiviteter. Ett jobb omfattar inställningar som är gemensamma för aktiviteter, till exempel prioritet och vilken pool som aktiviteterna ska köras på. Från början har jobbet inga uppgifter.

1. I vyn batch-konto väljer du **jobb**  >  **Lägg till**.

1. Ange ett **Jobb-ID** som kallas *myjob*. I **Pool**väljer du *mypool*. Behåll standardinställningarna för återstående inställningar och välj **OK**.

## <a name="create-tasks"></a>Skapa uppgifter

Välj nu jobbet för att öppna sidan **aktiviteter** . Här skapar du exempel aktiviteter som ska köras i jobbet. Normalt skapar du flera aktiviteter som batch-köer och distribuerar för att köras på datornoderna. I det här exemplet skapar du två identiska aktiviteter. Varje aktivitet kör en kommandorad för att visa Batch-miljövariablerna på en beräkningsnod och väntar sedan i 90 sekunder.

När du använder Batch är det på kommandoraden som du anger din app eller ditt skript. Batch tillhandahåller ett antal sätt att distribuera appar och skript till beräkningsnoder.

Så här skapar du den första aktiviteten:

1. Välj **Lägg till**.

1. Ange ett **Aktivitets-ID** som kallas *mytask*.

1. På **Kommandoraden** anger du `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Behåll standardinställningarna för återstående inställningar och välj **Skicka**.

När du har skapat en aktivitet köar Batch den så att den körs på poolen. När en nod kan köra den, körs aktiviteten.

Upprepa stegen ovan om du vill skapa en andra uppgift. Ange ett annat **Aktivitets-ID**, men ange en identisk kommandorad. Om den första aktiviteten fortfarande körs startar Batch den andra aktiviteten på den andra noden i poolen.

## <a name="view-task-output"></a>Visa aktivitetens utdata

De exempel uppgifter som du har skapat kommer att slutföras på några minuter. Om du vill visa resultatet av en slutförd uppgift väljer du aktiviteten och väljer sedan **filer på noden**. Välj filen `stdout.txt` om du vill visa standardutdata för aktiviteten. Informationen liknar följande:

:::image type="content" source="media/quick-create-portal/task-output.png" alt-text="Skärm bild av utdata från en slutförd uppgift.":::

Innehållet visar Azure Batch-miljövariabler som ställts in på noden. När du skapar dina egna Batch-jobb och aktiviteter kan du referera till dessa miljövariabler i aktivitetens kommandorader och i de appar och skript som körs av kommandoraderna.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta med Batch-självstudier och -exempel ska du använda Batch-kontot och det länkade lagringskontot som skapats i denna snabbstart. Själva Batch-kontot kostar ingenting.

Du debiteras för poolen medan noderna körs, även om inga jobb är schemalagda. Ta bort poolen när du inte längre behöver den. I kontovyn väljer du **Pooler** och namnet på poolen. Välj sedan **Ta bort**.  När du tar bort poolen raderas alla aktivitetsutdata på noderna.

Ta bort resursgruppen, Batch-kontot och alla relaterade resurser när de inte längre behövs. Om du vill göra det väljer du resursgruppen för Batch-kontot och sedan **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Batch-konto, en Batch-pool och ett Batch-jobb. Jobbet körde exempelaktiviteter och du visade utdata som skapats på en av noderna. Nu när du förstår nyckelbegreppen för Batch-tjänsten är du redo att testa Batch med mer realistiska arbetsbelastningar i större skala. Om du vill veta mer om Azure Batch ska du fortsätta med Azure Batch-självstudierna.

> [!div class="nextstepaction"]
> [Azure Batch-självstudier](./tutorial-parallel-dotnet.md)
