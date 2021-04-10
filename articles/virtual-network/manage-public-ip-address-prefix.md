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
ms.openlocfilehash: 2e32faad698fbf316d51123cc8b7845a3b262c7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938664"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Skapa, ändra eller ta bort ett prefix för offentlig IP-adress

Lär dig mer om ett offentligt IP-adressprefix och hur du skapar, ändrar och tar bort ett. Ett offentligt IP-adressprefix är ett sammanhängande adress intervall baserat på antalet offentliga IP-adresser som du anger. Adresserna är tilldelade till din prenumeration. När du skapar en offentlig IP-adressresurs kan du tilldela en statisk offentlig IP-adress från prefixet och associera adressen med virtuella datorer, belastningsutjämnare eller andra resurser för att aktivera Internet anslutning. Om du inte är bekant med de offentliga IP-adressprefix, se [Översikt över offentliga IP](public-ip-address-prefix.md) -adressprefix

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande åtgärder innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto kan du registrera dig för ett [kostnads fritt utvärderings konto](https://azure.microsoft.com/free).
- Om du använder portalen öppnar du https://portal.azure.com och loggar in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. I den här självstudien krävs Azure PowerShell module version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du använder kommando rads kommandon i Azure för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. I den här självstudien krävs Azure CLI version 2.0.41 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra `az login` för att skapa en anslutning till Azure.

Det konto som du loggar in på eller ansluta till Azure med måste tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder som anges i [behörigheter](#permissions).

Prefix för offentliga IP-adresser har en avgift. Mer information finns i [prissättning](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Skapa ett offentligt IP-adressprefix

1. I det övre vänstra hörnet i portalen väljer du **+ skapa en resurs**.
2. Ange *offentligt IP-prefix* i rutan *Sök i Marketplace* . När det **offentliga IP** -adressprefixet visas i Sök resultaten väljer du det.
3. Under **offentlig IP**-adressprefix väljer du **skapa**.
4. Ange eller välj värden för följande inställningar under **skapa offentlig IP-adressprefix** och välj sedan **skapa**:

   |Inställning|Obligatoriskt?|Information|
   |---|---|---|
   |Prenumeration|Ja|Måste finnas i samma [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) som den resurs som du vill associera den offentliga IP-adressen med.|
   |Resursgrupp|Ja|Kan finnas i samma eller olika [resurs grupp](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) som den resurs som du vill associera den offentliga IP-adressen med.|
   |Name|Ja|Namnet måste vara unikt inom den resurs grupp du väljer.|
   |Region|Ja|Måste finnas i samma [region](https://azure.microsoft.com/regions)som de offentliga IP-adresserna som du ska tilldela adresser från intervallet.|
   |Prefixlängd|Ja| Storleken på det prefix du behöver. En/28 eller 16 IP-adresser är standardinställningen.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ Network Public-IP prefix Create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>I regioner med tillgänglighets zoner kan du använda PowerShell-eller CLI-kommandon för att skapa ett offentligt IP-adressprefix som antingen: icke-zonindelade, associerat med en speciell zon eller använda zon-redundans.  För API version 2020-08-01 eller senare, om en zon parameter inte anges, skapas ett icke-zonindelade offentligt IP-adressprefix. För versioner av API: t som är äldre än 2020-08-01 skapas ett prefix för zonens redundant offentlig IP-adress. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Skapa en statisk offentlig IP-adress från ett prefix
När du har skapat ett prefix måste du skapa statiska IP-adresser från prefixet. Följ stegen nedan för att göra detta.

1. I rutan som innehåller *Sök resurserna* för text Sök högst upp i Azure Portal, skriver du *offentliga IP-* adressprefix. När de **allmänna IP-adressprefix** visas i Sök resultaten väljer du det.
2. Välj det prefix som du vill skapa offentliga IP-adresser från.
3. När det visas i Sök resultaten markerar du det och klickar på **+ Lägg till IP-adress** i översikts avsnittet.
4. Ange eller välj värden för följande inställningar under **skapa offentlig IP-adress**. Eftersom ett prefix är för standard-SKU, IPv4 och statisk, behöver du bara ange följande information:

   |Inställning|Obligatoriskt?|Information|
   |---|---|---|
   |Name|Ja|Namnet på den offentliga IP-adressen måste vara unikt inom den resurs grupp du väljer.|
   |Tids gräns för inaktivitet (minuter)|Inga|Hur många minuter som en TCP-eller HTTP-anslutning är öppen utan att lita på klienter för att skicka Keep-Alive-meddelanden. |
   |DNS-namnetikett|Inga|Måste vara unikt inom Azure-regionen som du skapar namnet i (för alla prenumerationer och alla kunder). Azure registrerar automatiskt namn och IP-adress i DNS så att du kan ansluta till en resurs med namnet. Azure lägger till ett standard under nät som *location.cloudapp.Azure.com* (där platsen är den plats du väljer) till det namn som du anger för att skapa det fullständigt kvalificerade DNS-namnet. Mer information finns i [använda Azure DNS med en offentlig Azure-IP-adress](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

Alternativt kan du använda CLI-och PS-kommandona nedan med parametrarna--Public-IP-prefix (CLI) och-PublicIpPrefix (PS) för att skapa en offentlig IP-adressresurs. 

|Verktyg|Kommando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-or-delete-a-prefix"></a>Visa eller ta bort ett prefix

1. I rutan som innehåller *Sök resurserna* för text Sök högst upp i Azure Portal, skriver du *offentliga IP-* adressprefix. När de **allmänna IP-adressprefix** visas i Sök resultaten väljer du det.
2. Välj namnet på det offentliga IP-adressprefix som du vill visa, ändra inställningarna för eller ta bort i listan.
3. Slutför något av följande alternativ, beroende på om du vill visa, ta bort eller ändra prefixet för det offentliga IP-adressen.
   - **Visa**: **översikts** avsnittet visar nyckel inställningar för det offentliga IP-adressprefixet, till exempel prefix.
   - **Ta** bort: om du vill ta bort det offentliga IP-adressprefixet väljer du **ta bort** i avsnittet **Översikt** . Om adresser inom prefixet är kopplade till offentliga IP-adressresurser måste du först ta bort de offentliga IP-adress resurserna. Se [ta bort en offentlig IP-adress](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ Network Public-IP-adressprefix](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) för att visa en lista över offentliga IP-adresser, [AZ Network Public-IP prefix show](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) för att Visa inställningar; [AZ Network Public-IP-prefix uppdatering](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) att uppdatera; [AZ Network Public-IP prefix Delete](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) att ta bort|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) för att hämta ett objekt för en offentlig IP-adress och visa dess inställningar, [set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) för att uppdatera inställningarna. [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) att ta bort|

## <a name="permissions"></a>Behörigheter

För att utföra åtgärder på offentliga IP-adressprefix måste ditt konto tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller en [anpassad](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som har tilldelats lämpliga åtgärder i följande tabell:

| Action                                                            | Name                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft. Network/publicIPPrefixes/Read                           | Läs ett offentligt IP-adressprefix                                |
| Microsoft. Network/publicIPPrefixes/Write                          | Skapa eller uppdatera ett offentligt IP-adressprefix                    |
| Microsoft. Network/publicIPPrefixes/Delete                         | Ta bort ett offentligt IP-adressprefix                              |
|Microsoft. Network/publicIPPrefixes/JOIN/åtgärd                     | Skapa en offentlig IP-adress från ett prefix |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om scenarier och fördelar med att använda ett [offentligt IP-prefix](public-ip-address-prefix.md)
