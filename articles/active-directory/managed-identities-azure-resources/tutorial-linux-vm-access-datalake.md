---
title: Självstudie `:` Använd en hanterad identitet för att få åtkomst till Azure Data Lake Store – Linux – Azure AD
description: En självstudiekurs som visar hur du använder en systemtilldelad hanterad identitet för virtuell Linux-dator för åtkomst till Azure Data Lake Store.
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
ms.date: 01/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d465419dfe36fd5dd67abdef22a6f54fba69a98e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89267470"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Självstudier: Använda systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Azure Data Lake Store

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Den här självstudien visar hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator (VM) för att få åtkomst till Azure Data Lake Store. Lär dig att: 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Bevilja din virtuella dator åtkomst till Azure Data Lake Store.
> * Få en åtkomsttoken genom att använda den virtuella datorns systemtilldelade hanterade identitet för åtkomst till Azure Data Lake Store.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-access"></a>Bevilja åtkomst

I det här avsnittet visas hur du beviljar din VM-åtkomst till filer och mappar i Azure Data Lake Store. Du kan använda en befintlig Data Lake Store-instans eller skapa en ny för det här steget. Så här skapar du en Data Lake Store-instans med hjälp av Azure-portalen i [snabbstarten för Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md). Det finns även snabbstarter som använder Azure CLI och Azure PowerShell i [dokumentationen om Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md).

I Data Lake Store skapar du en ny mapp och ger den systemtilldelade hanterade identiteten på virtuell Linux-dator behörighet att läsa, skriva och köra filer i mappen:

1. I Azure-portalen väljer du **Data Lake Store** i den vänstra rutan.
2. Välj den Data Lake Store-instans som du vill använda.
3. Klicka på **Datautforskaren** i kommandofältet.
4. Rotmappen för Data Lake Store-instansen har valts. Välj **Åtkomst** i kommandofältet.
5. Välj **Lägg till**.  I rutan **Välj** anger du namnet på den virtuella datorn – till exempel **DevTestVM**. Välj den virtuella datorn från sökresultatet och klicka sedan på **Välj**.
6. Klicka på **Välj behörigheter**.  Välj **Läs** och **Kör**, lägg till **den här mappen** och lägg till den med **endast åtkomstbehörighet**. Välj **OK**.  Behörigheten bör nu har lagts till.
7. Stäng den fönstret **Åtkomst**.
8. För den här självstudien skapar du en ny mapp. Välj **Ny mapp** i kommandofältet och ge den nya mappen ett namn, till exempel **TestFolder**.  Välj **OK**.
9. Välj den mapp du skapade och välj sedan **Åtkomst** i kommandofältet.
10. Välj **Lägg till**, som i steg 5. I rutan **Välj** anger du namnet på den virtuella datorn. Välj den virtuella datorn från sökresultatet och klicka sedan på **Välj**.
11. Välj **Välj behörigheter**, som i steg 6. Välj **Läs**, **Skriv** och **Kör**, lägga till **den här mappen** och lägg till den som **åtkomstbehörighetsinlägg och standardbehörighetsinlägg**. Välj **OK**.  Behörigheten bör nu har lagts till.

Hanterade identiteter för Azure-resurser kan nu utföra alla åtgärder på filer i mappen du har skapat. Mer information om hur du hanterar åtkomst till Data Lake Store finns i [Åtkomstkontroll i Data Lake Store](../../data-lake-store/data-lake-store-access-control.md).

## <a name="get-an-access-token"></a>Hämta en åtkomsttoken 

Det här avsnittet visar hur du hämtar en åtkomsttoken och anropar Data Lake Store fil systemet. Azure Data Lake Store har inbyggt stöd för Azure AD-autentisering, vilket gör att åtkomsttoken som hämtas via hanterade identiteter för Azure-resurser. För att autentisera till Data Lake Store-filsystemet skickar du en åtkomsttoken som utfärdats av Azure AD till slutpunkten för ditt Data Lake Store-filsystem. Åtkomsttoken är i ett Authorization-huvud i formatet "Bearer \<ACCESS_TOKEN_VALUE\> ".  Mer information om Data Lake Store-stöd för Azure AD-autentisering finns avsnittet om [autentisering med Data Lake Store med hjälp av Azure Active Directory](../../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).

I den här självstudien autentiserar du REST-API:et för Data Lake Store-filsystemet med hjälp av CURL för att göra REST-begäranden.

> [!NOTE]
> Klient-SDK:er för Data Lake Store-filsystemet stöder inte ännu hanterade identiteter för Azure-resurser.

För att slutföra de här stegen behöver du en SSH-klient. Om du använder Windows kan du använda SSH-klienten i [Windows-undersystemet för Linux](/windows/wsl/about). Om du behöver hjälp med att konfigurera SSH-klientens nycklar, se [hur du använder SSH-nycklar med Windows på Azure](../../virtual-machines/linux/ssh-from-windows.md) eller [hur du skapar och använder ett offentligt och privat SSH-nyckelpar för virtuella Linux-datorer i Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Bläddra till din virtuella Linux-dator i portalen. I **Översikt** väljer du **Anslut**.  
2. Anslut till den virtuella datorn med valfri SSH-klient. 
3. I terminalfönstret, med hjälp av CURL, skickar du en begäran till den lokala hanterade identiteter för Azure-resursslutpunkter för att hämta en åtkomsttoken för Data Lake Store-filsystemet. Resurs-ID: t för Data Lake Store är `https://datalake.azure.net/` .  Det är viktigt att inkludera avslutande snedstreck i resurs-ID.
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -H Metadata:true   
   ```
    
   Ett lyckat svar returnerar den åtkomsttoken som du använder för att autentisera till Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Med hjälp av CURL kan du skicka en förfrågan till REST-slutpunkten för ditt Data Lake Store-filsystem för att lista mappar i rotmappen. Det här är ett enkelt sätt att kontrollera att allt är korrekt konfigurerat. Kopiera värdet för åtkomsttoken från föregående steg. Det är viktigt att strängen ”Bearer” (ägartoken) i auktoriseringsrubriken har versalt ”B.” Du hittar namnet på din Data Lake Store-instans i **översikten** av **Data Lake Store**-fönstret i Azure-portalen.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Ett lyckat svar ut så här:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Nu kan du ladda upp en fil till din Data Lake Store-instans. Skapa först en fil som ska laddas upp.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Med hjälp av CURL kan du skicka en förfrågan till REST-slutpunkten för ditt Data Lake Store-filsystem för att ladda upp filen till mappen du skapade innan. Överföringen innebär en omdirigering och CURL följer omdirigeringen automatiskt. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Ett lyckat svar ut så här:

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Du kan lägga till i filer, ladda ned filer med mera med hjälp av API:er för Data Lake Store-filsystemet.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Azure Data Lake Store. Läs mer om Azure Data Lake Store:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md)