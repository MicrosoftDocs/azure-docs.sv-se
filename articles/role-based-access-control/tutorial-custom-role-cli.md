---
title: 'Självstudie: skapa en anpassad Azure-roll med Azure CLI – Azure RBAC'
description: Kom igång med att skapa en anpassad Azure-roll med Azure CLI och rollbaserad åtkomst kontroll i Azure (Azure RBAC) i den här självstudien.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.custom: devx-track-azurecli
ms.openlocfilehash: 319bca74c8e781e5dc5022e9fb901b2edca24a80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87485651"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-cli"></a>Självstudie: skapa en anpassad Azure-roll med hjälp av Azure CLI

Om de [inbyggda Azure-rollerna](built-in-roles.md) inte uppfyller organisationens specifika behov kan du skapa dina egna anpassade roller. För den här självstudien skapar du en anpassad roll med namnet Reader Support Tickets (Läsare av supportbegäranden) med hjälp av Azure CLI. Med den här anpassade rollen kan användare visa allt i en prenumerations hanteringsplan samt öppna supportbegäranden.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en anpassad roll
> * Lista anpassade roller
> * Uppdatera en anpassad roll
> * Ta bort en anpassad roll

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna genomföra den här kursen behöver du följande:

- Behörigheter att skapa anpassade roller som [Owner](built-in-roles.md#owner) (Ägare) eller [User Access Administrator](built-in-roles.md#user-access-administrator) (Administratör för användaråtkomst)
- [Azure Cloud Shell](../cloud-shell/overview.md) eller [Azure CLI](/cli/azure/install-azure-cli)

## <a name="sign-in-to-azure-cli"></a>Logga in på Azure CLI

Logga in på [Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Det enklaste sättet att skapa en anpassad roll är att utgå från en JSON-mall, lägga till dina ändringar och sedan skapa en ny roll.

1. Granska listan över åtgärder för [Microsoft.Support-resursprovidern](resource-provider-operations.md#microsoftsupport). Det är bra att känna till de åtgärder som är tillgängliga för att skapa dina behörigheter.

    | Åtgärd | Beskrivning |
    | --- | --- |
    | Microsoft.Support/register/action | Registrerar till supportresursprovidern |
    | Microsoft.Support/supportTickets/read | Hämtar information om supportbegäran (inklusive status, allvarlighetsgrad, kontaktinformation och kommunikation) eller hämtar listan över supportbegäranden för alla prenumerationer. |
    | Microsoft.Support/supportTickets/write | Skapar eller uppdaterar en supportbegäran. Du kan skapa en supportbegäran om tekniska problem, fakturering, kvoter eller prenumerationshantering. Du kan uppdatera allvarlighetsgraden, kontaktinformation och kommunikation för befintliga supportbegäranden. |
    
1. Skapa en ny fil med namnet **ReaderSupportRole.json**.

1. Öppna ReaderSupportRole.json i en textredigerare och lägg till följande JSON.

    Information om de olika egenskaperna finns i [Azure-anpassade roller](custom-roles.md).

    ```json
    {
      "Name": "",
      "IsCustom": true,
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId1}"
      ]
    }
    ```
    
1. Lägg till följande åtgärder i egenskapen `Actions`. Med de här åtgärderna kan användare visa allt i prenumerationen samt skapa supportbegäranden.

    ```
    "*/read",
    "Microsoft.Support/*"
    ```

1. Hämta ID för din prenumeration med hjälp av kommandot [az account list](/cli/azure/account#az-account-list).

    ```azurecli
    az account list --output table
    ```

1. I `AssignableScopes` ersätter du `{subscriptionId1}` med ditt prenumerations-ID.

    Du måste lägga till explicita prenumerations-ID:n; annars tillåts du inte importera rollen i din prenumeration.

1. Ändra egenskaperna `Name` och `Description` till ”Reader Support Tickets” (Läsare av supportbegäranden) och ”View everything in the subscription and also open support tickets” (Visa allt i prenumerationen och även öppna supportbegäranden).

    Din JSON-fil ska se ut så här:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. För att skapa en ny anpassad roll använder du kommandot [az role definition create](/cli/azure/role/definition#az-role-definition-create) och anger definitionsfilen för JSON-rollen.

    ```azurecli
    az role definition create --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

    Den nya anpassade rollen är nu tillgänglig och kan tilldelas till användare, grupper eller tjänstens huvudnamn precis som inbyggda roller.

## <a name="list-custom-roles"></a>Lista anpassade roller

- För att lista alla dina anpassade roller använder du kommandot [az role definition list](/cli/azure/role/definition#az-role-definition-list) med parametern `--custom-role-only`.

    ```azurecli
    az role definition list --custom-role-only true
    ```
    
    ```Output
    [
      {
        "additionalProperties": {},
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ],
        "description": "View everything in the subscription and also open support tickets.",
        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
        "name": "22222222-2222-2222-2222-222222222222",
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Support/*",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Insights/diagnosticSettings/*/read"
            ],
            "additionalProperties": {},
            "dataActions": [],
            "notActions": [],
            "notDataActions": []
          }
        ],
        "roleName": "Reader Support Tickets",
        "roleType": "CustomRole",
        "type": "Microsoft.Authorization/roleDefinitions"
      }
    ]
    ```
    
    Du kan även visa den anpassade rollen i Azure-portalen.

    ![skärmbild av anpassad roll som importerats i Azure-portalen](./media/tutorial-custom-role-cli/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Uppdatera en anpassad roll

Om du vill uppdatera den anpassade rollen uppdaterar du JSON-filen och uppdaterar sedan den anpassade rollen.

1. Öppna filen ReaderSupportRole.json.

1. I `Actions` lägger du till åtgärden för att skapa och hantera resursgruppsdistributioner `"Microsoft.Resources/deployments/*"`. Se till att inkludera ett kommatecken efter föregående åtgärd.

    Din uppdaterade JSON-fil ska se ut så här:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. För att uppdatera den anpassade rollen använder du kommandot [az role definition update](/cli/azure/role/definition#az-role-definition-update) och anger den uppdaterade JSON-filen.

    ```azurecli
    az role definition update --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*",
            "Microsoft.Resources/deployments/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```
    
## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll

- Använd kommandot [az role definition delete](/cli/azure/role/definition#az-role-definition-delete) och ange rollnamnet eller roll-ID för att ta bort den anpassade rollen.

    ```azurecli
    az role definition delete --name "Reader Support Tickets"
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa eller uppdatera anpassade Azure-roller med Azure CLI](custom-roles-cli.md)
