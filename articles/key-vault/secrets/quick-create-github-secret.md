---
title: Snabb start – Använd Azure Key Vault hemligheter i GitHub åtgärder arbets flöden
description: Använd Key Vault hemligheter i GitHub åtgärder för att automatisera dina arbets flöden för program utveckling
author: juliakm
ms.custom: github-actions-azure
ms.author: jukullam
ms.date: 11/24/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: c1e8062210de1d7d99f57a4e0b155492f7dfdd9a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785984"
---
# <a name="use-key-vault-secrets-in-github-actions-workflows"></a>Använda Key Vault hemligheter i arbets flöden för GitHub-åtgärder

Använd Key Vault hemligheter i dina [GitHub-åtgärder](https://help.github.com/en/articles/about-github-actions) och lagra lösen ord och andra hemligheter på ett säkert sätt i ett Azure Key Vault. Läs mer om [Key Vault](../general/overview.md). 

Med Key Vault-och GitHub-åtgärder har du fördelarna med ett centraliserat hanterings verktyg för hemligheter och alla fördelar med GitHub-åtgärder. GitHub-åtgärder är en uppsättning funktioner i GitHub för att automatisera dina arbets flöden för program utveckling. Du kan distribuera arbets flöden på samma plats där du lagrar kod och samar beta om pull-begäranden och-problem. 


## <a name="prerequisites"></a>Krav 
- Ett GitHub-konto. Om du inte har någon kan du registrera dig [kostnads fritt](https://github.com/join).  
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En Azure App ansluten till en GitHub-lagringsplats. I det här exemplet används [distributions behållare för att Azure App Service](/azure/developer/javascript/tutorial-vscode-docker-node-01). 
- Ett Azure Key Vault.  Du kan skapa en Azure Key Vault med hjälp av Azure Portal, Azure CLI eller Azure PowerShell.

## <a name="workflow-file-overview"></a>Översikt över arbets flödes fil

Ett arbets flöde definieras av en YAML-fil (. yml) i `/.github/workflows/` sökvägen i lagrings platsen. Den här definitionen innehåller de olika stegen och parametrarna som utgör arbets flödet.

Filen har autentisering med GitHub-åtgärder två avsnitt:

|Avsnitt  |Aktiviteter  |
|---------|---------|
|**Autentisering** | 1. definiera ett huvud namn för tjänsten. <br /> 2. skapa en GitHub-hemlighet. <br /> 3. Lägg till en roll tilldelning. |
|**Key Vault** | 1. Lägg till nyckel valvs åtgärden. <br /> 2. referens Key Vault Secret. |

## <a name="authentication"></a>Autentisering

Du kan skapa ett [huvud namn för tjänsten](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) med kommandot [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) i [Azure CLI](/cli/azure/). Kör det här kommandot med [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja knappen **prova** .

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

I exemplet ovan ersätter du plats hållarna med ditt prenumerations-ID och resurs gruppens namn. Ersätt plats hållaren `myApp` med namnet på ditt program. Utdata är ett JSON-objekt med roll tilldelningens autentiseringsuppgifter som ger åtkomst till din App Service-app på liknande sätt som nedan. Kopiera det här JSON-objektet för senare. Du behöver bara avsnitten med `clientId` `clientSecret` värdena,, `subscriptionId` och `tenantId` . 

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

### <a name="configure-the-github-secret"></a>Konfigurera GitHub-hemligheten

Skapa hemligheter för dina Azure-autentiseringsuppgifter, resurs grupp och prenumerationer. 

1. I [GitHub](https://github.com/)bläddrar du till din lagrings plats.

1. Välj **inställningar > hemligheter > ny hemlighet**.

1. Klistra in hela JSON-utdata från Azure CLI-kommandot i fältet hemligt värde. Ge hemligheten namnet `AZURE_CREDENTIALS` .

1. Kopiera värdet för `clientId` att använda senare. 

### <a name="add-a-role-assignment"></a>Lägg till en rolltilldelning 
 
Du måste bevilja åtkomst till Azure-tjänstens huvud namn så att du kan komma åt nyckel valvet för `get` och- `list` åtgärder. Om du inte gör det kommer du inte att kunna använda tjänstens huvud namn. 

Ersätt `keyVaultName` med namnet på ditt nyckel valv och `clientIdGUID` med värdet för `clientId` . 

```azurecli-interactive
    az keyvault set-policy -n {keyVaultName} --secret-permissions get list --spn {clientIdGUID}
```

## <a name="use-the-azure-key-vault-action"></a>Använd Azure Key Vault-åtgärden

Med [Azure Key Vault-åtgärden](https://github.com/marketplace/actions/azure-key-vault-get-secrets)kan du hämta en eller flera hemligheter från en Azure Key Vault-instans och använda den i dina GitHub-åtgärds arbets flöden.

Hemligheter som hämtas anges som utdata och även som miljövariabler. Variabler maskeras automatiskt när de skrivs ut till konsolen eller loggar.

```yaml
    - uses: Azure/get-keyvault-secrets@v1
      with:
        keyvault: "my Vault" # name of key vault in Azure portal
        secrets: 'mySecret'  # comma separated list of secret keys to fetch from key vault 
      id: myGetSecretAction # ID for secrets that you will reference
```

Om du vill använda ett nyckel valv i arbets flödet behöver du både Key Vault-åtgärden och för att referera till den åtgärden. 

I det här exemplet heter nyckel valvet `containervault` . Två Key Vault-hemligheter läggs till i miljön med Key Vault-åtgärden- `containerPassword` och `containerUsername` . 

Nyckel valvs värden refereras senare till i Docker login-aktiviteten med prefixet `steps.myGetSecretAction.outputs` . 

```yaml
name: Example key vault flow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - uses: actions/checkout@v2
    - uses: Azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - uses: Azure/get-keyvault-secrets@v1
      with: 
        keyvault: "containervault"
        secrets: 'containerPassword, containerUsername'
      id: myGetSecretAction
    - uses: azure/docker-login@v1
      with:
        login-server: myregistry.azurecr.io
        username: ${{ steps.myGetSecretAction.outputs.containerUsername }}
        password: ${{ steps.myGetSecretAction.outputs.containerPassword }}
    - run: |
        docker build . -t myregistry.azurecr.io/myapp:${{ github.sha }}
        docker push myregistry.azurecr.io/myapp:${{ github.sha }}     
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'myregistry.azurecr.io/myapp:${{ github.sha }}'
```

## <a name="clean-up-resources"></a>Rensa resurser

När din Azure-app, GitHub-lagringsplats och nyckel valv inte längre behövs, rensar du de resurser som du har distribuerat genom att ta bort resurs gruppen för appen, GitHub-lagringsplatsen och nyckel valvet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure Key Vault](../general/overview.md)