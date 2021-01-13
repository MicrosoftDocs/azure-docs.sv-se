---
title: Använda GitHub Actions för att distribuera en statisk webbplats till Azure Storage
description: Azure Storage statisk webbplats som är värd för GitHub-åtgärder
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 01/11/2021
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: d8727bd747ef6d035cabbccf2ad42b80937a06a8
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180208"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Konfigurera ett GitHub-åtgärds arbets flöde för att distribuera din statiska webbplats i Azure Storage

Kom igång med [GitHub-åtgärder](https://docs.github.com/en/free-pro-team@latest/actions) genom att använda ett arbets flöde för att distribuera en statisk plats till ett Azure Storage-konto. När du har skapat ett arbets flöde för GitHub-åtgärder kommer du att kunna distribuera din webbplats automatiskt till Azure från GitHub när du gör ändringar i din plats kod.

> [!NOTE]
> Om du använder [Azures statiska Web Apps](../../static-web-apps/index.yml)behöver du inte konfigurera ett arbets flöde för GitHub-åtgärder manuellt.
> Azures statiska Web Apps skapar automatiskt ett GitHub åtgärds arbets flöde åt dig. 

## <a name="prerequisites"></a>Krav

Ett Azure-prenumerations-och GitHub-konto. 

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En GitHub-lagringsplats med din statiska webbplats kod. Om du inte har ett GitHub-konto kan du [Registrera dig kostnads fritt](https://github.com/join).  
- En fungerande statisk webbplats som finns i Azure Storage. Lär dig att vara [värd för en statisk webbplats i Azure Storage](storage-blob-static-website-how-to.md). Om du vill följa det här exemplet bör du även distribuera [Azure CDN](static-website-content-delivery-network.md).

> [!NOTE]
> Det är vanligt att använda ett Content Delivery Network (CDN) för att minska svars tiden för användarna runtom i världen och för att minska antalet transaktioner till ditt lagrings konto. Distribution av statiskt innehåll till en molnbaserad lagrings tjänst kan minska behovet av potentiellt kostsam beräknings instans. Mer information finns i [mönster för statisk innehålls värd](/azure/architecture/patterns/static-content-hosting).

## <a name="generate-deployment-credentials"></a>Generera autentiseringsuppgifter för distribution

Du kan skapa ett [huvud namn för tjänsten](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) med kommandot [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) i [Azure CLI](/cli/azure/). Kör det här kommandot med [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja knappen **prova** .

Ersätt plats hållaren `myStaticSite` med namnet på platsen som finns i Azure Storage. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

I exemplet ovan ersätter du plats hållarna med ditt prenumerations-ID och resurs gruppens namn. Utdata är ett JSON-objekt med roll tilldelningens autentiseringsuppgifter som ger åtkomst till ditt lagrings konto, ungefär som nedan. Kopiera det här JSON-objektet för senare.

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
> Det är alltid en bra idé att bevilja minimal åtkomst. Omfånget i föregående exempel är begränsat till den särskilda App Service-appen och inte hela resurs gruppen.

## <a name="configure-the-github-secret"></a>Konfigurera GitHub-hemligheten

1. I [GitHub](https://github.com/)bläddrar du till din lagrings plats.

1. Välj **inställningar > hemligheter > ny hemlighet**.

1. Klistra in hela JSON-utdata från Azure CLI-kommandot i fältet hemligt värde. Ge hemligheten ett namn som `AZURE_CREDENTIALS` .

    När du konfigurerar arbets flödes filen senare använder du hemligheten för indata `creds` från åtgärden för Azure-inloggning. Exempel:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>Lägg till ditt arbets flöde

1. Gå till **åtgärder** för din GitHub-lagringsplats. 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="Meny alternativ för GitHub-åtgärder":::

1. Välj **Konfigurera ditt arbets flöde själv**. 

1. Ta bort allt efter `on:` avsnittet i arbets flödes filen. Ditt återstående arbets flöde kan till exempel se ut så här. 

    ```yaml
    name: CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]
    ```

1. Byt namn på arbets flödet `Blob storage website CI` och Lägg till utcheckningen och inloggnings åtgärderna. De här åtgärderna kommer att checka ut din platskod och autentisera med Azure med hjälp av `AZURE_CREDENTIALS` GitHub-hemligheten som du skapade tidigare. 

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

1. Använd Azure CLI-åtgärden för att överföra din kod till Blob Storage och rensa CDN-slutpunkten. För `az storage blob upload-batch` ersätter du plats hållaren med ditt lagrings konto namn. Skriptet överförs till `$web` behållaren. För `az cdn endpoint purge` ersätter du plats hållarna med ditt CDN-profil namn, CDN-slutpunktens namn och resurs grupp.

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

1. Slutför ditt arbets flöde genom att lägga till en åtgärd för att logga ut från Azure. Här är det slutförda arbets flödet. Filen kommer att visas i `.github/workflows` mappen på din lagrings plats.

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

1. Gå till **åtgärder** för din GitHub-lagringsplats. 

1. Öppna det första resultatet för att se detaljerade loggar för arbets flödets körning. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="Logg för körning av GitHub-åtgärder":::

## <a name="clean-up-resources"></a>Rensa resurser

När den statiska webbplatsen och GitHub-lagringsplatsen inte längre behövs, rensar du de resurser som du har distribuerat genom att ta bort resurs gruppen och din GitHub-lagringsplats. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azures statiska Web Apps](../../static-web-apps/index.yml)