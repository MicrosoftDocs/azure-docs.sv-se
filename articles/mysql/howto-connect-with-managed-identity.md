---
title: Ansluta med hanterad identitet – Azure Database for MySQL
description: Lär dig mer om hur du ansluter och autentiserar med hanterad identitet för autentisering med Azure Database for MySQL
author: sunilagarwal
ms.author: sunila
ms.service: mysql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: c9c5c938650d1932349f17bde6b30c65718ef72a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774691"
---
# <a name="connect-with-managed-identity-to-azure-database-for-mysql"></a>Ansluta med hanterad identitet till Azure Database for MySQL

Den här artikeln visar hur du använder en användar tilldelad identitet för en virtuell Azure-dator för att komma åt Azure Database for MySQL server. Hanterade tjänstidentiteter hanteras automatiskt av Azure och gör att du kan autentisera mot tjänster som stöder Azure AD-autentisering, utan att du behöver skriva in autentiseringsuppgifter i koden. 

Lär dig att:

- Ge den virtuella datorn åtkomst till en Azure Database for MySQL server
- Skapa en användare i databasen som representerar den virtuella datorns användar tilldelade identitet
- Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använd den för att fråga en Azure Database for MySQL server
- Implementera tokenhämtning i ett C#-exempelprogram

> [!IMPORTANT]
> Anslutning med hanterad identitet är endast tillgängligt för MySQL 5.7 och nyare.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till funktionen för hanterade identiteter för Azure-resurser kan du läsa igenom den här [översikten](../../articles/active-directory/managed-identities-azure-resources/overview.md). Om du inte har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill göra den resursskapande och rollhantering som krävs måste ditt konto ha behörigheten "Ägare" i lämpligt omfång (din prenumeration eller resursgrupp). Om du behöver hjälp med rolltilldelning kan du läsa Tilldela [Azure-roller för att hantera åtkomst till dina Azure-prenumerationsresurser.](../../articles/role-based-access-control/role-assignments-portal.md)
- Du behöver en virtuell Azure-dator (till exempel Ubuntu Linux) som du vill använda för att få åtkomst till databasen med hjälp av hanterad identitet
- Du behöver en Azure Database for MySQL databasserver som har [Azure AD-autentisering](howto-configure-sign-in-azure-ad-authentication.md) konfigurerad
- Om du vill följa C#-exemplet slutför du först guiden om hur du [ansluter med C#](connect-csharp.md)

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>Skapa en användar tilldelad hanterad identitet för den virtuella datorn

Skapa en identitet i din prenumeration med hjälp av [kommandot az identity create.](/cli/azure/identity#az_identity_create) Du kan använda samma resursgrupp som den virtuella datorn körs i, eller en annan.

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

## <a name="creating-a-mysql-user-for-your-managed-identity"></a>Skapa en MySQL-användare för din hanterade identitet

Anslut nu som Azure AD-administratörsanvändare till mySQL-databasen och kör följande SQL-instruktioner:

```sql
SET aad_auth_validate_oids_in_tenant = OFF;
CREATE AADUSER 'myuser' IDENTIFIED BY 'CLIENT_ID';
```

Den hanterade identiteten har nu åtkomst vid autentisering med `myuser` användarnamnet (ersätt med val av namn).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Hämta åtkomsttoken från Azure Instance Metadata Service

Ditt program kan nu hämta en åtkomsttoken från tjänsten Azure Instance Metadata och använda den för autentisering med databasen.

Den här tokenhämtningen görs genom att göra en HTTP-begäran `http://169.254.169.254/metadata/identity/oauth2/token` till och skicka följande parametrar:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (som du hämtade tidigare)

Du får tillbaka ett JSON-resultat som innehåller ett fält – det här långa textvärdet är åtkomsttoken för hanterad identitet som du bör använda som lösenord när du ansluter `access_token` till databasen.

I testsyfte kan du köra följande kommandon i gränssnittet. Observera att du `curl` behöver `jq` , och `mysql` -klienten installerad.

```bash
# Retrieve the access token
accessToken=$(curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token)

# Connect to the database
mysql -h SERVER --user USER@SERVER --enable-cleartext-plugin --password=$accessToken
```

Nu är du ansluten till den databas som du konfigurerade tidigare.

## <a name="connecting-using-managed-identity-in-c"></a>Ansluta med hanterad identitet i C #

Det här avsnittet visar hur du hämtar en åtkomsttoken med hjälp av den virtuella datorns användar tilldelade hanterade identitet och använder den för att anropa Azure Database for MySQL. Azure Database for MySQL har inbyggt stöd för Azure AD-autentisering, så att den direkt kan acceptera åtkomsttoken som hämtas med hjälp av hanterade identiteter för Azure-resurser. När du skapar en anslutning till MySQL skickar du åtkomsttoken i lösenordsfältet.

Här är ett .NET-kodexempel på hur du öppnar en anslutning till MySQL med hjälp av en åtkomsttoken. Den här koden måste köras på den virtuella datorn för att få åtkomst till slutpunkten för den virtuella datorns användar tilldelade hanterade identitet. .NET Framework 4.6 eller senare eller .NET Core 2.2 eller senare krävs för att använda metoden för åtkomsttoken. Ersätt värdena för HOST, USER, DATABASE och CLIENT_ID.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using System.Threading.Tasks;
using MySql.Data.MySqlClient;

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

        static async Task Main(string[] args)
        {
            //
            // Get an access token for MySQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for MySQL is https://ossrdbms-aad.database.windows.net/
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
            // Open a connection to the MySQL server using the access token.
            //
            var builder = new MySqlConnectionStringBuilder
            {
                Server = Host,
                Database = Database,
                UserID = User,
                Password = accessToken,
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT VERSION()";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine("\nConnected!\n\nMySQL version: {0}", reader.GetString(0));
                        }
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

MySQL version: 5.7.27
```

## <a name="next-steps"></a>Nästa steg

* Gå igenom de övergripande begreppen [för Azure Active Directory-autentisering med Azure Database for MySQL](concepts-azure-ad-authentication.md)
