---
title: Azure Hybrid-förmån och virtuella Linux-datorer
description: Med Azure Hybrid-förmån kan du spara pengar på dina virtuella Linux-datorer som körs på Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 5b565a3e653d05ffcecfbc5718c74d07d8f45373
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109172"
---
# <a name="azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Azure Hybrid-förmån – hur det gäller för Virtuella Linux-datorer

## <a name="overview"></a>Översikt

Azure Hybrid-förmån är en licens förmån som hjälper dig att avsevärt minska kostnaderna för att köra dina Red Hat Enterprise Linux (RHEL) och SUSE Linux Enterprise Server (SLES) virtuella datorer i molnet. Med den här förmånen betalar du bara för infrastruktur kostnaderna för din virtuella dator eftersom program varu avgiften täcks av din RHEL-eller SLES-prenumeration. Förmånen gäller för alla PAYG-avbildningar (RHEL-och SLES Marketplace).

> [!IMPORTANT]
> Azure Hybrid-förmån för virtuella Linux-datorer är tillgänglig för allmänheten nu


## <a name="benefit-description"></a>Förmåns Beskrivning

Med hjälp av Azure Hybrid-förmån kan du lättare migrera dina lokala RHEL-och SLES-servrar till Azure genom att konvertera befintliga RHEL-och SLES PAYG-datorer på Azure för att få en BYOS-fakturering. Vanligt vis debiteras virtuella datorer som distribueras från PAYG-avbildningar på Azure både en infrastruktur avgift och en program varu avgift. Med Azure Hybrid-förmån kan virtuella datorer konverteras till en BYOS-fakturerings modell utan att omdistribueras, vilket undviker eventuell stillestånds risk.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Azure Hybrid-förmån kostnads visualisering på virtuella Linux-datorer.":::

När du aktiverar fördelen med en RHEL-eller SLES-VM debiteras du inte längre för den ytterligare program varu avgiften som normalt uppstår på en PAYG VM. I stället påbörjas den virtuella datorn med en BYOS avgift som bara omfattar beräknings maskin varu avgiften och ingen program varu avgift.

Om du vill kan du också konvertera en virtuell dator som har förmånen aktiverat på den till en PAYG fakturerings modell.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Omfattning för Azure Hybrid-förmån berättigande för virtuella Linux-datorer

Azure Hybrid-förmån är tillgängligt för alla RHEL-och SLES Marketplace-PAYG-avbildningar. Förmånen är ännu inte tillgänglig för RHEL-eller SLES Marketplace-BYOS-bilder eller anpassade avbildningar.

Reserverade instanser, dedikerade värdar och SQL hybrid-förmåner är inte berättigade till Azure Hybrid-förmån om du redan använder fördelarna med virtuella Linux-datorer.

## <a name="how-to-get-started"></a>Så här kommer du igång

### <a name="red-hat-customers"></a>Red Hat-kunder

Azure Hybrid-förmån för RHEL är tillgängligt för kunder som har aktiva/oanvända RHEL-prenumerationer som är tillgängliga för användning i Azure och som har aktiverat en eller flera av dessa prenumerationer för användning i Azure med [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) -programmet. 

1.  Aktivera en eller flera av dina kvalificerade RHEL-prenumerationer för användning i Azure med hjälp av [Red Hat Cloud accesss kund gränssnitt](https://access.redhat.com/management/cloud).
1.  De Azure-prenumerationer som du tillhandahöll under den Red Hat Cloud Access-aktiveringen kommer sedan att kunna använda Azure Hybrid-förmån funktionen.
1.  Tillämpa Azure Hybrid-förmån på någon av dina befintliga RHEL PAYG-VM: ar samt eventuella nya RHEL-VM: er som du distribuerar från Azure Marketplace PAYG-avbildningar.
1.  Följ rekommenderade [Nästa steg](https://access.redhat.com/articles/5419341) för att konfigurera uppdaterings källor för dina virtuella RHEL-datorer och för rikt linjer för RHEL-prenumerationer.


### <a name="suse-customers"></a>SUSE kunder

1.    Registrera dig för det offentliga moln programmet för SUSE
1.    Tillämpa förmånen för dina befintliga virtuella datorer via Azure CLI
1.    Registrera dina virtuella datorer som tar del av förmånen med en separat källa till uppdateringar


### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Aktivera och inaktivera förmånen i Azure CLI

Du kan använda kommandot "AZ VM Update" för att uppdatera befintliga virtuella datorer. För virtuella RHEL-datorer kör du kommandot med parametern--licens typ för RHEL_BYOS. För virtuella SLES-datorer kör du kommandot med parametern--licens typ för SLES_BYOS.

#### <a name="cli-example-to-enable-the-benefit"></a>CLI-exempel för att aktivera förmånen:
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>CLI-exempel för att inaktivera förmånen:
Om du vill inaktivera förmånen använder du värdet none för licens typ
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>CLI-exempel för att aktivera förmånen för ett stort antal virtuella datorer
Om du vill aktivera förmånen för ett stort antal virtuella datorer kan du använda- `--ids` parametern i Azure CLI.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

I följande exempel visas två metoder för att hämta en lista över resurs-ID: n – en på resurs grupps nivå, en på prenumerations nivå.
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="check-ahb-status-of-a-vm"></a>Kontrol lera AHB status för en virtuell dator
Du kan visa status för AHB för en virtuell dator på två sätt: använda Azure CLI eller använda Azure-Instance Metadata Service (Azure IMDS).


### <a name="azure-cli"></a>Azure CLI

`az vm get-instance-view`Kommandot kan användas för detta ändamål. Leta efter ett licenseType-fält i svaret. Om fältet licenseType finns och värdet är "RHEL_BYOS" eller "SLES_BYOS" har den virtuella datorn förmånen aktive rad.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

Från den virtuella datorn kan du fråga IMDS-bestyrkade metadata för att fastställa den virtuella datorns licenseType. LicenseType-värdet RHEL_BYOS eller SLES_BYOS anger att den virtuella datorn har förmånen aktive rad. Läs mer om attestering av metadata [här](./instance-metadata-service.md#attested-data)

## <a name="compliance"></a>Efterlevnad

### <a name="red-hat"></a>Red Hat

Kunder som använder Azure Hybrid-förmån för RHEL accepterar de [juridiska villkoren](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) och den [sekretess policy](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) som är ASSOCIERAD med Azure Marketplace RHEL-erbjudanden.

Kunder som använder Azure Hybrid-förmån för RHEL har tre alternativ för att tillhandahålla program uppdateringar och korrigeringsfiler för de virtuella datorerna:

1.  [Red Hat, uppdaterings infrastruktur (RHUI)](../workloads/redhat/redhat-rhui.md) (standard alternativet)
1.  Red Hat satellit-Server
1.  Red Hat-prenumerations hanterare

Kunder som väljer alternativet RHUI kan fortsätta att använda RHUI som primär uppdaterings källa för sina AHB RHEL-VM: ar utan att koppla RHEL-prenumerationer till dessa virtuella datorer.  Kunder som väljer alternativet RHUI ansvarar för att säkerställa kompatibilitet med RHEL-prenumeration.

Kunder som väljer antingen Red Hat satellit-Server eller Red Hat-prenumerations hanterare bör ta bort RHUI-konfigurationen och sedan ansluta en moln åtkomst aktive rad RHEL-prenumeration till sina AHB RHEL-VM: ar.  

Mer information om kompatibilitet med Red Hat-prenumeration, program uppdateringar och källor för virtuella AHB RHEL-datorer finns [här](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

För att kunna använda Azure Hybrid-förmån för dina virtuella SLES-datorer måste du först vara registrerad med det offentliga SUSE moln programmet. Läs mer om programmet här. När du har köpt SUSE-prenumerationer måste du registrera dina virtuella datorer med dessa prenumerationer på din egen källa med uppdateringar med hjälp av antingen SUSE kund Center, prenumerations hanterings verktygets Server eller SUSE Manager.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
*F: kan jag använda licens typen "RHEL_BYOS" med en SLES-avbildning eller vice versa?*

A: Nej, du kan inte. Försök att ange en licens typ som felaktigt matchar den distribution som körs på den virtuella datorn kommer inte att uppdatera några fakturerings-metadata. Men om du av misstag anger fel licens typ kan du fortfarande aktivera förmånen om du uppdaterar den virtuella datorn igen till rätt licens typ.

*F: Jag har registrerat dig med Red Hat Cloud Access men det går fortfarande inte att aktivera förmånen för mina virtuella RHEL-datorer. Vad gör jag?*

A: det kan ta lite tid för prenumerations registreringen av Red Hat Cloud Access att sprida sig från Red Hat till Azure. Kontakta Microsoft-supporten om du fortfarande ser felet efter en arbets dag.

*F: Jag har distribuerat en virtuell dator med hjälp av en RHEL BYOS "gyllene bild". Kan jag konvertera faktureringen för dessa bilder från BYOS till PAYG?*

A: Nej, du kan inte. Azure Hybrid-förmån stöder endast konvertering för avbildningar enligt principen betala per användning.

*F: Jag har distribuerat en virtuell dator med hjälp av en RHEL BYOS "gyllene bild". Kan jag konvertera faktureringen för dessa bilder från BYOS till PAYG?*

A: Nej, du kan inte. Azure Hybrid-förmån stöder endast konvertering för avbildningar enligt principen betala per användning.

*F: Jag har laddat upp en egen RHEL-avbildning från lokal (via Azure Migrate, ASR eller på annat sätt) till Azure. Kan jag konvertera faktureringen för dessa bilder från BYOS till PAYG?*

A: Nej, du kan inte. Azure Hybrid-förmån funktionen är bara tillgänglig för RHEL-och SLES Marketplace-avbildningar idag. 

*F: Jag har laddat upp en egen RHEL-avbildning från lokal (via Azure Migrate, ASR eller på annat sätt) till Azure. Behöver jag göra något för att dra nytta av Azure Hybrid-förmån?*

A: Nej. RHEL-avbildningar som du överför betraktas redan som BYOS och du debiteras bara för Azures infrastruktur kostnader. Du ansvarar för RHEL prenumerations kostnader precis som du gör för dina lokala miljöer. 

*F: kan jag använda Azure Hybrid-förmån på virtuella datorer som har distribuerats från Marketplace RHEL och SLES SAP-avbildningar?*

A: Ja, du kan. Du kan använda licens typen RHEL_BYOS för virtuella datorer i RHEL och SLES_BYOS för konverteringar av virtuella datorer som distribueras från Marketplace RHEL och SLES SAP-avbildningar.

*F: kan jag använda Azure Hybrid-förmån på skalnings uppsättningen för virtuella datorer (VMSS) för RHEL och SLES?*

A: Nej, du kan inte. VMSS är inte inom omfånget för Azure Hybrid-förmån idag för RHEL och SLES för närvarande.

*F: kan jag använda Azure Hybrid-förmån på reserverade instanser (RIs) för RHEL och SLES?*

A: Nej, du kan inte. Det finns för närvarande inte i omfånget Azure Hybrid-förmån för RHEL och SLES.

*F: kan jag använda Azure Hybrid-förmån på en virtuell dator som har distribuerats för SQL Server på RHEL-avbildningar?*

A: Nej, du kan inte. Det finns ingen plan för att stödja dessa.
 

## <a name="common-issues"></a>Vanliga problem
Det här avsnittet innehåller en lista över vanliga problem som kan påträffas och åtgärder för att minska.

| Fel | Åtgärd |
| ----- | ---------- |
| "Åtgärden kunde inte slutföras eftersom våra poster visar att du inte har aktiverat Red Hat Cloud Access på din Azure-prenumeration..." | För att kunna använda förmånen med virtuella datorer i RHEL måste du först registrera din Azure-prenumeration (er) med Red Hat Cloud Access. Besök den här länken om du vill veta mer om hur du registrerar Azure-prenumerationer för Red Hat Cloud Access

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du skapar och uppdaterar virtuella datorer och lägger till licens typer (RHEL_BYOS SLES_BYOS) för Azure Hybrid-förmån med hjälp av [Azure CLI här.](/cli/azure/vm?preserve-view=true&view=azure-cli-latest)