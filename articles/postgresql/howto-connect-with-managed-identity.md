---
title: Ansluta med hanterad identitet – Azure Database for PostgreSQL – enskild server
description: Lär dig mer om hur du ansluter och autentiserar med hjälp av hanterad identitet för autentisering med Azure Database for PostgreSQL
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: beb5930e98a5c5498349dc3aa773423ee5d281bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792479"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>Ansluta med hanterad identitet till Azure Database for PostgreSQL

Den här artikeln visar hur du använder en användar tilldelad identitet för en virtuell Azure-dator för att komma åt en Azure Database for PostgreSQL server. Hanterade tjänstidentiteter hanteras automatiskt av Azure och gör att du kan autentisera mot tjänster som stöder Azure AD-autentisering, utan att du behöver skriva in autentiseringsuppgifter i koden. 

Lär dig att:
- Ge den virtuella datorn åtkomst till en Azure Database for PostgreSQL server
- Skapa en användare i databasen som representerar den virtuella datorns användar-tilldelade identitet
- Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använd den för att fråga en Azure Database for PostgreSQL server
- Implementera tokenhämtning i ett C#-exempelprogram

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till funktionen för hanterade identiteter för Azure-resurser kan du läsa igenom den här [översikten](../../articles/active-directory/managed-identities-azure-resources/overview.md). Om du inte har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- För att kunna skapa nödvändiga resurser och hantera roller måste ditt konto ha behörigheten "Ägare" i lämpligt omfång (din prenumeration eller resursgrupp). Om du behöver hjälp med rolltilldelning kan du läsa Tilldela [Azure-roller för att hantera åtkomst till dina Azure-prenumerationsresurser.](../../articles/role-based-access-control/role-assignments-portal.md)
- Du behöver en virtuell Azure-dator (till exempel Ubuntu Linux) som du vill använda för att få åtkomst till din databas med hjälp av hanterad identitet
- Du behöver en Azure Database for PostgreSQL databasserver som har [Azure AD-autentisering](howto-configure-sign-in-aad-authentication.md) konfigurerad
- Om du vill följa C#-exemplet slutför du först guiden om hur du [ansluter med C#](connect-csharp.md)

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>Skapa en användar tilldelad hanterad identitet för den virtuella datorn

Skapa en identitet i din prenumeration med hjälp av [kommandot az identity](/cli/azure/identity#az_identity_create) create. Du kan använda samma resursgrupp som den virtuella datorn körs i, eller en annan.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

Om du vill konfigurera identiteten i följande steg använder du kommandot [az identity show](/cli/azure/identity#az_identity_show) för att lagra identitetens resurs-ID och klient-ID i variabler.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

Nu kan vi tilldela den användar tilldelade identiteten till den virtuella datorn med [kommandot az vm identity assign:](/cli/azure/vm/identity#az_vm_identity_assign)

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

För att slutföra installationen visar du värdet för klient-ID:t, som du behöver i följande steg:

```bash
echo $clientID
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>Skapa en PostgreSQL-användare för din hanterade identitet

Anslut nu som Azure AD-administratörsanvändare till din PostgreSQL-databas och kör följande SQL-uttryck:

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

Den hanterade identiteten har nu åtkomst vid autentisering med `myuser` användarnamnet (ersätt med val av namn).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Hämta åtkomsttoken från Azure Instance Metadata Service

Programmet kan nu hämta en åtkomsttoken från tjänsten Azure Instance Metadata och använda den för autentisering med databasen.

Den här tokenhämtningen görs genom att göra en HTTP-begäran `http://169.254.169.254/metadata/identity/oauth2/token` till och skicka följande parametrar:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (som du hämtade tidigare)

Du får tillbaka ett JSON-resultat som innehåller ett fält – det här långa textvärdet är åtkomsttoken för hanterad identitet som du bör använda som lösenord när du ansluter `access_token` till databasen.

I testsyfte kan du köra följande kommandon i gränssnittet. Observera att du `curl` behöver `jq` , och `psql` -klienten installerad.

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

Nu är du ansluten till den databas som du konfigurerade tidigare.

## <a name="connecting-using-managed-identity-in-c"></a>Ansluta med hanterad identitet i C #

Det här avsnittet visar hur du hämtar en åtkomsttoken med hjälp av den virtuella datorns användar tilldelade hanterade identitet och använder den för att anropa Azure Database for PostgreSQL. Azure Database for PostgreSQL har inbyggt stöd för Azure AD-autentisering, så att åtkomsttoken som hämtas med hanterade identiteter för Azure-resurser kan accepteras direkt. När du skapar en anslutning till PostgreSQL skickar du åtkomsttoken i lösenordsfältet.

Här är ett .NET-kodexempel på hur du öppnar en anslutning till PostgreSQL med hjälp av en åtkomsttoken. Den här koden måste köras på den virtuella datorn för att få åtkomst till slutpunkten för den virtuella datorns användar tilldelade hanterade identitet. .NET Framework 4.6 eller senare eller .NET Core 2.2 eller senare krävs för att använda metoden för åtkomsttoken. Ersätt värdena för HOST, USER, DATABASE och CLIENT_ID.

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

När du kör det här kommandot får du utdata som liknar följande:

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.6, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>Nästa steg

* Gå igenom de övergripande begreppen [för Azure Active Directory autentisering med Azure Database for PostgreSQL](concepts-aad-authentication.md)
