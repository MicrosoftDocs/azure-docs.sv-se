---
title: 'Snabb start: skapa en profil för hög tillgänglighet för program – Azure PowerShell – Azure Traffic Manager'
description: Den här snabb starten beskriver hur du skapar en Traffic Manager-profil för att skapa ett webb program med hög tillgänglighet.
services: traffic-manager
author: duongau
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: duau
ms.openlocfilehash: 9b34a17cc9add0bed4bffb7677aa81bb17f3125b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91631570"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Snabb start: skapa en Traffic Manager-profil för ett webb program med hög tillgänglighet med hjälp av Azure PowerShell

I den här snabbstarten beskrivs hur du skapar en Traffic Manager-profil som ger hög tillgänglighet för din webbapp.

I den här snabb starten skapar du två instanser av ett webb program. Var och en av dem körs i olika Azure-regioner. Du skapar Traffic Manager-profil baserat på [slutpunktsprioritet](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profilen dirigerar användartrafik till den primära plats som kör webbappen. Traffic Manager övervakar kontinuerligt webbappen. Om den primära platsen inte är tillgänglig ger den automatisk redundans till säkerhetskopieringsplatsen.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln version 5.4.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resurs grupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive

# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager profil med [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) som dirigerar användar trafik baserat på slut punkts prioritet.

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>Skapa Web Apps

I den här snabb starten behöver du två instanser av ett webb program som distribueras i två olika Azure-regioner (*västra USA* och *östra USA*). Var och en av dessa kommer att fungera som primär- och redundansslutpunkter för Traffic Manager.

### <a name="create-web-app-service-plans"></a>Skapa webb App Services planer
Skapa webb program tjänst planer med [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) för de två instanser av webb programmet som du distribuerar i två olika Azure-regioner.

```azurepowershell-interactive

# Variables
$App1Name="AppServiceTM1$Random"
$App2Name="AppServiceTM2$Random"
$Location1="WestUS"
$Location2="EastUS"

# Create an App service plan
New-AzAppservicePlan -Name "$App1Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "$App2Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>Skapa en webbapp i App Service planen
Skapa två instanser av webb programmet med [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) i App Service planer i regionerna *västra USA* och *östra USA* Azure.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter
Lägg till de två Web Apps som Traffic Manager slut punkter med [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) till Traffic Manager-profilen enligt följande:
- Lägg till webbappen som finns i regionen *USA, västra* Azure som den primära slut punkten för att dirigera all användar trafik. 
- Lägg till webbappen som finns i Azure-regionen *USA, östra* som redundansväxlingen. När den primära slutpunkten inte är tillgänglig dirigeras trafiken automatiskt till redundansslutpunkten.

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "$App1Name-$Location1" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "$App2Name-$Location2" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Testa Traffic Manager-profil

I det här avsnittet kontrollerar domännamnet för Traffic Manager-profilen. Du kan även konfigurera den primära slutpunkten till att inte vara tillgänglig. Slutligen ser du att webbappen fortfarande är tillgänglig. Det beror på att Traffic Manager skickar trafiken till redundansslutpunkten.

### <a name="determine-the-dns-name"></a>Bestämma DNS-namnet

Bestäm DNS-namnet för Traffic Manager profilen med [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Kopiera värdet **RelativeDnsName** . DNS-namnet på din Traffic Manager-profil är *http://<* relativednsname *>. trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken
1. I en webbläsare anger du DNS-namnet för din Traffic Manager profil (*http://<* relativednsname *>. trafficmanager.net*) för att Visa webbappens standard webbplats.

    > [!NOTE]
    > I det här snabbstartsscenariot dirigeras alla begäranden till den primära slutpunkten. Den är inställd på **Prioritet 1**.
2. Om du vill visa Traffic Manager redundans i praktiken inaktiverar du den primära platsen med [disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Kopiera DNS-namnet för din Traffic Manager profil (*http://<* relativednsname *>. trafficmanager.net*) om du vill visa webbplatsen i en ny webbläsarsession.
4. Kontrollera att webbappen fortfarande är tillgänglig.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar tar du bort resurs grupperna, webb programmen och alla relaterade resurser med hjälp av [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Traffic Manager profil som ger hög tillgänglighet för ditt webb program. Om du vill veta mer om att dirigera trafik kan du gå vidare till Traffic Manager-självstudierna.

> [!div class="nextstepaction"]
> [Självstudier för Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
