---
title: Använda GitHub Actions för att distribuera en statisk webbplats till Azure Storage
description: Azure Storage värdtjänster för statiska webbplatser med GitHub Actions
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 01/11/2021
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 3ae0904eda2608026ad09ba8b8993008380725f4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788537"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Konfigurera ett arbetsflöde GitHub Actions att distribuera din statiska webbplats i Azure Storage

Kom igång med [GitHub Actions med](https://docs.github.com/en/actions) hjälp av ett arbetsflöde för att distribuera en statisk webbplats till ett Azure Storage-konto. När du har GitHub Actions ett arbetsflöde kan du automatiskt distribuera webbplatsen till Azure från GitHub när du gör ändringar i koden för din webbplats.

> [!NOTE]
> Om du använder [Azure Static Web Apps](../../static-web-apps/index.yml)behöver du inte manuellt konfigurera ett GitHub Actions arbetsflöde.
> Azure Static Web Apps automatiskt ett GitHub Actions arbetsflöde åt dig. 

## <a name="prerequisites"></a>Förutsättningar

En Azure-prenumeration och ett GitHub-konto. 

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- En GitHub-lagringsplats med din statiska webbplatskod. Om du inte har något GitHub-konto kan [du registrera dig kostnadsfritt.](https://github.com/join)  
- En fungerande statisk webbplats som finns i Azure Storage. Lär dig hur [du är värd för en statisk webbplats i Azure Storage](storage-blob-static-website-how-to.md). Om du vill följa det här exemplet bör du även [distribuera Azure CDN](static-website-content-delivery-network.md).

> [!NOTE]
> Det är vanligt att använda ett nätverk för innehållsleverans (CDN) för att minska svarstiden för användarna över hela världen och för att minska antalet transaktioner till ditt lagringskonto. Att distribuera statiskt innehåll till en molnbaserad lagringstjänst kan minska behovet av potentiellt dyra beräkningsinstanser. Mer information finns i Mönster [för värdtjänster för statiskt innehåll.](/azure/architecture/patterns/static-content-hosting)

## <a name="generate-deployment-credentials"></a>Generera autentiseringsuppgifter för distribution

Du kan skapa ett [huvudnamn](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) för tjänsten [med kommandot az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) i Azure [CLI.](/cli/azure/) Kör det här kommandot [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja **knappen Prova.**

Ersätt platshållaren `myStaticSite` med namnet på din webbplats som finns i Azure Storage. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

I exemplet ovan ersätter du platshållarna med ditt prenumerations-ID och namnet på resursgruppen. Utdata är ett JSON-objekt med autentiseringsuppgifter för rolltilldelning som ger åtkomst till ditt lagringskonto på ett sätt som liknar nedan. Kopiera det här JSON-objektet för senare tillfälle.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Det är alltid en bra idé att bevilja minsta möjliga åtkomst. Omfånget i föregående exempel är begränsat till den App Service appen och inte hela resursgruppen.

## <a name="configure-the-github-secret"></a>Konfigurera GitHub-hemligheten

1. I [GitHub](https://github.com/)bläddrar du till din lagringsplats.

1. Välj **Inställningar > Hemligheter > Ny hemlighet**.

1. Klistra in hela JSON-utdata från Azure CLI-kommandot i hemlighetens värdefält. Ge hemligheten ett namn som `AZURE_CREDENTIALS` .

    När du konfigurerar arbetsflödesfilen senare använder du hemligheten som indata `creds` för azure-inloggningsåtgärden. Exempel:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>Lägga till arbetsflödet

1. Gå till **Åtgärder för** din GitHub-lagringsplats. 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="Menyalternativet GitHub Actions":::

1. Välj **Konfigurera arbetsflödet själv.** 

1. Ta bort allt efter `on:` avsnittet i arbetsflödesfilen. Ditt återstående arbetsflöde kan till exempel se ut så här. 

    ```yaml
    name: CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]
    ```

1. Byt namn på `Blob storage website CI` arbetsflödet och lägg till utchecknings- och inloggningsåtgärderna. De här åtgärderna kommer att checka ut din webbplatskod och autentisera med Azure med hjälp av `AZURE_CREDENTIALS` den GitHub-hemlighet som du skapade tidigare. 

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Använd Azure CLI-åtgärden för att ladda upp koden till Blob Storage och för att rensa CDN-slutpunkten. För `az storage blob upload-batch` ersätter du platshållaren med namnet på ditt lagringskonto. Skriptet laddas upp till `$web` containern. För `az cdn endpoint purge` ersätter du platshållarna med ditt CDN-profilnamn, CDN-slutpunktsnamn och resursgrupp.

    ```yaml
        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
    ``` 

1. Slutför arbetsflödet genom att lägga till en åtgärd för utloggning av Azure. Här är det färdiga arbetsflödet. Filen visas i mappen på `.github/workflows` lagringsplatsen.

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}

        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
      
      # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>Granska distributionen

1. Gå till **Åtgärder för** din GitHub-lagringsplats. 

1. Öppna det första resultatet om du vill se detaljerade loggar för arbetsflödets körning. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="Loggen med GitHub-åtgärder körs":::

## <a name="clean-up-resources"></a>Rensa resurser

När din statiska webbplats och GitHub-lagringsplatsen inte längre behövs rensar du de resurser som du distribuerade genom att ta bort resursgruppen och GitHub-lagringsplatsen. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure Static Web Apps](../../static-web-apps/index.yml)
