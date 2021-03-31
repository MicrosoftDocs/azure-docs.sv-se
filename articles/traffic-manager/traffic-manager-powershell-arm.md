---
title: Använda PowerShell för att hantera Traffic Manager i Azure
description: Med den här utbildnings vägen börjar du använda Azure PowerShell för Traffic Manager.
services: traffic-manager
documentationcenter: na
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 21076fe23301c189d9987f78706cc81691ce7a4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89400576"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Använda PowerShell för att hantera Traffic Manager

Azure Resource Manager är det prioriterade hanterings gränssnittet för tjänster i Azure. Azure Traffic Manager-profiler kan hanteras med Azure Resource Manager-baserade API: er och verktyg.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>Resursmodell

Azure Traffic Manager konfigureras med hjälp av en samling inställningar som kallas för en Traffic Manager profil. Den här profilen innehåller DNS-inställningar, inställningar för trafik cirkulation, inställningar för slut punkts övervakning och en lista över tjänst slut punkter som trafiken dirigeras till.

Varje Traffic Manager profil representeras av en resurs av typen ' TrafficManagerProfiles '. På REST API nivå är URI: n för varje profil följande:

`https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}`

## <a name="setting-up-azure-powershell"></a>Konfigurera Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De här anvisningarna använder Microsoft Azure PowerShell. I följande artikel förklaras hur du installerar och konfigurerar Azure PowerShell.

* [Hur du installerar och konfigurerar Azure PowerShell](/powershell/azure/)

I exemplen i den här artikeln förutsätter vi att du har en befintlig resurs grupp. Du kan skapa en resurs grupp med hjälp av följande kommando:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager kräver att alla resurs grupper har en plats. Den här platsen används som standard för resurser som skapats i den resurs gruppen. Men eftersom Traffic Manager profil resurser är globala, inte regionala, är valet av resurs grupps plats ingen påverkan på Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Om du vill skapa en Traffic Manager profil använder du `New-AzTrafficManagerProfile` cmdleten:

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Följande tabell beskriver parametrarna:

| Parameter | Beskrivning |
| --- | --- |
| Name |Resurs namnet för den Traffic Manager profil resursen. Profiler i samma resurs grupp måste ha unika namn. Det här namnet är skilt från DNS-namnet som används för DNS-frågor. |
| ResourceGroupName |Namnet på den resurs grupp som innehåller profil resursen. |
| TrafficRoutingMethod |Anger den metod för trafikroutning som används för att avgöra vilken slut punkt som returneras som svar på en DNS-fråga. Möjliga värden är "prestanda", "viktad" eller "prioritet". |
| RelativeDnsName |Anger hostname-delen av DNS-namnet som anges av den här Traffic Manager profilen. Det här värdet kombineras med DNS-domännamnet som används av Azure-Traffic Manager för att forma det fullständiga domän namnet (FQDN) för profilen. Om du till exempel anger värdet "contoso" blir "contoso.trafficmanager.net". |
| TTL |Anger TTL-värdet (Time-to-Live) för DNS i sekunder. Detta TTL-värde informerar de lokala DNS-matcharna och DNS-klienter så länge DNS-svar för den här Traffic Manager profilen ska cachelagras. |
| MonitorProtocol |Anger det protokoll som ska användas för att övervaka slut punktens hälsa. Möjliga värden är HTTP och HTTPS. |
| MonitorPort |Anger den TCP-port som används för att övervaka slut punktens hälsa. |
| MonitorPath |Anger sökvägen i förhållande till slut punkts domänens namn som används för att avsöka för slut punkts hälsa. |

Cmdleten skapar en Traffic Manager-profil i Azure och returnerar ett motsvarande profil objekt till PowerShell. I det här läget innehåller profilen inga slut punkter. Mer information om hur du lägger till slut punkter i en Traffic Manager-profil finns i lägga till Traffic Manager slut punkter.

## <a name="get-a-traffic-manager-profile"></a>Hämta en Traffic Manager profil

Använd cmdleten för att hämta ett befintligt Traffic Manager Profile-objekt `Get-AzTrafficManagerProfle` :

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Denna cmdlet returnerar ett Traffic Manager Profile-objekt.

## <a name="update-a-traffic-manager-profile"></a>Uppdatera en Traffic Manager-profil

Om du ändrar Traffic Manager profiler följer en tre stegs process:

1. Hämta profilen med `Get-AzTrafficManagerProfile` eller Använd profilen som returnerades av `New-AzTrafficManagerProfile` .
2. Ändra profilen. Du kan lägga till och ta bort slut punkter eller ändra slut punkt eller profil parametrar. Dessa ändringar är offline-åtgärder. Du ändrar bara det lokala objektet i minnet som representerar profilen.
3. Genomför ändringarna med `Set-AzTrafficManagerProfile` cmdleten.

Alla profil egenskaper kan ändras förutom profilens RelativeDnsName. Om du vill ändra RelativeDnsName måste du ta bort profilen och en ny profil med ett nytt namn.

Följande exempel visar hur du ändrar profilens TTL-värde:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

Det finns tre typer av Traffic Manager slut punkter:

1. **Azure-slutpunkter** är tjänster som finns i Azure
2. **Externa slut punkter** är tjänster som ligger utanför Azure
3. **Kapslade slut punkter** används för att skapa kapslade hierarkier av Traffic Manager profiler. Kapslade slut punkter möjliggör avancerade konfigurationer för trafik-routning för komplexa program.

I alla tre fall kan slut punkter läggas till på två sätt:

1. Med en tre stegs process som beskrivits tidigare. Fördelen med den här metoden är att flera slut punkts ändringar kan göras i en enda uppdatering.
2. Använda New-AzTrafficManagerEndpoint-cmdleten. Denna cmdlet lägger till en slut punkt till en befintlig Traffic Manager-profil i en enda åtgärd.

## <a name="adding-azure-endpoints"></a>Lägger till Azure-slutpunkter

Azure slut punkts referens tjänster som finns i Azure. Två typer av Azure-slutpunkter stöds:

1. Azure App Service
2. Azure PublicIpAddress-resurser (som kan kopplas till en belastningsutjämnare eller en virtuell dator NIC). PublicIpAddress måste ha ett angivet DNS-namn för att kunna användas i Traffic Manager.

I varje fall:

* Tjänsten anges med parametern "targetResourceId" i `Add-AzTrafficManagerEndpointConfig` eller `New-AzTrafficManagerEndpoint` .
* "Target" och "EndpointLocation" underförstådd av TargetResourceId.
* Att ange vikt är valfritt. Vikter används endast om profilen har kon figurer ATS för att använda den "viktade" Traffic-routing-metoden. Annars ignoreras de. Om det här värdet anges måste värdet vara ett tal mellan 1 och 1000. Standardvärdet är ' 1 '.
* Det är valfritt att ange prioriteten. Prioriteter används endast om profilen har kon figurer ATS för att använda Traffic-metoden "priority". Annars ignoreras de. Giltiga värden är mellan 1 och 1000 med lägre värden som anger en högre prioritet. Om den anges för en slut punkt måste de anges för alla slut punkter. Om det utelämnas används standardvärden som börjar på 1 i den ordning som slut punkterna visas.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>Exempel 1: lägga till App Service slut punkter med `Add-AzTrafficManagerEndpointConfig`

I det här exemplet skapar vi en Traffic Manager profil och lägger till två App Service slut punkter med hjälp av `Add-AzTrafficManagerEndpointConfig` cmdleten.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>Exempel 2: lägga till en publicIpAddress-slutpunkt med `New-AzTrafficManagerEndpoint`

I det här exemplet läggs en offentlig IP-adressresurs till i Traffic Manager profilen. Den offentliga IP-adressen måste ha ett konfigurerat DNS-namn och kan bindas antingen till NÄTVERKSKORTet för en virtuell dator eller till en belastningsutjämnare.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Lägger till externa slutpunkter

Traffic Manager använder externa slut punkter för att dirigera trafik till tjänster som ligger utanför Azure. Precis som med Azure-slutpunkter kan externa slut punkter läggas till antingen genom att använda `Add-AzTrafficManagerEndpointConfig` följt av `Set-AzTrafficManagerProfile` eller `New-AzTrafficManagerEndpoint` .

När du anger externa slut punkter:

* Slut punkts domän namnet måste anges med parametern Target
* Om metoden "prestanda" Traffic-routing används krävs "EndpointLocation". Annars är den valfri. Värdet måste vara ett [giltigt namn](https://azure.microsoft.com/regions/)på en Azure-region.
* Vikt och prioritet är valfria.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Exempel 1: lägga till externa slut punkter med `Add-AzTrafficManagerEndpointConfig` och `Set-AzTrafficManagerProfile`

I det här exemplet skapar vi en Traffic Manager profil, lägger till två externa slut punkter och genomför ändringarna.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>Exempel 2: lägga till externa slut punkter med `New-AzTrafficManagerEndpoint`

I det här exemplet lägger vi till en extern slut punkt i en befintlig profil. Profilen anges med namnet på profilen och resurs gruppen.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Lägga till kapslade slut punkter

Varje Traffic Manager profil anger en enda metod för trafik dirigering. Det finns dock scenarier som kräver mer avancerad trafikroutning än routningen som tillhandahålls av en enda Traffic Manager profil. Du kan kapsla Traffic Manager profiler för att kombinera fördelarna med mer än en metod för trafikroutning. Med kapslade profiler kan du åsidosätta standard Traffic Manager beteende som stöder större och mer komplexa program distributioner. Mer detaljerade exempel finns i [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).

Kapslade slut punkter konfigureras i den överordnade profilen med en angiven slut punkts typ, "NestedEndpoints". När du anger kapslade slut punkter:

* Slut punkten måste anges med parametern "targetResourceId"
* Om metoden "prestanda" Traffic-routing används krävs "EndpointLocation". Annars är den valfri. Värdet måste vara ett [giltigt namn](https://azure.microsoft.com/regions/)på en Azure-region.
* Vikt och prioritet är valfria för Azure-slutpunkter.
* Parametern "MinChildEndpoints" är valfri. Standardvärdet är ' 1 '. Om antalet tillgängliga slut punkter sjunker under det här tröskelvärdet, tar den överordnade profilen över den underordnade profilen "degraderad" och diverts trafik till andra slut punkter i den överordnade profilen.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Exempel 1: lägga till kapslade slut punkter med `Add-AzTrafficManagerEndpointConfig` och `Set-AzTrafficManagerProfile`

I det här exemplet skapar vi nya Traffic Manager underordnade och överordnade profiler, lägger till den underordnade som en kapslad slut punkt till den överordnade och genomför ändringarna.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

För det kortfattat i det här exemplet har vi inte lagt till några andra slut punkter i de underordnade eller överordnade profilerna.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>Exempel 2: lägga till kapslade slut punkter med `New-AzTrafficManagerEndpoint`

I det här exemplet lägger vi till en befintlig underordnad profil som en kapslad slut punkt till en befintlig överordnad profil. Profilen anges med namnet på profilen och resurs gruppen.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Lägga till slut punkter från en annan prenumeration

Traffic Manager kan arbeta med slut punkter från olika prenumerationer. Du måste växla till prenumerationen med den slut punkt som du vill lägga till för att hämta nödvändiga ininformation till Traffic Manager. Sedan måste du växla till prenumerationerna med Traffic Manager profilen och lägga till slut punkten till den. Exemplet nedan visar hur du gör detta med en offentlig IP-adress.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Uppdatera en Traffic Manager slut punkt

Det finns två sätt att uppdatera en befintlig Traffic Manager slut punkt:

1. Hämta Traffic Manager profilen med `Get-AzTrafficManagerProfile` , uppdatera slut punkts egenskaperna i profilen och genomför ändringarna med `Set-AzTrafficManagerProfile` . Den här metoden har fördelen att kunna uppdatera fler än en slut punkt i en enda åtgärd.
2. Hämta Traffic Manager slut punkten med `Get-AzTrafficManagerEndpoint` , uppdatera slut punkts egenskaperna och spara ändringarna med `Set-AzTrafficManagerEndpoint` . Den här metoden är enklare eftersom den inte kräver indexering i slut punkts matrisen i profilen.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>Exempel 1: uppdatera slut punkter med `Get-AzTrafficManagerProfile` och `Set-AzTrafficManagerProfile`

I det här exemplet ändrar vi prioriteten för två slut punkter i en befintlig profil.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>Exempel 2: uppdatera en slut punkt med hjälp av `Get-AzTrafficManagerEndpoint` och `Set-AzTrafficManagerEndpoint`

I det här exemplet ändrar vi vikten för en enda slut punkt i en befintlig profil.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Aktivera och inaktivera slut punkter och profiler

Traffic Manager tillåter att enskilda slut punkter aktive ras och inaktive ras, samt möjliggör aktivering och inaktive ring av hela profiler.
Dessa ändringar kan göras genom att hämta/uppdatera/ange slut punkten eller profil resurserna. För att förenkla dessa vanliga åtgärder stöds de också via dedikerade cmdletar.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Exempel 1: Aktivera och inaktivera en Traffic Manager-profil

Om du vill aktivera en Traffic Manager profil använder du `Enable-AzTrafficManagerProfile` . Profilen kan anges med ett profil objekt. Profile-objektet kan skickas via pipelinen eller med hjälp av parametern-TrafficManagerProfile. I det här exemplet anger vi profilen med namnet på profilen och resurs gruppen.

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Så här inaktiverar du en Traffic Manager profil:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Du uppmanas att bekräfta med cmdleten Disable-AzTrafficManagerProfile. Den här uppmaningen kan ignoreras med parametern-Force.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Exempel 2: Aktivera och inaktivera en Traffic Manager-slutpunkt

Om du vill aktivera en Traffic Manager-slutpunkt använder du `Enable-AzTrafficManagerEndpoint` . Det finns två sätt att ange slut punkten

1. Använda ett TrafficManagerEndpoint-objekt som överförts via pipelinen eller med parametern-TrafficManagerEndpoint
2. Använd slut punkts namn, slut punkts typ, profil namn och resurs grupp namn:

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

På samma sätt kan du inaktivera en Traffic Manager slut punkt:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Som med `Disable-AzTrafficManagerProfile` `Disable-AzTrafficManagerEndpoint` uppmanas cmdleten att bekräfta. Den här uppmaningen kan ignoreras med parametern-Force.

## <a name="delete-a-traffic-manager-endpoint"></a>Ta bort en Traffic Manager-slutpunkt

Om du vill ta bort enskilda slut punkter använder du `Remove-AzTrafficManagerEndpoint` cmdleten:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Den här cmdleten efterfrågar en bekräftelse. Den här uppmaningen kan ignoreras med parametern-Force.

## <a name="delete-a-traffic-manager-profile"></a>Ta bort en Traffic Manager profil

Om du vill ta bort en Traffic Manager profil använder du `Remove-AzTrafficManagerProfile` cmdleten och anger namnet på profilen och resurs gruppen:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Den här cmdleten efterfrågar en bekräftelse. Den här uppmaningen kan ignoreras med parametern-Force.

Profilen som ska tas bort kan också anges med ett profil objekt:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

Den här sekvensen kan också vara skickas:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Nästa steg

[Traffic Manager övervakning](traffic-manager-monitoring.md)

[Prestandaöverväganden för Traffic Manager](traffic-manager-performance-considerations.md)
