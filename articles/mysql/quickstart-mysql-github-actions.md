---
title: 'Snabbstart: Ansluta till Azure MySQL med GitHub Actions'
description: Använda Azure MySQL från ett GitHub Actions arbetsflöde
author: juliakm
ms.service: mysql
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 807fdbb1844eb6f89f71e639537a65baf8c76ad5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761771"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-mysql"></a>Snabbstart: Använda GitHub Actions för att ansluta till Azure MySQL

**GÄLLER FÖR**: :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for MySQL – enskild server Azure Database for MySQL – flexibel :::image type="icon" source="./media/applies-to/yes.png" border="false"::: server

Kom igång med [GitHub Actions genom att](https://docs.github.com/en/actions) använda ett arbetsflöde för att distribuera databasuppdateringar till [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/).


## <a name="prerequisites"></a>Förutsättningar

Du behöver följande: 
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- En GitHub-lagringsplats med exempeldata ( `data.sql` ). Om du inte har något GitHub-konto kan [du registrera dig kostnadsfritt.](https://github.com/join)  
- En Azure Database for MySQL server.
    - [Snabbstart: Skapa Azure Database for MySQL server i Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="workflow-file-overview"></a>Översikt över arbetsflödesfil

Ett GitHub Actions arbetsflöde definieras av en YAML-fil (.yml) i `/.github/workflows/` sökvägen på lagringsplatsen. Den här definitionen innehåller de olika steg och parametrar som utgör arbetsflödet.

Filen har två avsnitt:

|Avsnitt  |Uppgifter  |
|---------|---------|
|**Autentisering** | 1. Definiera ett huvudnamn för tjänsten. <br /> 2. Skapa en GitHub-hemlighet. |
|**Distribuera** | 1. Distribuera databasen. |

## <a name="generate-deployment-credentials"></a>Generera autentiseringsuppgifter för distribution

Du kan skapa ett [huvudnamn](../active-directory/develop/app-objects-and-service-principals.md) för tjänsten [med kommandot az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac&preserve-view=true) i Azure [CLI.](/cli/azure/) Kör det här kommandot [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja **knappen Prova.**

Ersätt platshållarna `server-name` med namnet på din MySQL-server som finns i Azure. Ersätt och `subscription-id` med `resource-group` prenumerations-ID:t och resursgruppen som är anslutna till MySQL-servern.  

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

Utdata är ett JSON-objekt med autentiseringsuppgifterna för rolltilldelning som ger åtkomst till din databas på ett sätt som liknar nedan. Kopiera JSON-utdataobjektet för senare tillfälle.

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
> Det är alltid en bra idé att bevilja minsta möjliga åtkomst. Omfånget i föregående exempel är begränsat till den specifika servern och inte hela resursgruppen.

## <a name="copy-the-mysql-connection-string"></a>Kopiera MySQL-anslutningssträngen 

I Azure Portal du till servern Azure Database for MySQL och öppnar **Inställningar**  >  **Anslutningssträngar**. Exempel på **ADO.NET**-anslutningssträng. Ersätt platshållarvärdena för `your_database` och `your_password` . Anslutningssträngen ser ut ungefär så här. 

> [!IMPORTANT]
> - För enskild server använder **du Uid= adminusername@servername**. Observera att **@servername** krävs.
> - För flexibel server använder **du Uid= adminusername** utan @servername . Observera att MySQL – flexibel server är en förhandsversion. 


```output
   Server=my-mysql-server.mysql.database.azure.com; Port=3306; Database={your_database}; Uid=adminname@my-mysql-server; Pwd={your_password}; SslMode=Preferred;
```
Du använder anslutningssträngen som en GitHub-hemlighet. 

## <a name="configure-the-github-secrets"></a>Konfigurera GitHub-hemligheterna

1. I [GitHub](https://github.com/)bläddrar du till din lagringsplats.

1. Välj **Inställningar > Hemligheter > Ny hemlighet**.

1. Klistra in hela JSON-utdata från Azure CLI-kommandot i hemlighetens värdefält. Ge hemligheten namnet `AZURE_CREDENTIALS` .

    När du konfigurerar arbetsflödesfilen senare använder du hemligheten som indata `creds` för azure-inloggningsåtgärden. Exempel:

    ```yaml
    - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Välj **Ny hemlighet** igen. 

1. Klistra in värdet för anslutningssträngen i hemlighetens värdefält. Ge hemligheten namnet `AZURE_MYSQL_CONNECTION_STRING` .


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

1. Byt namn på `MySQL for GitHub Actions` arbetsflödet och lägg till utchecknings- och inloggningsåtgärderna. De här åtgärderna kommer att checka ut din webbplatskod och autentisera med Azure med hjälp av `AZURE_CREDENTIALS` den GitHub-hemlighet som du skapade tidigare. 

    ```yaml
    name: MySQL for GitHub Actions

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

1. Använd Azure MySQL Deploy-åtgärden för att ansluta till din MySQL-instans. Ersätt `MYSQL_SERVER_NAME` med namnet på servern. Du bör ha en MySQL-datafil `data.sql` med namnet på lagringsplatsens rotnivå. 

    ```yaml
    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'
    ``` 

1. Slutför arbetsflödet genom att lägga till en åtgärd för utloggning av Azure. Här är det slutförda arbetsflödet. Filen visas i mappen på `.github/workflows` lagringsplatsen.

    ```yaml
    name: MySQL for GitHub Actions

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

            - uses: azure/mysql@v1
                with:
                    server-name: MYSQL_SERVER_NAME
                    connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
                    sql-file: './data.sql'

            # Azure logout 
            - name: logout
                run: |
                    az logout
    ```

## <a name="review-your-deployment"></a>Granska distributionen

1. Gå till **Åtgärder för** din GitHub-lagringsplats. 

1. Öppna det första resultatet om du vill se detaljerade loggar för arbetsflödets körning. 
 
    :::image type="content" source="media/quickstart-mysql-github-actions/github-actions-run-mysql.png" alt-text="Loggen med GitHub-åtgärder körs":::

## <a name="clean-up-resources"></a>Rensa resurser

När din Azure MySQL-databas och lagringsplats inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen och GitHub-lagringsplatsen. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure- och GitHub-integrering](/azure/developer/github/)
