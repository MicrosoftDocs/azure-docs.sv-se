---
title: Registrera en peering service Preview-anslutning med hjälp av Azure CLI
description: Lär dig hur du registrerar en peering service-anslutning med hjälp av Azure CLI
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: e7b696ba052b2aca9e14628327c07275845607ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540594"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Registrera en peering service-anslutning med hjälp av Azure CLI

Azure peering-tjänsten är en nätverks tjänst som förbättrar kund anslutningen till Microsofts moln tjänster, till exempel Microsoft 365, Dynamics 365, SaaS-tjänster (Software as a Service), Azure eller Microsoft-tjänster som är tillgängliga via det offentliga Internet. I den här artikeln får du lära dig hur du registrerar en peering service-anslutning med hjälp av Azure CLI.

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Kör [az version](/cli/azure/reference-index#az_version) om du vill hitta versionen och de beroende bibliotek som är installerade. Om du vill uppgradera till den senaste versionen kör du [az upgrade](/cli/azure/reference-index#az_upgrade).

## <a name="prerequisites"></a>Förutsättningar 

Du måste ha följande:

### <a name="azure-account"></a>Azure-konto

Du måste ha ett giltigt och aktivt Microsoft Azure-konto. Detta konto krävs för att konfigurera peering-tjänstens anslutning. Peering-tjänsten är en resurs i Azure-prenumerationer.

### <a name="connectivity-provider"></a>Anslutningsleverantör

Du kan arbeta med en Internet-leverantör eller en Internet Exchange-partner för att få peering-tjänsten att ansluta ditt nätverk till Microsoft-nätverket.

Se till att anslutnings leverantörerna är partner kopplade till Microsoft.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

### <a name="1-select-your-subscription"></a>1. Välj din prenumeration

Välj den prenumeration som du vill registrera peering-tjänstens anslutning för.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

Om du inte redan har en resurs grupp måste du skapa en innan du registrerar peering-tjänstens anslutning. Du kan skapa en resurs grupp genom att köra följande kommando:

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2. registrera din prenumeration med resurs leverantören och funktions flaggan

Innan du fortsätter med stegen för att registrera peering-tjänstens anslutning med hjälp av Azure CLI registrerar du prenumerationen med resurs leverantören och funktions flaggan med hjälp av Azure CLI. Azure CLI-kommandon anges här:

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3. registrera peering-tjänstens anslutning

Registrera peering-tjänstens anslutning med hjälp av följande kommando uppsättningar via Azure CLI. I det här exemplet registreras peering-tjänstens anslutning med namnet myPeeringService.

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4. registrera prefixet

Registrera prefixet som tillhandahålls av anslutnings leverantören genom att köra följande kommandon via Azure CLI. Det här exemplet registrerar prefixet prefixet med namnet prefix.

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>Nästa steg

- Mer information om peering service-anslutning finns i [peering service-anslutning](connection.md).
- Om du vill veta mer om telemetri för peering service-anslutning läser du [telemetri för peering service-anslutning](connection-telemetry.md).
- För att mäta telemetri, se [mått på telemetri för anslutning](measure-connection-telemetry.md).
- Information om hur du registrerar anslutningen med hjälp av Azure PowerShell finns i [Registrera en peering-tjänst anslutning-Azure PowerShell](powershell.md).
- Information om hur du registrerar anslutningen med hjälp av Azure Portal finns i [Registrera en peering-tjänst anslutning-Azure Portal](azure-portal.md).
