---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 3c9679f3d66d58c7a6c847b54c84438c979ecd39
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936874"
---
[Azure privat slutpunkt](../articles/private-link/private-endpoint-overview.md) är ett nätverksgränssnitt som ansluter dig privat och säkert till en tjänst som drivs av Azure Private Link.  Privat slutpunkt använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt tar in tjänsten i ditt virtuella nätverk.

Du kan använda privat slut punkt för dina funktioner som finns i [Premium](../articles/azure-functions/functions-premium-plan.md) -och [App Service](../articles/azure-functions/dedicated-plan.md) -planerna.

När du skapar en inkommande privat slut punkts anslutning för functions måste du också ha en DNS-post för att matcha den privata adressen.  Som standard skapas en privat DNS-post åt dig när du skapar en privat slut punkt med hjälp av Azure Portal.

Mer information finns i [använda privata slut punkter för Web Apps](../articles/app-service/networking/private-endpoint.md).