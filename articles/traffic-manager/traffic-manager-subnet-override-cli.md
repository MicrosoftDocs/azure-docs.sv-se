---
title: Azure Traffic Manager åsidosättning av undernät med Hjälp av Azure CLI | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur åsidosättning av Traffic Manager-undernät kan användas för att åsidosätta routningsmetoden för en Traffic Manager-profil för att dirigera trafik till en slutpunkt baserat på slutanvändares IP-adress via fördefinierade IP-intervall till slutpunktsmappningar.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: fab167884d9060edc4f626d3ee05fa0b23389d92
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767807"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Traffic Manager åsidosättning av undernät med Hjälp av Azure CLI

Traffic Manager åsidosättning av undernät kan du ändra routningsmetoden för en profil.  Tillägget av en åsidosättning dirigerar trafik baserat på slutanvändarens IP-adress med ett fördefinierat IP-intervall till slutpunktsmappning. 

## <a name="how-subnet-override-works"></a>Så här fungerar åsidosättning av undernät

När åsidosättningar av undernät läggs till i en Traffic Manager-profil Traffic Manager först om det finns en åsidosättning av undernätet för slutanvändarens IP-adress. Om en sådan hittas dirigeras användarens DNS-fråga till motsvarande slutpunkt.  Om ingen mappning hittas Traffic Manager till profilens ursprungliga routningsmetod. 

IP-adressintervallen kan antingen anges som CIDR-intervall (till exempel 1.2.3.0/24) eller som adressintervall (till exempel 1.2.3.4-5.6.7.8). IP-intervallen som är associerade med varje slutpunkt måste vara unika för den slutpunkten. Eventuella överlappningar av IP-intervall mellan olika slutpunkter gör att profilen avvisas av Traffic Manager.

Det finns två typer av routningsprofiler som stöder åsidosättningar av undernät:

* **Geografisk** – Traffic Manager hittar en åsidosättning av undernätet för DNS-frågans IP-adress dirigeras frågan till slutpunkten oavsett slutpunktens hälsotillstånd.
* **Prestanda** – Traffic Manager hittar en åsidosättning av undernätet för DNS-frågans IP-adress dirigerar den bara trafiken till slutpunkten om den är felfri.  Traffic Manager kommer att gå tillbaka till heuristiken för prestandadirigering om slutpunkten för åsidosättning av undernät inte är felfri.

## <a name="create-a-traffic-manager-subnet-override"></a>Skapa en Traffic Manager åsidosättning av undernät

Om du vill Traffic Manager åsidosätta ett undernät kan du använda Azure CLI för att lägga till undernäten för åsidosättningen till Traffic Manager slutpunkten.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Uppdatera Traffic Manager med åsidosättning av undernät.
Använd Azure CLI för att uppdatera slutpunkten [med az network traffic-manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_update).

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

Du kan ta bort IP-adressintervallen genom att köra [slutpunktsuppdateringen az network traffic-manager](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_update) med **alternativet --remove.**

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Traffic Manager [trafikroutningsmetoder](traffic-manager-routing-methods.md).

Lär dig mer om [trafikroutningsmetoden för undernät](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)