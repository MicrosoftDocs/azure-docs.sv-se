---
title: Distribuera Resource Manager med hjälp av GitHub Actions
description: Beskriver hur du distribuerar Azure Resource Manager (ARM-mallar) med hjälp av GitHub Actions.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: ec29ae019555c54ccdcef9dd743706f8d6401bbd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781981"
---
# <a name="deploy-arm-templates-by-using-github-actions"></a>Distribuera ARM-mallar med hjälp av GitHub Actions

[GitHub Actions](https://docs.github.com/en/actions) är en uppsättning funktioner i GitHub för att automatisera arbetsflöden för programutveckling på samma plats som du lagrar kod och samarbetar kring pull-begäranden och problem.

Använd åtgärden [Distribuera Azure Resource Manager mall för](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) att automatisera distributionen av en Azure Resource Manager (ARM-mall) till Azure.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Ett GitHub-konto. Om du inte har någon kan du registrera dig [kostnadsfritt.](https://github.com/join)
    - En GitHub-lagringsplats för att lagra Resource Manager mallar och arbetsflödesfiler. Information om hur du skapar en finns [i Skapa en ny lagringsplats.](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)


## <a name="workflow-file-overview"></a>Översikt över arbetsflödesfil

Ett arbetsflöde definieras av en YAML-fil (.yml) i `/.github/workflows/` sökvägen på lagringsplatsen. Den här definitionen innehåller de olika steg och parametrar som utgör arbetsflödet.

Filen har två avsnitt:

|Avsnitt  |Uppgifter  |
|---------|---------|
|**Autentisering** | 1. Definiera ett huvudnamn för tjänsten. <br /> 2. Skapa en GitHub-hemlighet. |
|**Distribuera** | 1. Distribuera Resource Manager mall. |

## <a name="generate-deployment-credentials"></a>Generera autentiseringsuppgifter för distribution


Du kan skapa ett [huvudnamn](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) för tjänsten [med kommandot az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) i Azure [CLI.](/cli/azure/) Kör det här kommandot [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja **knappen Prova.**

Skapa en resursgrupp om du inte redan har en.

```azurecli-interactive
    az group create -n {MyResourceGroup} -l {location}
```

Ersätt platshållaren `myApp` med namnet på ditt program.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

I exemplet ovan ersätter du platshållarna med ditt prenumerations-ID och namnet på resursgruppen. Utdata är ett JSON-objekt med autentiseringsuppgifter för rolltilldelning som ger åtkomst till din App Service som liknar nedan. Kopiera det här JSON-objektet för senare tillfälle. Du behöver bara avsnitten med `clientId` värdena , , och `clientSecret` `subscriptionId` `tenantId` .

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
> Det är alltid en bra idé att bevilja minsta möjliga åtkomst. Omfånget i föregående exempel är begränsat till resursgruppen.



## <a name="configure-the-github-secrets"></a>Konfigurera GitHub-hemligheterna

Du måste skapa hemligheter för dina autentiseringsuppgifter, resursgrupp och prenumerationer i Azure.

1. I [GitHub](https://github.com/)bläddrar du till din lagringsplats.

1. Välj **Inställningar > Hemligheter > Ny hemlighet**.

1. Klistra in hela JSON-utdata från Azure CLI-kommandot i hemlighetens värdefält. Ge hemligheten namnet `AZURE_CREDENTIALS` .

1. Skapa en till hemlighet med namnet `AZURE_RG` . Lägg till namnet på resursgruppen i hemlighetens värdefält (exempel: `myResourceGroup` ).

1. Skapa ytterligare en hemlighet med namnet `AZURE_SUBSCRIPTION` . Lägg till ditt prenumerations-ID i hemlighetens värdefält (exempel: `90fd3f9d-4c61-432d-99ba-1273f236afa2` ).

## <a name="add-resource-manager-template"></a>Lägga Resource Manager mall

Lägg till Resource Manager mall på GitHub-lagringsplatsen. Den här mallen skapar ett lagringskonto.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Du kan placera filen var som helst på lagringsplatsen. Arbetsflödesexempel i nästa avsnitt förutsätter att mallfilen heter **azuredeploy.js** och lagras i roten på lagringsplatsen.

## <a name="create-workflow"></a>Skapa arbetsflöde

Arbetsflödesfilen måste lagras i **mappen .github/workflows** i roten på din lagringsplats. Filnamnstillägget för arbetsflödet kan vara **antingen .yml** eller **.yaml**.

1. Från GitHub-lagringsplatsen väljer **du Åtgärder** på den översta menyn.
1. Välj **Nytt arbetsflöde.**
1. Välj **konfigurera ett arbetsflöde själv.**
1. Byt namn på arbetsflödesfilen om du föredrar ett annat namn än **main.yml**. Exempel: **deployStorageAccount.yml**.
1. Ersätt innehållet i YML-filen med följande:

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@main

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

          # Deploy ARM template
        - name: Run ARM deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS

          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```
    > [!NOTE]
    > Du kan i stället ange en JSON-formatparameterfil i ARM-distributionen (exempel: `.azuredeploy.parameters.json` ).

    Det första avsnittet i arbetsflödesfilen innehåller:

    - **name**: Namnet på arbetsflödet.
    - **på**: Namnet på de GitHub-händelser som utlöser arbetsflödet. Arbetsflödet utlöses när det finns en push-händelse på main-grenen som ändrar minst en av de två angivna filerna. De två filerna är arbetsflödesfilen och mallfilen.

1. Välj **Start commit** (Starta incheckning).
1. Välj **Genomför direkt till huvudgrenen**.
1. Välj **Genomför ny fil** (eller Genomför **ändringar).**

Eftersom arbetsflödet är konfigurerat för att utlösas av antingen arbetsflödesfilen eller mallfilen som uppdateras, startar arbetsflödet direkt efter att du har gjort ändringarna.

## <a name="check-workflow-status"></a>Kontrollera arbetsflödesstatus

1. Välj **fliken** Åtgärder. Arbetsflödet Create **deployStorageAccount.yml** visas. Det tar 1–2 minuter att köra arbetsflödet.
1. Välj arbetsflödet för att öppna det.
1. Välj **Kör ARM-distribution** på menyn för att verifiera distributionen.

## <a name="clean-up-resources"></a>Rensa resurser
När resursgruppen och lagringsplatsen inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen och GitHub-lagringsplatsen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa din första ARM-mall](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Learn-modul: Automatisera distributionen av ARM-mallar med hjälp av GitHub Actions](/learn/modules/deploy-templates-command-line-github-actions/)
