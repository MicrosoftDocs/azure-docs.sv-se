---
title: Resource Manager mallexempel – Azure Front Door
description: Information om exempelmallar Azure Resource Manager för Azure Front Door.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 04/16/2021
ms.openlocfilehash: e939aec8f1cf5fa615904bb81bad82ea3cf22c61
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587484"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Azure Resource Manager mallar för Azure Front Door

> [!Note]
> Den här dokumentationen är Azure Front Door Standard/Premium (förhandsversion). Letar du efter information om Azure Front Door? Visa [här.](../front-door-overview.md)

Följande tabell innehåller länkar till Azure Resource Manager mallar för Azure Front Door, med referensarkitekturer, inklusive andra Azure-tjänster.

| Exempel | Beskrivning |
|-|-|
| [Front Door (snabbstart)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium/) | Skapar en grundläggande Front Door inklusive en slutpunkt, ursprungsgrupp, ursprung och väg.  |
| [Regeluppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Skapar en Front Door-profil och regeluppsättning.  |
| [WAF-princip med hanterad regeluppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-waf-managed/) | Skapar en Front Door-profil och WAF med hanterad regeluppsättning.  |
| [WAF-princip med anpassad regel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-waf-custom/) | Skapar en Front Door-profil och WAF med anpassad regel.  |
|**App Service ursprung**| **Beskrivning** |
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | Skapar en App Service-app med en offentlig slutpunkt och en Front Door profil.  |
| [App Service med Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | Skapar en App Service-app med en privat slutpunkt och en Front Door profil.  |
| [App Service miljö med Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | Skapar en App Service miljö, en app med en privat slutpunkt och en Front Door profil.  |
|**Azure Functions ursprung**| **Beskrivning** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | Skapar en Azure Functions-app med en offentlig slutpunkt och en Front Door profil.  |
| [Azure Functions med Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | Skapar en Azure Functions-app med en privat slutpunkt och en Front Door profil.  |
|**API Management ursprung**| **Beskrivning** |
| [API Management (extern)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | Skapar en API Management instans med extern VNet-integrering och en Front Door profil.  |
|**Lagrings ursprung**| **Beskrivning** |
| [Statisk lagringswebbplats](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | Skapar ett Azure Storage och en statisk webbplats med en offentlig slutpunkt och en Front Door profil.  |
| [Lagringsblobar med Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | Skapar ett Azure Storage-konto och blobcontainer med en privat slutpunkt och en Front Door profil.  |
|**Application Gateway ursprung**| **Beskrivning** |
| [Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-application-gateway-public) | Skapar en Application Gateway och en Front Door profil. |
|**Ursprung för virtuell dator**| **Beskrivning** |
| [Virtuell dator med Private Link tjänst](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-vm-private-link) | Skapar en virtuell dator Private Link tjänst och en Front Door profil. |
| | |
