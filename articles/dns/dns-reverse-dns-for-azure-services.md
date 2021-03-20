---
title: Omvänd DNS för Azure-tjänster – Azure DNS
description: Med den här utbildnings vägen kan du komma igång med att konfigurera omvänd DNS-sökning för tjänster som finns i Azure.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: c72e17e601ebf87b0f344a4723159ae22abc81d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919892"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Konfigurera omvänd DNS för tjänster som finns i Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Den här artikeln förklarar hur du konfigurerar omvänd DNS-sökning för tjänster som finns i Azure.

Tjänster i Azure använder IP-adresser som tilldelats av Azure och ägs av Microsoft. Dessa omvända DNS-poster (PTR-poster) måste skapas i motsvarande Microsoft-ägda zoner för omvänd DNS-sökning. Den här artikeln beskriver hur du gör detta.

Det här scenariot ska inte förväxlas med möjligheten att vara [värd för zoner för omvänd DNS-sökning för dina tilldelade IP-intervall i Azure DNS](dns-reverse-dns-hosting.md). I det här fallet måste IP-intervallen som representeras av zonen för omvänd sökning tilldelas till din organisation, vanligt vis av din Internet leverantör.

Innan du läser den här artikeln bör du känna till den här [översikten över omvänd DNS och support i Azure](dns-reverse-dns-overview.md).

I Azure DNS exponeras beräknings resurser (till exempel virtuella datorer, skalnings uppsättningar för virtuella datorer eller Service Fabric kluster) via en PublicIpAddress-resurs. Omvänd DNS-sökning konfigureras med egenskapen "ReverseFqdn" för PublicIpAddress.


Omvänd DNS stöds för närvarande inte för Azure App Service och Application Gateway.

## <a name="validation-of-reverse-dns-records"></a>Validering av omvända DNS-poster

En tredje part bör inte kunna skapa omvända DNS-poster för deras Azure-tjänst mappning till dina DNS-domäner. För att förhindra detta kan Azure bara skapa en omvänd DNS-post där domän namnet som anges i den omvända DNS-posten är samma som, eller som kan matchas till, DNS-namnet eller IP-adressen för en PublicIpAddress eller en moln tjänst i samma Azure-prenumeration.

Den här verifieringen utförs bara när den omvända DNS-posten har angetts eller ändrats. Regelbunden omautentisering utförs inte.

Exempel: Antag att PublicIpAddress-resursen har DNS-namnet contosoapp1.northus.cloudapp.azure.com och IP-23.96.52.53. ReverseFqdn för PublicIpAddress kan anges som:
* DNS-namnet för PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* DNS-namnet för en annan PublicIpAddress i samma prenumeration, till exempel contosoapp2.westus.cloudapp.azure.com
* Ett anpassad DNS-namn, till exempel app1.contoso.com, så länge det här namnet *först* konfigureras som en CNAME till ContosoApp1.northus.cloudapp.Azure.com, eller till en annan PublicIpAddress i samma prenumeration.
* Ett anpassad DNS-namn, till exempel app1.contoso.com, så länge det här namnet *först* konfigureras som en A-post till IP-23.96.52.53 eller till IP-adressen för en annan PublicIpAddress i samma prenumeration.

Samma begränsningar gäller för omvänd DNS för Cloud Services.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Omvänd DNS för PublicIpAddress-resurser

Det här avsnittet innehåller detaljerade anvisningar för hur du konfigurerar omvänd DNS för PublicIpAddress-resurser i distributions modellen för Resource Manager med hjälp av antingen Azure PowerShell, den klassiska Azure-CLI eller Azure CLI. Det finns för närvarande inte stöd för att konfigurera omvänd DNS för PublicIpAddress-resurser via Azure Portal.

Azure har för närvarande endast stöd för omvänd DNS för IPv4 PublicIpAddress-resurser. Det stöds inte för IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Lägg till omvänd DNS till en befintlig PublicIpAddresses

#### <a name="powershell"></a>PowerShell

Så här uppdaterar du omvänd DNS till en befintlig PublicIpAddress:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Om du vill lägga till omvänd DNS till en befintlig PublicIpAddress som inte redan har ett DNS-namn, måste du också ange ett DNS-namn:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

Så här lägger du till omvänd DNS till en befintlig PublicIpAddress:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Om du vill lägga till omvänd DNS till en befintlig PublicIpAddress som inte redan har ett DNS-namn, måste du också ange ett DNS-namn:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

Så här lägger du till omvänd DNS till en befintlig PublicIpAddress:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Om du vill lägga till omvänd DNS till en befintlig PublicIpAddress som inte redan har ett DNS-namn, måste du också ange ett DNS-namn:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Skapa en offentlig IP-adress med omvänd DNS

För att skapa en ny PublicIpAddress med egenskapen omvänd DNS redan angiven:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Visa omvänd DNS för en befintlig PublicIpAddress

Så här visar du det konfigurerade värdet för en befintlig PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Ta bort omvänd DNS från befintliga offentliga IP-adresser

Ta bort en omvänd DNS-egenskap från en befintlig PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Konfigurera omvänd DNS för Cloud Services

Det här avsnittet innehåller detaljerade anvisningar för hur du konfigurerar omvänd DNS för Cloud Services i den klassiska distributions modellen med hjälp av Azure PowerShell. Det finns inte stöd för att konfigurera omvänd DNS för Cloud Services via Azure Portal, den klassiska Azure-CLI eller Azure CLI.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Lägg till omvänd DNS till befintlig Cloud Services

Så här lägger du till en omvänd DNS-post i en befintlig moln tjänst:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Skapa en moln tjänst med omvänd DNS

För att skapa en ny moln tjänst med egenskapen omvänd DNS redan angiven:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Visa omvänd DNS för befintliga Cloud Services

Så här visar du den omvända DNS-egenskapen för en befintlig moln tjänst:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Ta bort omvänd DNS från befintliga Cloud Services

Så här tar du bort en omvänd DNS-egenskap från en befintlig moln tjänst:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>Vanliga frågor

### <a name="how-much-do-reverse-dns-records-cost"></a>Hur mycket kostar det att omvända DNS-poster?

De är kostnads fria!  Det kostar inget extra att omvända DNS-poster eller frågor.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Kommer mina omvända DNS-poster att matchas från Internet?

Ja. När du har angett egenskapen omvänd DNS för din Azure-tjänst hanterar Azure alla DNS-delegeringar och DNS-zoner som krävs för att säkerställa att omvänd DNS-post matchar alla Internet användare.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Har standard omvända DNS-poster skapats för mina Azure-tjänster?

Nej. Omvänd DNS är en valbar funktion. Inga standardvärden för omvänd DNS-poster skapas om du väljer att inte konfigurera dem.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Vad är formatet för det fullständigt kvalificerade domän namnet (FQDN)?

FQDN anges i vidarebefordran och måste avslutas med en punkt (till exempel "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Vad händer om verifierings kontrollen för den omvända DNS jag har angett Miss lyckas?

När den omvända DNS-valideringen Miss lyckas, Miss lyckas åtgärden att konfigurera den omvända DNS-posten. Korrigera det omvända DNS-värdet vid behov och försök igen.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Kan jag konfigurera omvänd DNS för Azure App Service?

Nej. Omvänd DNS stöds inte för Azure App Service.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Kan jag konfigurera flera omvända DNS-poster för min Azure-tjänst?

Nej. Azure har stöd för en enda omvänd DNS-post för varje Azure-moln tjänst eller PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Kan jag konfigurera omvänd DNS för IPv6 PublicIpAddress-resurser?

Nej. Azure har för närvarande endast stöd för omvänd DNS för IPv4 PublicIpAddress-resurser och Cloud Services.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Kan jag skicka e-postmeddelanden till externa domäner från mina Azure Compute-tjänster?

Den tekniska möjligheten att skicka e-post direkt från en Azure-distribution beror på prenumerations typen. Oavsett prenumerations typ rekommenderar Microsoft att använda betrodda e-posttjänster för att skicka utgående e-post. Mer information finns i [förbättrad Azure-säkerhet för att skicka e-post – November 2017 uppdatering](../virtual-network/troubleshoot-outbound-smtp-connectivity.md).

## <a name="next-steps"></a>Nästa steg

Mer information om omvänd DNS finns i [Omvänd DNS-sökning på Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Lär dig hur du kan vara [värd för zonen för omvänd sökning för det IP-adressintervall som tilldelas Internet leverantör i Azure DNS](dns-reverse-dns-for-azure-services.md).
