---
title: SharePoint-filer – QnA Maker
description: Lägg till säkra SharePoint-datakällor i kunskaps basen för att utöka kunskaps basen med frågor och svar som kan skyddas med Active Directory.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 0832b54e02cabecb0b1f0e7af600b8adc621a8b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584778"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Lägg till en säker SharePoint-datakälla i kunskaps basen

Lägg till säkra molnbaserade SharePoint-datakällor i kunskaps basen för att utöka kunskaps basen med frågor och svar som kan skyddas med Active Directory.

När du lägger till ett skyddat SharePoint-dokument i kunskaps basen måste du begära Active Directory-behörighet för QnA Maker som QnA Maker Manager. När den här behörigheten ges från Active Directory Manager till QnA Maker för åtkomst till SharePoint behöver den inte anges igen. Varje efterföljande dokument tillägg till kunskaps basen behöver inte auktoriseras om det finns i samma SharePoint-resurs.

Om QnA Maker Knowledge Base Manager inte är Active Directory Manager måste du kommunicera med Active Directory Manager för att slutföra den här processen.

## <a name="prerequisites"></a>Förutsättningar

* Molnbaserad SharePoint-QnA Maker använder Microsoft Graph för behörigheter. Om ditt SharePoint är lokalt kan du inte extrahera från SharePoint eftersom Microsoft Graph inte kan fastställa behörigheter.
* URL-format – QnA Maker stöder endast SharePoint-URL: er som genereras för delning och är av format `https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>Lägg till filtyper som stöds i kunskaps basen

Du kan lägga till alla [filtyper](../index.yml) som stöds av QNA Maker från en SharePoint-webbplats till din kunskaps bas. Du kan behöva bevilja [behörigheter](#permissions) om fil resursen är skyddad.

1. I biblioteket med SharePoint-webbplatsen väljer du filens ellips-meny, `...` .
1. Kopiera filens URL.

   ![Hämta SharePoint-filens URL genom att välja filens ellips-meny och sedan kopiera URL: en.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. I QnA Maker Portal, på sidan **Inställningar** , lägger du till URL: en i kunskaps basen.

### <a name="images-with-sharepoint-files"></a>Bilder med SharePoint-filer

Om filerna innehåller bilder extraheras de inte. Du kan lägga till avbildningen från QnA Maker Portal efter att filen extraherats till QnA-par.

Lägg till avbildningen med följande markdown-syntax:

```markdown
![Explanation or description of image](URL of public image)
```

Texten i hakparenteserna, `[]` förklarar bilden. URL: en i parenteser, `()` är den direkta länken till bilden.

När du testar QnA-paret i den interaktiva test panelen visas bilden i stället för markdown-texten på den QnA Maker portalen. Detta validerar att avbildningen kan hämtas offentligt från klient programmet.

## <a name="permissions"></a>Behörigheter

Beviljande av behörigheter sker när en skyddad fil från en SharePoint-Server läggs till i en kunskaps bas. Beroende på hur SharePoint har kon figurer ATS och behörigheterna för den person som lägger till filen, kan detta kräva:

* inga ytterligare steg – den person som lägger till filen har alla behörigheter som krävs.
* steg för både [kunskaps bas hanteraren](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) och [Active Directory Manager](#active-directory-manager-grant-file-read-access-to-qna-maker).

Se stegen nedan.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Kunskaps bas hanterare: Lägg till SharePoint-datakälla i QnA Maker Portal

När **QNA Maker Manager** lägger till ett skyddat SharePoint-dokument i en kunskaps bas, initierar kunskaps bas hanteraren en begäran om behörighet som Active Directorys hanteraren måste slutföra.

Begäran börjar med ett popup-fönster för att autentisera till ett Active Directory-konto.

![Autentisera användar konto](../media/add-sharepoint-datasources/authenticate-user-account.png)

När QnA Maker Manager väljer kontot får Azure Active Directory administratören ett meddelande om att de behöver tillåta QnA Maker-appen (inte QnA Maker Manager) åtkomst till SharePoint-resursen. Azure Active Directory Manager måste göra detta för varje SharePoint-resurs, men inte i alla dokument i resursen.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active Directory-hanteraren: ge Läs behörighet till filen QnA Maker

Active Directory Manager (inte QnA Maker Manager) måste bevilja åtkomst till QnA Maker för att få åtkomst till SharePoint-resursen genom att välja [den här länken](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) för att auktorisera QNA Maker portalens SharePoint-Enterprise-app så att den har fil Läs behörighet.

![Azure Active Directory Manager beviljar behörighet interaktivt](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`.
-->
<!--
### Grant access from the interactive pop-up window

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions.

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**.

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**.

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**.

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows.

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Bevilja åtkomst från Azure Active Directory administrations Center

1. Active Directory Manager loggar in på Azure Portal och öppnar **[företags program](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**.

1. Sök efter `QnAMakerPortalSharePoint` välj QNA Maker-appen.

    [![Sök efter QnAMakerPortalSharePoint i listan över företags appar](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. Gå till **behörigheter** under **säkerhet**. Välj **bevilja administrativt godkännande för organisationen**.

    [![Välj autentiserad användare för Active Directory admin](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Välj ett Sign-On konto med behörighet att bevilja behörighet för Active Directory.




## <a name="add-sharepoint-data-source-with-apis"></a>Lägg till SharePoint-datakälla med API: er

Det finns en lösning för att lägga till det senaste SharePoint-innehållet via API med Azure Blob Storage. nedan följer stegen: 
1.  Hämta SharePoint-filerna lokalt. Användaren som anropar API: n måste ha åtkomst till SharePoint. 
1.  Överför dem till Azure Blob Storage. Då skapas en säker delad åtkomst med [hjälp av SAS-token.](../../../storage/common/storage-sas-overview.md#how-a-shared-access-signature-works) 
1. Skicka BLOB-URL: en som genereras med SAS-token till API för QnA Maker. Om du vill tillåta att frågan svarar på extrahering från filerna måste du lägga till suffixets filtyp som "&ext = PDF" eller "&ext = doc" i slutet av URL: en innan du skickar den till API för QnA Maker.


<!--
## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64).

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes.

    * Remove the equal character, `=` from the end of the value.
    * Replace `/` with `_`.
    * Replace `+` with `-`.
    * Append `u!` to be beginning of the string.

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) or [updating a knowledge base](/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center.
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**.
1. Select **Delete** to remove permissions.

-->

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Samar beta med din kunskaps bas](../index.yml)
