---
title: '`:`Självstudie: Använda hanterad identitet för Azure Storage åtkomst med HJÄLP av SAS-autentiseringsuppgifter – Azure AD'
description: En självstudiekurs som visar hur du använder en system tilldelad hanterad identitet för en virtuell Windows-dator för att komma åt Azure Storage, med hjälp av en SAS-autentiseringsbehörighet i stället för en åtkomstnyckel för lagringskonto.
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
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4f8b23d8f717e430e865e391a40692773f0beace
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776401"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-a-sas-credential"></a>Självstudie: Använda en system tilldelad hanterad identitet för en virtuell Windows-dator för Azure Storage åtkomst via en SAS-autentiseringsbehörighet

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Den här självstudien visar hur du använder en system tilldelad identitet för en virtuell Windows-dator (VM) för att hämta en SAS-autentiseringsbehörighet (signatur för delad åtkomst) för lagring. Mer specifikt, en [autentiseringsuppgift för tjänst-SAS](../../storage/common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

En tjänst-SAS ger möjlighet att bevilja begränsad åtkomst till objekt i ett lagringskonto, under begränsad tid och en specifik tjänst (i vårt fall blobtjänsten), utan att exponera en åtkomstnyckel för kontot. Du kan en använda SAS-autentiseringsuppgift som vanligt när du gör lagringsåtgärder, till exempel när du använder Storage SDK. I den här självstudien visar vi hur du laddar upp och ned en blob med hjälp Azure Storage PowerShell. Du lär dig hur du:

> [!div class="checklist"]
> * Skapa ett lagringskonto
> * Ge den virtuella datorn åtkomst till en SAS för lagringskonton i Resource Manager 
> * Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använde den när du hämtar SAS:en från Resource Manager 

## <a name="prerequisites"></a>Förutsättningar

- En förståelse för hanterade identiteter. Om du inte känner till funktionen för hanterade identiteter för Azure-resurser kan du läsa igenom den här [översikten](overview.md). 
- Ett Azure-konto [och registrera dig för ett kostnadsfritt konto.](https://azure.microsoft.com/free/)
- "Ägarbehörigheter" i lämpligt omfång (din prenumeration eller resursgrupp) för att utföra nödvändiga steg för resursskapande och rollhantering. Om du behöver hjälp med rolltilldelning kan du läsa Tilldela [Azure-roller för att hantera åtkomst till dina Azure-prenumerationsresurser.](../../role-based-access-control/role-assignments-portal.md)
- Du behöver också en virtuell Windows-dator som har system tilldelade hanterade identiteter aktiverade.
  - Om du behöver skapa en virtuell dator för den här självstudien kan du följa artikeln Skapa en virtuell dator [med system tilldelad identitet aktiverad](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto 

Nu skapar du ett lagringskonto, om du inte redan har ett. Du kan också hoppa över det här steget och ge den virtuella datorns system tilldelade hanterade identitet åtkomst till SAS-autentiseringssuppgifter för ett befintligt lagringskonto. 

1. Klicka på knappen **+/Skapa ny tjänst** som finns i övre vänstra hörnet i Azure-portalen.
2. Klicka på **Lagring** och sedan på **Lagringskonto**. Panelen ”Skapa lagringskonto” visas.
3. Ange ett namn för lagringskontot. Du ska använda namnet senare.  
4. **Distributionsmodell** **och Typ** av konto ska vara inställda på "Resource Manager" respektive "Generell användning". 
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

Azure Storage har inte inbyggt stöd för Azure Active Directory-autentisering.  Du kan dock använda en hanterad identitet för att hämta en lagrings-SAS från Resource Manager och sedan använda SAS för att få åtkomst till lagringen.  I det här steget ger du den virtuella datorns systemtilldelade hanterade identitet åtkomst till SAS för lagringskontot.   

1. Gå tillbaka till det lagringskonto du nyss skapade.   
2. Klicka på länken **åtkomstkontroll (IAM)** i vänstra panelen.  
3. Klicka på **+ Lägg till rolltilldelning** längst upp på sidan för att lägga till en ny rolltilldelning för den virtuella datorn
4. Ställ in Lagringskontodeltagare som **Roll**, till höger på sidan.  
5. I nästa listruta väljer du resursen Virtuell dator under **Tilldela behörighet till**.  
6. Kontrollera sedan att rätt prenumeration visas i listrutan **Prenumeration**. Välj Alla resursgrupper under **Resursgrupper**.  
7. Under **Välj** väljer du sedan din virtuella Windows-dator i listrutan och klickar på **Spara**. 

    ![Alternativ bildtext](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använd den för att anropa Azure Resource Manager 

Under resten av självstudien arbetar vi från den virtuella datorn.

Du måste använda Azure Resource Manager PowerShell-cmdletar i den här delen.  Om du inte har installerat den laddar du [ned den senaste versionen innan](/powershell/azure/) du fortsätter.

1. Gå till **Virtuella datorer** på Azure Portal, gå till din virtuella Windows-dator och klicka sedan på **Anslut** längst upp på sidan **Översikt**.
2. Ange ditt **användarnamn** och **lösenord** som du lade till när du skapade den virtuella Windows-datorn. 
3. Nu när du har skapat en **Anslutning till fjärrskrivbord** med den virtuella datorn.
4. Öppna PowerShell i fjärrsessionen och använd Invoke-WebRequest för att hämta en Azure Resource Manager-token från den lokala hanterade identiteten för Azure-resursslutpunkten.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Värdet för parametern ”resource” måste vara en exakt matchning av vad som förväntas av Azure AD. När du använder Azure Resource Manager-resurs-ID:t måste du ta med det avslutande snedstrecket i URI:n.
    
    Extrahera sedan elementet ”Content”, som lagras som en JSON-formaterad sträng (JavaScript Object Notation) i $response-objektet. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Extrahera sedan åtkomsttoken från svaret.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Hämta en SAS-autentiseringsuppgift från Azure Resource Manager om du vill göra lagringsanrop 

Använd nu PowerShell för att Resource Manager med hjälp av den åtkomsttoken som vi hämtade i föregående avsnitt för att skapa en SAS-autentiseringsbehörighet för lagring. När vi har SAS-autentiseringssuppgifter kan vi anropa lagringsåtgärder.

För denna begäran använder vi följande begäranparametrar och skapa SAS-autentiseringsuppgiften:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Dessa parametrar används i SAS-autentiseringsuppgiftens POST-begäran. Mer information om parametrarna som krävs när du skapar en SAS-autentiseringsuppgift finns i [referensen för tjänsten för SAS REST-listan](/rest/api/storagerp/storageaccounts/listservicesas).

Konvertera först parametrarna till JSON och anropa sedan lagringsslutpunkten `listServiceSas` för att skapa SAS-autentiseringssuppgifter:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> Eftersom URL:en är skiftlägeskänslig måste du använda exakt samma skiftläge som du använde tidigare när du namngav resursgruppen, inklusive versalt ”G” i ”resourceGroups”. 

Nu kan vi extrahera SAS-autentiseringssuppgifter från svaret:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Om du inspekterar SAS-autentiseringssuppgifter ser du något som liknar detta:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Nu ska vi skapa en fil med namnet ”test.txt”. Använd sedan SAS-autentiseringsuppgifter för att autentisera med cmdleten, ladda upp filen till `New-AzStorageContent` blobcontainern och ladda sedan ned filen.

```bash
echo "This is a test text file." > test.txt
```

Se till att installera alla Azure Storage-cmdlets först med hjälp av `Install-Module Azure.Storage`. Ladda upp bloben som du precis skapat med hjälp av PowerShell-cmdleten `Set-AzStorageBlobContent`:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Svar:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Du kan också ladda ned bloben som du precis laddade upp med hjälp av PowerShell-cmdleten `Get-AzStorageBlobContent`:

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Svar:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder en system tilldelad hanterad identitet på en virtuell Windows-dator för att komma åt Azure Storage med hjälp av en SAS-autentiseringsbehörighet.  Läs mer om Azure Storage SAS här:

> [!div class="nextstepaction"]
>[Använda signaturer för delad åtkomst (SAS)](../../storage/common/storage-sas-overview.md)
