---
title: 'Självstudie: åtkomst Azure Storage med SAS-autentiseringsuppgift – Linux – Azure AD'
description: En självstudie som visar hur du använder en systemtilldelad hanterad identitet för virtuell Linux-dator och kommer åt Azure Storage med hjälp av en SAS-autentiseringsuppgift, istället för en åtkomstnyckel för lagringskonto.
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
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3edc63a1532bb6889fc490e400dbb57e7bce10d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93360419"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-identity-to-access-azure-storage-via-a-sas-credential"></a>Självstudier: Komma åt Azure Storage via en SAS-autentiseringsuppgift med en systemtilldelad hanterad identitet för virtuell Linux-dator

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Den här självstudien visar hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator och hämtar en SAS-autentiseringsuppgift (en signatur för delad åtkomst) för lagring. Mer specifikt, en [autentiseringsuppgift för tjänst-SAS](../../storage/common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

> [!NOTE]
> Den SAS-nyckel som skapas i den här självstudien kommer inte att begränsas/bindas till den virtuella datorn.  

En tjänst-SAS ger möjlighet att ge begränsad åtkomst till objekt i ett lagringskonto under en begränsad tid och för en särskild tjänst (i vårt fall blob-tjänsten) utan att göra kontots åtkomstnyckel tillgänglig. Du kan en använda SAS-autentiseringsuppgift som vanligt när du gör lagringsåtgärder, till exempel när du använder Storage SDK. I den här självstudien visar vi hur man laddar upp och ned en blob med hjälp av Azure Storage CLI. Du lär dig hur du:


> [!div class="checklist"]
> * Skapa ett lagringskonto
> * Skapa en blob-container i lagringskontot
> * Ge den virtuella datorn åtkomst till en SAS för lagringskonton i Resource Manager 
> * Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använde den när du hämtar SAS:en från Resource Manager 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto 

Nu skapar du ett lagringskonto, om du inte redan har ett.  Du kan även hoppa över det här steget och ge den virtuella datorns systemtilldelade hanterade identitet åtkomst till nycklarna till ett befintligt lagringskonto. 

1. Klicka på knappen **+/Skapa ny tjänst** som finns i övre vänstra hörnet i Azure-portalen.
2. Klicka på **Lagring** och sedan på **Lagringskonto**. Panelen ”Skapa lagringskonto” visas.
3. Ange ett **namn** för lagrings kontot som du kommer att använda senare.  
4. **Distributionsmodell** och **Typ av konto** ska vara inställda på Resurshanterare respektive Generell användning. 
5. Kontrollera att informationen under **Prenumeration** och **Resursgrupp** matchar informationen som du angav när du skapade den virtuella datorn i föregående steg.
6. Klicka på **Skapa**.

    ![Skapa ett nytt lagringskonto](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Skapa en blob-container i lagringskontot

Senare ska vi ladda upp och ned en fil till det nya lagringskontot. Eftersom filer kräver blob-lagring måste vi skapa en blob-container som filen ska lagras i.

1. Gå tillbaka till det lagringskonto du nyss skapade.
2. Klicka på länken **Containers** i vänstra panelen, under Blob Service.
3. Klicka på **+ Container** längst upp på sidan. Panelen ”Ny container” visas.
4. Ge containern ett namn, välj en åtkomstnivå och klicka sedan på **OK**. Du ska använda det här namnet senare i självstudien. 

    ![Skapa lagringscontainer](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>Ge den virtuella datorns systemtilldelade hanterade identitet åtkomst till att använda lagrings-SAS

Azure Storage har inte inbyggt stöd för Azure Active Directory-autentisering.  Men du kan använda den virtuella datorns systemtilldelade hanterade identitet för att hämta en lagrings-SAS från Resource Manager, och sedan få åtkomst till lagring med hjälp av SAS:en.  I det här steget ger du den virtuella datorns systemtilldelade hanterade identitet åtkomst till SAS för lagringskontot.   

1. Gå tillbaka till det lagringskonto du nyss skapade.
2. Klicka på länken **åtkomstkontroll (IAM)** i vänstra panelen.  
3. Klicka på **+ Lägg till rolltilldelning** längst upp på sidan för att lägga till en ny rolltilldelning för den virtuella datorn
4. Ställ in Lagringskontodeltagare som **Roll**, till höger på sidan. 
5. I nästa listruta väljer du resursen Virtuell dator under **Tilldela behörighet till**.  
6. Kontrollera sedan att rätt prenumeration visas i listrutan **Prenumeration**. Välj Alla resursgrupper under **Resursgrupper**.  
7. Under **Välj** väljer du sedan din virtuella Linux-dator i listrutan och klickar på **Spara**.  

    ![Alternativ bildtext](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använd den för att anropa Azure Resource Manager

Under resten av självstudiekursen arbetar vi från den virtuella datorn som vi skapade tidigare.

Om du vill slutföra de här stegen behöver du en SSH-klient. Om du använder Windows kan du använda SSH-klienten i [Windows-undersystemet för Linux](/windows/wsl/install-win10). Om du behöver hjälp att konfigurera SSH-klientens nycklar läser du [Så här använder du SSH-nycklar med Windows i Azure](../../virtual-machines/linux/ssh-from-windows.md) eller [How to create and use an SSH public and private key pair for Linux VMs in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md) (Skapa och använda SSH-nyckelpar med privata och offentliga nycklar för virtuella Linux-datorer i Azure).

1. Gå till **Virtuella datorer** på Azure Portal, gå till den virtuella Linux-datorn och klicka sedan längst upp på **Anslut** på sidan **Översikt**. Kopiera strängen för anslutning till din virtuella dator. 
2. Anslut till den virtuella datorn med hjälp av SSH-klienten.  
3. Nu uppmanas du att ange **lösenordet** som du lade till när du skapade **den virtuella Linux-datorn**. Därefter bör du loggas in.  
4. Använd CURL och hämta en åtkomsttoken för Azure Resource Manager.  

    CURL-begäran och -svar för åtkomsttoken visas nedan:
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true    
    ```
    
    > [!NOTE]
    > I föregående begäran måste värdet för ”resource”-parametern vara en exakt matchning av vad som förväntas av Azure AD. När du använder Azure Resource Manager-resurs-ID:t måste du ta med det avslutande snedstrecket i URI:n.
    > I följande svar har access_token-elementet förkort ATS för det kortfattat.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Hämta en SAS-autentiseringsuppgift från Azure Resource Manager om du vill göra lagringsanrop

Anropa Resource Manager via CURL med hjälp av den åtkomsttoken som vi hämtade i föregående avsnitt och skapa en SAS-autentiseringsuppgift för lagring. När vi har fått SAS-autentiseringsuppgiften kan vi anropa uppladdnings- och nedladdningsåtgärder för lagring.

För denna begäran använder vi följande begäranparametrar och skapa SAS-autentiseringsuppgiften:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite.  Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Dessa parametrar används i SAS-autentiseringsuppgiftens POST-begäran. Mer information om parametrarna som krävs när du skapar en SAS-autentiseringsuppgift finns i [referensen för tjänsten för SAS REST-listan](/rest/api/storagerp/storageaccounts/listservicesas).

Använd följande CURL-begäran och få SAS-autentiseringsuppgiften. Ersätt parametervärdena `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, `<CONTAINER NAME>` och `<EXPIRATION TIME>` med dina egna värden. Ersätt värdet `<ACCESS TOKEN>` med den åtkomsttoken som du hämtade tidigare:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> Eftersom texten i den tidigare URL:en är skiftlägeskänslig måste du använda samma skiftläge för dina resursgrupper. Dessutom är det viktigt att du är medveten om att det här är en POST-begäran och inte en GET-begäran.

CURL-svaret returnerar SAS-autentiseringsuppgiften:  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

Skapa en blob-exempelfil att ladda upp till din bloblagringscontainer. På en virtuell Linux-dator kan du göra detta med hjälp av följande kommando. 

```bash
echo "This is a test file." > test.txt
```

Sedan autentiserar du med CLI-kommandot `az storage` med hjälp av SAS-autentiseringsuppgiften och laddar upp filen till blobcontainern. För det här steget behöver du [installera den senaste versionen av Azure CLI](/cli/azure/install-azure-cli) på din virtuella dator, om du inte redan har gjort det.

```azurecli
 az storage blob upload --container-name 
                        --file 
                        --name
                        --account-name 
                        --sas-token
```

Svar: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-21T03:58:56+00:00"
}
```

Du kan dessutom ladda ned filen med hjälp av Azure CLI och autentisera med SAS-autentiseringsuppgiften. 

Begäran: 

```azurecli
az storage blob download --container-name
                         --file 
                         --name 
                         --account-name
                         --sas-token
```

Svar: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-21T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Azure Storage med hjälp av en SAS-autentiseringsuppgift.  Läs mer om Azure Storage SAS här:

> [!div class="nextstepaction"]
>[Använda signaturer för delad åtkomst (SAS)](../../storage/common/storage-sas-overview.md)
