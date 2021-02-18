---
title: Självstudie `:` Använd en hanterad identitet för att få åtkomst till Azure Cosmos DB – Linux – Azure AD
description: En självstudie som steg för steg beskriver hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att få åtkomst till Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: b81f80af69b47152f7111066070e173bb1ede5f4
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093946"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Självstudie: Använda en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Azure Cosmos DB 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

I den här självstudien lär du dig hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att komma åt Azure Cosmos DB. Lär dig att:

> [!div class="checklist"]
> * Skapa ett Cosmos DB-konto
> * Skapa en samling i Cosmos DB-kontot
> * Ge den systemtilldelade hanterade identiteten åtkomst till en Azure Cosmos DB-instans
> * Hämta `principalID` för den virtuella Linux-datorns systemtilldelade hanterade identitet
> * Hämta en åtkomsttoken och använd den för att anropa Azure Resource Manager
> * Hämta åtkomstnycklar från Azure Resource Manager för att göra Cosmos DB-anrop

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till funktionen för hanterade identiteter för Azure-resurser kan du läsa igenom den här [översikten](overview.md). 
- Om du inte har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- För att du ska kunna utföra det resursskapande och den rollhantering som krävs måste ditt konto ha behörigheten ”Ägare” på lämpligt omfång (din prenumeration eller resursgrupp). Om du behöver hjälp med roll tilldelning, se [tilldela Azure-roller för att hantera åtkomst till dina Azure-prenumerations resurser](../../role-based-access-control/role-assignments-portal.md).
- Om du vill köra exempel skripten har du två alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md)som du kan öppna med knappen **prova** på det övre högra hörnet av kodblock.
    - Kör skript lokalt genom att installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli)och logga sedan in på Azure med [AZ-inloggning](/cli/azure/reference-index#az-login). Använd ett konto som är kopplat till den Azure-prenumeration som du vill skapa resurser i.

## <a name="create-a-cosmos-db-account"></a>Skapa ett Cosmos DB-konto 

Om du inte redan har ett Cosmos DB-konto skapar du ett. Du kan hoppa över det här steget och använda ett befintligt Cosmos DB-konto. 

1. Klicka på knappen **+ Skapa en resurs** längst upp till vänster i Azure-portalen.
2. Klicka på **Databaser** och sedan på **Azure Cosmos DB**. Panelen ”Nytt konto” visas.
3. Ange ett **ID** för Cosmos DB-kontot som du ska använda senare.  
4. **API** bör ha värdet ”SQL”. Metoden som beskrivs i den här självstudien kan användas med de andra tillgängliga API-typerna, men stegen i den här självstudien gäller för SQL-API:et.
5. Kontrollera att informationen under **Prenumeration** och **Resursgrupp** matchar informationen som du angav när du skapade den virtuella datorn i föregående steg.  Välj en **plats** där Cosmos DB är tillgänglig.
6. Klicka på **Skapa**.

### <a name="create-a-collection-in-the-cosmos-db-account"></a>Skapa en samling i Cosmos DB-kontot

Lägg till en datasamling till Cosmos DB-kontot som du kan skicka frågor mot i senare steg.

1. Gå till Cosmos DB-kontot som du precis skapat.
2. Klicka på knappen **+/Lägg till samling** på fliken **Översikt**. Panelen ”Lägg till samling” visas.
3. Tilldela samlingen ett databas-ID och ett samlings-ID, välj en lagringskapacitet, ange en partitionsnyckel, ange ett värde för dataflöde och klicka sedan på **OK**.  I den här självstudien räcker det att du använder ”Test” som databas-ID och samlings-ID, väljer en fast lagringskapacitet och det lägsta dataflödet (400 RU/s).  

## <a name="grant-access"></a>Bevilja åtkomst

För att få åtkomst till åtkomstnycklarna för Cosmos DB-kontot från Resource Manager i följande avsnitt måste du hämta `principalID` för den virtuella Linux-datorns systemtilldelade hanterade identitet.  Se till att ersätta `<SUBSCRIPTION ID>` , `<RESOURCE GROUP>` (resurs gruppen där den virtuella datorn finns) och `<VM NAME>` parameter värden med dina egna värden.

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
Svaret innehåller information om den systemtilldelade hanterade identiteten (notera principalID eftersom du behöver det i nästa avsnitt):

```output  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }
```

### <a name="grant-your-linux-vms-system-assigned-identity-access-to-the-cosmos-db-account-access-keys"></a>Ge den virtuella Linux-datorns systemtilldelade identitet åtkomst till åtkomstnycklarna för Cosmos DB-kontot

Cosmos DB har inte inbyggt stöd för Azure AD-autentisering. Du kan emellertid använda en hanterad identitet för att hämta en åtkomstnyckel för Cosmos DB från Resource Manager och sedan använda nyckeln för att komma åt Cosmos DB. I det här steget ger du den systemtilldelade hanterade identiteten åtkomst till nycklarna till Cosmos DB-kontot.

Om du ska ge den systemtilldelade hanterade identiteten åtkomst till Cosmos DB-kontot i Azure Resource Manager med hjälp av Azure CLI uppdaterar du värdet för `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` och `<COSMOS DB ACCOUNT NAME>` för din miljö. Ersätt `<MI PRINCIPALID>` med den `principalId`-egenskap som returneras av kommandot `az resource show` i Hämta principalID för den virtuella Linux-datorns MI.  Cosmos DB stöder två detaljnivåer när åtkomstnycklar används: läs- och skrivåtkomst till kontot samt skrivskyddad åtkomst till kontot.  Tilldela rollen `DocumentDB Account Contributor` om du vill använda läs-/skrivnycklar för kontot, eller rollen `Cosmos DB Account Reader Role` om du vill använda skrivskyddade nycklar för kontot:

```azurecli-interactive
az role assignment create --assignee <MI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

Svaret innehåller information om rolltilldelningen som skapats:

```output
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="access-data"></a>Åtkomst till data

För resten av självstudien arbetar du från den virtuella datorn.

För att slutföra de här stegen behöver du en SSH-klient. Om du använder Windows kan du använda SSH-klienten i [Windows-undersystemet för Linux](/windows/wsl/install-win10). Om du behöver hjälp att konfigurera SSH-klientens nycklar läser du [Så här använder du SSH-nycklar med Windows i Azure](../../virtual-machines/linux/ssh-from-windows.md) eller [How to create and use an SSH public and private key pair for Linux VMs in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md) (Skapa och använda SSH-nyckelpar med privata och offentliga nycklar för virtuella Linux-datorer i Azure).

1. Gå till **Virtuella datorer** på Azure Portal, gå till den virtuella Linux-datorn och klicka sedan längst upp på **Anslut** på sidan **Översikt**. Kopiera strängen för anslutning till din virtuella dator. 
2. Anslut till den virtuella datorn med hjälp av SSH-klienten.  
3. Nu uppmanas du att ange **lösenordet** som du lade till när du skapade **den virtuella Linux-datorn**. Därefter bör du loggas in.  
4. Använd CURL för att hämta en åtkomsttoken för Azure Resource Manager: 
     
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
 
    > [!NOTE]
    > I föregående begäran måste värdet för ”resource”-parametern vara en exakt matchning av vad som förväntas av Azure AD. När du använder Azure Resource Manager-resurs-ID:t måste du ta med det avslutande snedstrecket i URI:n.
    > I följande svar har access_token-elementet kortats ned.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
### <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Hämta åtkomstnycklar från Azure Resource Manager för att göra Cosmos DB-anrop  

Använd CURL och anropa Resource Manager med hjälp av den åtkomsttoken som du fick i föregående avsnitt för att hämta åtkomstnyckeln för Cosmos DB-kontot. När vi väl har åtkomstnyckeln kan vi skicka frågor mot Cosmos DB. Ersätt parametervärdena `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` och `<COSMOS DB ACCOUNT NAME>` med dina egna värden. Ersätt värdet `<ACCESS TOKEN>` med den åtkomsttoken som du hämtade tidigare.  Om du vill hämta läs- och skrivnycklar använder du nyckelåtgärdstypen `listKeys`.  Om du vill hämta skrivskyddade nycklar använder du nyckelåtgärdstypen `readonlykeys`:

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Texten i föregående URL är Skift läges känslig, så Använd det Skift läge som matchar ärendet som används i namnet på din resurs grupp. Dessutom är det viktigt att du är medveten om att det här är en POST-begäran, inte en GET-begäran, samt att du skickar ett värde för att registrera en längdbegränsning med -d som kan vara NULL.  

CURL-svaret returnerar listan med nycklar.  Om du till exempel hämtar de skrivskyddade nycklarna:  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

Nu när du har åtkomstnyckeln för Cosmos DB-kontot kan du skicka den till en Cosmos DB-SDK och göra anrop för att komma åt kontot.  Som ett enkelt exempel kan du skicka åtkomstnyckeln till Azure CLI.  Du kan hämta `<COSMOS DB CONNECTION URL>` från fliken **Översikt** på bladet för Cosmos DB-kontot på Microsoft Azure-portalen.  Ersätt `<ACCESS KEY>` med värdet som returnerades ovan:

```azurecli-interactive
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Det här CLI-kommandot returnerar information om samlingen:

```output
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder en systemtilldelad hanterad identitet på en virtuell Linux-dator för att få åtkomst till Cosmos DB.  Mer information om Cosmos DB finns här:

> [!div class="nextstepaction"]
>[Översikt över Azure Cosmos DB](../../cosmos-db/introduction.md)
