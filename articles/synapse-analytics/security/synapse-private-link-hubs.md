---
title: Ansluta till en Synapse Studio med privata länkar
description: I den här artikeln får du lära dig hur du ansluter till din Azure Synapse Studio med privata länkar
author: NanditaV
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: d49868199d8f9f2da97f08dd06f29afd8f553bd9
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97586983"
---
# <a name="connect-to-azure-synapse-studio-using-azure-private-link-hubs"></a>Ansluta till Azure Synapse Studio med hjälp av Azure Private Link Hubs 

I den här artikeln förklaras hur privata Länkar för privata länk hubbar i Azure Synapse Analytics används för att på ett säkert sätt ansluta till Synapse Studio. 

## <a name="azure-private-link-hubs"></a>Azure Private Link Hubs 
Du kan ansluta säkert till Azure Synapse Studio från ditt virtuella Azure-nätverk med hjälp av privata länkar. Azure Synapse Analytics Private Link Hub är Azure-resurser som fungerar som kopplingar mellan ditt skyddade nätverk och Synapse Studio-webbmiljön. 

Det finns två steg för att ansluta till Synapse Studio med hjälp av privata länkar. Först måste du skapa en resurs för privata länk hubbar. För det andra måste du skapa en privat slut punkt från ditt virtuella Azure-nätverk till den här privata länk hubben. Du kan sedan använda privata slut punkter för att kommunicera säkert med Synapse Studio. Du måste integrera de privata slut punkterna med DNS-lösningen, antingen din lokala lösning eller Azure Privat DNS. 

## <a name="azure-private-links-hubs-and-azure-synapse-studio"></a>Hubbar för Azure privata länkar och Azure Synapse Studio
Du kan använda en enda Azure Synapse-resurs för privata Länkar för att ansluta till alla dina Azure Synapse Analytics-arbetsytor med Azure Synapse Studio. Arbets ytorna behöver inte finnas i samma region som Azure Synapse Private Link Hub. Azure Synapse Private Link Hub-resursen kan också användas för anslutningar till Synapse-arbetsytor i olika prenumerationer eller Azure AD-klienter.

Du kan skapa en privat länks hubb genom att söka efter *Synapse privata länk hubbar* i Azure Portal och välja **Azure Synapse Analytics (privata länk hubbar)** från tjänster. Följ stegen i guiden för hur du [ansluter till arbets ytans resurser från ett begränsat nätverk](./how-to-connect-to-workspace-from-restricted-network.md) för mer information.

>[!NOTE]
>Privata länk NAV är avsedda för att på ett säkert sätt läsa in det statiska innehållet Synapse Studio via privata länkar. Du måste skapa **separata, privata slut punkter** till de resurser som du vill ansluta till i arbets ytan, till exempel etablerade/dedikerade SQL-pooler eller Spark-pooler. 

## <a name="azure-private-links-hubs-and-azure-virtual-network"></a>Hubbar för Azure privat länkar och Azure Virtual Network
Du måste ansluta ditt virtuella Azure-nätverk till Synapse-resursen för privat länk för att skydda den slutpunkt-till-slutpunkt-anslutningen till Synapse Studio. För detta måste du skapa en privat slut punkt från ditt virtuella nätverk till den privata länk Hub som du skapade. Du kan använda Azure Portal för din privata länk hubb och gå till avsnittet privat slut punkt. Välj "+ privat slut punkt" för att skapa en ny privat slut punkt som ansluter till din privata länk hubb.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-private-endpoint.png" alt-text="Skärm bild som visar sidan anslutningar för privata slut punkter.":::

Se till att välja resurs typen "Microsoft. Synapse/privateLinkHubs" på fliken "resurs". :::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-resource-type.png" alt-text="skärm bild som visar sidan &quot;skapa en privat slut punkt&quot; med &quot;resurs typ&quot; markerad.":::

På fliken konfiguration väljer du "privatelink.azuresynapse.net" för Privat DNS zoner vid integrering med ditt virtuella nätverk och en privat DNS-zon.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-dns-zones.png" alt-text="Skapa en privat slut punkt till en privat länk hubb":::

## <a name="next-steps"></a>Nästa steg

[Ansluta till arbets ytans resurser från ett begränsat nätverk](./how-to-connect-to-workspace-from-restricted-network.md)

Läs mer om [hanterad arbets yta Virtual Network](./synapse-workspace-managed-vnet.md)

Läs mer om [hanterade privata slut punkter](./synapse-workspace-managed-private-endpoints.md)

[Skapa hanterade privata slut punkter till dina data källor](./how-to-create-managed-private-endpoints.md)

[Ansluta till Synapse-arbetsytan med privata slut punkter](./how-to-connect-to-workspace-with-private-links.md)

