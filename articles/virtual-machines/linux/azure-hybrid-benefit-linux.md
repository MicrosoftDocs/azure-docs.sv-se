---
title: Azure Hybrid-förmån och virtuella Linux-datorer
description: Lär dig hur Azure Hybrid-förmån kan hjälpa dig att spara pengar på dina virtuella Linux-datorer som körs på Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machines
ms.subservice: azure-hybrid-benefit
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 73747222b9131fa85ae6ac01c9dedd5b0bbe1d63
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543421"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Hur Azure Hybrid-förmån gäller för virtuella Linux-datorer

Azure Hybrid-förmån är en licens förmån som hjälper dig att avsevärt minska kostnaderna för att köra dina Red Hat Enterprise Linux (RHEL) och SUSE Linux Enterprise Server (SLES) virtuella datorer (VM) i molnet. Med den här förmånen betalar du bara för infrastruktur kostnaderna för din virtuella dator eftersom din RHEL-eller SLES-prenumeration täcker program varu avgiften. Förmånen är tillgänglig för alla RHEL-och SLES Marketplace-avbildningar (PAYG).

Azure Hybrid-förmån för virtuella Linux-datorer är nu offentligt tillgängliga.

## <a name="benefit-description"></a>Förmåns Beskrivning

Med hjälp av Azure Hybrid-förmån kan du migrera dina lokala RHEL-och SLES-servrar till Azure genom att konvertera befintliga RHEL-och SLES PAYG-datorer på Azure för att få en BYOS-fakturering. Vanligt vis debiteras virtuella datorer som distribueras från PAYG-avbildningar på Azure både en infrastruktur avgift och en program varu avgift. Med Azure Hybrid-förmån kan virtuella datorer för virtuella datorer konverteras till en BYOS-fakturerings modell utan en omdistribution, så att du kan undvika eventuell stillestånds risk.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Azure Hybrid-förmån kostnads visualisering på virtuella Linux-datorer.":::

När du har aktiverat förmånen för RHEL eller SLES VM kommer du inte längre att debiteras för den ytterligare program varu avgiften som normalt uppstår på en PAYG VM. I stället påbörjas den virtuella datorn med en BYOS avgift som bara omfattar beräknings maskin varu avgiften och ingen program varu avgift.

Du kan också välja att konvertera en virtuell dator där förmånen har Aktiver ATS till en PAYG fakturerings modell.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Omfattning för Azure Hybrid-förmån berättigande för virtuella Linux-datorer

Azure Hybrid-förmån är tillgängligt för alla RHEL-och SLES-PAYG-avbildningar från Azure Marketplace. Förmånen är ännu inte tillgänglig för RHEL-eller SLES-BYOS-avbildningar eller anpassade avbildningar från Azure Marketplace.

Azures dedikerade värd instanser och SQL hybrid-förmåner är inte berättigade till Azure Hybrid-förmån om du redan använder fördelarna med virtuella Linux-datorer.

## <a name="get-started"></a>Kom igång

### <a name="red-hat-customers"></a>Red Hat-kunder

Azure Hybrid-förmån för RHEL är tillgänglig för Red Hat-kunder som uppfyller båda dessa kriterier:

- Ha aktiva eller oanvända RHEL-prenumerationer som är tillgängliga för användning i Azure
- Ha aktiverat en eller flera av dessa prenumerationer för användning i Azure med [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) -programmet

> [!IMPORTANT]
> Se till att rätt prenumeration har Aktiver ATS i [Cloud-Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) -programmet.

Börja använda fördelarna med Red Hat:

1. Aktivera en eller flera av dina kvalificerade RHEL-prenumerationer för användning i Azure med hjälp av [Red Hat Cloud accesss kund gränssnitt](https://access.redhat.com/management/cloud).

   De Azure-prenumerationer som du anger under den Red Hat Cloud Access-aktiveringen kommer sedan att kunna använda Azure Hybrid-förmån funktionen.
1. Tillämpa Azure Hybrid-förmån på någon av dina befintliga RHEL PAYG-VM: ar och eventuella nya RHEL-VM: er som du distribuerar från Azure Marketplace PAYG-avbildningar. Du kan använda Azure Portal eller Azure CLI för att aktivera förmånen.
1. Följ rekommenderade [Nästa steg](https://access.redhat.com/articles/5419341) för att konfigurera uppdaterings källor för dina virtuella RHEL-datorer och för rikt linjer för RHEL-prenumerationer.


### <a name="suse-customers"></a>SUSE kunder

Börja använda fördelarna med SUSE:

1. Registrera dig för det offentliga moln programmet för SUSE.
1. Använd förmånen för dina nyligen skapade eller befintliga virtuella datorer via Azure Portal eller Azure CLI.
1. Registrera dina virtuella datorer som får förmånen med en separat källa till uppdateringar.

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Aktivera och inaktivera förmånen i Azure Portal

Du kan aktivera förmånen för befintliga virtuella datorer genom att gå till **konfigurations** alternativet till vänster och följa stegen där. Du kan aktivera förmånen för nya virtuella datorer när den virtuella datorn skapas.

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-vm"></a>Azure Portal exempel för att aktivera förmånen för en befintlig virtuell dator:
1. Besök [Microsoft Azure-portalen](https://portal.azure.com/)
1. Gå till sidan Skapa en virtuell dator på portalen.
 ![AHB när den virtuella datorn skapades](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. Klicka på kryss rutan för att aktivera AHB-konvertering och Använd moln åtkomst licenser.
 ![AHB när du skapar en VM-kryss ruta](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. Skapa en virtuell dator efter nästa uppsättning instruktioner
1. Markera **konfigurations** bladet så visas alternativet aktive rad. 
![AHB konfigurations blad efter att du har skapat](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-during-creation-of-vm"></a>Azure Portal exempel för att aktivera förmånen när du skapar en virtuell dator:
1. Besök [Microsoft Azure-portalen](https://portal.azure.com/)
1. Öppna sidan för den virtuella datorn som du vill använda konverteringen på.
1. Gå till **konfigurations** alternativet till vänster. Avsnittet licens visas. Om du vill aktivera AHB-konverteringen markerar du alternativ knappen Ja och markerar kryss rutan Bekräfta.
![AHB konfigurations blad efter att du har skapat](./media/azure-hybrid-benefit/create-configuration-blade.png)

>[!NOTE]
> Om du har skapat en **anpassad ögonblicks** bild eller en **delad avbildning (sig)** av en RHEL-eller SLES PAYG Marketplace-avbildning kan du bara använda Azure CLI för att aktivera Azure Hybrid-förmån. Detta är en känd begränsning och det finns för närvarande ingen tids linje för att tillhandahålla den här funktionen på Azure-portalen.

## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Aktivera och inaktivera förmånen i Azure CLI

Du kan använda `az vm update` kommandot för att uppdatera befintliga virtuella datorer. Kör kommandot med en-parameter för virtuella RHEL-datorer `--license-type` `RHEL_BYOS` . Kör kommandot med en-parameter för virtuella SLES-datorer `--license-type` `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>CLI-exempel för att aktivera förmånen
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>CLI-exempel för att inaktivera förmånen
Om du vill inaktivera förmånen använder du `--license-type` värdet `None` :

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>CLI-exempel för att aktivera förmånen för ett stort antal virtuella datorer
Om du vill aktivera förmånen för ett stort antal virtuella datorer kan du använda- `--ids` parametern i Azure CLI:

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

I följande exempel visas två metoder för att hämta en lista över resurs-ID: en på resurs grupps nivå och en på prenumerations nivå.

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>Använd Azure Hybrid-förmån när den virtuella datorns skapande tid
Förutom att använda Azure Hybrid-förmån på befintliga virtuella datorer som du betalar per användning kan du anropa det när du skapar den virtuella datorn. Fördelarna med att göra detta är threefold:
- Du kan etablera både PAYG-och BYOS-VM: ar med samma avbildning och process.
- Det möjliggör framtida ändringar i licens läge, något som inte är tillgängligt med en BYOS avbildning eller om du använder en egen virtuell dator.
- Den virtuella datorn kommer att anslutas till Red Hats uppdaterings infrastruktur (RHUI) som standard för att säkerställa att den förblir uppdaterad och säker. Du kan ändra den uppdaterade mekanismen efter distributionen när du vill.

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>Kontrol lera Azure Hybrid-förmån status för en virtuell dator
Du kan visa Azure Hybrid-förmån status för en virtuell dator med hjälp av Azure CLI eller Azure Instance Metadata Service.

### <a name="azure-cli"></a>Azure CLI

Du kan använda `az vm get-instance-view` kommandot för det här ändamålet. Leta efter ett `licenseType` fält i svaret. Om `licenseType` fältet finns och värdet är `RHEL_BYOS` eller `SLES_BYOS` , har den virtuella datorn förmånen aktive rad.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

Från den virtuella datorn kan du fråga de beskrivande metadata i Azure Instance Metadata Service för att fastställa den virtuella datorns `licenseType` värde. `licenseType`Värdet `RHEL_BYOS` eller `SLES_BYOS` anger att den virtuella datorn har förmånen aktive rad. [Läs mer om attestering av metadata](./instance-metadata-service.md#attested-data).

## <a name="compliance"></a>Efterlevnad

### <a name="red-hat"></a>Red Hat

Kunder som använder Azure Hybrid-förmån för RHEL accepterar de [juridiska villkoren](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) och den [sekretess policy](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) som är ASSOCIERAD med Azure Marketplace RHEL-erbjudanden.

Kunder som använder Azure Hybrid-förmån för RHEL har tre alternativ för att tillhandahålla program uppdateringar och korrigeringsfiler för de virtuella datorerna:

- [Red Hat-uppdaterings infrastruktur](../workloads/redhat/redhat-rhui.md) (standard alternativet)
- Red Hat satellit-Server
- Red Hat-prenumerations hanterare

Kunder som väljer alternativet RHUI kan fortsätta att använda RHUI som primär uppdaterings källa för sina Azure Hybrid-förmån RHEL-VM: ar utan att koppla RHEL-prenumerationer till dessa virtuella datorer. Kunder som väljer alternativet RHUI ansvarar för att säkerställa kompatibilitet med RHEL-prenumeration.

Kunder som väljer antingen Red Hat satellit-Server eller Red Hat-prenumerations hanterare bör ta bort RHUI-konfigurationen och sedan ansluta en moln åtkomst aktive rad RHEL-prenumeration till sina Azure Hybrid-förmån RHEL-VM: ar.  

Mer information om kompatibilitet med Red Hat-prenumeration, program uppdateringar och källor för Azure Hybrid-förmån virtuella datorer med RHEL finns i [artikeln Red Hat om hur du använder RHEL-prenumerationer med Azure Hybrid-förmån](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

Om du vill använda Azure Hybrid-förmån för virtuella SLES-datorer och information om hur du flyttar från SLES PAYG till BYOS eller flyttar från SLES BYOS till PAYG, se [SUSE Linux Enterprise och Azure Hybrid-förmån](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/). 

## <a name="azure-hybrid-benefit-on-reserved-instances-is-in-preview"></a>Azure Hybrid-förmån på reserverade instanser är i för hands version

Azure Reservations (Azure Reserved Virtual Machine Instances) hjälper dig att spara pengar genom att använda ett år eller tre års planer för flera produkter. Du kan lära dig mer om [reserverade instanser här](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations). Azure Hybrid-förmån finns tillgänglig som för hands version för [reserverad virtuell dator instans (RIs)](https://review.docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations#charges-covered-by-reservation). Det innebär att om du har köpt beräknings kostnader till ett rabatterat pris med hjälp av RI kan du tillämpa AHB-förmånen på licens kostnaderna för RHEL och SUSE på den. De steg som krävs för att tillämpa AHB-förmånen för en RI-instans är exakt samma som för en vanlig virtuell dator.
![AHB för RIs](./media/azure-hybrid-benefit/reserved-instances.png)

>[!NOTE]
>Om du redan har köpt reservationer för RHEL-eller SUSE PAYG-program på Azure Marketplace väntar du tills reservationen är klar innan du använder Azure Hybrid-förmån..


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
*F: kan jag använda en licens typ `RHEL_BYOS` med en SLES-avbildning eller vice versa?*

A: Nej, du kan inte. Om du försöker ange en licens typ som felaktigt matchar distributionen som körs på den virtuella datorn uppdateras inga fakturerings-metadata. Men om du av misstag anger fel licens typ kan du fortfarande aktivera förmånen om du uppdaterar den virtuella datorn igen till rätt licens typ.

*F: Jag har registrerat dig med Red Hat Cloud Access men kan fortfarande inte aktivera fördelarna med mina RHEL-VM: ar. Vad ska jag göra?*

A: det kan ta lite tid för prenumerations registreringen av Red Hat Cloud Access att sprida sig från Red Hat till Azure. Kontakta Microsoft-supporten om du fortfarande ser felet efter en arbets dag.

*F: Jag har distribuerat en virtuell dator med hjälp av RHEL BYOS "guld bild". Kan jag konvertera faktureringen för dessa bilder från BYOS till PAYG?*

A: Nej, du kan inte. Azure Hybrid-förmån stöder endast konvertering för avbildningar enligt principen betala per användning.

*F: Jag har laddat upp en egen RHEL-avbildning från lokalt (via Azure Migrate, Azure Site Recovery eller på annat sätt) till Azure. Kan jag konvertera faktureringen för dessa bilder från BYOS till PAYG?*

A: Nej, du kan inte. Azure Hybrid-förmån funktionen är för närvarande endast tillgänglig för RHEL-och SLES-avbildningar på Azure Marketplace. 

*F: Jag har laddat upp en egen RHEL-avbildning från lokalt (via Azure Migrate, Azure Site Recovery eller på annat sätt) till Azure. Behöver jag göra något för att dra nytta av Azure Hybrid-förmån?*

A: Nej, du behöver inte. RHEL-avbildningar som du överför betraktas redan som BYOS och du debiteras bara för Azures infrastruktur kostnader. Du är ansvarig för RHEL prenumerations kostnader, precis som du är för dina lokala miljöer. 

*F: kan jag använda Azure Hybrid-förmån på virtuella datorer som distribueras från Azure Marketplace RHEL och SLES SAP-avbildningar?*

A: Ja, du kan. Du kan använda licens typen för `RHEL_BYOS` för virtuella RHEL-datorer och `SLES_BYOS` för konverteringar av virtuella datorer som distribueras från Azure Marketplace RHEL och SLES SAP-avbildningar.

*F: kan jag använda Azure Hybrid-förmån på skalnings uppsättningar för virtuella datorer för RHEL och SLES?*

A: Ja, Azure Hybrid-förmån på skalnings uppsättningar för virtuella datorer för RHEL och SLES är i för hands version. Du kan [lära dig mer om den här förmånen och hur du använder den här](https://docs.microsoft.com/azure/virtual-machine-scale-sets/azure-hybrid-benefit-linux-vmss). 

*F: kan jag använda Azure Hybrid-förmån på reserverade instanser för RHEL och SLES?*

A: Ja, Azure Hybrid-förmån på en reserverad instans för RHEL och SLES är i för hands version. Du kan [lära dig mer om den här förmånen och hur du använder den här](#azure-hybrid-benefit-on-reserved-instances-is-in-preview).

*F: kan jag använda Azure Hybrid-förmån på en virtuell dator som har distribuerats för SQL Server på RHEL-avbildningar?*

A: Nej, du kan inte. Det finns ingen plan för att stödja de här virtuella datorerna.

*F: kan jag använda Azure Hybrid-förmån på RHEL Virtual Data Center-prenumerationen?*

A: Nej, du kan inte. VDC stöds inte i Azure alls, inklusive AHB.  
 

## <a name="common-problems"></a>Vanliga problem
Det här avsnittet innehåller vanliga problem som kan uppstå och åtgärder för att minska.

| Fel | Åtgärd |
| ----- | ---------- |
| "Åtgärden kunde inte slutföras eftersom våra poster visar att du inte har aktiverat Red Hat Cloud Access på din Azure-prenumeration..." | Om du vill använda förmånen med virtuella datorer i RHEL måste du först [Registrera dina Azure-prenumerationer med Red Hat Cloud Access](https://access.redhat.com/management/cloud).

## <a name="next-steps"></a>Nästa steg
* [Lär dig hur du skapar och uppdaterar virtuella datorer och lägger till licens typer (RHEL_BYOS, SLES_BYOS) för Azure Hybrid-förmån med hjälp av Azure CLI](/cli/azure/vm)
