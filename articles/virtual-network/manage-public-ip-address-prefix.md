---
title: Skapa, ändra eller ta bort ett offentligt IP-adressprefix för Azure
titlesuffix: Azure Virtual Network
description: Lär dig mer om offentliga IP-adressprefix och hur du skapar, ändrar eller tar bort dem. Se var du hittar ytterligare information.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 173fa3a8288ccceb07048e83fcec35d67b2fd35f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783439"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Skapa, ändra eller ta bort ett prefix för offentlig IP-adress

Lär dig mer om ett offentligt IP-adressprefix och hur du skapar, ändrar och tar bort ett. Ett offentligt IP-adressprefix är ett sammanhängande adressintervall baserat på antalet offentliga IP-adresser som du anger. Adresserna tilldelas till din prenumeration. När du skapar en offentlig IP-adressresurs kan du tilldela en statisk offentlig IP-adress från prefixet och koppla adressen till virtuella datorer, lastbalanserare eller andra resurser för att aktivera Internetanslutning. Om du inte är bekant med prefix för offentliga IP-adresser kan du gå till [Översikt över prefix för offentliga IP-adresser](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande uppgifter innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto kan du registrera dig för ett kostnadsfritt [utvärderingskonto.](https://azure.microsoft.com/free)
- Om du använder portalen öppnar du https://portal.azure.com och loggar in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kan du antingen köra [kommandona i Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudien kräver Azure PowerShell version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du använder Azure CLI-kommandon (Kommandoradsgränssnitt) för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här självstudien kräver Azure CLI version 2.0.41 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra för `az login` att skapa en anslutning till Azure.

Det konto som du loggar in på, eller [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ansluter till Azure [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) med, måste tilldelas till nätverksdeltagarerollen eller till en anpassad roll som har tilldelats lämpliga åtgärder som anges i [Behörigheter](#permissions).

Offentliga IP-adressprefix har en avgift. Mer information finns i [prissättningen](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Skapa ett offentligt IP-adressprefix

1. Längst upp till vänster i portalen väljer du **+ Skapa en resurs**.
2. Ange *det offentliga IP-prefixet* *i rutan Sök på Marketplace.* När **Prefix för offentlig IP-adress** visas i sökresultatet väljer du det.
3. Under **Prefix för offentlig IP-adress** väljer du **Skapa.**
4. Ange eller välj värden för följande inställningar under Skapa prefix **för offentlig IP-adress** och välj sedan **Skapa:**

   |Inställning|Obligatoriskt?|Information|
   |---|---|---|
   |Prenumeration|Yes|Måste finnas i samma prenumeration [som](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) den resurs som du vill associera den offentliga IP-adressen med.|
   |Resursgrupp|Yes|Kan finnas i samma eller en annan [resursgrupp som](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) den resurs som du vill koppla den offentliga IP-adressen till.|
   |Name|Yes|Namnet måste vara unikt inom den resursgrupp som du väljer.|
   |Region|Yes|Måste finnas i samma [region som](https://azure.microsoft.com/regions)de offentliga IP-adresser som du tilldelar adresser från intervallet.|
   |Prefixstorlek|Yes| Storleken på prefixet du behöver. Standardvärdet är en /28- eller 16-IP-adress.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>I regioner med tillgänglighetszoner kan du använda PowerShell- eller CLI-kommandon för att skapa ett offentligt IP-adressprefix som antingen: icke-zonindelad, associerad med en specifik zon eller för att använda zonredundans.  För API-version 2020-08-01 eller senare skapas ett prefix för en icke-zonindead offentlig IP-adress om en zonparameter inte anges. För versioner av API:et som är äldre än 2020-08-01 skapas ett zonredundant offentligt IP-adressprefix. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Skapa en statisk offentlig IP-adress från ett prefix
När du har skapat ett prefix måste du skapa statiska IP-adresser från prefixet . Följ stegen nedan för att göra detta.

1. I rutan som innehåller texten Sök efter *resurser längst* upp i Azure Portal anger du *prefixet för offentlig IP-adress.* När **offentliga IP-adressprefix** visas i sökresultatet väljer du det.
2. Välj det prefix som du vill skapa offentliga IP-adresser från.
3. När det visas i sökresultatet markerar du det och klickar på **+Lägg till IP-adress** i avsnittet Översikt.
4. Ange eller välj värden för följande inställningar under **Skapa offentlig IP-adress.** Eftersom ett prefix är för Standard SKU, IPv4 och static behöver du bara ange följande information:

   |Inställning|Obligatoriskt?|Information|
   |---|---|---|
   |Name|Yes|Namnet på den offentliga IP-adressen måste vara unikt inom den resursgrupp som du väljer.|
   |Tidsgräns för inaktivitet (minuter)|No|Hur många minuter en TCP- eller HTTP-anslutning ska vara öppen utan att klienterna behöver skicka keep-alive-meddelanden. |
   |DNS-namnetikett|No|Måste vara unikt i den Azure-region som du skapar namnet i (för alla prenumerationer och alla kunder). Azure registrerar automatiskt namn och IP-adress i dess DNS så att du kan ansluta till en resurs med namnet. Azure lägger till ett standardundernät, till exempel *location.cloudapp.azure.com* (där platsen är den plats du väljer) till det namn du anger för att skapa det fullständigt kvalificerade DNS-namnet. Mer information finns i Använda [Azure DNS med en offentlig IP-adress i Azure.](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)|

Du kan också använda CLI- och PS-kommandona nedan med parametrarna --public-ip-prefix (CLI) och -PublicIpPrefix (PS) för att skapa en offentlig IP-adressresurs. 

|Verktyg|Kommando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-or-delete-a-prefix"></a>Visa eller ta bort ett prefix

1. I rutan som innehåller texten Sök efter *resurser längst* upp i Azure Portal anger du *prefixet för offentlig IP-adress.* När **offentliga IP-adressprefix** visas i sökresultatet väljer du det.
2. Välj namnet på det offentliga IP-adressprefix som du vill visa, ändra inställningarna för eller ta bort från listan.
3. Slutför något av följande alternativ, beroende på om du vill visa, ta bort eller ändra prefixet för den offentliga IP-adressen.
   - **Vy:** Avsnittet **Översikt visar** viktiga inställningar för det offentliga IP-adressprefixet, till exempel prefix.
   - **Ta** bort : Om du vill ta bort det offentliga IP-adressprefixet **väljer** du Ta bort i **avsnittet** Översikt. Om adresser i prefixet är associerade med offentliga IP-adressresurser måste du först ta bort de offentliga IP-adressresurserna. Se [Ta bort en offentlig IP-adress.](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_list) to list public IP addresses, [az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show) to show settings; [az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_update) to update; [az network public-ip prefix delete to](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete) delete|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) för att hämta ett offentligt IP-adressobjekt och visa dess inställningar, [Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) för att uppdatera inställningarna. [Remove-AzPublicIpPrefix att](/powershell/module/az.network/remove-azpublicipprefix) ta bort|

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter med offentliga IP-adressprefix måste ditt [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) konto tilldelas till nätverksdeltagarerollen eller till en anpassad roll som har tilldelats lämpliga åtgärder som anges i följande tabell: [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)

| Action                                                            | Name                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Läsa ett offentligt IP-adressprefix                                |
| Microsoft.Network/publicIPPrefixes/write                          | Skapa eller uppdatera ett offentligt IP-adressprefix                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Ta bort ett offentligt IP-adressprefix                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Skapa en offentlig IP-adress från ett prefix |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om scenarier och fördelar med att använda ett [offentligt IP-prefix](public-ip-address-prefix.md)
