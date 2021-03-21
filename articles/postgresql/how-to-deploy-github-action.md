---
title: 'Snabb start: ansluta till Azure-PostgreSQL med GitHub-åtgärder'
description: Använda Azure-PostgreSQL från ett GitHub-åtgärds arbets flöde
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.author: sumuth
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 2e546801f95d9d884bdfb3f09a18b3fa6e2d78a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97365121"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-postgresql"></a>Snabb start: använda GitHub-åtgärder för att ansluta till Azure PostgreSQL

<Token>**gäller för:** :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for PostgreSQL-en server :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for PostgreSQL – flexibel Server</Token>

Kom igång med [GitHub-åtgärder](https://docs.github.com/en/actions) genom att använda ett arbets flöde för att distribuera databas uppdateringar till [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande:
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En GitHub-lagringsplats med exempel data ( `data.sql` ). Om du inte har ett GitHub-konto kan du [Registrera dig kostnads fritt](https://github.com/join).
- En Azure Database for PostgreSQL-Server.
    - [Snabbstart: Skapa en Azure Database for PostgreSQL-server i Azure Portal](quickstart-create-server-database-portal.md)

## <a name="workflow-file-overview"></a>Översikt över arbets flödes fil

Ett arbets flöde för GitHub-åtgärder definieras av en YAML-fil (. yml) i `/.github/workflows/` sökvägen i lagrings platsen. Den här definitionen innehåller de olika stegen och parametrarna som utgör arbets flödet.

Filen har två avsnitt:

|Avsnitt  |Uppgifter  |
|---------|---------|
|**Autentisering** | 1. definiera ett huvud namn för tjänsten. <br /> 2. skapa en GitHub-hemlighet. |
|**Distribuera** | 1. distribuera databasen. |

## <a name="generate-deployment-credentials"></a>Generera autentiseringsuppgifter för distribution

Du kan skapa ett [huvud namn för tjänsten](../active-directory/develop/app-objects-and-service-principals.md) med kommandot [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac&preserve-view=true) i [Azure CLI](/cli/azure/). Kör det här kommandot med [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja knappen **prova** .

Ersätt plats hållarna `server-name` med namnet på din postgresql-server som finns på Azure. Ersätt `subscription-id` och `resource-group` med PRENUMERATIONS-ID och resurs grupp som är ansluten till din postgresql-server.

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

Utdata är ett JSON-objekt med roll tilldelningens autentiseringsuppgifter som ger åtkomst till databasen som liknar den nedan. Kopiera det här utdata-JSON-objektet för senare.

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
> Det är alltid en bra idé att bevilja minimal åtkomst. Omfattningen i föregående exempel är begränsad till den särskilda servern och inte hela resurs gruppen.

## <a name="copy-the-postgresql-connection-string"></a>Kopiera anslutnings strängen PostgreSQL

I Azure Portal går du till Azure Database for PostgreSQL-servern och öppnar **Inställningar**  >  **anslutnings strängar**. Exempel på **ADO.NET**-anslutningssträng. Ersätt plats hållarnas värden för `your_database` och `your_password` . Anslutnings strängen kommer att se ut ungefär så här.

> [!IMPORTANT]
> - Använd för enskild server ```user=adminusername@servername```  . Observera att det ```@servername``` är obligatoriskt.
> - För flexibel Server använder ```user= adminusername``` du utan  ```@servername``` .

```output
psql host={servername.postgres.database.azure.com} port=5432 dbname={your_database} user={adminusername} password={your_database_password} sslmode=require
```

Du kommer att använda anslutnings strängen som GitHub-hemlighet.

## <a name="configure-the-github-secrets"></a>Konfigurera GitHub hemligheter

1. I [GitHub](https://github.com/)bläddrar du till din lagrings plats.

1. Välj **inställningar > hemligheter > ny hemlighet**.

1. Klistra in hela JSON-utdata från Azure CLI-kommandot i fältet hemligt värde. Ge hemligheten namnet `AZURE_CREDENTIALS` .

    När du konfigurerar arbets flödes filen senare använder du hemligheten för indata `creds` från åtgärden för Azure-inloggning. Exempel:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Välj **ny hemlighet** igen.

1. Klistra in värdet för anslutnings strängen i fältet hemligt värde. Ge hemligheten namnet `AZURE_POSTGRESQL_CONNECTION_STRING` .


## <a name="add-your-workflow"></a>Lägg till ditt arbets flöde

1. Gå till **åtgärder** för din GitHub-lagringsplats.

2. Välj **Konfigurera ditt arbets flöde själv**.

2. Ta bort allt efter `on:` avsnittet i arbets flödes filen. Ditt återstående arbets flöde kan till exempel se ut så här.

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Byt namn på arbets flödet `PostgreSQL for GitHub Actions` och Lägg till utcheckningen och inloggnings åtgärderna. De här åtgärderna kommer att checka ut din platskod och autentisera med Azure med hjälp av `AZURE_CREDENTIALS` GitHub-hemligheten som du skapade tidigare.

    ```yaml
    name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

2. Använd Azure PostgreSQL Deploy-åtgärden för att ansluta till din PostgreSQL-instans. Ersätt `POSTGRESQL_SERVER_NAME` med namnet på servern. Du bör ha en PostgreSQL-datafil med namnet `data.sql` på rot nivån för din lagrings plats.

    ```yaml
     - uses: azure/postgresql@v1
       with:
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        server-name: POSTGRESQL_SERVER_NAME
        sql-file: './data.sql'
    ```

3. Slutför ditt arbets flöde genom att lägga till en åtgärd för att logga ut från Azure. Här är det slutförda arbets flödet. Filen kommer att visas i `.github/workflows` mappen på din lagrings plats.

    ```yaml
   name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/postgresql@v1
      with:
        server-name: POSTGRESQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        sql-file: './data.sql'

        # Azure logout
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Granska distributionen

1. Gå till **åtgärder** för din GitHub-lagringsplats.

1. Öppna det första resultatet för att se detaljerade loggar för arbets flödets körning.

    :::image type="content" source="media/how-to-deploy-github-action/gitbub-action-postgres-success.png" alt-text="Logg för körning av GitHub-åtgärder":::

## <a name="clean-up-resources"></a>Rensa resurser

När din Azure PostgreSQL-databas och lagrings plats inte längre behövs, rensar du de resurser som du har distribuerat genom att ta bort resurs gruppen och din GitHub-lagringsplats.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure och GitHub-integrering](/azure/developer/github/)
<br/>
> [!div class="nextstepaction"]
> [Lär dig hur du ansluter till servern](how-to-connect-query-guide.md)
