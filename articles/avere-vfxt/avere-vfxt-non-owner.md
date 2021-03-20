---
title: Aver vFXT-lösning för icke-ägare – Azure
description: Lösning för att tillåta användare utan prenumerations ägare behörighet att distribuera AVERT vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 0d9b1060ee35af6cbc2e1b95b0f7813072c52d2e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85505383"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Auktorisera icke-ägare för att distribuera Avere vFXT

Dessa instruktioner är en lösning som gör att en användare utan prenumerations ägare kan skapa ett AVERT vFXT för Azure-systemet.

(Det rekommenderade sättet att distribuera det Avera vFXT systemet är att låta en användare med ägar behörighet utföra stegen som beskrivs i [förbereda för att skapa ett AVERT vFXT](avere-vfxt-prereqs.md).)  

I lösningen ingår att skapa ytterligare en åtkomst roll som ger användare behörighet att installera klustret. Rollen måste skapas av en prenumerations ägare, och en ägare måste tilldela den till lämpliga användare.

En prenumerations ägare måste också [acceptera användnings villkoren](avere-vfxt-prereqs.md) för den Avera vFXT Marketplace-avbildningen.

> [!IMPORTANT]
> Alla dessa steg måste utföras av en användare med ägar behörighet för den prenumeration som ska användas för klustret.

1. Kopiera dessa rader och spara dem i en fil (till exempel `averecreatecluster.json` ). Använd ditt prenumerations-ID i `AssignableScopes` instruktionen.

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",

           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. Kör det här kommandot för att skapa rollen:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Exempel:

    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Tilldela den här rollen till den användare som ska skapa klustret:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

När du har slutfört den här processen ger rollen alla användare följande behörigheter för prenumerationen:

* Skapa och konfigurera nätverks infrastrukturen
* Skapa kluster styrenheten
* Kör skript för att skapa kluster från kluster styrenheten för att skapa klustret
