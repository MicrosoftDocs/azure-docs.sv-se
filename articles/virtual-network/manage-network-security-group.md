---
title: Skapa, ändra eller ta bort en Azure-nätverkssäkerhetsgrupp
titlesuffix: Azure Virtual Network
description: Lär dig var du hittar information om säkerhetsregler och hur du skapar, ändrar eller tar bort en nätverkssäkerhetsgrupp.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 82f23c1fea29e2a88dd2a67ec9c89c7bf05bfff7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783511"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Skapa, ändra eller ta bort en nätverkssäkerhetsgrupp

Med säkerhetsregler i nätverkssäkerhetsgrupper kan du filtrera den typ av nätverkstrafik som kan flöda in och ut från virtuella nätverksundernät och nätverksgränssnitt. Mer information om nätverkssäkerhetsgrupper finns i [Översikt över nätverkssäkerhetsgrupper.](./network-security-groups-overview.md) Slutför sedan [självstudien Filtrera nätverkstrafik](tutorial-filter-network-traffic.md) för att få lite erfarenhet av nätverkssäkerhetsgrupper.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du inte har ett konto kan du konfigurera ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Utför någon av dessa uppgifter innan du påbörjar resten av den här artikeln:

- **Portalanvändare:** Logga in på [Azure Portal](https://portal.azure.com) ditt Azure-konto.

- **PowerShell-användare:** Kör antingen [kommandona i Azure Cloud Shell](https://shell.azure.com/powershell)eller kör PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. På Azure Cloud Shell webbläsarflik hittar du  listrutan Välj miljö och väljer **sedan PowerShell** om det inte redan är markerat.

    Om du kör PowerShell lokalt använder du Azure PowerShell version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az.Network` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Kör `Connect-AzAccount` för att skapa en anslutning med Azure.

- **Azure CLI-användare (Command-Line Interface):** Kör antingen kommandona [i Azure Cloud Shell](https://shell.azure.com/bash)eller kör CLI från datorn. Använd Azure CLI version 2.0.28 eller senare om du kör Azure CLI lokalt. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Kör `az login` för att skapa en anslutning med Azure.

Det konto som du loggar in på eller [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ansluter till Azure [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) med måste tilldelas rollen Nätverksdeltagare eller till en anpassad roll som har tilldelats lämpliga åtgärder som anges i [Behörigheter](#permissions).

## <a name="work-with-network-security-groups"></a>Arbeta med nätverkssäkerhetsgrupper

Du kan skapa, [visa alla](#view-all-network-security-groups), visa [information om](#view-details-of-a-network-security-group), [ändra](#change-a-network-security-group)och ta [bort](#delete-a-network-security-group) en nätverkssäkerhetsgrupp. Du kan också [associera eller koppla bort en nätverkssäkerhetsgrupp](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) från ett nätverksgränssnitt eller undernät.

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Det finns en gräns för hur många nätverkssäkerhetsgrupper du kan skapa för varje Azure-plats och -prenumeration. Mer information finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. I menyn i [Azure-portalen](https://portal.azure.com) eller på sidan **Start** väljer du **Skapa en resurs**.

2. Välj **Nätverk** och sedan **Nätverkssäkerhetsgrupp.**

3. På sidan **Skapa nätverkssäkerhetsgrupp** går du **till fliken Grundläggande** inställningar och anger värden för följande inställningar:

    | Inställning | Action |
    | --- | --- |
    | **Prenumeration** | Välj din prenumeration. |
    | **Resursgrupp** | Välj en befintlig resursgrupp eller välj Skapa **ny för** att skapa en ny resursgrupp. |
    | **Namn** | Ange en unik textsträng i en resursgrupp. |
    | **Region** | Välj den plats som du vill använda. |

4. Välj **Granska + skapa**.

5. När du ser meddelandet **Valideringen har** godkänts väljer du **Skapa**.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Visa alla nätverkssäkerhetsgrupper

Gå till Azure Portal [för](https://portal.azure.com) att visa dina nätverkssäkerhetsgrupper. Sök efter och välj **Nätverkssäkerhetsgrupper.** Listan över nätverkssäkerhetsgrupper visas för din prenumeration.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg list](/cli/azure/network/nsg#az_network_nsg_list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Visa information om en nätverkssäkerhetsgrupp

1. Gå till Azure Portal [för](https://portal.azure.com) att visa dina nätverkssäkerhetsgrupper. Sök efter och välj **Nätverkssäkerhetsgrupper.**

2. Välj namnet på nätverkssäkerhetsgruppen.

På menyraden i nätverkssäkerhetsgruppen, under Inställningar, kan du visa inkommande säkerhetsregler,  **utgående säkerhetsregler,** nätverksgränssnitt och undernät som **nätverkssäkerhetsgruppen** är associerad med.

Under **Övervakning** kan du aktivera eller inaktivera **diagnostikinställningar.** Under **Support + felsökning** kan du visa Gällande **säkerhetsregler.** Mer information finns i [Diagnostisk loggning för en nätverkssäkerhetsgrupp och](virtual-network-nsg-manage-log.md) Diagnostisera problem med [filtreringen av VM-nätverkstrafik.](diagnose-network-traffic-filter-problem.md)

Mer information om vanliga Azure-inställningar finns i följande artiklar:

- [Aktivitetslogg](../azure-monitor/essentials/platform-logs-overview.md)
- [Åtkomstkontroll (IAM)](../role-based-access-control/overview.md)
- [Taggar](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Lås](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automationsskript](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg show](/cli/azure/network/nsg#az_network_nsg_show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Ändra en nätverkssäkerhetsgrupp

1. Gå till Azure Portal [för](https://portal.azure.com) att visa dina nätverkssäkerhetsgrupper. Sök efter och välj **Nätverkssäkerhetsgrupper.**

2. Välj namnet på den nätverkssäkerhetsgrupp som du vill ändra.

De vanligaste ändringarna är att lägga [](#delete-a-security-rule)till en säkerhetsregel, [](#create-a-security-rule)ta bort en regel och associera eller koppla bort en nätverkssäkerhetsgrupp till eller från ett undernät eller [nätverksgränssnitt](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg update](/cli/azure/network/nsg#az_network_nsg_update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Associera eller koppla bort en nätverkssäkerhetsgrupp till eller från ett undernät eller nätverksgränssnitt

Information om hur du associerar en nätverkssäkerhetsgrupp med eller kopplar bort en nätverkssäkerhetsgrupp från ett nätverksgränssnitt finns i Associera en nätverkssäkerhetsgrupp till eller koppla bort en nätverkssäkerhetsgrupp från ett [nätverksgränssnitt.](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) Information om hur du associerar en nätverkssäkerhetsgrupp med eller kopplar bort en nätverkssäkerhetsgrupp från ett undernät finns i [Ändra undernätsinställningar.](virtual-network-manage-subnet.md#change-subnet-settings)

### <a name="delete-a-network-security-group"></a>Ta bort en nätverkssäkerhetsgrupp

Om en nätverkssäkerhetsgrupp är associerad med undernät eller nätverksgränssnitt kan den inte tas bort. Ta bort en nätverkssäkerhetsgrupp från alla undernät och nätverksgränssnitt innan du försöker ta bort den.

1. Gå till Azure Portal [för](https://portal.azure.com) att visa dina nätverkssäkerhetsgrupper. Sök efter och välj **Nätverkssäkerhetsgrupper.**

2. Välj namnet på den nätverkssäkerhetsgrupp som du vill ta bort.

3. I verktygsfältet för nätverkssäkerhetsgruppen väljer du Ta **bort**. Välj sedan **Ja** i bekräftelsedialogrutan.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg delete](/cli/azure/network/nsg#az_network_nsg_delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Arbeta med säkerhetsregler

En nätverkssäkerhetsgrupp innehåller noll eller flera säkerhetsregler. Du kan skapa, [visa alla](#view-all-security-rules), visa [information om](#view-details-of-a-security-rule), [ändra](#change-a-security-rule)och ta [bort](#delete-a-security-rule) en säkerhetsregel.

### <a name="create-a-security-rule"></a>Skapa en säkerhetsregel

Det finns en gräns för hur många regler per nätverkssäkerhetsgrupp du kan skapa för varje Azure-plats och -prenumeration. Mer information finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. Gå till Azure Portal [för](https://portal.azure.com) att visa dina nätverkssäkerhetsgrupper. Sök efter och välj **Nätverkssäkerhetsgrupper.**

2. Välj namnet på den nätverkssäkerhetsgrupp som du vill lägga till en säkerhetsregel i.

3. På menyraden för nätverkssäkerhetsgruppen väljer du **Inkommande säkerhetsregler** eller **Utgående säkerhetsregler.**

    Flera befintliga regler visas, inklusive vissa som du kanske inte har lagt till. När du skapar en nätverkssäkerhetsgrupp skapas flera standardsäkerhetsregler i den. Mer information finns i [standardsäkerhetsreglerna.](./network-security-groups-overview.md#default-security-rules)  Du kan inte ta bort standardsäkerhetsregler, men du kan åsidosätta dem med regler som har högre prioritet.

4. <a name="security-rule-settings"></a>Välj **Lägg till**. Välj eller lägg till värden för följande inställningar och välj sedan **OK:**

    | Inställning | Värde | Information |
    | ------- | ----- | ------- |
    | **Källa** | Något av följande:<ul><li>**Alla**</li><li>**IP-adresser**</li><li>**Tjänsttagg** (inkommande säkerhetsregel) eller **VirtualNetwork** (utgående säkerhetsregel)</li><li>**&nbsp; &nbsp; Programsäkerhetsgrupp**</li></ul> | <p>Om du väljer **IP-adresser** måste du också ange Käll-IP-adresser/CIDR-intervall. </p><p>Om du väljer **Tjänsttagg** kan du även välja **källtjänsttaggen**.</p><p>Om du väljer **Programsäkerhetsgrupp** måste du också välja en befintlig programsäkerhetsgrupp. Om du väljer **Programsäkerhetsgrupp** för både **Källa** och Mål **måste** nätverksgränssnitten i båda programsäkerhetsgrupperna finnas i samma virtuella nätverk.</p> |
    | **Käll-IP-adresser/CIDR-intervall** | En kommaavgränsad lista över IP-adresser och CIDR-intervall (Classless Interdomain Routing) | <p>Den här inställningen visas om du ändrar **Källa till** **IP-adresser**. Du måste ange ett enskilt värde eller en kommaavgränsad lista med flera värden. Ett exempel på flera värden är `10.0.0.0/16, 192.188.1.1` . Det finns gränser för antalet värden som du kan ange. Mer information finns i [Azure-gränser.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)</p><p>Om den IP-adress som du anger är tilldelad till en virtuell Azure-dator anger du dess privata IP-adress, inte dess offentliga IP-adress. Azure bearbetar säkerhetsregler när den offentliga IP-adressen översätts till en privat IP-adress för inkommande säkerhetsregler, men innan den översätter en privat IP-adress till en offentlig IP-adress för utgående regler. Mer information om offentliga och privata IP-adresser i Azure finns i [IP-adresstyper.](./public-ip-addresses.md)</p> |
    | **Källtjänsttagg** | En tjänsttagg från listrutan | Den här valfria inställningen visas om du **ställer in Källa** på **Tjänsttagg** för en inkommande säkerhetsregel. En tjänsttagg är en fördefinierad identifierare för en kategori med IP-adresser. Mer information om tillgängliga tjänsttaggar och vad varje tagg representerar finns i [Tjänsttaggar.](./network-security-groups-overview.md#service-tags) |
    | **Källprogramsäkerhetsgrupp** | En befintlig programsäkerhetsgrupp | Den här inställningen visas om du ställer **in Källa** **på Programsäkerhetsgrupp**. Välj en programsäkerhetsgrupp som finns i samma region som nätverksgränssnittet. Lär dig hur [du skapar en programsäkerhetsgrupp.](#create-an-application-security-group) |
    | **Källportintervall** | Något av följande:<ul><li>En enda port, till exempel `80`</li><li>Ett portintervall, till exempel `1024-65535`</li><li>En kommaavgränsad lista över enskilda portar och/eller portintervall, till exempel `80, 1024-65535`</li><li>En asterisk ( `*` ) för att tillåta trafik på valfri port</li></ul> | Den här inställningen anger vilka portar som regeln tillåter eller nekar trafik på. Det finns gränser för antalet portar som du kan ange. Mer information finns i [Azure-gränser.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) |
    | **Mål** | Något av följande:<ul><li>**Alla**</li><li>**IP-adresser**</li><li>**Tjänsttagg** (utgående säkerhetsregel) eller **VirtualNetwork** (inkommande säkerhetsregel)</li><li>**&nbsp; &nbsp; Programsäkerhetsgrupp**</li></ul> | <p>Om du väljer **IP-adresser** anger du även **Mål-IP-adresser/CIDR-intervall.**</p><p>Om du väljer **VirtualNetwork** tillåts trafik till alla IP-adresser inom det virtuella nätverkets adressutrymme. **VirtualNetwork** är en tjänsttagg.</p><p>Om du väljer **Programsäkerhetsgrupp** måste du sedan välja en befintlig programsäkerhetsgrupp. Lär dig hur [du skapar en programsäkerhetsgrupp.](#create-an-application-security-group)</p> |
    | **Mål-IP-adresser/CIDR-intervall** | En kommaavgränsad lista över IP-adresser och CIDR-intervall | <p>Den här inställningen visas om du ändrar **Mål till** **IP-adresser.** På samma **sätt som** **käll- och käll-IP-adresser/CIDR-intervall** kan du ange en eller flera adresser eller intervall. Det finns gränser för det antal som du kan ange. Mer information finns i [Azure-gränser.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)</p><p>Om den IP-adress som du anger är tilldelad till en virtuell Azure-dator måste du se till att du anger dess privata IP-adress, inte dess offentliga IP-adress. Azure bearbetar säkerhetsregler när den offentliga IP-adressen har översätts till en privat IP-adress för inkommande säkerhetsregler, men innan Azure översätter en privat IP-adress till en offentlig IP-adress för utgående regler. Mer information om offentliga och privata IP-adresser i Azure finns i [IP-adresstyper.](./public-ip-addresses.md)</p> |
    | **Måltjänsttagg** | En tjänsttagg från listrutan | Den här valfria inställningen visas om du ändrar **Mål** till **Tjänsttagg** för en utgående säkerhetsregel. En tjänsttagg är en fördefinierad identifierare för en kategori med IP-adresser. Mer information om tillgängliga tjänsttaggar och vad varje tagg representerar finns i [Tjänsttaggar.](./network-security-groups-overview.md#service-tags) |
    | **Målprogramsäkerhetsgrupp** | En befintlig programsäkerhetsgrupp | Den här inställningen visas om du anger **Mål** till **Programsäkerhetsgrupp**. Välj en programsäkerhetsgrupp som finns i samma region som nätverksgränssnittet. Lär dig hur [du skapar en programsäkerhetsgrupp.](#create-an-application-security-group) |
    | **Målportintervall** | Något av följande:<ul><li>En enda port, till exempel `80`</li><li>Ett portintervall, till exempel `1024-65535`</li><li>En kommaavgränsad lista över enskilda portar och/eller portintervall, till exempel `80, 1024-65535`</li><li>En asterisk ( `*` ) för att tillåta trafik på valfri port</li></ul> | Precis som **med Källportintervall** kan du ange en eller flera portar och intervall. Det finns gränser för det antal som du kan ange. Mer information finns i [Azure-gränser.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) |
    | **Protokoll** | **Alla**, **TCP,** **UDP** eller **ICMP** | Du kan begränsa regeln till Transmission Control Protocol (TCP), UDP (User Datagram Protocol) eller Internet Control Message Protocol (ICMP). Standardvärdet är att regeln gäller för alla protokoll. |
    | **Åtgärd** | **Tillåt** eller **neka** | Den här inställningen anger om den här regeln tillåter eller nekar åtkomst för den angivna käll- och målkonfigurationen. |
    | **Priority** | Ett värde mellan 100 och 4096 som är unikt för alla säkerhetsregler i nätverkssäkerhetsgruppen | Azure bearbetar säkerhetsregler i prioritetsordning. Ju lägre nummer, desto högre prioritet. Vi rekommenderar att du lämnar ett mellanrum mellan prioritetsnummer när du skapar regler, till exempel 100, 200 och 300. Om du lämnar luckor blir det enklare att lägga till regler i framtiden, så att du kan ge dem högre eller lägre prioritet än befintliga regler. |
    | **Namn** | Ett unikt namn för regeln i nätverkssäkerhetsgruppen | Namnet kan vara upp till 80 tecken. Det måste börja med en bokstav eller en siffra och det måste sluta med en bokstav, en siffra eller ett understreck. Namnet får bara innehålla bokstäver, siffror, understreck, punkter eller bindestreck. |
    | **Beskrivning** | En textbeskrivning | Du kan också ange en textbeskrivning för säkerhetsregeln. Beskrivningen får inte vara längre än 140 tecken. |

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Visa alla säkerhetsregler

En nätverkssäkerhetsgrupp innehåller noll eller fler regler. Mer information om den information som visas när du visar regler finns i [Översikt över nätverkssäkerhetsgrupp.](./network-security-groups-overview.md)

1. Gå till [Azure Portal](https://portal.azure.com) för att visa reglerna för en nätverkssäkerhetsgrupp. Sök efter och välj **Nätverkssäkerhetsgrupper.**

2. Välj namnet på den nätverkssäkerhetsgrupp som du vill visa reglerna för.

3. På menyraden för nätverkssäkerhetsgruppen väljer du **Inkommande säkerhetsregler** eller **Utgående säkerhetsregler.**

Listan innehåller alla regler som du har skapat och nätverkssäkerhetsgruppens [standardsäkerhetsregler.](./network-security-groups-overview.md#default-security-rules)

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az_network_nsg_rule_list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Visa information om en säkerhetsregel

1. Gå till [Azure Portal](https://portal.azure.com) för att visa reglerna för en nätverkssäkerhetsgrupp. Sök efter och välj **Nätverkssäkerhetsgrupper.**

2. Välj namnet på den nätverkssäkerhetsgrupp som du vill visa information om en regel för.

3. I nätverkssäkerhetsgruppens menyrad väljer du **Inkommande säkerhetsregler eller** **Utgående säkerhetsregler.**

4. Välj den regel som du vill visa information om. En förklaring av alla inställningar finns i [Säkerhetsregelinställningar.](#security-rule-settings)

    > [!NOTE]
    > Den här proceduren gäller endast för en anpassad säkerhetsregel. Det fungerar inte om du väljer en standardsäkerhetsregel.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg rule show](/cli/azure/network/nsg/rule#az_network_nsg_rule_show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Ändra en säkerhetsregel

1. Slutför stegen i [Visa information om en säkerhetsregel](#view-details-of-a-security-rule).

2. Ändra inställningarna efter behov och välj sedan **Spara.** En förklaring av alla inställningar finns i [Säkerhetsregelinställningar.](#security-rule-settings)

    > [!NOTE]
    > Den här proceduren gäller endast för en anpassad säkerhetsregel. Du får inte ändra en standardsäkerhetsregel.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Ta bort en säkerhetsregel

1. Slutför stegen i [Visa information om en säkerhetsregel](#view-details-of-a-security-rule).

2. Välj **Ta bort** och välj sedan **Ja**.

    > [!NOTE]
    > Den här proceduren gäller endast för en anpassad säkerhetsregel. Du kan inte ta bort en standardsäkerhetsregel.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg rule delete](/cli/azure/network/nsg/rule#az_network_nsg_rule_delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Arbeta med programsäkerhetsgrupper

En programsäkerhetsgrupp innehåller noll eller flera nätverksgränssnitt. Mer information finns i [programsäkerhetsgrupper.](./network-security-groups-overview.md#application-security-groups) Alla nätverksgränssnitt i en programsäkerhetsgrupp måste finnas i samma virtuella nätverk. Information om hur du lägger till ett nätverksgränssnitt i en programsäkerhetsgrupp finns i [Lägga till ett nätverksgränssnitt i en programsäkerhetsgrupp.](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)

### <a name="create-an-application-security-group"></a>Skapa en programsäkerhetsgrupp

1. I menyn i [Azure-portalen](https://portal.azure.com) eller på sidan **Start** väljer du **Skapa en resurs**.

2. I sökrutan anger du *Programsäkerhetsgrupp*.

3. På sidan **Programsäkerhetsgrupp** väljer du **Skapa**.

4. På sidan **Skapa en programsäkerhetsgrupp** går du till **fliken Grundläggande** inställningar och anger värden för följande inställningar:

    | Inställning | Action |
    | --- | --- |
    | **Prenumeration** | Välj din prenumeration. |
    | **Resursgrupp** | Välj en befintlig resursgrupp eller välj Skapa **ny för** att skapa en ny resursgrupp. |
    | **Namn** | Ange en unik textsträng i en resursgrupp. |
    | **Region** | Välj den plats som du vill använda. |

5. Välj **Granska + skapa**.

6. På fliken **Granska + skapa** väljer  du Skapa när du ser meddelandet Valideringen **har godkänts.**

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network asg create](/cli/azure/network/asg#az_network_asg_create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Visa alla programsäkerhetsgrupper

Gå till Azure Portal [för](https://portal.azure.com) att visa dina programsäkerhetsgrupper. Sök efter och välj **Programsäkerhetsgrupper.** I Azure Portal visas en lista över dina programsäkerhetsgrupper.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network asg list](/cli/azure/network/asg#az_network_asg_list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Visa information om en specifik programsäkerhetsgrupp

1. Gå till Azure Portal [för](https://portal.azure.com) att visa en programsäkerhetsgrupp. Sök efter och välj **Programsäkerhetsgrupper.**

2. Välj namnet på den programsäkerhetsgrupp som du vill visa information om.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network asg show](/cli/azure/network/asg#az_network_asg_show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Ändra en programsäkerhetsgrupp

1. Gå till Azure Portal [för](https://portal.azure.com) att visa en programsäkerhetsgrupp. Sök efter och välj **Programsäkerhetsgrupper**.

2. Välj namnet på den programsäkerhetsgrupp som du vill ändra.

3. Välj **Ändra** bredvid den inställning som du vill ändra. Du kan till exempel lägga till eller ta **bort taggar,** eller så kan du ändra **resursgruppen** eller **prenumerationen**.

    > [!NOTE]
    > Du kan inte ändra platsen.

    På menyraden kan du också välja **Åtkomstkontroll (IAM).** På sidan **Åtkomstkontroll (IAM)** kan du tilldela eller ta bort behörigheter till programsäkerhetsgruppen.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network asg update](/cli/azure/network/asg#az_network_asg_update) |
| PowerShell | Ingen PowerShell-cmdlet |

### <a name="delete-an-application-security-group"></a>Ta bort en programsäkerhetsgrupp

Du kan inte ta bort en programsäkerhetsgrupp om den innehåller några nätverksgränssnitt. Om du vill ta bort alla nätverksgränssnitt från programsäkerhetsgruppen ändrar du antingen inställningarna för nätverksgränssnittet eller tar bort nätverksgränssnitten. Mer information finns i Lägga [till i eller ta bort från programsäkerhetsgrupper eller](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) Ta bort ett [nätverksgränssnitt.](virtual-network-network-interface.md#delete-a-network-interface)

1. Gå till Azure Portal [för](https://portal.azure.com) att hantera dina programsäkerhetsgrupper. Sök efter och välj **Programsäkerhetsgrupper**.

2. Välj namnet på den programsäkerhetsgrupp som du vill ta bort.

3. Välj **Ta** bort och välj sedan **Ja för** att ta bort programsäkerhetsgruppen.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network asg delete](/cli/azure/network/asg#az_network_asg_delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter i nätverkssäkerhetsgrupper, säkerhetsregler och programsäkerhetsgrupper måste ditt [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) konto tilldelas till rollen Nätverksdeltagare eller till en anpassad roll som har tilldelats lämpliga behörigheter enligt listan i följande tabeller: [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)

### <a name="network-security-group"></a>Nätverkssäkerhetsgrupp

| Action                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Hämta nätverkssäkerhetsgrupp                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Skapa eller uppdatera nätverkssäkerhetsgrupp                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Ta bort nätverkssäkerhetsgrupp                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Associera en nätverkssäkerhetsgrupp med ett undernät eller nätverksgränssnitt 


>[!NOTE]
> Om du `write` vill utföra åtgärder på en nätverkssäkerhetsgrupp måste prenumerationskontot ha minst behörighet för `read` resursgruppen tillsammans med `Microsoft.Network/networkSecurityGroups/write` behörighet.


### <a name="network-security-group-rule"></a>Regel för nätverkssäkerhetsgrupp

| Action                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/securityRules/read            |   Hämta regel                                                            |
| Microsoft.Network/networkSecurityGroups/securityRules/write           |   Skapa eller uppdatera regel                                               |
| Microsoft.Network/networkSecurityGroups/securityRules/delete          |   Ta bort regel                                                         |

### <a name="application-security-group"></a>Programsäkerhetsgrupp

| Action                                                                     | Name                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Ansluta en IP-konfiguration till en programsäkerhetsgrupp|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Ansluta en säkerhetsregel till en programsäkerhetsgrupp    |
| Microsoft.Network/applicationSecurityGroups/read                           | Hämta en programsäkerhetsgrupp                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Skapa eller uppdatera en programsäkerhetsgrupp           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Ta bort en programsäkerhetsgrupp                     |

## <a name="next-steps"></a>Nästa steg

- Skapa en nätverks- eller programsäkerhetsgrupp med [hjälp av PowerShell-](powershell-samples.md) eller [Azure CLI-exempelskript](cli-samples.md) eller Azure [Resource Manager mallar](template-samples.md)
- Skapa och tilldela [Azure Policy definitioner](./policy-reference.md) för virtuella nätverk
