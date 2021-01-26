---
title: Exempel på Azure CLI-skriptdistribution
description: Så här skapar du ett säkert Service Fabric Linux-kluster i Azure med hjälp av kommando rads gränssnittet för Azure (CLI).
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 675f6b9ac3ebb05539432b5febc279275f9de7f6
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785776"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>skapa ett säkert Service Fabric Linux-kluster i Azure

Med det här kommandot skapar du ett självsignerat certifikat, lägger till det i ett nyckelvalv och hämtar certifikatet lokalt.  Det nya certifikatet används för att skydda klustret när distribueras.  Du kan också använda ett befintligt certifikat i stället för att skapa ett nytt.  Oavsett hur du gör måste certifikatets ämnesnamn överensstämma med den domän du använder för att få åtkomst till Service Fabric-klustret. Den här matchningen krävs för att tillhandahålla TLS för klustrets slut punkter för HTTPS-hantering och Service Fabric Explorer. Det går inte att hämta ett TLS/SSL-certifikat från en certifikat utfärdare för `.cloudapp.azure.com` domänen. Du måste skaffa ett anpassat domännamn för ditt kluster. När du begär ett certifikat från en certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamn du använder för klustret.

Installera vid behov [Azure CLI](/en-us/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

[!code-sh[main](../../../cli_scripts/service-fabric/create-cluster/create-cluster.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptet har körts kan följande kommando användas för att ta bort resursgruppen, klustret och alla relaterade resurser.

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az sf cluster create](/cli/azure/sf/cluster) | Skapar ett nytt Service Fabric-kluster.  |

## <a name="next-steps"></a>Nästa steg

Fler Service Fabric CLI-exempel för Azure Service Fabric finns i [Service Fabric CLI-exempel](../samples-cli.md).
