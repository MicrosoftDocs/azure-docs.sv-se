---
title: 'Snabbstart: Ansluta till Azure SQL Database med GitHub Actions'
description: Använda Azure SQL från ett GitHub Actions arbetsflöde
author: juliakm
services: sql-database
ms.service: sql-database
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: ceb9f0f9ef2a88532d5af16a03fcfd0282da84f8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787295"
---
# <a name="use-github-actions-to-connect-to-azure-sql-database"></a>Använd GitHub Actions för att ansluta till Azure SQL Database

Kom igång med [GitHub Actions hjälp](https://docs.github.com/en/actions) av ett arbetsflöde för att distribuera databasuppdateringar till [Azure SQL Database](../azure-sql-iaas-vs-paas-what-is-overview.md). 

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande: 
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- En GitHub-lagringsplats med ett dacpac-paket ( `Database.dacpac` ). Om du inte har något GitHub-konto kan [du registrera dig kostnadsfritt.](https://github.com/join)  
- En Azure SQL Database.
    - [Snabbstart: Skapa en Azure SQL Database enkel databas](single-database-create-quickstart.md)
    - [Så här skapar du ett dacpac-paket från din befintliga SQL Server Database](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)

## <a name="workflow-file-overview"></a>Översikt över arbetsflödesfil

Ett GitHub Actions definieras av en YAML-fil (.yml) i `/.github/workflows/` sökvägen på din lagringsplats. Den här definitionen innehåller de olika steg och parametrar som utgör arbetsflödet.

Filen har två avsnitt:

|Avsnitt  |Uppgifter  |
|---------|---------|
|**Autentisering** | 1. Definiera ett huvudnamn för tjänsten. <br /> 2. Skapa en GitHub-hemlighet. |
|**Distribuera** | 1. Distribuera databasen. |

## <a name="generate-deployment-credentials"></a>Generera autentiseringsuppgifter för distribution

Du kan skapa ett [huvudnamn](../../active-directory/develop/app-objects-and-service-principals.md) för tjänsten [med kommandot az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) i Azure [CLI.](/cli/azure/) Kör det här kommandot [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja **knappen Prova.**

Ersätt platshållarna `server-name` med namnet på din SQL-server som finns i Azure. Ersätt och `subscription-id` med `resource-group` prenumerations-ID:t och resursgruppen som är anslutna till SQL-servern.  

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

Utdata är ett JSON-objekt med autentiseringsuppgifterna för rolltilldelning som ger åtkomst till databasen som liknar det här exemplet. Kopiera ditt JSON-utdataobjekt för senare.

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
> Det är alltid en bra idé att bevilja lägsta åtkomst. Omfånget i föregående exempel är begränsat till den specifika servern och inte hela resursgruppen.

## <a name="copy-the-sql-connection-string"></a>Kopiera SQL-anslutningssträngen 

I den Azure Portal går du till Azure SQL Database och öppnar **Inställningar**  >  **Anslutningssträngar**. Exempel på **ADO.NET**-anslutningssträng. Ersätt platshållarvärdena för `your_database` och `your_password` . Anslutningssträngen ser ut ungefär som dessa utdata. 

```output
    Server=tcp:my-sql-server.database.windows.net,1433;Initial Catalog={your-database};Persist Security Info=False;User ID={admin-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Du använder anslutningssträngen som en GitHub-hemlighet. 

## <a name="configure-the-github-secrets"></a>Konfigurera GitHub-hemligheterna

1. Bläddra till din lagringsplats i [GitHub.](https://github.com/)

1. Välj **Inställningar > Hemligheter > Ny hemlighet**.

1. Klistra in hela JSON-utdata från Azure CLI-kommandot i hemlighetens värdefält. Ge hemligheten namnet `AZURE_CREDENTIALS` .

    När du konfigurerar arbetsflödesfilen senare använder du hemligheten som indata `creds` för Azure-inloggningsåtgärden. Exempel:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Välj **Ny hemlighet** igen. 

1. Klistra in värdet för anslutningssträngen i hemlighetens värdefält. Ge hemligheten namnet `AZURE_SQL_CONNECTION_STRING` .


## <a name="add-your-workflow"></a>Lägga till arbetsflödet

1. Gå till **Åtgärder för** din GitHub-lagringsplats. 

2. Välj **Konfigurera arbetsflödet själv.** 

2. Ta bort allt efter `on:` avsnittet i arbetsflödesfilen. Ditt återstående arbetsflöde kan till exempel se ut så här. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Byt namn på `SQL for GitHub Actions` arbetsflödet och lägg till utchecknings- och inloggningsåtgärderna. De här åtgärderna kommer att checka ut din webbplatskod och autentisera med Azure med hjälp av `AZURE_CREDENTIALS` den GitHub-hemlighet som du skapade tidigare. 

    ```yaml
    name: SQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Använd åtgärden Azure SQL Deploy för att ansluta till din SQL-instans. Ersätt `SQL_SERVER_NAME` med namnet på servern. Du bör ha ett dacpac-paket ( `Database.dacpac` ) på lagringsplatsens rotnivå. 

    ```yaml
    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        sql-file: './Database.dacpac'
    ``` 

1. Slutför arbetsflödet genom att lägga till en åtgärd för utloggning av Azure. Här är det slutförda arbetsflödet. Filen visas i mappen på `.github/workflows` lagringsplatsen.

    ```yaml
   name: SQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        sql-file: './Database.dacpac'

        # Azure logout 
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Granska distributionen

1. Gå till **Åtgärder för** din GitHub-lagringsplats. 

1. Öppna det första resultatet om du vill se detaljerade loggar över arbetsflödets körning. 
 
   :::image type="content" source="media/quickstart-sql-github-actions/github-actions-run-sql.png" alt-text="Logg över GitHub-åtgärder körs":::

## <a name="clean-up-resources"></a>Rensa resurser

När din Azure SQL databas och lagringsplats inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen och GitHub-lagringsplatsen. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure- och GitHub-integrering](/azure/developer/github/)