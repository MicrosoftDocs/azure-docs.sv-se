---
title: Visa månads vis uppskattad labb kostnads trend i Azure DevTest Labs
description: Den här artikeln innehåller information om hur du spårar kostnaden för ditt labb (månatligt uppskattat kostnads trend) i Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6a2a9bef9e54ef7deda123aad34cf0c576fd158f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98892345"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Spåra kostnader som är associerade med ett labb i Azure DevTest Labs
Den här artikeln innehåller information om hur du spårar kostnaden för ditt labb. Det visar dig hur du visar den uppskattade kostnads trenden för den aktuella kalender månaden för labbet. Artikeln visar också hur du visar månads-till-datum-kostnader per resurs i labbet.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Visa den månatliga uppskattade labb kostnads trenden 
I det här avsnittet får du lära dig hur du använder diagrammet **månatlig uppskattad kostnads trend** för att visa den aktuella kalender månadens beräknade kostnad hittills och den planerade månads kostnaden för den aktuella kalender månaden. Du lär dig också hur du hanterar labb kostnader genom att ställa in utgifts mål och tröskelvärden som, när de uppnås, utlöser DevTest Labs för att rapportera resultatet till dig.

Följ de här stegen om du vill visa ett uppskattat månatligt kostnads trend diagram: 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.
3. I listan med labb väljer du ditt labb.  
4. Välj **konfiguration och principer** på den vänstra menyn.  
4. Välj **kostnads trend** i avsnittet **kostnads uppföljning** på den vänstra menyn. Följande skärm bild visar ett exempel på ett kostnads diagram. 
   
    ![Kostnads diagram](./media/devtest-lab-configure-cost-management/graph.png)

    Det **beräknade kostnad** svärdet är den aktuella kalender månadens uppskattade kostnad hittills. Projektkostnaden **är den** uppskattade kostnaden för hela den aktuella kalender månaden, beräknad med hjälp av labb kostnaden för de föregående fem dagarna.

    Kostnads beloppen avrundas uppåt till närmaste heltal. Exempel: 

   * 5,01 avrundar upp till 6 
   * 5,50 avrundar upp till 6
   * 5,99 avrundar upp till 6

     I takt med att diagrammet visas är de kostnader som du ser som standard i diagrammet *uppskattade* kostnader med avgifts priset [betala per](https://azure.microsoft.com/offers/ms-azr-0003p/) användning. Du kan också ange dina egna utgifts mål som visas i diagrammen genom att [Hantera kostnads målen för ditt labb.](#managing-cost-targets-for-your-lab)

     Följande kostnader ingår *inte* i kostnads beräkningen:

   * CSP-och DreamSpark-prenumerationer stöds inte för närvarande eftersom Azure DevTest Labs använder Azures fakturerings-API: er för att beräkna labb kostnaden, som inte stöder CSP-eller DreamSpark-prenumerationer.
   * Dina erbjudande priser. För närvarande kan du inte använda erbjudandet (visas under din prenumeration) som du har förhandlat med Microsoft eller Microsoft-partner. Endast betala per användning-pris används.
   * Dina skatter
   * Dina rabatter
   * Din fakturerings valuta. För närvarande visas labb kostnaden bara i USD-valutan.

### <a name="managing-cost-targets-for-your-lab"></a>Hantera kostnads mål för ditt labb
Med DevTest Labs kan du bättre hantera kostnaderna i labbet genom att ange ett utgifts mål som du sedan kan se i diagrammet månads vis uppskattad kostnads trend. DevTest Labs kan också skicka ett meddelande till dig när de angivna mål utgifterna eller tröskelvärdet har uppnåtts. 

1. På sidan **kostnads trend** väljer du **Hantera mål**.

    ![Knappen Hantera mål](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. På sidan **Hantera mål** anger du ett mål och tröskelvärden för utgifter. Du kan också ange om varje valt tröskelvärde rapporteras i kostnads trend diagrammet eller via en webhook-avisering.

    ![Hantera mål fönstret](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Välj en tids period under vilken du vill att kostnads mål ska spåras.
      - **Varje månad**: kostnads mål spåras per månad.
      - **Fast**: kostnads mål spåras för det datum intervall som du anger i Start-och slutdatum. Normalt motsvarar dessa värden hur länge projektet är schemalagt att köras.
   - Ange en **mål kostnad**. Till exempel hur mycket du planerar att spendera på det här labbet under den tids period som du har definierat.
   - Välj om du vill aktivera eller inaktivera eventuella tröskelvärden som ska rapporteras – i steg om 25% – upp till 125% av din angivna **mål kostnad**.
      - **Meddela**: när detta tröskelvärde är uppfyllt, meddelas du av en webhook-URL som du anger.
      - **Rita i diagram**: när det här tröskelvärdet uppfylls, ritas resultatet i diagrammet för kostnads trend som du kan visa, enligt beskrivningen i diagrammet månads vis uppskattad kostnads trend.
   - Om du väljer att **meddela** när tröskelvärdet är uppfyllt måste du ange en webhook-URL. I avsnittet Cost integrations väljer **du klicka här för att lägga till en integrering**. Ange en **webhook-URL** i fönstret Konfigurera meddelande och välj sedan **OK**.

       ![Konfigurera meddelande fönstret](./media/devtest-lab-configure-cost-management/configure-notification-new.png)

     - Om du anger **meddela** måste du definiera en webhook-URL.
     - Om du definierar en webhook-URL måste du ange ett **meddelande** **till i** fönstret kostnads tröskel.
     - Du måste skapa en webhook innan du anger den här.  

       Mer information om Webhooks finns i [skapa en webhook eller API Azure-funktion](../azure-functions/functions-bindings-http-webhook.md). 

## <a name="view-cost-by-resource"></a>Visa kostnad per resurs 
Med funktionen för månatlig kostnads trend i labbet kan du se hur mycket du har spenderat under den aktuella kalender månaden. Den visar även projektionen av utgifterna fram till slutet av månaden, baserat på dina utgifter under de senaste sju dagarna. För att hjälpa dig att förstå varför utgifterna i labbet uppfyller tröskelvärdena tidigt i, kan du använda funktionen **kostnad per resurs** som visar den månads aktuella kostnaden **per resurs** i en tabell.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.
3. I listan med labb väljer du önskat labb.  
4. Välj **konfiguration och principer** på den vänstra menyn.
5. Välj **kostnad per resurs** i avsnittet **kostnads uppföljning** på den vänstra menyn. Du ser de kostnader som är kopplade till varje resurs som är associerad med ett labb. 

    ![Kostnad efter resurs](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Den här funktionen hjälper dig att enkelt identifiera de resurser som kostar mest så att du kan vidta åtgärder för att minska test utgifterna. Kostnaden för en virtuell dator baseras till exempel på storleken på den virtuella datorn. Ju större storlek på den virtuella datorn, desto mer är kostnaden. Du kan enkelt hitta storleken på en virtuell dator och ägaren, så att du kan prata med den virtuella dator ägaren för att ta reda på varför den här virtuella dator storleken behövs och om det finns möjlighet att sänka storleken.

[Principen för automatisk avstängning](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) hjälper dig att minska kostnaderna genom att stänga av virtuella labb datorer vid en viss tidpunkt på dagen. En labb användare kan dock välja bort avstängnings principen, vilket ökar kostnaden för att köra den virtuella datorn. Du kan välja en virtuell dator i tabellen för att se om den har valts ut ur principen för automatisk avstängning. Om så är fallet kan du prata med den virtuella dator ägaren för att se varför den virtuella datorn har valts ut ur principen.
 
## <a name="next-steps"></a>Nästa steg
Här är några saker du kan prova härnäst:

* [Definiera labb principer](devtest-lab-set-lab-policy.md) – lär dig hur du anger de olika principerna som används för att styra hur labbet och dess virtuella datorer används. 
* [Skapa anpassad avbildning](devtest-lab-create-template.md) – när du skapar en virtuell dator anger du en bas, som kan vara antingen en anpassad avbildning eller en Marketplace-avbildning. Den här artikeln visar hur du skapar en anpassad avbildning från en VHD-fil.
* [Konfigurera Marketplace-avbildningar](devtest-lab-configure-marketplace-images.md) – DevTest Labs har stöd för att skapa virtuella datorer baserat på Azure Marketplace-avbildningar. Den här artikeln beskriver hur du anger vilka Azure Marketplace-avbildningar som kan användas när du skapar virtuella datorer i ett labb.
* [Skapa en virtuell dator i ett labb](devtest-lab-add-vm.md) – visar hur du skapar en virtuell dator från en bas avbildning (antingen anpassad eller Marketplace) och hur du arbetar med artefakter på den virtuella datorn.
