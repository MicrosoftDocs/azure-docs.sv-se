---
title: Anslut med hanterad identitet – Azure Database for PostgreSQL-enskild server
description: Lär dig mer om hur du ansluter och autentiserar med hanterad identitet för autentisering med Azure Database for PostgreSQL
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b0d6b083228ddab9d958bf3480b592f7ee0d892d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092646"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>Ansluta med hanterad identitet till Azure Database for PostgreSQL

Den här artikeln visar hur du använder en användardefinierad identitet för en virtuell Azure-dator (VM) för att få åtkomst till en Azure Database for PostgreSQL-Server. Hanterade tjänstidentiteter hanteras automatiskt av Azure och gör att du kan autentisera mot tjänster som stöder Azure AD-autentisering, utan att du behöver skriva in autentiseringsuppgifter i koden. 

Lär dig att:
- Bevilja din VM-åtkomst till en Azure Database for PostgreSQL-Server
- Skapa en användare i databasen som representerar den VM-tilldelade identiteten för den virtuella datorn
- Hämta en åtkomsttoken med den virtuella dator identiteten och Använd den för att fråga en Azure Database for PostgreSQL Server
- Implementera hämtning av token i ett exempel program i C#

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till funktionen för hanterade identiteter för Azure-resurser kan du läsa igenom den här [översikten](../../articles/active-directory/managed-identities-azure-resources/overview.md). Om du inte har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- För att kunna utföra den nödvändiga resurs skapande och roll hantering måste ditt konto ha behörigheten "ägare" i lämpligt omfång (din prenumeration eller resurs grupp). Om du behöver hjälp med roll tilldelning, se [tilldela Azure-roller för att hantera åtkomst till dina Azure-prenumerations resurser](../../articles/role-based-access-control/role-assignments-portal.md).
- Du behöver en virtuell Azure-dator (till exempel köra Ubuntu Linux) som du vill använda för att komma åt din databas med hanterad identitet
- Du behöver en Azure Database for PostgreSQL databas server där [Azure AD-autentisering](howto-configure-sign-in-aad-authentication.md) har kon figurer ATS
- Om du vill följa C#-exemplet måste du först slutföra guiden så här ansluter du till [c#](connect-csharp.md)

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>Skapa en användardefinierad hanterad identitet för din virtuella dator

Skapa en identitet i din prenumeration med kommandot [AZ Identity Create](/cli/azure/identity#az-identity-create) . Du kan använda samma resurs grupp som den virtuella datorn körs i, eller en annan.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

Om du vill konfigurera identiteten i följande steg, använder du kommandot [AZ Identity show](/cli/azure/identity#az-identity-show) för att lagra identitetens resurs-ID och klient-ID i variabler.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

Vi kan nu tilldela den användarspecifika identiteten till den virtuella datorn med kommandot [AZ VM Identity Assign](/cli/azure/vm/identity#az-vm-identity-assign) :

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

Slutför installationen genom att visa värdet för klient-ID: t som du behöver i följande steg:

```bash
echo $clientID
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>Skapa en PostgreSQL-användare för din hanterade identitet

Anslut nu som administratörs användare i Azure AD till PostgreSQL-databasen och kör följande SQL-uttryck:

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

Den hanterade identiteten har nu åtkomst vid autentisering med användar namnet `myuser` (Ersätt med ett namn som du väljer).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Hämtar åtkomsttoken från Azure instance metadata service

Ditt program kan nu hämta en åtkomsttoken från Azure-instansens metadatatjänst och använda den för autentisering med-databasen.

Den här token-hämtningen görs genom att göra en HTTP-förfrågan till `http://169.254.169.254/metadata/identity/oauth2/token` och skicka följande parametrar:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (som du hämtade tidigare)

Du får tillbaka ett JSON-resultat som innehåller ett `access_token` fält – detta långa text värde är den hanterade identitets åtkomst-token som du bör använda som lösen ord när du ansluter till databasen.

I test syfte kan du köra följande kommandon i gränssnittet. Observera att du behöver `curl` , `jq` och- `psql` klienten är installerad.

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

Du är nu ansluten till den databas som du har konfigurerat tidigare.

## <a name="connecting-using-managed-identity-in-c"></a>Ansluta med hanterad identitet i C #

I det här avsnittet visas hur du hämtar en åtkomsttoken med den virtuella datorns tilldelade hanterade identitet och använder den för att anropa Azure Database for PostgreSQL. Azure Database for PostgreSQL har inbyggt stöd för Azure AD-autentisering, så att den direkt kan acceptera åtkomsttoken som erhållits med hanterade identiteter för Azure-resurser. När du skapar en anslutning till PostgreSQL skickar du åtkomsttoken i fältet lösen ord.

Här är ett .NET-kod exempel för att öppna en anslutning till PostgreSQL med hjälp av en åtkomsttoken. Den här koden måste köras på den virtuella datorn för att få åtkomst till slut punkten för den användare som tilldelats den virtuella datorn. .NET Framework 4,6 eller högre eller .NET Core 2,2 eller högre krävs för att använda åtkomsttoken. Ersätt värdena för värd, användare, databas och CLIENT_ID.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using Npgsql;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";
        private static string ClientId = "CLIENT_ID";

        static void Main(string[] args)
        {
            //
            // Get an access token for PostgreSQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for PostgreSQL is https://ossrdbms-aad.database.windows.net/
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=" + ClientId);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";
            string accessToken = null;

            try
            {
                // Call managed identities for Azure resources endpoint.
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader and extract access token.
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                var list = JsonSerializer.Deserialize<Dictionary<string, string>>(stringResponse);
                accessToken = list["access_token"];
            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the PostgreSQL server using the access token.
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    Database,
                    5432,
                    accessToken);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                conn.Open();

                using (var command = new NpgsqlCommand("SELECT version()", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine("\nConnected!\n\nPostgres version: {0}", reader.GetString(0));
                    }
                }
            }
        }
    }
}
```

När det körs ger det här kommandot utdata som detta:

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.6, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>Nästa steg

* Granska de övergripande begreppen för [Azure Active Directory autentisering med Azure Database for PostgreSQL](concepts-aad-authentication.md)
