---
title: Använda Azure Managed Identities för att skapa miljöer i DevTest Labs | Microsoft Docs
description: Lär dig hur du använder hanterade identiteter i Azure för att distribuera miljöer i ett labb i Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f3e4b4d7030eb26c25b291e03caaa430d1979c4
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185792"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Använd Azure Managed Identities för att distribuera miljöer i ett labb 

Som labb ägare kan du använda en hanterad identitet för att distribuera miljöer i ett labb. Den här funktionen är användbar i scenarier där miljön innehåller eller har referenser till Azure-resurser, till exempel nyckel valv, delade avbildnings gallerier och nätverk som är externa i miljöns resurs grupp. Det gör det möjligt att skapa sand Box miljöer som inte är begränsade till resurs gruppen för den miljön.

> [!NOTE]
> För närvarande stöds en enda användardefinierad identitet per labb. 

## <a name="prerequisites"></a>Krav

- [Skapa, Visa, ta bort eller tilldela en roll till en användare som tilldelats en hanterad identitet med hjälp av Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 
    
    Se till att din hanterade identitet har skapats i samma region och prenumeration som ditt labb. Den hanterade identiteten behöver inte finnas i samma resurs grupp.

## <a name="use-azure-portal"></a>Använda Azure-portalen

I det här avsnittet ska du, som labb ägare, använda Azure Portal för att lägga till en användardefinierad identitet i labbet. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **DevTest Labs**.
1. I listan med labb väljer du det labb du vill ha.
1. Välj **konfiguration och principer**  ->  **identitet (för hands version)**. 
1. Om du vill lägga till en tilldelad identitet väljer du fliken **tilldelad användare** .
1. Tryck på **Lägg till** .
1. Välj en befintlig användare som du har skapat och/eller har åtkomst till från List rutan.
 
    ![Lägg till användarens hanterad identitet](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. Tryck på **Spara** överst på sidan.

    När du har sparat använder labbet den här identiteten för att distribuera alla labb miljöer. Du kan också komma åt identitets resursen i Azure genom att välja identiteten i listan. 

Labb ägaren behöver inte göra något särskilt medan du distribuerar en miljö förutsatt att identiteten som läggs till i labbet har behörighet till de externa resurser som miljön behöver åtkomst till. 

Om du vill ändra den användar hanterade identitet som är tilldelad till labbet tar du bort den identitet som är kopplad till labbet först och lägger sedan till en annan i labbet. Om du vill ta bort en identitet som är kopplad till labbet väljer du **... (tre punkter)** och klicka på **ta bort**. 

## <a name="use-api"></a>Använd API

1. När du har skapat en identitet noterar du resurs-ID för den här identiteten. Den bör se ut som i följande exempel: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Utför en skicka https-metod för att lägga till en ny `ServiceRunner` resurs i labbet som liknar följande exempel. Service löpare-resursen är en proxykonfiguration för att hantera och kontrol lera hanterade identiteter i DevTest Labs. Namnet på tjänstens löpare kan vara ett giltigt namn, men vi rekommenderar att du använder namnet på den hanterade identitets resursen. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    Här är ett exempel: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
När användarens tilldelade identitet läggs till i labbet, kommer Azure DevTest Labs-tjänsten använda den när Azure Resource Manager miljöer distribueras. Om du till exempel behöver din Resource Manager-mall för att få åtkomst till en extern delad bild galleri bild, kontrollerar du att identiteten som du har lagt till i labbet har minst den behörighet som krävs för den delade avbildnings Galleri resursen. 
