---
title: Skapa, ändra eller ta bort en Azure-nätverks säkerhets grupp
titlesuffix: Azure Virtual Network
description: Lär dig var du hittar information om säkerhets regler och hur du skapar, ändrar eller tar bort en nätverks säkerhets grupp.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 5de909d0d57ae212fa562eb31551e2271d307d47
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694265"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Skapa, ändra eller ta bort en nätverkssäkerhetsgrupp

Säkerhets regler i nätverks säkerhets grupper gör att du kan filtrera den typ av nätverks trafik som kan flöda in i och ut ur virtuella nätverks under nät och nätverks gränssnitt. Mer information om nätverks säkerhets grupper finns i [Översikt över nätverks säkerhets](./network-security-groups-overview.md)grupper. Nu ska du gå igenom självstudien [filtrera nätverks trafik](tutorial-filter-network-traffic.md) för att få lite erfarenhet av nätverks säkerhets grupper.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du inte har något konfigurerar du ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Slutför någon av dessa uppgifter innan du påbörjar resten av den här artikeln:

- **Portal användare**: Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-konto.

- **PowerShell-användare**: kör antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller kör PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. På fliken Azure Cloud Shell webbläsare letar du upp List rutan **Välj miljö** och väljer sedan **PowerShell** om den inte redan är markerad.

    Om du kör PowerShell lokalt använder du Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az.Network` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Kör `Connect-AzAccount` för att skapa en anslutning med Azure.

- **Kommando rads gränssnitt för Azure (CLI)**: kör antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller kör CLI från datorn. Använd Azure CLI version 2.0.28 eller senare om du kör Azure CLI lokalt. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Kör `az login` för att skapa en anslutning med Azure.

Det konto som du loggar in på eller ansluta till Azure med måste tilldelas [rollen nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder som anges i [behörigheter](#permissions).

## <a name="work-with-network-security-groups"></a>Arbeta med nätverkssäkerhetsgrupper

Du kan skapa, [Visa alla](#view-all-network-security-groups), [Visa information om](#view-details-of-a-network-security-group), [ändra](#change-a-network-security-group)och [ta bort](#delete-a-network-security-group) en nätverks säkerhets grupp. Du kan också [Koppla eller koppla bort](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) en nätverks säkerhets grupp från ett nätverks gränssnitt eller ett undernät.

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Det finns en gräns för hur många nätverks säkerhets grupper du kan skapa för varje Azure-plats och prenumeration. Läs mer i Azure- [prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. I menyn i [Azure-portalen](https://portal.azure.com) eller på sidan **Start** väljer du **Skapa en resurs**.

2. Välj **nätverk** och välj sedan **nätverks säkerhets grupp**.

3. På sidan **skapa nätverks säkerhets grupp** går du till fliken **grundläggande** och anger värden för följande inställningar:

    | Inställning | Action |
    | --- | --- |
    | **Prenumeration** | Välj din prenumeration. |
    | **Resursgrupp** | Välj en befintlig resurs grupp eller skapa en ny resurs grupp genom att välja **Skapa ny** . |
    | **Namn** | Ange en unik text sträng inom en resurs grupp. |
    | **Region** | Välj den plats som du vill använda. |

4. Välj **Granska + skapa**.

5. När du ser meddelandet **valideringen har skickats** väljer du **skapa**.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Visa alla nätverks säkerhets grupper

Gå till [Azure Portal](https://portal.azure.com) för att se dina nätverks säkerhets grupper. Sök efter och välj **nätverks säkerhets grupper**. Listan över nätverks säkerhets grupper visas för din prenumeration.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ Network NSG List](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Visa information om en nätverks säkerhets grupp

1. Gå till [Azure Portal](https://portal.azure.com) för att se dina nätverks säkerhets grupper. Sök efter och välj **nätverks säkerhets grupper**.

2. Välj namnet på din nätverks säkerhets grupp.

I meny fältet i nätverks säkerhets gruppen, under **Inställningar**, kan du Visa de **inkommande säkerhets reglerna**, **utgående säkerhets regler**, **nätverks gränssnitt** och **undernät** som nätverks säkerhets gruppen är kopplad till.

Under **övervakning** kan du aktivera eller inaktivera **diagnostikinställningar**. Under **support + fel sökning** kan du Visa **effektiva säkerhets regler**. Läs mer i [diagnostisk loggning för en nätverks säkerhets grupp](virtual-network-nsg-manage-log.md) och [diagnostisera ett problem med trafik filter för virtuella datorer](diagnose-network-traffic-filter-problem.md).

Mer information om de vanliga Azure-inställningarna i listan finns i följande artiklar:

- [Aktivitetslogg](../azure-monitor/essentials/platform-logs-overview.md)
- [Åtkomstkontroll (IAM)](../role-based-access-control/overview.md)
- [Taggar](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Lås](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automationsskript](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ Network NSG show](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Ändra en nätverks säkerhets grupp

1. Gå till [Azure Portal](https://portal.azure.com) för att se dina nätverks säkerhets grupper. Sök efter och välj **nätverks säkerhets grupper**.

2. Välj namnet på den nätverks säkerhets grupp som du vill ändra.

De vanligaste ändringarna är att [lägga till en säkerhets regel](#create-a-security-rule), [ta bort en regel](#delete-a-security-rule)och [Koppla eller koppla bort en nätverks säkerhets grupp till eller från ett undernät eller nätverks gränssnitt](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ Network NSG Update](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Koppla eller koppla bort en nätverks säkerhets grupp till eller från ett undernät eller nätverks gränssnitt

Om du vill koppla en nätverks säkerhets grupp till eller koppla bort en nätverks säkerhets grupp från ett nätverks gränssnitt, se [associera en nätverks säkerhets grupp till eller koppla bort en nätverks säkerhets grupp från ett nätverks gränssnitt](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Om du vill koppla en nätverks säkerhets grupp till eller koppla bort en nätverks säkerhets grupp från ett undernät, se [ändra under näts inställningar](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Ta bort en nätverks säkerhets grupp

Om en nätverks säkerhets grupp är kopplad till undernät eller nätverks gränssnitt kan du inte ta bort den. Koppla bort en nätverks säkerhets grupp från alla undernät och nätverks gränssnitt innan du försöker ta bort den.

1. Gå till [Azure Portal](https://portal.azure.com) för att se dina nätverks säkerhets grupper. Sök efter och välj **nätverks säkerhets grupper**.

2. Välj namnet på den nätverks säkerhets grupp som du vill ta bort.

3. I verktygsfältet nätverks säkerhets gruppens verktygsfält väljer du **ta bort**. Välj **Ja** i bekräftelse dialog rutan.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ Network NSG Delete](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Arbeta med säkerhets regler

En nätverks säkerhets grupp innehåller noll eller flera säkerhets regler. Du kan skapa, [Visa alla](#view-all-security-rules), [Visa information om](#view-details-of-a-security-rule), [ändra](#change-a-security-rule)och [ta bort](#delete-a-security-rule) en säkerhets regel.

### <a name="create-a-security-rule"></a>Skapa en säkerhets regel

Det finns en gräns för hur många regler per nätverks säkerhets grupp som du kan skapa för varje Azure-plats och prenumeration. Läs mer i Azure- [prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Gå till [Azure Portal](https://portal.azure.com) för att se dina nätverks säkerhets grupper. Sök efter och välj **nätverks säkerhets grupper**.

2. Välj namnet på den nätverks säkerhets grupp som du vill lägga till en säkerhets regel i.

3. I meny raden för nätverks säkerhets gruppen väljer du **inkommande säkerhets regler** eller **utgående säkerhets regler**.

    Flera befintliga regler visas, inklusive några som du kanske inte har lagt till. När du skapar en nätverks säkerhets grupp skapas flera standard säkerhets regler i den. Mer information finns i [Standard säkerhets regler](./network-security-groups-overview.md#default-security-rules).  Du kan inte ta bort standard säkerhets regler, men du kan åsidosätta dem med regler som har högre prioritet.

4. <a name="security-rule-settings"></a>Välj **Lägg till**. Välj eller Lägg till värden för följande inställningar och välj sedan **OK**:

    | Inställning | Värde | Information |
    | ------- | ----- | ------- |
    | **Källa** | En av:<ul><li>**Alla**</li><li>**IP-adresser**</li><li>**Service tag** (inkommande säkerhets regel) eller **VirtualNetwork** (utgående säkerhets regel)</li><li>**Program &nbsp; säkerhets &nbsp; grupp**</li></ul> | <p>Om du väljer **IP-adresser** måste du även ange **Källans IP-adresser/CIDR-intervall**.</p><p>Om du väljer **service tag** kan du också välja en **source service-tagg**.</p><p>Om du väljer **program säkerhets grupp** måste du också välja en befintlig program säkerhets grupp. Om du väljer **program säkerhets grupp** för både **källa** och **mål** måste nätverks gränssnitten i båda program säkerhets grupperna finnas i samma virtuella nätverk.</p> |
    | **Käll-IP-adresser/CIDR-intervall** | En kommaavgränsad lista över IP-adresser och CIDR-intervall (Classless Interdomain Routing) | <p>Den här inställningen visas om du ändrar **källa** till **IP-adresser**. Du måste ange ett enskilt värde eller en kommaavgränsad lista med flera värden. Ett exempel på flera värden är `10.0.0.0/16, 192.188.1.1` . Det finns gränser för antalet värden som du kan ange. Mer information finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Om den IP-adress du anger är tilldelad till en virtuell Azure-dator anger du dess privata IP-adress, inte dess offentliga IP-adress. Azure bearbetar säkerhets regler efter att den översätter den offentliga IP-adressen till en privat IP-adress för inkommande säkerhets regler, men innan den översätter en privat IP-adress till en offentlig IP-adress för utgående regler. Mer information om offentliga och privata IP-adresser i Azure finns i [IP-diagramtyper](./public-ip-addresses.md).</p> |
    | **Käll tjänst tag gen** | En service tag från List rutan | Den här valfria inställningen visas om du anger taggen **Source** to **service** för en inkommande säkerhets regel. En service tag är en fördefinierad identifierare för en kategori med IP-adresser. Om du vill veta mer om tillgängliga Service märken och hur varje tagg representerar, se [service Taggar](./network-security-groups-overview.md#service-tags). |
    | **Säkerhets grupp för käll program** | En befintlig program säkerhets grupp | Den här inställningen visas om du anger **källa** till **program säkerhets grupp**. Välj en program säkerhets grupp som finns i samma region som nätverks gränssnittet. Lär dig hur du [skapar en program säkerhets grupp](#create-an-application-security-group). |
    | **Källportintervall** | En av:<ul><li>En enda port, till exempel `80`</li><li>Ett port intervall, till exempel `1024-65535`</li><li>En kommaavgränsad lista över enskilda portar och/eller port intervall, till exempel `80, 1024-65535`</li><li>En asterisk ( `*` ) för att tillåta trafik på vilken port som helst</li></ul> | Den här inställningen anger vilka portar som regeln tillåter eller nekar trafik. Det finns gränser för antalet portar som du kan ange. Mer information finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Mål** | En av:<ul><li>**Alla**</li><li>**IP-adresser**</li><li>**Service tag** (utgående säkerhets regel) eller **VirtualNetwork** (inkommande säkerhets regel)</li><li>**Program &nbsp; säkerhets &nbsp; grupp**</li></ul> | <p>Om du väljer **IP-adresser** anger du även **mål-IP-adresser/CIDR-intervall**.</p><p>Om du väljer **VirtualNetwork** tillåts trafik till alla IP-adresser i det virtuella nätverkets adress utrymme. **VirtualNetwork** är en service tag.</p><p>Om du väljer **program säkerhets grupp** måste du sedan välja en befintlig program säkerhets grupp. Lär dig hur du [skapar en program säkerhets grupp](#create-an-application-security-group).</p> |
    | **Mål-IP-adresser/CIDR-intervall** | En kommaavgränsad lista med IP-adresser och CIDR-intervall | <p>Den här inställningen visas om du ändrar **målet** till **IP-adresser**. Precis som **käll** **-och käll-IP-adresser/CIDR-intervall** kan du ange en eller flera adresser eller intervall. Det finns gränser för antalet som du kan ange. Mer information finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Om den IP-adress du anger är tilldelad till en virtuell Azure-dator måste du ange dess privata IP-adress, inte dess offentliga IP-adress. Azure bearbetar säkerhets regler efter att den översätter den offentliga IP-adressen till en privat IP-adress för inkommande säkerhets regler, men innan Azure översätter en privat IP-adress till en offentlig IP-adress för utgående regler. Mer information om offentliga och privata IP-adresser i Azure finns i [IP-diagramtyper](./public-ip-addresses.md).</p> |
    | **Måltjänsttagg** | En service tag från List rutan | Den här valfria inställningen visas om du ändrar **målet** till **service tag** för en utgående säkerhets regel. En service tag är en fördefinierad identifierare för en kategori med IP-adresser. Om du vill veta mer om tillgängliga Service märken och hur varje tagg representerar, se [service Taggar](./network-security-groups-overview.md#service-tags). |
    | **Säkerhets grupp för mål program** | En befintlig program säkerhets grupp | Den här inställningen visas om du anger **mål** för **program säkerhets gruppen**. Välj en program säkerhets grupp som finns i samma region som nätverks gränssnittet. Lär dig hur du [skapar en program säkerhets grupp](#create-an-application-security-group). |
    | **Målportintervall** | En av:<ul><li>En enda port, till exempel `80`</li><li>Ett port intervall, till exempel `1024-65535`</li><li>En kommaavgränsad lista över enskilda portar och/eller port intervall, till exempel `80, 1024-65535`</li><li>En asterisk ( `*` ) för att tillåta trafik på vilken port som helst</li></ul> | Precis som med **käll ports intervall** kan du ange en eller flera portar och intervall. Det finns gränser för antalet som du kan ange. Mer information finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Protokoll** | **Valfri**, **TCP**, **UDP** eller **ICMP** | Du kan begränsa regeln till Transmission Control Protocol (TCP), User Datagram Protocol (UDP) eller Internet Control Message Protocol (ICMP). Standard regeln gäller för alla protokoll. |
    | **Åtgärd** | **Tillåt** eller **neka** | Den här inställningen anger om den här regeln tillåter eller nekar åtkomst för den angivna käll-och mål konfigurationen. |
    | **Priority** | Ett värde mellan 100 och 4096 som är unikt för alla säkerhets regler i nätverks säkerhets gruppen | Azure bearbetar säkerhets regler i prioritetsordning. Ju lägre siffra, desto högre prioritet. Vi rekommenderar att du lämnar ett mellanrum mellan prioritets nummer när du skapar regler, till exempel 100, 200 och 300. Om du lämnar luckor blir det enklare att lägga till regler i framtiden, så att du kan ge dem högre eller lägre prioritet än befintliga regler. |
    | **Namn** | Ett unikt namn för regeln inom nätverks säkerhets gruppen | Namnet kan bestå av upp till 80 tecken. Det måste börja med en bokstav eller en siffra och måste sluta med en bokstav, en siffra eller ett under streck. Namnet får bara innehålla bokstäver, siffror, under streck, punkter eller bindestreck. |
    | **Beskrivning** | En text Beskrivning | Du kan också ange en text Beskrivning av säkerhets regeln. Beskrivningen får inte vara längre än 140 tecken. |

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Visa alla säkerhets regler

En nätverks säkerhets grupp innehåller noll eller flera regler. Mer information om den information som visas när du visar regler finns i [Översikt över nätverks säkerhets grupper](./network-security-groups-overview.md).

1. Gå till [Azure Portal](https://portal.azure.com) om du vill se reglerna för en nätverks säkerhets grupp. Sök efter och välj **nätverks säkerhets grupper**.

2. Välj namnet på den nätverks säkerhets grupp som du vill visa reglerna för.

3. I meny raden för nätverks säkerhets gruppen väljer du **inkommande säkerhets regler** eller **utgående säkerhets regler**.

Listan innehåller alla regler som du har skapat och nätverks säkerhets gruppens [Standard säkerhets regler](./network-security-groups-overview.md#default-security-rules).

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Visa information om en säkerhetsregel

1. Gå till [Azure Portal](https://portal.azure.com) om du vill se reglerna för en nätverks säkerhets grupp. Sök efter och välj **nätverks säkerhets grupper**.

2. Välj namnet på den nätverks säkerhets grupp som du vill visa information om en regel för.

3. I meny raden för nätverks säkerhets gruppen väljer du **inkommande säkerhets regler** eller **utgående säkerhets regler**.

4. Välj den regel som du vill visa information om. En förklaring av alla inställningar finns i [säkerhets regel inställningar](#security-rule-settings).

    > [!NOTE]
    > Den här proceduren gäller endast för en anpassad säkerhets regel. Det fungerar inte om du väljer en standard säkerhets regel.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ Network NSG Rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Ändra en säkerhets regel

1. Slutför stegen i [Visa information om en säkerhets regel](#view-details-of-a-security-rule).

2. Ändra inställningarna efter behov och välj sedan **Spara**. En förklaring av alla inställningar finns i [säkerhets regel inställningar](#security-rule-settings).

    > [!NOTE]
    > Den här proceduren gäller endast för en anpassad säkerhets regel. Du får inte ändra en standard säkerhets regel.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Ta bort en säkerhets regel

1. Slutför stegen i [Visa information om en säkerhets regel](#view-details-of-a-security-rule).

2. Välj **Ta bort** och välj sedan **Ja**.

    > [!NOTE]
    > Den här proceduren gäller endast för en anpassad säkerhets regel. Du har inte behörighet att ta bort en standard säkerhets regel.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [ta bort AZ Network NSG Rule Delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Arbeta med programsäkerhetsgrupper

En program säkerhets grupp innehåller noll eller flera nätverks gränssnitt. Mer information finns i [program säkerhets grupper](./network-security-groups-overview.md#application-security-groups). Alla nätverks gränssnitt i en program säkerhets grupp måste finnas i samma virtuella nätverk. Information om hur du lägger till ett nätverks gränssnitt i en program säkerhets grupp finns i [lägga till ett nätverks gränssnitt i en program säkerhets grupp](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Skapa en program säkerhets grupp

1. I menyn i [Azure-portalen](https://portal.azure.com) eller på sidan **Start** väljer du **Skapa en resurs**.

2. I rutan Sök anger du *program säkerhets grupp*.

3. På sidan **program säkerhets grupp** väljer du **skapa**.

4. På sidan **skapa en program säkerhets grupp** går du till fliken **grundläggande** och anger värden för följande inställningar:

    | Inställning | Action |
    | --- | --- |
    | **Prenumeration** | Välj din prenumeration. |
    | **Resursgrupp** | Välj en befintlig resurs grupp eller skapa en ny resurs grupp genom att välja **Skapa ny** . |
    | **Namn** | Ange en unik text sträng inom en resurs grupp. |
    | **Region** | Välj den plats som du vill använda. |

5. Välj **Granska + skapa**.

6. Klicka på **skapa** på fliken **Granska + skapa** när du ser meddelandet **valideringen har skickats** .

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ Network grupperna Create](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Visa alla program säkerhets grupper

Gå till [Azure Portal](https://portal.azure.com) för att se dina program säkerhets grupper. Sök efter och välj **program säkerhets grupper**. Azure Portal visar en lista över dina program säkerhets grupper.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ Network grupperna List](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Visa information om en specifik program säkerhets grupp

1. Gå till [Azure Portal](https://portal.azure.com) om du vill visa en program säkerhets grupp. Sök efter och välj **program säkerhets grupper**.

2. Välj namnet på den program säkerhets grupp som du vill visa information om.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ Network grupperna show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Ändra en program säkerhets grupp

1. Gå till [Azure Portal](https://portal.azure.com) om du vill visa en program säkerhets grupp. Sök efter och välj **program säkerhets grupper**.

2. Välj namnet på den program säkerhets grupp som du vill ändra.

3. Välj **ändra** bredvid den inställning som du vill ändra. Du kan till exempel lägga till eller ta bort **taggar**, eller så kan du ändra **resurs gruppen** eller **prenumerationen**.

    > [!NOTE]
    > Du kan inte ändra platsen.

    I meny raden kan du också välja **åtkomst kontroll (IAM)**. På sidan **åtkomst kontroll (IAM)** kan du tilldela eller ta bort behörigheter till program säkerhets gruppen.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ Network grupperna Update](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | Ingen PowerShell-cmdlet |

### <a name="delete-an-application-security-group"></a>Ta bort en program säkerhets grupp

Du kan inte ta bort en program säkerhets grupp om den innehåller nätverks gränssnitt. Om du vill ta bort alla nätverks gränssnitt från program säkerhets gruppen ändrar du antingen inställningarna för nätverks gränssnittet eller tar bort nätverks gränssnitten. Mer information finns i [Lägg till i eller ta bort från program säkerhets grupper](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) eller [ta bort ett nätverks gränssnitt](virtual-network-network-interface.md#delete-a-network-interface).

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera dina program säkerhets grupper. Sök efter och välj **program säkerhets grupper**.

2. Välj namnet på den program säkerhets grupp som du vill ta bort.

3. Välj **ta bort** och välj sedan **Ja** för att ta bort program säkerhets gruppen.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ Network grupperna Delete](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter i nätverks säkerhets grupper, säkerhets regler och program säkerhets grupper måste ditt konto tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga behörigheter enligt följande tabeller:

### <a name="network-security-group"></a>Nätverkssäkerhetsgrupp

| Action                                                        |   Namn                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft. Network/networkSecurityGroups/Read                  |   Hämta nätverks säkerhets grupp                                          |
| Microsoft. Network/networkSecurityGroups/Write                 |   Skapa eller uppdatera nätverks säkerhets grupp                             |
| Microsoft. Network/networkSecurityGroups/Delete                |   Ta bort nätverks säkerhets grupp                                       |
| Microsoft. Network/networkSecurityGroups/JOIN/åtgärd           |   Koppla en nätverks säkerhets grupp till ett undernät eller ett nätverks gränssnitt 


>[!NOTE]
> Om du vill utföra `write` åtgärder på en nätverks säkerhets grupp måste prenumerations kontot ha minst `read` behörighet för resurs gruppen tillsammans med `Microsoft.Network/networkSecurityGroups/write` behörigheten.


### <a name="network-security-group-rule"></a>Regel för nätverks säkerhets grupp

| Action                                                        |   Namn                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft. Network/networkSecurityGroups/securityRules/Read            |   Hämta regel                                                            |
| Microsoft. Network/networkSecurityGroups/securityRules/Write           |   Skapa eller uppdatera regel                                               |
| Microsoft. Network/networkSecurityGroups/securityRules/Delete          |   Ta bort regel                                                         |

### <a name="application-security-group"></a>Programsäkerhetsgrupp

| Action                                                                     | Namn                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft. Network/applicationSecurityGroups/joinIpConfiguration/Action     | Koppla en IP-konfiguration till en program säkerhets grupp|
| Microsoft. Network/applicationSecurityGroups/joinNetworkSecurityRule/Action | Koppla en säkerhets regel till en program säkerhets grupp    |
| Microsoft. Network/applicationSecurityGroups/Read                           | Hämta en program säkerhets grupp                        |
| Microsoft. Network/applicationSecurityGroups/Write                          | Skapa eller uppdatera en program säkerhets grupp           |
| Microsoft. Network/applicationSecurityGroups/Delete                         | Ta bort en program säkerhets grupp                     |

## <a name="next-steps"></a>Nästa steg

- Skapa en nätverks-eller program säkerhets grupp med [PowerShell](powershell-samples.md) -eller [Azure CLI](cli-samples.md) -exempel skript eller Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tilldela [Azure policy definitioner](./policy-reference.md) för virtuella nätverk
