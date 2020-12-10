---
title: Distribuera Azure-dedikerade värdar med hjälp av Azure Portal
description: Distribuera virtuella datorer och skalnings uppsättningar till dedikerade värdar med hjälp av Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: b166363a8c64a4a4c5d34efa55dcaefa09d6df49
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007950"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts-using-the-portal"></a>Distribuera virtuella datorer och skalnings uppsättningar till dedikerade värdar med hjälp av portalen 

Den här artikeln vägleder dig genom hur du skapar en dedikerad Azure- [värd](dedicated-hosts.md) som värd för dina virtuella datorer. 


## <a name="limitations"></a>Begränsningar

- De storlekar och maskin varu typer som är tillgängliga för dedikerade värdar varierar beroende på region. Mer information hittar du på [prissättnings sidan](https://aka.ms/ADHPricing) för värden.

## <a name="create-a-host-group"></a>Skapa en värd grupp

En **värd grupp** är en resurs som representerar en samling dedikerade värdar. Du skapar en värd grupp i en region och en tillgänglighets zon och lägger till värdar i den. När du planerar för hög tillgänglighet finns det ytterligare alternativ. Du kan använda ett eller båda av följande alternativ med dina dedikerade värdar: 
- Sträck över flera tillgänglighets zoner. I så fall måste du ha en värd grupp i var och en av de zoner som du vill använda.
- Sträck över flera fel domäner som är mappade till fysiska rack. 
 
I båda fallen måste du ange antalet fel domäner för värd gruppen. Om du inte vill spänna över fel domäner i gruppen använder du antalet fel domäner 1. 

Du kan också välja att använda både tillgänglighets zoner och fel domäner. 

I det här exemplet ska vi skapa en värd grupp med hjälp av 1 tillgänglighets zon och 2 fel domäner. 


1. Öppna Azure [Portal](https://portal.azure.com). 
1. Välj **skapa en resurs** i det övre vänstra hörnet.
1. Sök efter **värd grupp** och välj sedan **värd grupper** från resultaten.
1. På sidan **värd grupper** väljer du **skapa**.
1. Välj den prenumeration som du vill använda och välj sedan **Skapa ny** för att skapa en ny resurs grupp.
1. Skriv *myDedicatedHostsRG* som **namn** och välj sedan **OK**.
1. För **värd grupp namn** skriver du *myHostGroup*.
1. För **plats** väljer du **östra USA**.
1. För **tillgänglighets zon** väljer du **1**.
1. För **antal fel domäner** väljer du **2**.
1. Välj **automatisk placering** för att automatiskt tilldela virtuella datorer och skalnings uppsättnings instanser till en tillgänglig värd i gruppen.
1. Välj **Granska + skapa** och vänta sedan på verifiering.
1. När du ser meddelandet **valideringen som skickats** väljer du **skapa** för att skapa värd gruppen.

Det bör bara ta en stund att skapa värd gruppen.


## <a name="create-a-dedicated-host"></a>Skapa en dedikerad värd

Skapa nu en dedikerad värd i värd gruppen. Förutom ett namn för värden måste du ange SKU för värden. Värd-SKU: n samlar in de VM-serier som stöds samt maskin varu generering för den dedikerade värden.

Mer information om värd-SKU: er och priser finns i [prissättning för Azure-dedikerad värd](https://aka.ms/ADHPricing).

Om du anger ett fel domän antal för värd gruppen uppmanas du att ange fel domänen för värden.  

1. Välj **skapa en resurs** i det övre vänstra hörnet.
1. Sök efter **dedikerad värd** och välj sedan **dedikerade värdar** från resultaten.
1. På sidan **dedikerade värdar** väljer du **skapa**.
1. Välj den prenumeration som du vill använda.
1. Välj *myDedicatedHostsRG* som **resurs grupp**.
1. I **instans information** skriver du in *värd* för **namnet** och väljer *östra USA* för platsen.
1. I **maskin varu profil** väljer du *standard Es3 Family – typ 1* för **storleks familjen**, väljer *myHostGroup* för **värd gruppen** och väljer sedan *1* för **fel domänen**. Lämna standardvärdena för resten av fälten.
1. När du är färdig väljer du **Granska + skapa** och väntar på verifiering.
1. När du ser meddelandet **valideringen som skickats** väljer du **skapa** för att skapa värden.

## <a name="create-a-vm"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** längst upp till vänster i Azure-portalen.
1. Sök efter och välj den avbildning som du vill använda i sökrutan ovanför Azure Marketplace-resurser och välj sedan **skapa**.
1. På fliken **grundläggande** , under **projekt information**, se till att rätt prenumeration är markerad och välj sedan *myDedicatedHostsRG* som **resurs grupp**. 
1. Under **Instansinformation** skriver du *myVM* för **Namn på virtuell dator** och väljer *USA, östra* som **Plats**.
1. I **tillgänglighets alternativ** väljer du **tillgänglighets zon** väljer du *1* i list rutan.
1. Välj **ändra storlek** som storlek. I listan över tillgängliga storlekar väljer du en från Esv3-serien, som **standard E2s v3**. Du kan behöva rensa filtret för att se alla tillgängliga storlekar.
1. Fyll i resten av fälten på fliken **grundläggande** vid behov.
1. Om du vill ange vilken värd som ska användas för din virtuella dator väljer du fliken **Avancerat** på sidan och väljer *myHostGroup* för värd **grupp** *och värd* **för värd avsnittet** i avsnittet **värd** . Annars placeras den virtuella datorn automatiskt på en värd med kapacitet.
    ![Välj värd grupp och värd](./media/dedicated-hosts-portal/advanced.png)
1. Låt de återstående standardinställningarna vara och välj sedan knappen **Granska + skapa** längst ned på sidan.
1. När du ser meddelandet att valideringen har slutförts väljer du **skapa**.

Det tar några minuter för den virtuella datorn att distribueras.

## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning 

När du distribuerar en skalnings uppsättning anger du värd gruppen.

1. Sök efter *skalnings uppsättning* och välj **skalnings uppsättningar för virtuella datorer** i listan.
1. Välj **Lägg till** för att skapa en ny skalnings uppsättning.
1. Fyll i fälten på fliken **grundläggande** som vanligt, men se till att du väljer en VM-storlek från serien som du valde för din dedikerade värd, som **standard E2s v3**.
1. På fliken **Avancerat** väljer du **maximal spridning** vid **spridnings algoritm** .
1. I **värd grupp** väljer du värd gruppen i list rutan. Om du nyss skapade gruppen kan det ta en minut att lägga till i listan.

## <a name="add-an-existing-vm"></a>Lägg till en befintlig virtuell dator 

Du kan lägga till en virtuell dator som avslutas till en dedikerad värd, men den virtuella datorn måste först vara Stop\Deallocated. Innan du flyttar en virtuell dator till en dedikerad värd kontrollerar du att VM-konfigurationen stöds:

- Storleken på den virtuella datorn måste vara i samma storleks familj som den dedikerade värden. Om din dedikerade värd till exempel är DSv3, kan storleken på den virtuella datorn vara Standard_D4s_v3, men det gick inte att Standard_A4_v2. 
- Den virtuella datorn måste finnas i samma region som den dedikerade värden.
- Den virtuella datorn kan inte ingå i en närhets placerings grupp. Ta bort den virtuella datorn från närhets placerings gruppen innan du flyttar den till en dedikerad värd. Mer information finns i [flytta en virtuell dator från en närhets placerings grupp](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- Den virtuella datorn får inte finnas i en tillgänglighets uppsättning.
- Om den virtuella datorn finns i en tillgänglighets zon måste den vara samma tillgänglighets zon som värd gruppen. Inställningarna för tillgänglighets zonen för den virtuella datorn och värd gruppen måste matcha.

Flytta den virtuella datorn till en dedikerad värd med hjälp av [portalen](https://portal.azure.com).

1. Öppna sidan för den virtuella datorn.
1. Välj **stoppa** för att stop\deallocate den virtuella datorn.
1. Välj **konfiguration** på den vänstra menyn.
1. Välj en värd grupp och en värd från List menyerna.
1. När du är färdig väljer du **Spara** längst upp på sidan.
1. När den virtuella datorn har lagts till i värden väljer du **Översikt** på den vänstra menyn.
1. Längst upp på sidan väljer du **Starta** för att starta om den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i Översikt över [dedikerade värdar](dedicated-hosts.md) .

- Det finns en exempel mall som du hittar [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), som använder både zoner och fel domäner för maximal återhämtning i en region.

- Du kan också distribuera en dedikerad värd med hjälp av [Azure CLI](./linux/dedicated-hosts-cli.md) eller [PowerShell](./windows/dedicated-hosts-powershell.md).
