---
title: Aktivera hanterade identiteter på dina virtuella labb datorer i Azure DevTest Labs
description: Den här artikeln visar hur en labb ägare kan aktivera användare tilldelade hanterade identiteter på dina virtuella labb datorer.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4bf2900acebaeecd5cbc4cb65635aee6de87dda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88717648"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Aktivera användar tilldelning av hanterade identiteter på virtuella labb datorer i Azure DevTest Labs
Som labb ägare kan du aktivera användarspecifika hanterade identiteter på dina virtuella labb datorer (VM) i Azure DevTest Labs.

En hanterad identitet kan användas för att autentisera till en tjänst som stöder Azure Active Directory autentisering (AD), inklusive Key Vault, utan att skicka några autentiseringsuppgifter i koden. Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md).

Med den här funktionen kan labb användare dela Azure-resurser, till exempel Azure SQL Database i Labbets sammanhang. Autentiseringen till resursen beaktas av själva identiteten. När den har kon figurer ATS aktive ras varje befintlig/nyligen skapad virtuell labb dator med den här identiteten. Labb användare kan komma åt resurser när de har loggat in på sina datorer.

> [!NOTE]
> Du kan lägga till flera användare som tilldelats hanterade identiteter som ska aktive ras på dina virtuella labb datorer.

## <a name="use-azure-portal"></a>Använda Azure-portalen
Följ dessa steg om du vill lägga till en användare som tilldelats hanterad identitet för virtuella labb datorer:

1. [Skapa en användardefinierad hanterad identitet i din prenumeration](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Gå till sidan **konfiguration och principer** för ditt labb.
1. Välj **identitet (förhands granskning)** på den vänstra menyn.
1. Välj fliken **virtuell dator** .
1. Välj **Lägg till** för att välja en befintlig identitet från en i förväg ifylld listruta. 

    > [!div class="mx-imgBorder"]
    > ![Knappen Lägg till identitet](./media/enable-managed-identities-lab-vms/add-identity-button.png)
1. Välj en befintlig **användare hanterad identitet** från den nedbrutna listan och välj **OK**. 

    > [!div class="mx-imgBorder"]
    > ![Lägg till identitet](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>Använd API

1.  När du har skapat en identitet noterar du resurs-ID för identiteten. Den bör se ut som i följande exempel: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Kör en Lägg till en HTTPS-metod för att lägga till en ny **ServiceRunner** -resurs i labbet, som du ser i följande exempel. 

    Service löpare-resursen är en proxykonfiguration för att hantera och kontrol lera hanterade identiteter i DevTest Labs. Namnet på tjänstens löpare kan vara ett giltigt namn, men vi rekommenderar att du använder namnet på den hanterade identitets resursen.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md).







