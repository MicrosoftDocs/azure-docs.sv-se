---
title: Självstudie `:` Använd en hanterad identitet för att få åtkomst till Azure Storage – Linux – Azure AD
description: En självstudie som steg för steg beskriver hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att få åtkomst till Azure Storage.
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
ms.date: 10/23/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4c7612188043be070ead92c88838b567b22787d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98131278"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Självstudie: Använda en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Azure Storage 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

I den här självstudien lär du dig hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att komma åt Azure Storage. Lär dig att:

> [!div class="checklist"]
> * Skapa ett lagringskonto
> * Skapa en blobcontainer i ett lagringskonto
> * Ge en hanterade identitet på en virtuell Linux-dator åtkomst till en Azure Storage-container
> * Hämta en åtkomsttoken och använda den för att anropa Azure Storage

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

Om du vill köra CLI-exempelskripten i den här självstudien har du två alternativ:

- Använd [Azure Cloud Shell](~/articles/cloud-shell/overview.md) antingen från Azure Portal eller via knappen **Prova**, som du hittar i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](/cli/azure/install-azure-cli) (2.0.23 eller senare) om du föredrar att använda en lokal CLI-konsol.

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto 

I det här avsnittet skapar du ett lagringskonto. 

1. Klicka på knappen **+ Skapa en resurs** längst upp till vänster i Azure-portalen.
2. Klicka på **Lagring** och sedan på **Lagringskonto – blob, fil, tabell, kö**.
3. Under **Namn** anger du ett namn för lagringskontot.  
4. **Distributionsmodell** och **Typ av konto** ska vara inställda på **Resurshanterare** respektive **Lagring (generell användning v1)**. 
5. Kontrollera att informationen under **Prenumeration** och **Resursgrupp** matchar informationen som du angav när du skapade den virtuella datorn i föregående steg.
6. Klicka på **Skapa**.

    ![Skapa ett nytt lagringskonto](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Skapa en blobcontainer och ladda upp en fil till lagringskontot

Eftersom filer behöver bloblagring måste du skapa en blobcontainer som du lagrar filen i. Sedan laddar du upp en fil till blobcontainern i det nya lagringskontot.

1. Gå tillbaka till det lagringskonto du nyss skapade.
2. Under **BLOB service** klickar du på **behållare**.
3. Klicka på **+ Container** överst på sidan.
4. Under **Ny container** anger du ett namn för containern och under **Offentlig åtkomstnivå** behåller du standardvärdet.

    ![Skapa lagringscontainer](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Med hjälp av valfritt redigeringsprogram skapar du en fil som heter *hello world.txt* på den lokala datorn.  Öppna filen och lägg till följande text (utan citattecken): ”Hello world! :)”och spara den. 

6. Ladda upp filen till containern du skapade genom att klicka på containernamnet och sedan på **Ladda upp**
7. Under **Filer** i fönstret **Ladda upp blob** klickar du på mappikonen och bläddrar till filen **hello_world.txt** på din lokala dator. Välj sedan filen och klicka på **Ladda upp**.

    ![Ladda upp textfil](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Ge din virtuella dator åtkomst till en Azure Storage-container 

Du kan använda den hanterade identiteten på den virtuella datorn till att hämta data i Azure Storage Blob.

>[!NOTE]
> Mer information om de olika roller som du kan använda för att bevilja behörigheter till lagrings granskning ger [åtkomst till blobbar och köer med hjälp av Azure Active Directory](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights)

1. Gå tillbaka till det lagringskonto du nyss skapade.  
2. Klicka på länken **åtkomstkontroll (IAM)** i vänstra panelen.  
3. Klicka på **+ Lägg till roll tilldelning** ovanpå sidan för att lägga till en ny roll tilldelning för den virtuella datorn.
4. Under **roll** i list rutan väljer du **Storage BLOB data Reader**. 
5. I listrutan under **Tilldela behörighet till** väljer du **Virtuell dator**.  
6. Kontrollera sedan att rätt prenumeration är inställd i listrutan **Prenumeration**. Under **Resursgrupper** väljer du **Alla resursgrupper**.  
7. Under **Välj** väljer du din virtuella dator och klickar sedan på **Spara**.

    ![Tilldela behörigheter](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Hämta en åtkomsttoken och använda den för att anropa Azure Storage

Azure Storage har inbyggt stöd för Azure Active Directory-autentisering, vilket gör att åtkomsttoken som hämtas med en hanterad identitet kan accepteras direkt. Detta är en del av Azure Storages integrering med Azure AD, och skiljer sig från att ange autentiseringsuppgifter i anslutningssträngen.

För att kunna utföra följande steg måste du arbeta från den virtuella datorn som du skapade tidigare och du behöver en SSH-klient för att ansluta till den. Om du använder Windows kan du använda SSH-klienten i [Windows-undersystemet för Linux](/windows/wsl/about). Om du behöver hjälp att konfigurera SSH-klientens nycklar läser du [Så här använder du SSH-nycklar med Windows i Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) eller [How to create and use an SSH public and private key pair for Linux VMs in Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md) (Skapa och använda SSH-nyckelpar med privata och offentliga nycklar för virtuella Linux-datorer i Azure).

1. Gå till **Virtuella datorer** i Azure-portalen och sedan till den virtuella Linux-datorn. På sidan **Överblick** klickar du på **Anslut**. Kopiera strängen för anslutning till din virtuella dator.
2. **Anslut** till den virtuella datorn med valfri SSH-klient. 
3. I terminalfönstret skickar du en begäran till den lokala slutpunkten för hanterad identitet för att hämta en åtkomsttoken för Azure Storage.
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true
    ```
4. Nu använder du åtkomsttoken för att få åtkomst till Azure Storage, till exempel för att läsa innehåll i exempelfilen som du tidigare laddade upp till containern. Ersätt värdena för `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` och `<FILE NAME>` med de värden som du angav tidigare, och `<ACCESS TOKEN>` med den token som returnerades i föregående steg.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Svaret innehåller filens innehåll:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du aktiverar en systemtilldelad hanterad identitet för en virtuell Linux-dator för att få åtkomst till Azure Storage.  Läs mer om Azure Storage här:

> [!div class="nextstepaction"]
> [Azure Storage](../../storage/common/storage-introduction.md)
