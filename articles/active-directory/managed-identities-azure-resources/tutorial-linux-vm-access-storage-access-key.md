---
title: Självstudie `:` Använd en hanterad identitet för att få åtkomst till Azure Storage via åtkomst nyckel – Linux-Azure AD
description: En själv studie kurs som vägleder dig genom processen med att använda en virtuell Linux-dator med hanterad identitet för att få åtkomst till Azure Storage via en åtkomst nyckel.
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
ms.date: 03/04/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: e99a199fcc5f43f3710fe2e2fcfe55b7e624987b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317471"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage-via-access-key"></a>Självstudie: Använda en systemtilldelade hanterad identitet för en virtuell Linux-dator för åtkomst till Azure Storage via åtkomstnyckel

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

I den här självstudien lär du dig att hämta åtkomstnycklar till lagringskonton med en systemtilldelad hanterad identitet för en virtuell Linux-dator. Du kan använda en lagringsåtkomstnyckel som vanligt när du gör lagringsåtgärder, till exempel när du använder Storage SDK. I den här självstudien laddar vi upp och ned blobbar med hjälp av Azure CLI. Du lär dig hur du:

> [!div class="checklist"]
> * Ge din virtuella dator åtkomst till åtkomstnycklar för lagringskonton i Resource Manager 
> * Hämtar en åtkomsttoken med hjälp av den virtuella datorns identitet och använder den för att hämta lagringsåtkomstnycklarna från Resource Manager  

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
2. Klicka på länken **Containers** till vänster, under Blob Service.
3. Klicka på **+ Container** längst upp på sidan. Panelen ”Ny container” visas.
4. Ge containern ett namn, välj en åtkomstnivå och klicka sedan på **OK**. Du ska använda det här namnet senare i självstudien. 

    ![Skapa lagringscontainer](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-storage-account-access-keys"></a>Bevilja den virtuella datorns systemtilldelade hanterade identitet åtkomst till att använda lagringskontots åtkomstnycklar

I det här steget ger du den virtuella datorns systemtilldelade hanterade identitet åtkomst till nycklarna till lagringskontot.   

1. Gå tillbaka till det lagringskonto du nyss skapade.
2. Klicka på länken **åtkomstkontroll (IAM)** i vänstra panelen.  
3. Klicka på **+ Lägg till rolltilldelning** längst upp på sidan för att lägga till en ny rolltilldelning för den virtuella datorn
4. Ställ in **Roll** på ”Tjänstroll som operatör av lagringskontonyckel”, till höger på sidan. 
5. I nästa listruta väljer du resursen Virtuell dator under **Tilldela behörighet till**.  
6. Kontrollera sedan att rätt prenumeration visas i listrutan **Prenumeration**. Välj Alla resursgrupper under **Resursgrupper**.  
7. Under **Välj** väljer du sedan din virtuella Linux-dator i listrutan och klickar på **Spara**. 

    ![Alternativ bildtext](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

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
    > I följande svar har access_token-elementet kortats ned.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Hämta åtkomstnycklar för lagringskonton från Azure Resource Manager för att göra lagringsanrop  

Anropa Resource Manager via CURL med hjälp av den åtkomsttoken som vi hämtade i föregående avsnitt och hämta lagringsåtkomstnyckeln. När vi har fått lagringsåtkomstnyckeln kan vi anropa uppladdnings- och nedladdningsåtgärder för lagring. Ersätt parametervärdena `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` och `<STORAGE ACCOUNT NAME>` med dina egna värden. Ersätt värdet `<ACCESS TOKEN>` med den åtkomsttoken som du hämtade tidigare:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Eftersom texten i den tidigare URL:en är skiftlägeskänslig måste du använda samma skiftläge för dina resursgrupper. Dessutom är det viktigt att du är medveten om att det här är en POST-begäran, inte en GET-begäran, samt att du skickar ett värde för att registrera en längdbegränsning med -d som kan vara NULL.  

CURL-svaret returnerar listan med nycklar:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Skapa en blob-exempelfil att ladda upp till din bloblagringscontainer. På en virtuell Linux-dator kan du göra detta med hjälp av följande kommando. 

```bash
echo "This is a test file." > test.txt
```

Sedan autentiserar du med CLI-kommandot `az storage` med hjälp av lagringsåtkomstnyckeln och laddar upp filen till blobcontainern. För det här steget behöver du [installera den senaste versionen av Azure CLI](/cli/azure/install-azure-cli) på din virtuella dator, om du inte redan har gjort det.
 

```azurecli
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Svar: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

Du kan dessutom ladda ned filen med hjälp av Azure CLI och autentisera med lagringsåtkomstnyckeln. 

Begäran: 

```azurecli
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Svar: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
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
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
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

I den här självstudien har du lärt dig hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Azure Storage med hjälp av en åtkomstnyckel.  Mer information om Azure Storage-åtkomstnycklar finns här:

> [!div class="nextstepaction"]
>[Hantera dina lagringsåtkomstnycklar](../../storage/common/storage-account-create.md)
