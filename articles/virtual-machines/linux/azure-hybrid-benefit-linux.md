---
title: Azure Hybrid-förmån och virtuella Linux-datorer
description: Lär dig Azure Hybrid-förmån kan hjälpa dig att spara pengar på dina virtuella Linux-datorer som körs i Azure.
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
ms.openlocfilehash: 774f4be6a5aa0e0e772086c52938881c6637b261
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588198"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Så Azure Hybrid-förmån gäller för virtuella Linux-datorer

Azure Hybrid-förmån är en licensieringsförmån som hjälper dig att avsevärt minska kostnaderna för att köra dina virtuella Red Hat Enterprise Linux-datorer (RHEL) och virtuella SUSE Linux Enterprise Server-datorer (SLES) i molnet. Med den här förmånen betalar du bara för infrastrukturkostnaderna för den virtuella datorn eftersom din RHEL- eller SLES-prenumeration täcker programvaruavgiften. Förmånen är tillgänglig för alla RHEL- och SLES Marketplace PAYG-avbildningar (betala enligt din go-betalning).

Azure Hybrid-förmån för virtuella Linux-datorer är nu offentligt tillgänglig.

## <a name="benefit-description"></a>Beskrivning av förmån

Via Azure Hybrid-förmån kan du migrera dina lokala RHEL- och SLES-servrar till Azure genom att konvertera befintliga virtuella RHEL- och SLES PAYG-datorer i Azure till BYOS-fakturering (Bring Your Own Subscription). Virtuella datorer som distribueras från PAYG-avbildningar i Azure debiterar vanligtvis både en infrastrukturavgift och en programvaruavgift. Med Azure Hybrid-förmån kan virtuella PAYG-datorer konverteras till en BYOS-faktureringsmodell utan omdistribution, så att du kan undvika eventuella avbrottsrisker.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Azure Hybrid-förmån kostnadsvisualisering på virtuella Linux-datorer.":::

När du aktiverar förmånen på en virtuell RHEL- eller SLES-dator debiteras du inte längre för den extra programvaruavgift som vanligtvis tillkommer på en virtuell PAYG-dator. I stället börjar den virtuella datorn att debiteras med en BYOS-avgift, som endast inkluderar beräkningsmaskinvaruavgiften och ingen programvaruavgift.

Du kan också välja att konvertera en virtuell dator som har haft förmånen aktiverad på den tillbaka till en PAYG-faktureringsmodell.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Omfång för Azure Hybrid-förmån för virtuella Linux-datorer

Azure Hybrid-förmån är tillgängligt för alla RHEL- och SLES PAYG-avbildningar från Azure Marketplace. Förmånen är ännu inte tillgänglig för RHEL- eller SLES BYOS-avbildningar eller anpassade avbildningar från Azure Marketplace.

Azure Dedicated Host instanser och SQL-hybridförmåner är inte berättigade till Azure Hybrid-förmån om du redan använder förmånen med virtuella Linux-datorer.

## <a name="get-started"></a>Kom igång

### <a name="red-hat-customers"></a>Red Hat-kunder

Azure Hybrid-förmån för RHEL är tillgängligt för Red Hat-kunder som uppfyller båda dessa kriterier:

- Ha aktiva eller oanvända RHEL-prenumerationer som är berättigade till användning i Azure
- Har aktiverat en eller flera av dessa prenumerationer för användning i Azure med [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) program

> [!IMPORTANT]
> Kontrollera att rätt prenumeration har aktiverats i [molnåtkomstprogrammet.](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)

Så här börjar du använda förmånen för Red Hat:

1. Aktivera en eller flera av dina berättigade RHEL-prenumerationer för användning i Azure med hjälp [Red Hat Cloud Access kundgränssnittet](https://access.redhat.com/management/cloud).

   De Azure-prenumerationer som du anger Red Hat Cloud Access processen för att aktivera kommer sedan att tillåtas att använda Azure Hybrid-förmån-funktionen.
1. Tillämpa Azure Hybrid-förmån på någon av dina befintliga virtuella RHEL PAYG-datorer och alla nya virtuella RHEL-datorer som du distribuerar från Azure Marketplace PAYG-avbildningar. Du kan använda Azure Portal eller Azure CLI för att aktivera förmånen.
1. Följ de [rekommenderade nästa stegen för](https://access.redhat.com/articles/5419341) att konfigurera uppdateringskällor för dina virtuella RHEL-datorer och för riktlinjer för RHEL-prenumerationsefterlevnad.


### <a name="suse-customers"></a>SUSE-kunder

Så här börjar du använda förmånen för SUSE:

1. Registrera dig för SUSE Public Cloud Program.
1. Tillämpa förmånen på dina nyligen skapade eller befintliga virtuella datorer via Azure Portal eller Azure CLI.
1. Registrera dina virtuella datorer som får förmånen med en separat källa för uppdateringar.

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Aktivera och inaktivera förmånen i Azure Portal

Du kan aktivera förmånen på befintliga virtuella datorer genom att **gå till** konfigurationsalternativet till vänster och följa stegen där. Du kan aktivera förmånen på nya virtuella datorer när den virtuella datorn skapas.

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-vm"></a>Azure Portal exempel för att aktivera förmånen för en befintlig virtuell dator:
1. Besök [Microsoft Azure-portalen](https://portal.azure.com/)
1. Gå till sidan Skapa en virtuell dator i portalen.
 ![AHB när du skapar en virtuell dator](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. Klicka på kryssrutan för att aktivera AHB-konvertering och använda molnåtkomstlicenser.
 ![AHB när du skapar den virtuella datorn kryssruta](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. Skapa en virtuell dator enligt nästa uppsättning instruktioner
1. Kontrollera **bladet** Konfiguration så ser du att alternativet är aktiverat. 
![AHB-konfigurationsbladet när du har skapat](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-during-creation-of-vm"></a>Azure Portal exempel för att aktivera förmånen när du skapar en virtuell dator:
1. Besök [Microsoft Azure-portalen](https://portal.azure.com/)
1. Öppna sidan Virtuell dator där du vill tillämpa konverteringen.
1. Gå **till konfigurationsalternativet** till vänster. Du ser avsnittet Licensiering. Om du vill aktivera AHB-konvertering markerar du alternativknappen "Ja" och markerar kryssrutan Bekräftelse.
![AHB-konfigurationsbladet när du har skapat](./media/azure-hybrid-benefit/create-configuration-blade.png)

>[!NOTE]
> Om du har skapat en **anpassad** ögonblicksbild eller en delad avbildning **av** en RHEL- eller SLES PAYG Marketplace-avbildning kan du bara använda Azure CLI för att aktivera Azure Hybrid-förmån. Det här är en känd begränsning och det finns för närvarande ingen tidslinje för att tillhandahålla den här funktionen på Azure-portalen.

## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Aktivera och inaktivera förmånen i Azure CLI

Du kan använda kommandot `az vm update` för att uppdatera befintliga virtuella datorer. För virtuella RHEL-datorer kör du kommandot med `--license-type` parametern `RHEL_BYOS` . För virtuella SLES-datorer kör du kommandot med `--license-type` parametern `SLES_BYOS` .

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

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>CLI-exempel för att aktivera förmånen på ett stort antal virtuella datorer
Om du vill aktivera förmånen på ett stort antal virtuella datorer kan du använda `--ids` parametern i Azure CLI:

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

I följande exempel visas två metoder för att hämta en lista över resurs-ID:er: en på resursgruppsnivå och en på prenumerationsnivå.

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>Tillämpa Azure Hybrid-förmån när den virtuella datorn skapas
Förutom att tillämpa Azure Hybrid-förmån på befintliga virtuella datorer med användningsbaserade betalning kan du anropa den när den virtuella datorn skapas. Fördelarna med att göra det är trefaldiga:
- Du kan etablera både virtuella PAYG- och BYOS-datorer med hjälp av samma avbildning och process.
- Det möjliggör framtida licenslägesändringar, något som inte är tillgängligt med en avbildning med endast BYOS eller om du tar med din egen virtuella dator.
- Den virtuella datorn ansluts som standard till Red Hat Update Infrastructure (RHUI) för att säkerställa att den är uppdaterad och säker. Du kan ändra den uppdaterade mekanismen efter distributionen när som helst.

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>Kontrollera status Azure Hybrid-förmån virtuell dator
Du kan visa Azure Hybrid-förmån status för en virtuell dator med hjälp av Azure CLI eller med hjälp av Azure Instance Metadata Service.

### <a name="azure-cli"></a>Azure CLI

Du kan använda kommandot `az vm get-instance-view` för detta ändamål. Leta efter `licenseType` ett fält i svaret. Om fältet `licenseType` finns och värdet är eller har den virtuella datorn `RHEL_BYOS` `SLES_BYOS` förmånen aktiverad.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

Från själva den virtuella datorn kan du köra frågor mot de attesterade metadata i Azure Instance Metadata Service för att fastställa den virtuella datorns `licenseType` värde. Värdet `licenseType` eller anger att den virtuella datorn har `RHEL_BYOS` `SLES_BYOS` förmånen aktiverad. [Läs mer om attesterade metadata](./instance-metadata-service.md#attested-data).

## <a name="compliance"></a>Efterlevnad

### <a name="red-hat"></a>Red Hat

Kunder som använder Azure Hybrid-förmån för RHEL godkänner [de](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) juridiska standardvillkoren och sekretesspolicyn som är associerade med de Azure Marketplace RHEL-erbjudandena. [](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf)

Kunder som använder Azure Hybrid-förmån RHEL har tre alternativ för att tillhandahålla programuppdateringar och korrigeringar till dessa virtuella datorer:

- [Red Hat-uppdateringsinfrastruktur](../workloads/redhat/redhat-rhui.md) (standardalternativ)
- Red Hat-satellitserver
- Red Hat Subscription Manager

Kunder som väljer ALTERNATIVET RHUI kan fortsätta att använda RHUI som huvuduppdateringskälla för sina virtuella Azure Hybrid-förmån RHEL-datorer utan att koppla RHEL-prenumerationer till de virtuella datorerna. Kunder som väljer ALTERNATIVET RHUI ansvarar för att säkerställa RHEL-prenumerationens efterlevnad.

Kunder som väljer antingen Red Hat Satellite Server eller Red Hat Subscription Manager bör ta bort RHUI-konfigurationen och sedan koppla en Cloud Access-aktiverad RHEL-prenumeration till sina virtuella Azure Hybrid-förmån RHEL-datorer.  

Mer information om red hat-prenumerationsefterlevnad, programuppdateringar och källor för virtuella Azure Hybrid-förmån RHEL-datorer finns i Red Hat-artikeln om att använda [RHEL-prenumerationer med Azure Hybrid-förmån](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

Information om Azure Hybrid-förmån för dina virtuella SLES-datorer och information om hur du flyttar från SLES PAYG till BYOS eller flyttar från SLES BYOS till PAYG finns i [SUSE Linux Enterprise och Azure Hybrid-förmån](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/). 

## <a name="azure-hybrid-benefit-on-reserved-instances-is-in-preview"></a>Azure Hybrid-förmån reserverade instanser är i förhandsversion

Azure-reservationer (Azure Reserved Virtual Machine Instances) hjälper dig att spara pengar genom att betala för ett eller tre år för flera produkter. Du kan läsa mer om [reserverade instanser här.](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations) Den Azure Hybrid-förmån är tillgänglig i förhandsversionen [för reserverade VM-instanser (RU:er).](https://review.docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations#charges-covered-by-reservation) Det innebär att om du har köpt beräkningskostnader till ett rabatterat pris med RI kan du tillämpa AHB-förmånen på licenskostnaderna för RHEL och SUSE ovanpå det. Stegen för att tillämpa AHB-förmånen för en RI-instans förblir exakt desamma som för en vanlig virtuell dator.
![AHB för RU:er](./media/azure-hybrid-benefit/reserved-instances.png)

>[!NOTE]
>Om du redan har köpt reservationer för RHEL- eller SUSE PAYG-programvara på Azure Marketplace ska du vänta tills reservationslängden har slutförts innan du använder Azure Hybrid-förmån..


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
*F: Kan jag använda licenstypen `RHEL_BYOS` med en SLES-avbildning eller tvärtom?*

S: Nej, det kan du inte. Om du försöker ange en licenstyp som felaktigt matchar distributionen som körs på den virtuella datorn uppdateras inga faktureringsmetadata. Men om du av misstag anger fel licenstyp kommer en uppdatering av den virtuella datorn till rätt licenstyp fortfarande att aktivera förmånen.

*F: Jag har registrerat mig Red Hat Cloud Access men kan fortfarande inte aktivera förmånen på mina virtuella RHEL-datorer. Vad ska jag göra?*

S: Det kan ta lite tid för Red Hat Cloud Access prenumerationsregistreringen att spridas från Red Hat till Azure. Kontakta Microsoft-supporten om du fortfarande ser felet efter en arbetsdag.

*F: Jag har distribuerat en virtuell dator med hjälp av RHEL BYOS "gyllene bild". Kan jag konvertera faktureringen för dessa avbildningar från BYOS till PAYG?*

S: Nej, det kan du inte. Azure Hybrid-förmån har endast stöd för konvertering på avbildningar med betalning som du använder.

*F: Jag har laddat upp min egen RHEL-avbildning från en lokal plats (via Azure Migrate, Azure Site Recovery eller annat) till Azure. Kan jag konvertera faktureringen för dessa avbildningar från BYOS till PAYG?*

S: Nej, det kan du inte. Funktionen Azure Hybrid-förmån är för närvarande endast tillgänglig för RHEL- och SLES-avbildningar i Azure Marketplace. 

*F: Jag har laddat upp min egen RHEL-avbildning från en lokal plats (via Azure Migrate, Azure Site Recovery eller annat) till Azure. Behöver jag göra något för att dra nytta av Azure Hybrid-förmån?*

S: Nej, det har du inte. RHEL-avbildningar som du laddar upp betraktas redan som BYOS och du debiteras endast för Azure-infrastrukturkostnader. Du ansvarar för RHEL-prenumerationskostnaderna, precis som för dina lokala miljöer. 

*F: Kan jag använda Azure Hybrid-förmån på virtuella datorer som distribuerats från Azure Marketplace RHEL- och SLES SAP-avbildningar?*

S: Ja, det kan du. Du kan använda licenstypen för för virtuella RHEL-datorer och för konvertering av virtuella datorer som `RHEL_BYOS` `SLES_BYOS` distribuerats från Azure Marketplace RHEL- och SLES SAP-avbildningar.

*F: Kan jag använda Azure Hybrid-förmån på VM-skalningsuppsättningar för RHEL och SLES?*

S: Ja, Azure Hybrid-förmån på VM-skalningsuppsättningar för RHEL och SLES är i förhandsversion. Du kan [lära dig mer om den här förmånen och hur du använder den här](/azure/virtual-machine-scale-sets/azure-hybrid-benefit-linux). 

*F: Kan jag använda Azure Hybrid-förmån på reserverade instanser för RHEL och SLES?*

S: Ja, Azure Hybrid-förmån reserverad instans för RHEL och SLES är i förhandsversion. Du kan [lära dig mer om den här förmånen och hur du använder den här](#azure-hybrid-benefit-on-reserved-instances-is-in-preview).

*F: Kan jag använda Azure Hybrid-förmån på en virtuell dator som distribuerats för SQL Server på RHEL-avbildningar?*

S: Nej, det kan du inte. Det finns ingen plan för att stödja dessa virtuella datorer.

*F: Kan jag använda Azure Hybrid-förmån på min RHEL Virtual Data Center-prenumeration?*

S: Nej, det kan du inte. VDC stöds inte i Azure alls, inklusive AHB.  
 

## <a name="common-problems"></a>Vanliga problem
Det här avsnittet innehåller vanliga problem som du kan stöta på och steg för att åtgärda dem.

| Fel | Åtgärd |
| ----- | ---------- |
| "Åtgärden kunde inte slutföras eftersom våra poster visar att du inte har aktiverat Red Hat Cloud Access din Azure-prenumeration...." | Om du vill använda förmånen med virtuella RHEL-datorer måste du [först registrera dina Azure-prenumerationer med Red Hat Cloud Access](https://access.redhat.com/management/cloud).

## <a name="next-steps"></a>Nästa steg
* [Lär dig hur du skapar och uppdaterar virtuella datorer och lägger till licenstyper (RHEL_BYOS, SLES_BYOS) för Azure Hybrid-förmån med hjälp av Azure CLI](/cli/azure/vm)
