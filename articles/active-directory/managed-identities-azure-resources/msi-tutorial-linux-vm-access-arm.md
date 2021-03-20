---
title: Använda en användartilldelad hanterad identitet på virtuell Linux-dator för att få åtkomst till Azure Resource Manager
description: En självstudiekurs som beskriver steg för steg hur du använder en användartilldelad hanterad identitet på en virtuell Linux-dator för att få åtkomst till Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2020
ms.author: barclayn
ROBOTS: NOINDEX,NOFOLLOW
ms.collection: M365-identity-device-management
ms.openlocfilehash: 247b3c1b4341eff11069a6af324fff5cf1fba62c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546598"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Självstudier: Använda en användartilldelad hanterad identitet på en virtuell Linux-dator för att få åtkomst till Azure Resource Manager

Den här självstudien beskriver hur du skapar en användartilldelad hanterad identitet, hur du tilldelar den till en virtuell Linux-dator (VM) och hur du sedan använder identiteten för att få åtkomst till Azure Resource Manager-API:et. Hanterade identiteter för Azure-resurser hanteras automatiskt av Azure. Det gör det möjligt att autentisera mot tjänster som stöder Azure AD-autentisering, utan att du behöver bädda in autentiseringsuppgifter i din kod. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en användartilldelad hanterad identitet
> * Tilldela den användartilldelade hanterade identiteten till en virtuell Linux-dator 
> * Ge den användartilldelade hanterade identiteten åtkomst till en resursgrupp i Azure Resource Manager 
> * Hämta en åtkomsttoken med hjälp av den användartilldelade hanterade identiteten och använd den för att anropa Azure Resource Manager 

## <a name="prerequisites"></a>Förutsättningar

- Förståelse för hanterade identiteter. Om du inte känner till funktionen för hanterade identiteter för Azure-resurser kan du läsa igenom den här [översikten](overview.md). 
- Ett Azure-konto kan du [Registrera dig för ett kostnads fritt konto](https://azure.microsoft.com/free/).
- Du behöver också en virtuell Linux-dator. Om du behöver skapa en virtuell dator för den här självstudien kan du följa artikeln [skapa en virtuell Linux-dator med Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)
- Om du vill köra exempel skripten har du två alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md)som du kan öppna med knappen **prova** på det övre högra hörnet av kodblock.
    - Kör skript lokalt genom att installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli)och logga sedan in på Azure med [AZ-inloggning](/cli/azure/reference-index#az-login).

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

Skapa en användartilldelad hanterad identitet med hjälp av [az identity create](/cli/azure/identity#az-identity-create). Parametern `-g` anger resursgruppen där den användartilldelade hanterade identiteten skapas, och parametern `-n` anger dess namn. Ersätt parametervärdena `<RESOURCE GROUP>` och `<UAMI NAME>` med dina egna värden:
    
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <UAMI NAME>
```

Svaret innehåller information om den användartilldelade hanterade identiteten som skapats, liknande den i följande exempel. Notera `id`-värdet för den användartilldelade hanterade identiteten. Du behöver det i nästa steg:

```json
{
"clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
"clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
"id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>",
"location": "westcentralus",
"name": "<UAMI NAME>",
"principalId": "9012",
"resourceGroup": "<RESOURCE GROUP>",
"tags": {},
"tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
"type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

## <a name="assign-an-identity-to-your-linux-vm"></a>Tilldela en identitet till din virtuella Linux-dator

En användartilldelad hanterad identitet kan användas av klienter på flera Azure-resurser. Använd följande kommandon för att tilldela den användartilldelade hanterade identiteten till en enskild virtuell dator. Använd egenskapen `Id` som returnerades i föregående steg för `-IdentityID`-parametern.

Tilldela den användartilldelade hanterade identiteten till din virtuella Linux-dator med hjälp av [az vm identity assign](/cli/azure/vm). Ersätt parametervärdena `<RESOURCE GROUP>` och `<VM NAME>` med dina egna värden. Använd egenskapen `id` som returnerades i föregående steg för `--identities`-parametervärdet.

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>"
```

## <a name="grant-access-to-a-resource-group-in-azure-resource-manager"></a>Bevilja åtkomst till en resurs grupp i Azure Resource Manager

Hanterade identiteter för Azure-resurser tillhandahåller identiteter som din kod kan använda för att begära åtkomsttoken för autentisering mot resurs-API:er som stöder Azure AD-autentisering. I den här självstudiekursen använder koden Azure Resource Manager-API:et.  

Innan koden kan komma åt API:et måste du ge identiteten åtkomst till en resurs i Azure Resource Manager. I detta fall den resursgrupp som den virtuella datorn finns i. Uppdatera värdet för `<SUBSCRIPTION ID>` och `<RESOURCE GROUP>` baserat på din miljö. Ersätt även `<UAMI PRINCIPALID>` med `principalId`-egenskapen som returnerades av kommandot `az identity create` i [Skapa en användartilldelad hanterad identitet](#create-a-user-assigned-managed-identity):

```azurecli-interactive
az role assignment create --assignee <UAMI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

Svaret innehåller information om rolltilldelningen som skapats, liknande den i följande exempel:

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använd den för att anropa Resource Manager 

Under resten av självstudiekursen arbetar vi från den virtuella datorn som vi skapade tidigare.

För att slutföra de här stegen behöver du en SSH-klient. Om du använder Windows kan du använda SSH-klienten i [Windows-undersystemet för Linux](/windows/wsl/about). 

1. Logga in på Azure- [portalen](https://portal.azure.com).
2. Gå till **Virtuella datorer** på portalen, gå till den virtuella Linux-datorn och klicka sedan på **Anslut** under **Översikt**. Kopiera strängen för anslutning till din virtuella dator.
3. Anslut till den virtuella datorn med valfri SSH-klient. Om du använder Windows kan du använda SSH-klienten i [Windows-undersystemet för Linux](/windows/wsl/about). Om du behöver hjälp att konfigurera SSH-klientens nycklar läser du [Så här använder du SSH-nycklar med Windows i Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) eller [How to create and use an SSH public and private key pair for Linux VMs in Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md) (Skapa och använda SSH-nyckelpar med privata och offentliga nycklar för virtuella Linux-datorer i Azure).
4. Gör ett anrop i terminalfönstret med hjälp av CURL till IMDS-identitetsslutpunkten (Azure Instance Metadata Service) för att hämta en åtkomsttoken för Azure Resource Manager.  

   CURL-begäran om att hämta en åtkomsttoken visas i följande exempel. Ersätt `<CLIENT ID>` med `clientId`-egenskapen som returnerades av kommandot `az identity create` i [Skapa en användartilldelad hanterad identitet](#create-a-user-assigned-managed-identity): 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<UAMI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Värdet för parametern `resource` måste vara en exakt matchning av vad som förväntas av Azure AD. När du använder Resource Manager-resurs-ID:t måste du ta med det avslutande snedstrecket i URI:n. 
    
    Svaret innehåller den åtkomsttoken som du behöver för att komma åt Azure Resource Manager. 
    
    Svarsexempel:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

5. Använd din åtkomsttoken för att komma åt Azure Resource Manager, och läs egenskaperna för den resursgrupp som du tidigare gav den användartilldelade hanterade identiteten åtkomst till. Ersätt `<SUBSCRIPTION ID>` och `<RESOURCE GROUP>` med de värden som du angav tidigare, och `<ACCESS TOKEN>` med den token som returnerades i föregående steg.

    > [!NOTE]
    > Eftersom URL:en är skiftlägeskänslig måste du använda exakt samma skiftläge som du använde tidigare när du namngav resursgruppen, samt versalt ”G” i `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Svaret innehåller information om den specifika resursgruppen, liknande den i följande exempel: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar en användartilldelad hanterad identitet och kopplar den till en virtuell Linux-dator för att komma åt Azure Resource Manager-API:et.  Mer information om Azure Resource Manager finns här:

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)
