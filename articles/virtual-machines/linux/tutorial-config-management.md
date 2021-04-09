---
title: Självstudie – hantera konfiguration av virtuella Linux-datorer i Azure
description: I den här självstudien får du lära dig att identifiera ändringar och hantera paket uppdateringar på en virtuell Linux-dator
services: virtual-machines
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/27/2019
ms.author: magoedte
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 979cac8fd3d2c09443d52c9142a5e7c44127713a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552540"
---
# <a name="tutorial-monitor-changes-and-update-a-linux-virtual-machine-in-azure"></a>Självstudie: övervaka ändringar och uppdatera en virtuell Linux-dator i Azure

Med Azure [ändringsspårning](../../automation/change-tracking/overview.md) kan du enkelt identifiera ändringar och [uppdateringshantering](../../automation/update-management/overview.md) göra det möjligt att hantera operativ system uppdateringar för dina virtuella Azure Linux-datorer.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Hantera Linux-uppdateringar
> * Övervaka ändringar och inventering

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- I den här självstudien krävs version 2.0.30 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-vm"></a>Skapa en virtuell dator

Du behöver en virtuell dator för att kunna se diagnostik och mått i praktiken. Skapa först en resursgrupp med [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resursgrupp med namnet *myResourceGroupMonitor* på platsen *eastus* (Östra USA).

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Skapa nu en virtuell dator med [az vm create](/cli/azure/vm#az-vm-create). Följande exempel skapar en virtuell dator som heter *myVM*, och SSH-nycklar skapas om de inte redan finns på *~/.ssh/*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="manage-software-updates"></a>Hantera programuppdateringar

Med uppdateringshantering kan du hantera uppdateringar och korrigeringar för dina virtuella Azure Linux-datorer.
Du kan snabbt se status för tillgängliga uppdateringar, schemalägga installation av nödvändiga uppdateringar och granska distributionsresultat för att verifiera att uppdateringarna har tillämpats på den virtuella datorn, direkt från den virtuella datorn.

Prisinformation finns i [Automation-priser för uppdateringshantering](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Aktivera uppdateringshantering

Så här aktiverar du uppdateringshantering för dina virtuella datorer:

1. Välj **Virtuella datorer** till vänster på skärmen.
2. Välj en virtuell dator i listan.
3. Välj **Hantering av uppdateringar** i avsnittet **Åtgärder** på VM-skärmen. Skärmen **Aktivera hantering av uppdateringar** visas.

Verifieringen utförs för att fastställa om uppdateringshantering är aktiverat för den här virtuella datorn.
Verifieringen söker efter en Log Analytics-arbetsyta och ett länkat Automation-konto, och om lösningen är i arbetsytan.

En [Log Analytics](../../azure-monitor/logs/log-query-overview.md)-arbetsyta används för att samla in data som genereras av funktioner och tjänster som uppdateringshantering.
Arbetsytan tillhandahåller en enda plats för att granska och analysera data från flera källor.
Om du vill utföra ytterligare åtgärder på virtuella datorer som kräver uppdateringar kan Azure Automation köra runbooks mot virtuella datorer, till exempel ladda ned och installera uppdateringar.

Verifieringsprocessen kontrollerar också om den virtuella datorn har etablerats med Log Analytics-agenten och Automation Hybrid Runbook Worker. Den här agenten används för att kommunicera med den virtuella datorn och hämta information om uppdateringsstatus.

Välj Log Analytics arbets yta och Automation-konto och välj **Aktivera** för att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

Om några av följande krav saknades under publiceringen läggs de till automatiskt:

* [Log Analytics](../../azure-monitor/logs/log-query-overview.md) arbets yta
* [Automation-konto](../../automation/index.yml)
* En [Hybrid runbook worker](../../automation/automation-hybrid-runbook-worker.md) aktiveras på den virtuella datorn

Skärmen **Uppdateringshantering** öppnas. Konfigurera platsen, Log Analytics arbets ytan och automation-kontot som ska användas och välj **Aktivera**. Om fälten är nedtonade betyder det att någon annan automatiseringslösning är aktiverad för den virtuella datorn, och samma arbetsyta och Automation-konto måste användas.

![Aktivera lösningen för hantering av uppdateringar](./media/tutorial-monitoring/manage-updates-update-enable.png)

Det kan ta upp till 15 minuter att aktivera lösningen. Under tiden ska du inte stänga webbläsaren. När lösningen har aktiverats flödar information om saknade uppdateringar på den virtuella datorn till Azure Monitor-loggar. Det kan ta mellan 30 minuter och 6 timmar innan data blir tillgängliga för analys.

### <a name="view-update-assessment"></a>Visa kontroll av uppdateringar

När **uppdateringshantering** är aktiverat visas skärmen **Hantering av uppdateringar**. När utvärderingen av uppdateringarna är klar ser du en lista med uppdateringar som saknas på fliken **Uppdateringar som saknas**.

 ![Visa uppdateringsstatus](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

För att installera uppdateringar schemalägger du en distribution som passar ditt schema och servicefönster. Du kan välja vilka uppdateringstyper som ska tas med i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

Schemalägg en ny uppdateringsdistribution för den virtuella datorn genom att klicka på **Distribution av schemauppdatering** längst upp på skärmen **Hantering av uppdateringar**. På skärmen **Ny uppdateringsdistribution** anger du följande information:

Om du vill skapa en ny uppdaterings distribution väljer du **Schemalägg uppdaterings distribution**. Sidan **ny uppdaterings distribution** öppnas. Ange värden för egenskaperna som beskrivs i följande tabell och klicka sedan på **skapa**:

| Egenskap | Beskrivning |
| --- | --- |
| Name |Unikt namn som identifierar uppdateringsdistributionen. |
|Operativsystem| Linux eller Windows|
| Grupper att uppdatera |För Azure-datorer definierar du en fråga baserat på en kombination av prenumeration, resurs grupper, platser och taggar för att skapa en dynamisk grupp med virtuella Azure-datorer som ska ingå i distributionen. </br></br>För datorer som inte är Azure-datorer väljer du en befintlig sparad sökning för att välja en grupp datorer som inte är Azure-datorer att inkludera i distributionen. </br></br>Mer information finns i [Dynamiska grupper](../../automation/update-management/configure-groups.md)|
| Datorer som ska uppdateras |Välj en sparad sökning eller en importerad grupp, eller välj Dator i listrutan och välj enskilda datorer. Om du väljer **Datorer** visas beredskapen för datorn i kolumnen **Uppdatera agentberedskap**.</br> Information om de olika metoderna för att skapa datorgrupper i Azure Monitor-loggar finns i [datorgrupper i Azure Monitor-loggar](../../azure-monitor/logs/computer-groups.md) |
|Klassificering av uppdateringar|Välj alla uppdaterings klassificeringar som du behöver|
|Inkludera/exkludera uppdateringar|Då öppnas sidan **Inkludera/exkludera** . Uppdateringar som ska inkluderas eller exkluderas visas på en separat flik. Mer information om hur inkludering hanteras finns i [Schemalägga en uppdaterings distribution](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment) |
|Schemainställningar|Välj tid för start och välj antingen en gång eller återkommande för upprepningen|
| Före skript + efter skript|Välj de skript som ska köras före och efter distributionen|
| Underhålls period |Antal minuter som har angetts för uppdateringar. Värdet kan inte vara mindre än 30 minuter och högst 6 timmar |
| Starta om kontroll| Fastställer hur omstarter ska hanteras. De tillgängliga alternativen är:</br>Starta om vid behov (standard)</br>Starta alltid om</br>Starta aldrig om</br>Endast omstart – uppdateringar installeras inte|

Uppdaterings distributioner kan också skapas program mässigt. Information om hur du skapar en uppdaterings distribution med REST API finns i [program uppdaterings konfiguration – skapa](/rest/api/automation/softwareupdateconfigurations/create). Det finns också en exempel-Runbook som kan användas för att skapa en veckovis uppdaterings distribution. Mer information om denna Runbook finns i [skapa en veckovis uppdaterings distribution för en eller flera virtuella datorer i en resurs grupp](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

När du har konfigurerat schemat klickar du på **Skapa**. Därmed återgår du till statusinstrumentpanelen.
Observera att tabellen **Schemalagt** visar det distributionsschema som du skapade.

### <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen startar kan du se status för distributionen på fliken **Uppdateringsdistributioner** på skärmen **Uppdateringshantering**.
Om distributionen körs visas status **Pågår**. När distributionen har slutförts ändras status till **Lyckades**.
Om det uppstod något fel med en eller flera uppdateringar i distributionen visas status **Misslyckades delvis**.
Välj den slutförda uppdateringsdistributionen för att visa instrumentpanelen för distributionen.

![Statusinstrumentpanel för Uppdatera distribution för specifik distribution](./media/tutorial-monitoring/manage-updates-view-results.png)

På panelen för **uppdateringsresultat** visas en sammanfattning av det totala antalet uppdateringar och distributionsresultat för den virtuella datorn.
I tabellen till höger visas detaljer för varje uppdatering och installationsresultaten, som kan ha ett av följande värden:

* **Inget försök har gjorts** – uppdateringen installerades inte eftersom det inte fanns tillräckligt med tid baserat på den underhålls period som definierats.
* **Lyckades** – uppdateringen är klar
* **Misslyckades** -uppdateringen misslyckades

Välj **Alla loggar** om du vill se alla loggposter som har skapats för distributionen.

Välj panelen **Utdata** om du vill se jobbströmmen för den runbook som ansvarar för att hantera uppdateringsdistributionen på den virtuella måldatorn.

Välj **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

## <a name="monitor-changes-and-inventory"></a>Övervaka ändringar och inventering

Du kan samla in och visa inventeringar för program, filer, Linux-daemons, Windows-tjänster och Windows-registernycklar på dina datorer. Om du spårar konfigurationerna för dina datorer kan du identifiera driftproblem i miljön och bättre förstå datorernas tillstånd.

### <a name="enable-change-and-inventory-management"></a>Aktivera ändringsspårning och inventering

Så här aktiverar du ändringsspårning och inventering för din virtuella dator:

1. Välj **Virtuella datorer** till vänster på skärmen.
2. Välj en virtuell dator i listan.
3. Gå till avsnittet **Åtgärder** på VM-skärmen och klicka på **Inventering** eller **Ändringsspårning**. Skärmen **Aktivera ändringsspårning och inventering** öppnas.

Konfigurera platsen, Log Analytics arbets ytan och automation-kontot som ska användas och välj **Aktivera**. Om fälten är nedtonade betyder det att någon annan automatiseringslösning är aktiverad för den virtuella datorn, och samma arbetsyta och Automation-konto måste användas. Trots att lösningarna är separata på menyn är de samma lösning. När du aktiverar den ena aktiveras båda för den virtuella datorn.

![Aktivera Ändringsspårning och lager](./media/tutorial-monitoring/manage-inventory-enable.png)

När lösningen har aktiverats kan det dröja en stund medan lagret som samlas in på den virtuella datorn innan data visas.

### <a name="track-changes"></a>Spåra ändringar

På din virtuella dator väljer du **Ändringsspårning** under **ÅTGÄRDER**. Välj **Redigera inställningar**. Sidan **Ändringsspårning** visas. Välj typ av inställning som du vill spåra och klicka sedan på **+ Lägg till** för att konfigurera inställningarna. Det tillgängliga Linux-alternativet är **Linux-filer**

Mer information om ändringsspårning finns i [Felsöka ändringar på en virtuell dator](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Visa inventering

Från din virtuella dator väljer du **Inventering** under **ÅTGÄRDER**. På fliken **Programvara** finns det en tabell som anger programvaran som har hittats. Utförlig information för varje programpost visas i tabellen. Uppgifterna omfattar bland annat programvarunamn, version, utgivare och datum för senaste uppdatering.

![Visa inventering](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Övervaka aktivitetsloggar och ändringar

Från sidan **Ändringsspårning** på din virtuella dator väljer du **Hantera aktivitetslogganslutning**. Den här uppgiften öppnar sidan **Azure-aktivitetslogg**. Välj **Anslut** för att ansluta Ändringsspårning till Azure-aktivitetsloggen för din virtuella dator.

När den här inställningen är aktiverad går du till sidan **Översikt** för din virtuella dator och väljer **Stoppa** för att stoppa din virtuella dator. När du uppmanas väljer du **Ja** för att stoppa den virtuella datorn. När den har frigjorts väljer du **Starta** för att starta om din virtuella dator.

När en virtuell dator stoppas och startas loggar den virtuella datorn en händelse i aktivitetsloggen. Gå tillbaka till sidan **Ändringsspårning**. Välj fliken **Händelser** längst ned på sidan. Efter ett tag visas händelserna i diagrammet och tabellen. Du kan välja varje händelse för att visa detaljerad information om händelsen.

![Visa händelser i aktivitetsloggen](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Diagrammet visar ändringar som har skett över tid. När du har lagt till en aktivitetslogganslutning visar linjediagrammet högst upp händelser i Azure-aktivitetsloggen. Varje rad med stapeldiagram representerar en annan spårningsbar ändringstyp. Typerna är Linux-daemons, filer och program. På fliken Ändra visas information för de ändringar som visas i visualiseringen i fallande tidsordning för när ändringen skedde (senaste först).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du konfigurerat och granskat Ändringsspårning och Uppdateringshantering för den virtuella datorn. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en resursgrupp och virtuell dator
> * Hantera Linux-uppdateringar
> * Övervaka ändringar och inventering

Gå vidare till nästa självstudie om du vill veta mer om övervakning av den virtuella datorn.

> [!div class="nextstepaction"]
> [Övervakning av virtuella datorer](/previous-versions/azure/virtual-machines/linux/tutorial-monitor)