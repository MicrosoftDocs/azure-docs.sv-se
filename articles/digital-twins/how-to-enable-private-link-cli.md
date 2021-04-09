---
title: Aktivera privat åtkomst med privat länk (för hands version) – CLI
titleSuffix: Azure Digital Twins
description: Se Aktivera privat åtkomst för Azure Digitals dubbla lösningar med privat länk med hjälp av Azure CLI.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5bd7ffda508980a9a56d86037887fc53a0fed640
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102202951"
---
# <a name="enable-private-access-with-private-link-preview-azure-cli"></a>Aktivera privat åtkomst med privat länk (för hands version): Azure CLI

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

I den här artikeln beskrivs olika sätt att [aktivera privat länk med en privat slut punkt för en digital Azure-instans](concepts-security.md#private-network-access-with-azure-private-link-preview) (för närvarande i för hands version). Genom att konfigurera en privat slut punkt för Azure Digital-instansen kan du skydda Azure Digitals-instansen och eliminera offentlig exponering, samt undvika data exfiltrering från ditt [Azure-Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md).

Den här artikeln vägleder dig genom processen med hjälp av [**Azure CLI**](/cli/azure/what-is-azure-cli).

Här följer de steg som beskrivs i den här artikeln: 
1. Aktivera en privat länk och konfigurera en privat slut punkt för en Azure Digital-instansen.
1. Inaktivera eller aktivera offentliga nätverks åtkomst flaggor för att begränsa API-åtkomst till privata länk anslutningar.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan konfigurera en privat slut punkt behöver du ett [**Azure-Virtual Network (VNet)**](../virtual-network/virtual-networks-overview.md) där slut punkten kan distribueras. Om du inte redan har ett VNet, kan du följa ett av Azure Virtual Network [snabb starter](../virtual-network/quick-create-portal.md) för att konfigurera detta.

## <a name="manage-private-endpoints-for-an-azure-digital-twins-instance"></a>Hantera privata slut punkter för en digital Azure-instans 

När du använder [Azure CLI](/cli/azure/what-is-azure-cli)kan du konfigurera privata slut punkter med privat länk på en digital Azure-instans som redan finns (det går inte att lägga till den som en del av skapandet av en instans). Sedan kan du fortsätta att visa och hantera dem via ytterligare CLI-kommandon. 

>[!TIP]
> Du kan också skapa en privat länk slut punkt via tjänsten för privata länkar i stället för via din Azure Digital-instansen. Detta ger också samma konfigurations alternativ och samma slut resultat.
>
> Mer information om hur du konfigurerar resurser för privata länkar finns i dokumentationen för privat länk för [Azure Portal](../private-link/create-private-endpoint-portal.md), [Azure CLI](../private-link/create-private-endpoint-cli.md), [arm-mallar](../private-link/create-private-endpoint-template.md)eller [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Lägg till en privat slut punkt i en befintlig instans

Om du vill skapa en privat slut punkt och länka den till en Azure Digitals-instans använder du kommandot [**AZ Network Private-Endpoint Create**](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) . Identifiera den digitala Azure-instansen genom att använda dess fullständigt kvalificerade ID i `--private-connection-resource-id` parametern.

Här är ett exempel som använder kommandot för att skapa en privat slut punkt, med endast de parametrar som krävs.

```azurecli-interactive
az network private-endpoint create --connection-name {private_link_service_connection} -n {name_for_private_endpoint} -g {resource_group} --subnet {subnet_ID} --private-connection-resource-id "/subscriptions/{subscription_ID}/resourceGroups/{resource_group}/providers/Microsoft.DigitalTwins/digitalTwinsInstances/{Azure_Digital_Twins_instance_name}" 
```

En fullständig lista över obligatoriska och valfria parametrar, samt fler exempel på hur du skapar privata slut punkter finns i [ **AZ Network Private-Endpoint Create** Reference documentation](/cli/azure/network/private-endpoint#az_network_private_endpoint_create).

### <a name="manage-private-endpoint-connections-on-the-instance"></a>Hantera privata slut punkts anslutningar på instansen

När en privat slut punkt har skapats för din Azure Digital-instansen kan du använda anslutnings kommandona [**AZ DT Network Private-Endpoint**](/cli/azure/ext/azure-iot/dt/network/private-endpoint/connection) för att fortsätta hantera privata slut punkts **anslutningar** med avseende på instansen. Åtgärderna omfattar:
* Visa en privat slut punkts anslutning
* Ange tillstånd för anslutning till privat slutpunkt
* Ta bort anslutningen till en privat slutpunkt
* Visa en lista över alla anslutningar för privata slut punkter för en instans

Mer information och exempel finns i [referens dokumentationen för **AZ DT Network-den privata slut punkten**](/cli/azure/ext/azure-iot/dt/network/private-endpoint).

### <a name="manage-other-private-link-information-on-an-azure-digital-twins-instance"></a>Hantera annan privat länk information på en digital Azure-instans

Du kan få ytterligare information om status för privata Länkar för din instans med kommandona [**AZ DT Network Private-Link**](/cli/azure/ext/azure-iot/dt/network/private-link) . Åtgärderna omfattar:
* Lista privata länkar som är associerade med en digital Azure-instans
* Visa en privat länk som är associerad med instansen

Mer information och exempel finns i [ **AZ DT Network-Link** Reference Document](/cli/azure/ext/azure-iot/dt/network/private-link).

## <a name="disable--enable-public-network-access-flags"></a>Inaktivera/aktivera offentliga nätverks åtkomst flaggor

Du kan konfigurera Azure Digitals-instansen för att neka alla offentliga anslutningar och bara tillåta anslutningar via privata slut punkter för att förbättra nätverks säkerheten. Den här åtgärden görs med en **offentlig nätverks åtkomst flagga**. 

Med den här principen kan du begränsa API-åtkomst till privata länk anslutningar. När den offentliga nätverks åtkomst flaggan är *inaktive rad*, kommer alla REST API anrop till data planet för Azure Digital-instansen från det offentliga molnet att returneras `403, Unauthorized` . När principen är inställd på *inaktive rad* och en begäran görs via en privat slut punkt, kommer API-anropet att lyckas.

Den här artikeln visar hur du uppdaterar värdet för nätverks flaggan med hjälp av antingen [Azure CLI](/cli/azure/) eller [kommando verktyget ARMClient](https://github.com/projectkudu/ARMClient). Instruktioner för hur du gör det med Azure Portal finns i [Portal versionen](how-to-enable-private-link-portal.md) av den här artikeln.

### <a name="use-the-azure-cli"></a>Använda Azure CLI

I Azure CLI kan du inaktivera eller aktivera offentlig nätverks åtkomst genom att lägga till en `--public-network-access` parameter till `az dt create` kommandot. Även om det här kommandot kan användas för att skapa en ny instans kan du använda det för att redigera egenskaperna för en befintlig instans genom att ange namnet på en instans som redan finns. (Mer information om det här kommandot finns i [referens dokumentationen](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create) eller de [allmänna anvisningarna för att konfigurera en digital Azure-instans](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)).

Om du vill **inaktivera** offentlig nätverks åtkomst för en Azure Digital-instansen använder du `--public-network-access` parametern så här:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Disabled
```

Om du vill **Aktivera** offentlig nätverks åtkomst på en instans där den är inaktive rad, använder du följande liknande kommando:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Enabled
```

### <a name="usethe-armclientcommand-tool"></a>Använda kommando verktyget ARMClient 

Med [kommando verktyget ARMClient](https://github.com/projectkudu/ARMClient)aktive ras eller inaktive ras offentliga nätverks åtkomst med hjälp av kommandona nedan. 

Så här **inaktiverar** du offentlig nätverks åtkomst:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

Så här **aktiverar** du offentlig nätverks åtkomst:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>Nästa steg

Läs mer om privat länk för Azure: 
* [*Vad är Azure Private Link service?*](../private-link/private-link-service-overview.md)