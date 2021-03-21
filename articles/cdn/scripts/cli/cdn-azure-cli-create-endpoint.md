---
title: Skapa en profil och slut punkt för Azure Content Delivery Network (CDN) med Azure CLI
description: Exempel skript för Azure CLI för att skapa en Azure CDN profil, slut punkt, start grupp, ursprung och anpassad domän.
author: asudbring
ms.author: allensu
manager: danielgi
ms.date: 03/09/2021
ms.topic: sample
ms.service: azure-cdn
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce01c1f851a5dbaedc85d848b12bf0b0831a16ef
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726934"
---
# <a name="create-an-azure-cdn-profile-and-endpoint-using-the-azure-cli"></a>Skapa en Azure CDN profil och slut punkt med hjälp av Azure CLI

Som ett alternativ till Azure Portal kan du använda följande exempel på Azure CLI-skript för att hantera följande CDN-åtgärder:

- Skapa en CDN-profil.
- Skapa en CDN-slutpunkt.
- Skapa en CDN-Origin-grupp och gör den till standard gruppen.
- Skapa ett CDN-ursprung.
- Skapa en anpassad domän och Aktivera HTTPS.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-scripts"></a>Exempelskript

Om du inte redan har en resurs grupp för din CDN-profil skapar du den med kommandot `az group create` :

```azurecli
# Create a resource group to use for the CDN.
az group create --name MyResourceGroup --location eastus

```

Följande Azure CLI-skript skapar en CDN-profil och en CDN-slutpunkt:

```azurecli
# Create a CDN profile.
az cdn profile create --resource-group MyResourceGroup --name MyCDNProfile --sku Standard_Microsoft

# Create a CDN endpoint.
az cdn endpoint create --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --origin www.contoso.com

```

Följande Azure CLI-skript skapar en CDN Origin-grupp, anger standard ursprungs gruppen för en slut punkt och skapar ett nytt ursprung:

```azurecli
# Create an origin group.
az cdn origin-group create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyOriginGroup --origins origin-0

# Make the origin group the default group of an endpoint.
az cdn endpoint update --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --default-origin-group MyOriginGroup
                           
# Create another origin for an endpoint.
az cdn origin create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name origin-1 --host-name example.contoso.com

```

Följande Azure CLI-skript skapar en anpassad CDN-domän och aktiverar HTTPS. Innan du kan associera en anpassad domän med en Azure CDN slut punkt måste du först skapa en post för kanoniskt namn (CNAME) med Azure DNS eller din DNS-Provider för att peka på CDN-slutpunkten. Mer information finns i [skapa en CNAME DNS-post](../../../cdn/cdn-map-content-to-custom-domain.md#create-a-cname-dns-record).

```azurecli
# Associate a custom domain with an endpoint.
az cdn custom-domain create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain --hostname www.example.com

# Enable HTTPS on the custom domain.
az cdn custom-domain enable-https --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain

```

## <a name="clean-up-resources"></a>Rensa resurser

När du har kört exempel skripten använder du följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```azurecli
# Delete the resource group.
az group delete --name MyResourceGroup

```

## <a name="azure-cli-commands-used-in-this-article"></a>Azure CLI-kommandon som används i den här artikeln

- [AZ CDN-slutpunkt skapa](/cli/azure/cdn/endpoint#az_cdn_endpoint_create)
- [uppdatering av AZ CDN-slutpunkt](/cli/azure/cdn/endpoint#az_cdn_endpoint_update)
- [AZ CDN Origin Create](/cli/azure/cdn/origin#az_cdn_origin_create)
- [AZ CDN Origin-Group Create](/cli/azure/cdn/origin-group#az_cdn_origin_group_create)
- [Skapa AZ CDN Profile Create](/cli/azure/cdn/profile#az_cdn_profile_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [AZ CDN Custom-Domain Create](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_create)
- [AZ CDN Custom-Domain Enable-https](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_enable_https)
