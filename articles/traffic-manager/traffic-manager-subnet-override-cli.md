---
title: Åsidosättning av Azure Traffic Manager-undernät med hjälp av Azure CLI | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Traffic Manager över gångs punkt kan användas för att åsidosätta Dirigerings metoden för en Traffic Manager profil för att dirigera trafik till en slut punkt baserat på IP-adressen för slutanvändare via fördefinierade IP-intervall till slut punkts mappningar.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 2e289728c7fde9b98256d079d45067aba1d4d805
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211336"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Traffic Manager under näts Åsidosätt med Azure CLI

Med Traffic Manager under näts åsidosättning kan du ändra routningsmetod för en profil.  Att lägga till en åsidosättning dirigerar trafik baserat på slutanvändarens IP-adress med ett fördefinierat IP-intervall för slut punkts mappning. 

## <a name="how-subnet-override-works"></a>Hur under näts åsidosättning fungerar

När under näts åsidosättningar läggs till i en Traffic Manager-profil, kontrollerar Traffic Manager först om det finns en under näts åsidosättande för slutanvändarens IP-adress. Om en hittas dirigeras användarens DNS-fråga till motsvarande slut punkt.  Om ingen mappning hittas kommer Traffic Manager att återgå till profilens ursprungliga routningsmetod. 

IP-adressintervall kan anges som antingen CIDR-intervall (till exempel 1.2.3.0/24) eller som adress intervall (till exempel 1.2.3.4-5.6.7.8). De IP-intervall som är associerade med varje slut punkt måste vara unika för den slut punkten. Överlappande IP-intervall mellan olika slut punkter gör att profilen avvisas av Traffic Manager.

Det finns två typer av vägvals profiler som stöder under näts åsidosättningar:

* **Geografisk** -om Traffic Manager hittar en under näts ÅSIDOSÄTTNING för DNS-FRÅGAns IP-adress, kommer den att dirigera frågan till slut punkten oavsett hur hälsan är för slut punkten.
* **Prestanda** – om Traffic Manager hittar en under näts ÅSIDOSÄTTNING för DNS-FRÅGAns IP-adress dirigerar den bara trafiken till slut punkten om den är felfri.  Traffic Manager återgår till heuristiken för prestanda routning om det inte går att åsidosätta slut punkten för under nätet.

## <a name="create-a-traffic-manager-subnet-override"></a>Skapa en Traffic Manager under näts åsidosättning

Om du vill skapa en åsidosättning för Traffic Manager under nätet kan du använda Azure CLI för att lägga till undernät för åsidosättningen i Traffic Manager-slutpunkten.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Uppdatera Traffic Manager slut punkten med under nätets åsidosättning.
Använd Azure CLI för att uppdatera din slut punkt med [AZ Network Traffic-Manager Endpoint Update](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update).

```azurecli-interactive
### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints
```

Du kan ta bort IP-adressintervall genom att köra [slut punkten för AZ Network Traffic-Manager](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update) med alternativet **--Remove** .

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du Traffic Manager [metoder för trafik cirkulation](traffic-manager-routing-methods.md).

Lär dig mer om [routningsmetod för under näts trafik](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)