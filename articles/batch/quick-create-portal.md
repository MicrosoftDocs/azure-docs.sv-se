---
title: Azure snabbstart – Kör ditt första Batch-jobb i Azure Portal
description: Den här snabbstarten visar hur du använder Azure Portal för att skapa ett Batch-konto, en pool med beräkningsnoder och ett jobb som kör grundläggande aktiviteter i poolen.
ms.date: 08/17/2020
ms.topic: quickstart
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: 3333097b4bd55173725aa33bc4bfbae318510cf1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538537"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Snabbstart: Kör ditt första Batch-jobb på Azure-portalen

Kom igång med Azure Batch genom att använda Azure Portal för att skapa ett Batch-konto, en pool med beräkningsnoder (virtuella datorer) och ett jobb som kör aktiviteter i poolen. När du har slutfört den här snabbstarten kommer du att förstå viktiga begrepp i Batch-tjänsten och vara redo att testa Batch med mer realistiska arbetsbelastningar i större skala.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-batch-account"></a>Skapa ett Batch-konto

Följ dessa steg om du vill skapa ett Batch-konto som ska användas som exempel för testning. Du behöver ett Batch-konto för att skapa pooler och jobb. Som du ser här kan du länka ett Azure-lagringskonto till Batch-kontot. Även om det inte krävs för den här snabbstarten, är lagringskontot användbart för att distribuera program och lagra indata och utdata för de flesta verkliga arbetsbelastningarna.

1. I den [Azure Portal](https://portal.azure.com)väljer du **Skapa en resurs**  >  **Compute**  >  **Batch Service**. 

   :::image type="content" source="media/quick-create-portal/marketplace-batch.png" alt-text="Skärmbild av Batch-tjänsten i Azure Marketplace.":::

1. I fältet **Resursgrupp** väljer du **Skapa ny** och anger ett namn för resursgruppen.

1. Ange ett värde för **Kontonamn**. Det här namnet måste vara unikt inom den Valda **Azure-platsen.** Den får bara innehålla gemener och siffror och måste vara mellan 3 och 24 tecken.

1. Under **Lagringskonto** väljer du ett befintligt lagringskonto eller skapar ett nytt.

1. Ändra inte några andra inställningar. Välj **Granska + skapa** och välj sedan Skapa **för** att skapa Batch-kontot.

När meddelandet **Distributionen lyckades** visas går du till det Batch-konto som du skapade.

## <a name="create-a-pool-of-compute-nodes"></a>Skapa en pool med beräkningsnoder

Nu när du har ett Batch-konto kan du skapa en exempelpool med Windows beräkningsnoder för testning. Poolen för det här snabba exemplet består av två noder som kör en Windows Server 2019-avbildning från Azure Marketplace.

1. I Batch-kontot väljer du **Pooler Lägg**  >  **till**.

1. Ange ett **Pool-ID** som kallas *mypool*.

1. Vid **Operativsystem** väljer du följande inställningar (du kan utforska andra alternativ).
  
   |Inställning  |Värde  |
   |---------|---------|
   |**Avbildningstyp**|Marketplace|
   |**Utgivare**     |microsoftwindowsserver|
   |**Erbjudande**     |windowsserver|
   |**Sku**     |2019-datacenter-core-smalldisk|

1. Rulla nedåt för att ange inställningar för **Nodstorlek** och **Skala**. Föreslagen nodstorlek erbjuder en bra balans mellan prestanda och kostnad för det här snabba exemplet.
  
   |Inställning  |Värde  |
   |---------|---------|
   |**Nodprisnivå**     |Standard A1|
   |**Reserverade målnoder**     |2|

1. Behåll standardinställningarna för återstående inställningar och välj **OK** för att skapa poolen.

Batch skapar poolen omedelbart, men det tar några minuter att allokera och starta beräkningsnoderna. Under denna tid visar poolens **Allokeringstillstånd** som **Ändrar storlek**. Du kan gå vidare och skapa ett jobb och aktiviteter medan poolen ändrar storlek.

Efter några minuter ändras allokeringstillståndet **till Konstant** och noderna startar. Om du vill kontrollera tillståndet för noderna markerar du poolen och väljer sedan **Noder.** När en nods tillstånd anges som **Inaktiv** är noden redo att köra aktiviteter.

## <a name="create-a-job"></a>Skapa ett jobb

Nu när du har en pool ska du skapa ett jobb att köra på den. Ett Batch-jobb är en logisk grupp med en eller flera aktiviteter. Ett jobb omfattar inställningar som är gemensamma för aktiviteter, till exempel prioritet och vilken pool som aktiviteterna ska köras på. Från början har jobbet inga uppgifter.

1. I vyn Batch-konto väljer du Jobb **Lägg**  >  **till**.

1. Ange ett **Jobb-ID** som kallas *myjob*. I **Pool** väljer du *mypool*. Behåll standardinställningarna för återstående inställningar och välj **OK**.

## <a name="create-tasks"></a>Skapa uppgifter

Välj nu jobbet för att öppna **sidan** Uppgifter. Här skapar du exempelaktiviteter som ska köras i jobbet. Normalt skapar du flera aktiviteter som Batch köar och distribuerar för att köras på beräkningsnoderna. I det här exemplet skapar du två identiska aktiviteter. Varje aktivitet kör en kommandorad för att visa Batch-miljövariablerna på en beräkningsnod och väntar sedan i 90 sekunder.

När du använder Batch är det på kommandoraden som du anger din app eller ditt skript. Batch tillhandahåller ett antal sätt att distribuera appar och skript till beräkningsnoder.

Så här skapar du den första aktiviteten:

1. Välj **Lägg till**.

1. Ange ett **Aktivitets-ID** som kallas *mytask*.

1. På **Kommandoraden** anger du `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Behåll standardinställningarna för återstående inställningar och välj **Skicka.**

När du har skapat en aktivitet köar Batch den så att den körs på poolen. När en nod kan köra den, körs aktiviteten.

Upprepa stegen ovan för att skapa en andra uppgift. Ange ett annat **Aktivitets-ID**, men ange en identisk kommandorad. Om den första aktiviteten fortfarande körs startar Batch den andra aktiviteten på den andra noden i poolen.

## <a name="view-task-output"></a>Visa aktivitetens utdata

Exempeluppgifterna som du skapade slutförs på några minuter. Om du vill visa utdata för en slutförd uppgift väljer du uppgiften och sedan **Filer på noden**. Välj filen för `stdout.txt` att visa standardutdata för uppgiften. Informationen liknar följande:

:::image type="content" source="media/quick-create-portal/task-output.png" alt-text="Skärmbild av utdata från en slutförd uppgift.":::

Innehållet visar Azure Batch-miljövariabler som ställts in på noden. När du skapar dina egna Batch-jobb och aktiviteter kan du referera till dessa miljövariabler i aktivitetens kommandorader och i de appar och skript som körs av kommandoraderna.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta med Batch-självstudier och -exempel ska du använda Batch-kontot och det länkade lagringskontot som skapats i denna snabbstart. Själva Batch-kontot kostar ingenting.

Du debiteras för poolen medan noderna körs, även om inga jobb är schemalagda. Ta bort poolen när du inte längre behöver den. I kontovyn väljer du **Pooler** och namnet på poolen. Välj sedan **Ta bort**.  När du tar bort poolen raderas alla aktivitetsutdata på noderna.

Ta bort resursgruppen, Batch-kontot och alla relaterade resurser när de inte längre behövs. Om du vill göra det väljer du resursgruppen för Batch-kontot och sedan **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Batch-konto, en Batch-pool och ett Batch-jobb. Jobbet körde exempelaktiviteter och du visade utdata som skapats på en av noderna. Nu när du förstår nyckelbegreppen för Batch-tjänsten är du redo att testa Batch med mer realistiska arbetsbelastningar i större skala. Om du vill veta mer om Azure Batch ska du fortsätta med Azure Batch-självstudierna.

> [!div class="nextstepaction"]
> [Azure Batch-självstudier](./tutorial-parallel-dotnet.md)
