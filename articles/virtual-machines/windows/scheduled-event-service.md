---
title: Övervaka schemalagda händelser för dina virtuella datorer i Azure
description: Lär dig hur du övervakar dina virtuella Azure-datorer för schemalagda händelser.
author: mysarn
ms.service: virtual-machines
ms.subservice: scheduled-events
ms.date: 08/20/2019
ms.author: sarn
ms.topic: how-to
ms.openlocfilehash: 866522da162d22621bd37bf9d2f2fa6838206e17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674693"
---
# <a name="monitor-scheduled-events-for-your-azure-vms"></a>Övervaka schemalagda händelser för dina virtuella Azure-datorer

Uppdateringar tillämpas på olika delar av Azure varje dag för att hålla tjänsterna som körs på dem säkra och uppdaterade. Förutom planerade uppdateringar kan oplanerade händelser också uppstå. Om t. ex. maskin varu försämring eller fel upptäcks kan Azure-tjänster behöva utföra oplanerat underhåll. I de flesta fall är de här händelserna nästan transparenta för kunder, och de har ingen påverkan eller det tar några sekunder av den virtuella datorns frysning med hjälp av direktmigrering. Men för vissa program kan det leda till en påverkan även om några sekunder av den virtuella datorns frysning. Att veta i förväg om kommande Azure-underhåll är viktigt för att säkerställa bästa möjliga upplevelse för dessa program. [Schemalagda händelser-tjänsten](scheduled-events.md) ger dig ett programmerings gränssnitt för att få information om kommande underhåll och gör det möjligt att hantera underhållet på ett smidigt sätt. 

I den här artikeln visar vi hur du kan använda schemalagda händelser för att få meddelanden om underhålls händelser som kan påverka de virtuella datorerna och bygga lite grundläggande automatisering som kan hjälpa dig med övervakning och analys.


## <a name="routing-scheduled-events-to-log-analytics"></a>Vidarebefordra schemalagda händelser till Log Analytics

Schemalagda händelser är tillgängligt som en del av [Azure-instance metadata service](instance-metadata-service.md), som finns på alla virtuella Azure-datorer. Kunder kan skriva automatisering för att fråga slut punkten för deras virtuella datorer för att hitta schemalagda underhålls aviseringar och utföra åtgärder, t. ex. att spara statusen och ta den virtuella datorn ur rotationen. Vi rekommenderar att du skapar automatisering för att registrera Schemalagda händelser så att du kan ha en Gransknings logg för Azures underhålls händelser. 

I den här artikeln får du lära dig hur du samlar in underhålls Schemalagda händelser till Log Analytics. Sedan kommer vi att utlösa vissa grundläggande meddelande åtgärder, t. ex. att skicka ett e-postmeddelande till ditt team och få en historisk vy över alla händelser som påverkar dina virtuella datorer. För händelse agg regering och automation kommer vi att använda [Log Analytics](../../azure-monitor/logs/quick-create-workspace.md), men du kan använda valfri övervaknings lösning för att samla in dessa loggar och utlösa automatisering.

![Diagram över händelsens livs cykel](./media/notifications/events.png)

## <a name="prerequisites"></a>Förutsättningar

I det här exemplet måste du skapa en [virtuell Windows-dator i en tillgänglighets uppsättning](tutorial-availability-sets.md). Schemalagda händelser ange meddelanden om ändringar som kan påverka någon av de virtuella datorerna i din tillgänglighets uppsättning, moln tjänst, skalnings uppsättning för virtuell dator eller fristående virtuella datorer. Vi kommer att köra en [tjänst](https://github.com/microsoft/AzureScheduledEventsService) som avsöker efter schemalagda händelser på en av de virtuella datorer som fungerar som en insamlare, för att hämta händelser för alla andra virtuella datorer i tillgänglighets uppsättningen.    

Ta inte bort grupp resurs gruppen i slutet av självstudien.

Du måste också [skapa en Log Analytics arbets yta](../../azure-monitor/logs/quick-create-workspace.md) som vi ska använda för att samla in information från de virtuella datorerna i tillgänglighets uppsättningen.

## <a name="set-up-the-environment"></a>Konfigurera miljön

Du bör nu ha två första virtuella datorer i en tillgänglighets uppsättning. Nu måste vi skapa en tredje virtuell dator, `myCollectorVM` som kallas, i samma tillgänglighets uppsättning. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

Hämta filen installation. zip för projektet från [GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip).

Anslut till **myCollectorVM** och kopiera. zip-filen till den virtuella datorn och extrahera alla filer. Öppna en PowerShell-prompt på den virtuella datorn. Flytta din prompt till mappen som innehåller `SchService.ps1` , till exempel: `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>` och konfigurera tjänsten.

```powershell
.\SchService.ps1 -Setup
```

Starta tjänsten.

```powershell
.\SchService.ps1 -Start
```

Tjänsten kommer nu att börja avsökningen var 10: e sekund för schemalagda händelser och godkänna händelserna för att påskynda underhållet.  Frysning, omstart, omdistribution och Preempt är de händelser som samlas in av schema händelser.   Observera att du kan utöka skriptet för att utlösa vissa åtgärder innan du godkänner händelsen.

Verifiera tjänstens status och kontrol lera att den körs.

```powershell
.\SchService.ps1 -status  
```

Detta bör returnera `Running` .

Tjänsten kommer nu att börja avsökningen var 10: e sekund för schemalagda händelser och godkänna händelserna för att påskynda underhållet.  Låsning, omstart, omdistribution och Preempt är de händelser som samlas in av schema händelser. Du kan utöka skriptet för att utlösa vissa åtgärder innan du godkänner händelsen.

När någon av ovanstående händelser inhämtas av Schedule Event service, kommer den att loggas i händelse status för program händelse loggen, händelse typ, resurser (namn på virtuell dator) och NotBefore (minsta meddelande period). Du kan hitta händelserna med ID 1234 i program händelse loggen.

När tjänsten har kon figurer ATS och startats loggas händelser i Windows program loggar.   Du kan kontrol lera detta genom att starta om en av de virtuella datorerna i tillgänglighets uppsättningen och se till att en händelse loggas i logg boken i Windows-loggar > program loggen som visar att den virtuella datorn startats om. 

![Skärm bild av logg boken.](./media/notifications/event-viewer.png)

När händelser samlas in av tjänsten Schemalägg händelse kommer den att loggas i programmet med händelse status, händelse typ, resurser (VM-namn) och NotBefore (minsta meddelande period). Du kan hitta händelserna med ID 1234 i program händelse loggen.

> [!NOTE] 
> I det här exemplet fanns de virtuella datorerna i en tillgänglighets uppsättning, som aktiverade oss att ange en enda virtuell dator som insamlaren för att lyssna och dirigera schemalagda händelser till vårt Log Analytics-arbetsområde. Om du har fristående virtuella datorer kan du köra tjänsten på varje virtuell dator och sedan ansluta dem individuellt till Log Analytics-arbetsytan.
>
> För vår konfiguration valde vi Windows, men du kan skapa en liknande lösning i Linux.

Du kan när som helst stoppa/ta bort den schemalagda händelse tjänsten med hjälp av växlarna `–stop` och `–remove` .

## <a name="connect-to-the-workspace"></a>Anslut till arbets ytan


Nu vill vi ansluta en Log Analytics arbets yta till den virtuella datorn för insamlaren. Arbets ytan Log Analytics fungerar som en lagrings plats och vi konfigurerar händelse logg insamlingen för att avbilda program loggarna från den insamlade VM: en. 

 För att dirigera Schemalagda händelser till händelse loggen, som sparas som program logg av vår tjänst, måste du ansluta den virtuella datorn till din Log Analytics-arbetsyta.  
 
1. Öppna sidan för arbets ytan som du skapade.
1. Under **Anslut till en data källa väljer du** **virtuella Azure-datorer**.

    ![Ansluta till en virtuell dator som en data Källa](./media/notifications/connect-to-data-source.png)

1. Sök efter och välj **myCollectorVM**. 
1. På den nya sidan för **myCollectorVM** väljer du **Anslut**.

Då installeras [Microsoft Monitoring Agent](../extensions/oms-windows.md) på den virtuella datorn. Det tar några minuter att ansluta den virtuella datorn till arbets ytan och installera tillägget. 

## <a name="configure-the-workspace"></a>Konfigurera arbets ytan

1. Öppna sidan för din arbets yta och välj **Avancerade inställningar**.
1. Välj **data** på den vänstra menyn och välj sedan **Windows händelse loggar**.
1. I **samla in från följande händelse loggar** börjar du skriva *program* och väljer sedan **program** i listan.

    ![Välj avancerade inställningar](./media/notifications/advanced.png)

1. Lämna **fel**, **Varning** och **information** valt och välj **Spara** för att spara inställningarna.


> [!NOTE]
> Det kommer att finnas en fördröjning och det kan ta upp till 10 minuter innan loggen är tillgänglig. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Skapa en varnings regel med Azure Monitor 


När händelserna flyttas till Log Analytics kan du köra följande [fråga](../../azure-monitor/logs/log-analytics-tutorial.md) för att söka efter schema händelser.

1. Längst upp på sidan väljer du **loggar** och klistrar in följande i text rutan:

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. Välj **Spara** och skriv sedan `ogQuery` för namnet, lämna **frågan** som typ, Skriv `VMLogs` som **kategori** och välj sedan **Spara**. 

    ![Spara frågan](./media/notifications/save-query.png)

1. Välj **Ny aviseringsregel**. 
1. På sidan **Skapa regel** lämnar du `collectorworkspace` som **resurs**.
1. Under **villkor** väljer du posten *när kund loggs ökningen är <login undefined>*. Sidan **Konfigurera signal logik** öppnas.
1. Under **tröskelvärde** anger du *0* och väljer sedan **färdig**.
1. Under **åtgärder** väljer du **skapa åtgärds grupp**. Sidan **Lägg till åtgärds grupp** öppnas.
1. I **Åtgärds grupp namn** skriver du *myActionGroup*.
1. Skriv *myActionGroup* i **kort namn**.
1. I **resurs grupp** väljer du **myResourceGroupAvailability**.
1. Under åtgärder, under **Åtgärds namn** skriver du **e-post** och väljer sedan **e-post/SMS/push/röst**. Sidan **e-post/SMS/push/Voice** öppnas.
1. Välj **e**-postadress, Skriv in din e-postadress och välj sedan **OK**.
1. På sidan **Lägg till åtgärds grupp** väljer du **OK**. 
1. Skriv aviserings **regelns namn** under **aviserings information** på sidan **Skapa regel** *och skriv sedan* *e-postavisering* för **beskrivningen**.
1. När du är färdig väljer du **skapa aviserings regel**.
1. Starta om en av de virtuella datorerna i tillgänglighets uppsättningen. Inom några minuter bör du få ett e-postmeddelande om att aviseringen har utlösts.

Om du vill hantera dina aviserings regler går du till resurs gruppen, väljer **aviseringar** på den vänstra menyn och väljer **Hantera aviserings regler** överst på sidan.

     
## <a name="next-steps"></a>Nästa steg

Mer information finns på sidan för [schemalagda händelser](https://github.com/microsoft/AzureScheduledEventsService) på GitHub.