---
title: Skapa & ta bort en användardefinierad hanterad identitet med hjälp av Azure Resource Manager
description: Steg för steg-anvisningar om hur du skapar och tar bort användarspecifika hanterade identiteter med hjälp av Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: acbeb8e0fc638955995cc5aaf00f58d40adc69a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98184886"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Skapa, Visa och ta bort en användardefinierad hanterad identitet med hjälp av Azure Resource Manager


Hanterade identiteter för Azure-resurser ger Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva ange autentiseringsuppgifter i koden. 

I den här artikeln skapar du en användardefinierad hanterad identitet med hjälp av en Azure Resource Manager.

Det går inte att visa och ta bort en användardefinierad hanterad identitet med hjälp av en Azure Resource Manager mall.  Se följande artiklar för att skapa och lista en användardefinierad hanterad identitet:

- [Lista användare-tilldelad hanterad identitet](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Ta bort användardefinierad hanterad identitet](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en tilldelad och användardefinierad hanterad identitet](overview.md#managed-identity-types)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="template-creation-and-editing"></a>Skapa och redigera mall

Precis som med Azure Portal och skript ger Azure Resource Manager mallar möjlighet att distribuera nya eller ändrade resurser som definieras av en Azure-resurs grupp. Det finns flera alternativ för att redigera och distribuera mallar, både lokalt och Portal-baserat, inklusive:

- Med hjälp av en [anpassad mall från Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), som gör att du kan skapa en mall från grunden eller basera den på en befintlig gemensam eller [snabb starts mall](https://azure.microsoft.com/documentation/templates/).
- Härleda från en befintlig resurs grupp genom att exportera en mall från antingen [den ursprungliga distributionen](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)eller från det [aktuella läget för distributionen](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Använda en lokal [JSON-redigerare (till exempel vs-kod)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)och ladda upp och distribuera med hjälp av POWERSHELL eller cli.
- Använd Visual Studio [Azures resurs grupps projekt](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) för att både skapa och distribuera en mall. 

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet 

För att skapa en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitets deltagare](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Använd följande mall för att skapa en användardefinierad hanterad identitet. Ersätt `<USER ASSIGNED IDENTITY NAME>` värdet med dina egna värden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>Nästa steg

Information om hur du tilldelar en användardefinierad hanterad identitet till en virtuell Azure-dator med hjälp av en Azure Resource Manager mall finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av mallar](qs-configure-template-windows-vm.md).


