---
title: Autentisera Azure våren Cloud med Key Vault i GitHub-åtgärder
description: Så här använder du Key Vault med CI/CD-arbetsflöde för Azure våren Cloud med GitHub-åtgärder
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0ea0db1faf8c452958b8d95c193d45506057777c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673340"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Autentisera Azure våren Cloud med Key Vault i GitHub-åtgärder

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Key Vault är en säker plats för att lagra nycklar. Företags användare måste lagra autentiseringsuppgifter för CI/CD-miljöer i den omfattning som de styr. Nyckeln för att hämta autentiseringsuppgifter i nyckel valvet bör begränsas till resurs omfånget.  Den har enbart åtkomst till nyckel valvets omfång, inte hela Azure-omfånget. Det är som en nyckel som bara kan öppna en stark ruta, inte en huvud nyckel som kan öppna alla dörrar i en byggnad. Det är ett sätt att hämta en nyckel till en annan nyckel, vilket är användbart i ett CICD-arbetsflöde. 

## <a name="generate-credential"></a>Generera autentiseringsuppgifter
Om du vill generera en nyckel för att komma åt nyckel valvet, kör du kommandot nedan på den lokala datorn:

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
Det omfång som anges av `--scopes` parametern begränsar nyckel åtkomsten till resursen.  Den kan bara komma åt den starka rutan.

Med resultat:
```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Spara sedan resultaten till GitHub- **hemligheter** enligt beskrivningen i [Konfigurera din GitHub-lagringsplats och autentisera med Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate).

## <a name="add-access-policies-for-the-credential"></a>Lägg till åtkomst principer för autentiseringsuppgiften
De autentiseringsuppgifter som du har skapat ovan kan bara få allmän information om Key Vault, inte innehållet som den lagrar.  Om du vill hämta hemligheterna som lagras i Key Vault måste du ange åtkomst principer för autentiseringsuppgifterna.

Gå till **Key Vault** instrument panelen i Azure Portal, klicka på menyn **åtkomst kontroll** och öppna sedan fliken **roll tilldelningar** . Välj **appar** för **typ** och `This resource` för **omfång**.  Du bör se de autentiseringsuppgifter som du skapade i föregående steg:

 ![Ange åtkomst princip](./media/github-actions/key-vault1.png)

Kopiera namnet på autentiseringsuppgiften, till exempel `azure-cli-2020-01-19-04-39-02` . Öppna menyn **åtkomst principer** , klicka på länken **Lägg till åtkomst princip** .  Välj `Secret Management` för **mall** och välj sedan **huvud konto**. Klistra in **namnet på autentiseringsuppgiften i** / rutan **Välj** inmatare:

 ![Välj](./media/github-actions/key-vault2.png)

 Klicka på knappen **Lägg till** i dialog rutan **Lägg till åtkomst princip** och klicka sedan på **Spara**.

## <a name="generate-full-scope-azure-credential"></a>Generera Azure-autentiseringsuppgift med fullständig omfattning
Detta är huvud nyckeln för att öppna alla dörrar i byggnaden. Proceduren liknar föregående steg, men här ändrar vi omfattningen för att generera huvud nyckeln:

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Igen, resultat:
```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Kopiera hela JSON-strängen.  Bo tillbaka till **Key Vault** instrument panel. Öppna **hemligheter** -menyn och klicka på knappen **generera/importera** . Mata in det hemliga namnet, till exempel `AZURE-CREDENTIALS-FOR-SPRING` . Klistra in JSON-autentiseringsuppgiften i rutan **värde** inmatare. Du kanske märker att indata-rutan är ett textfält med en rad, i stället för ett text område med flera rader.  Du kan klistra in hela JSON-strängen där.

 ![Fullständiga autentiseringsuppgifter för omfattning](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Kombinera autentiseringsuppgifter i GitHub-åtgärder
Ange de autentiseringsuppgifter som ska användas när CICD-pipeline körs:

```console
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "<Your Key Vault Name>"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>Nästa steg
* [GitHub åtgärder för våren Cloud](./spring-cloud-howto-github-actions.md)
