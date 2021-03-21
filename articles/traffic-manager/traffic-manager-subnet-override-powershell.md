---
title: Åsidosättning av Azure Traffic Manager-undernät med Azure PowerShell | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Traffic Manager under näts åsidosättande används för att åsidosätta Dirigerings metoden för en Traffic Manager profil för att dirigera trafik till en slut punkt baserat på IP-adressen för slutanvändare via fördefinierade IP-intervall till slut punkts mappningar med hjälp av Azure PowerShell.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 7dd7f43044a9643eb7e9d5296dfb209e425d5fb6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504791"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Traffic Manager under näts Åsidosätt med Azure PowerShell

Med Traffic Manager under näts åsidosättning kan du ändra routningsmetod för en profil.  Att lägga till en åsidosättning dirigerar trafik baserat på slutanvändarens IP-adress med ett fördefinierat IP-intervall för slut punkts mappning. 

## <a name="how-subnet-override-works"></a>Hur under näts åsidosättning fungerar

När under näts åsidosättningar läggs till i en Traffic Manager-profil, kontrollerar Traffic Manager först om det finns en under näts åsidosättande för slutanvändarens IP-adress. Om en hittas dirigeras användarens DNS-fråga till motsvarande slut punkt.  Om det inte går att hitta någon mappning, kommer Traffic Manager att återgå till profilens ursprungliga routningsmetod. 

IP-adressintervall kan anges som antingen CIDR-intervall (till exempel 1.2.3.0/24) eller som adress intervall (till exempel 1.2.3.4-5.6.7.8). De IP-intervall som är associerade med varje slut punkt måste vara unika för den slut punkten. Överlappande IP-intervall mellan olika slut punkter gör att profilen avvisas av Traffic Manager.

Det finns två typer av vägvals profiler som stöder under näts åsidosättningar:

* **Geografisk** -om Traffic Manager hittar en under näts ÅSIDOSÄTTNING för DNS-FRÅGAns IP-adress, kommer den att dirigera frågan till slut punkten oavsett hur hälsan är för slut punkten.
* **Prestanda** – om Traffic Manager hittar en under näts ÅSIDOSÄTTNING för DNS-FRÅGAns IP-adress dirigerar den bara trafiken till slut punkten om den är felfri.  Traffic Manager återgår till heuristiken för prestanda routning om det inte går att åsidosätta slut punkten för under nätet.

## <a name="create-a-traffic-manager-subnet-override"></a>Skapa en Traffic Manager under näts åsidosättning

Om du vill skapa en åsidosättning för Traffic Manager under nätet kan du använda Azure PowerShell för att lägga till undernät för åsidosättningen i Traffic Manager slut punkten.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra kommandona som följer i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn behöver du Azure PowerShell-modulen, 1.0.0 eller senare. Du kan köra `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att logga in på Azure.


1. **Hämta Traffic Manager slut punkten:**

    Om du vill aktivera åsidosättande av undernät hämtar du den slut punkt som du vill lägga till åsidosättningen i och lagrar den i en variabel med hjälp av [Get-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint).

    Ersätt namn, PROFILENAME och ResourceGroupName med värdena för den slut punkt som du ändrar.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Lägg till IP-adressintervallet till slut punkten:**
    
    Om du vill lägga till IP-adressintervallet till slut punkten använder du [Add-AzTrafficManagerIpAddressRange](/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange) för att lägga till intervallet.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    När intervallen har lagts till använder du [set-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint) för att uppdatera slut punkten.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
Borttagning av IP-adressintervall kan slutföras med hjälp av [Remove-AzTrafficManagerIpAddressRange](/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange).

1.  **Hämta Traffic Manager slut punkten:**

    Om du vill aktivera åsidosättande av undernät hämtar du den slut punkt som du vill lägga till åsidosättningen i och lagrar den i en variabel med hjälp av [Get-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint).

    Ersätt namn, PROFILENAME och ResourceGroupName med värdena för den slut punkt som du ändrar.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Ta bort IP-adressintervallet från slut punkten:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     När intervallen har tagits bort använder du [set-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint) för att uppdatera slut punkten.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du Traffic Manager [metoder för trafik cirkulation](traffic-manager-routing-methods.md).

Lär dig mer om [routningsmetod för under näts trafik](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)