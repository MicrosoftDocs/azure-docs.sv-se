---
title: Hantera DNS-zoner i Azure DNS-PowerShell | Microsoft Docs
description: Du kan hantera DNS-zoner med Azure PowerShell. Den här artikeln beskriver hur du uppdaterar, tar bort och skapar DNS-zoner på Azure DNS
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: rohink
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6abcca9d9888dc8968d7233e7aee6cd76aa215f7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94965756"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Så här hanterar du DNS-zoner med PowerShell

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Klassisk Azure-CLI](./dns-operations-dnszones-cli.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

Den här artikeln visar hur du hanterar DNS-zoner med hjälp av Azure PowerShell. Du kan också hantera dina DNS-zoner med plattforms oberoende [Azure CLI](dns-operations-dnszones-cli.md) eller Azure Portal.

Den här guiden behandlar särskilt offentliga DNS-zoner. Information om hur du använder Azure PowerShell för att hantera privata zoner i Azure DNS finns i [komma igång med Azure DNS Private Zones med hjälp av Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

En DNS-zon skapas med hjälp av cmdleten `New-AzureRmDnsZone`.

I följande exempel skapas en DNS-zon med namnet *contoso.com* i resurs gruppen med namnet *MyResourceGroup*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

I följande exempel visas hur du skapar en DNS-zon med två [Azure Resource Manager Taggar](dns-zones-records.md#tags), *Project = demo* och *Kuvert = test*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Azure DNS stöder också privata DNS-zoner.  Mer information om privata DNS-zoner finns i [Using Azure DNS for private domains](private-dns-overview.md) (Använda Azure DNS för privata domäner). Ett exempel på hur man kan skapa en privat DNS-zon finns i [Kom igång med privata Azure DNS-zoner med PowerShell](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Hämta en DNS-zon

Använd cmdleten för att hämta en DNS-zon `Get-AzureRmDnsZone` . Den här åtgärden returnerar ett DNS-zon objekt som motsvarar en befintlig zon i Azure DNS. Objektet innehåller data om zonen (till exempel antalet post uppsättningar), men innehåller inte själva post uppsättningarna (se `Get-AzureRmDnsRecordSet` ).

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>Lista DNS-zoner

Genom att utelämna zonnamnet från `Get-AzureRmDnsZone` kan du räkna upp alla zoner i en resursgrupp. Den här åtgärden returnerar en matris med zonobjekt.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Genom att utelämna både zonnamnet och resursgruppsnamnet från `Get-AzureRmDnsZone` kan du räkna upp alla zoner i Azure-prenumerationen.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>Uppdatera en DNS-zon

Du kan göra ändringar i en DNS-zonresurs med `Set-AzureRmDnsZone`. Den här cmdleten uppdaterar inte någon av DNS-postuppsättningarna i zonen (mer information finns i [Hantera DNS-poster](dns-operations-recordsets.md)). Den används endast för att uppdatera zonresursens egenskaper. Egenskaperna för skrivbara zoner är för närvarande begränsade till [Azure Resource Manager taggar för zon resursen](dns-zones-records.md#tags).

Använd något av följande två sätt för att uppdatera en DNS-zon:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Ange zonen med zon namnet och resurs gruppen

Den här metoden ersätter de befintliga zon taggarna med de värden som anges.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Ange zonen med ett $zone-objekt

Den här metoden hämtar det befintliga zonfilen, ändrar taggarna och genomför ändringarna. På så sätt kan befintliga taggar bevaras.

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

När `Set-AzureRmDnsZone` du använder med ett $Zone objekt, används [etag-kontroller](dns-zones-records.md#etags) för att säkerställa att samtidiga ändringar inte skrivs över. Du kan använda den valfria `-Overwrite` växeln för att ignorera de här kontrollerna.

## <a name="delete-a-dns-zone"></a>Ta bort en DNS-zon

DNS-zoner kan tas bort med hjälp av `Remove-AzureRmDnsZone` cmdleten.

> [!NOTE]
> Om du tar bort en DNS-zon så tar du även bort DNS-posterna i zonen. Du kan inte ångra den här åtgärden. Om DNS-zonen används, så misslyckas de tjänster som använder zonen när zonen tas bort.
>
>Hur du gör för att förebygga oavsiktlig zonborttagning beskrivs i [Skydda DNS-zoner och poster](dns-protect-zones-recordsets.md).


Använd något av följande två sätt när du ska ta bort en DNS-zon:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Ange zonen med zonnamnet och resursgruppens namn

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Ange zonen med ett $zone-objekt

Du kan ange zonen som ska tas bort med hjälp av ett `$zone`-objekt som returnerats av `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

Zonobjektet kan även pipas i stället för att skickas som en parameter:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

Som med `Set-AzureRmDnsZone` kan du med hjälp av ett `$zone` objekt aktivera etag-kontroller för att se till att samtidiga ändringar inte tas bort. Använd `-Overwrite` växeln för att ignorera de här kontrollerna.

## <a name="confirmation-prompts"></a>Bekräftelsemeddelanden

Cmdletarna `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` och `Remove-AzureRmDnsZone` stöder alla bekräftelsemeddelanden.

Både `New-AzureRmDnsZone` och `Set-AzureRmDnsZone` frågar efter bekräftelse om `$ConfirmPreference` PowerShell-inställningsvariabeln har värdet `Medium` eller lägre. På grund av den potentiellt höga effekten av att ta bort en DNS-zon så uppmanar `Remove-AzureRmDnsZone`-cmdleten dig att bekräfta att `$ConfirmPreference` PowerShell-variabeln har något värde annat än `None`.

Eftersom standardvärdet för `$ConfirmPreference` är `High`, så frågar endast `Remove-AzureRmDnsZone` om bekräftelse som standard.

Du kan åsidosätta den aktuella `$ConfirmPreference`-inställningen med hjälp av parametern `-Confirm`. Om du anger `-Confirm` eller `-Confirm:$True`, så uppmanar cmdleten dig att bekräfta detta innan den körs. Om du anger `-Confirm:$False`, som ber cmdleten dig inte om bekräftelse.

Mer information om `-Confirm` och `$ConfirmPreference` finns i [Om inställningsvariabler](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [hanterar post uppsättningar och poster](dns-operations-recordsets.md) i din DNS-zon.
<br>
Lär dig hur du [delegerar din domän till Azure DNS](dns-domain-delegation.md).
<br>
Läs [Azure DNS PowerShell Reference-dokumentationen](/powershell/module/azurerm.dns).