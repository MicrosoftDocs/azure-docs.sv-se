---
title: 'Självstudie: Använd en hanterad identitet för att komma åt Azure SQL Database-Windows-Azure AD'
description: En själv studie kurs som vägleder dig genom processen med att använda en Windows VM-systemtilldelad hanterad identitet för att få åtkomst till Azure SQL Database.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4f56ce9fa86dc27b77ad6b463479d13c8e4e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91856520"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Självstudie: Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Azure SQL

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Den här självstudien visar hur du använder en systemtilldelad identitet för en virtuell Windows-dator (VM) för att få åtkomst till Azure SQL Database. Hanterade tjänstidentiteter hanteras automatiskt av Azure och gör att du kan autentisera mot tjänster som stöder Azure AD-autentisering, utan att du behöver skriva in autentiseringsuppgifter i koden. Lär dig att:

> [!div class="checklist"]
>
> * Ge din VM-åtkomst till Azure SQL Database
> * Aktivera Azure AD-autentisering
> * Skapa en innesluten användare i databasen som representerar den virtuella datorns systemtilldelade identitet
> * Hämta en åtkomsttoken med den virtuella dator identiteten och Använd den för att fråga Azure SQL Database

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="enable"></a>Aktivera

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]

## <a name="grant-access"></a>Bevilja åtkomst

Om du vill ge din VM åtkomst till en databas i Azure SQL Database kan du använda en befintlig [logisk SQL-Server](../../azure-sql/database/logical-servers.md) eller skapa en ny. Om du vill skapa en ny server och en databas med hjälp av Azure-portalen följer du den här [Azure SQL-snabbstarten](../../azure-sql/database/single-database-create-quickstart.md). Det finns även snabbstarter som använder Azure CLI och Azure PowerShell i [Azure SQL-dokumentationen](/azure/sql-database/).

Det finns två steg för att ge den virtuella datorn åtkomst till en databas:

1. Aktivera Azure AD-autentisering för servern.
2. Skapa en **innesluten användare** i databasen som representerar den virtuella datorns systemtilldelade identitet.

### <a name="enable-azure-ad-authentication"></a>Aktivera Azure AD-autentisering

**Så här [konfigurerar du Azure AD-autentisering](../../azure-sql/database/authentication-aad-configure.md):**

1. I Azure-portalen väljer du **SQL-servrar** från det vänstra navigeringsfältet.
2. Klicka på den SQL server som ska aktiveras för Azure AD-autentisering.
3. I avsnittet **Inställningar** avsnittet på bladet klickar du på **Active Directory-administratör**.
4. I kommandofältet klickar du på **Konfigurera administratör**.
5. Välj ett Azure AD-användarkonto som ska bli administratör för servern och klicka på **Välj**.
6. I kommandofältet klickar du på **Spara**.

### <a name="create-contained-user"></a>Skapa innesluten användare

I det här avsnittet visas hur du skapar en innesluten användare i databasen som representerar den virtuella datorns tilldelade identitet. För det här steget behöver du [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Innan du börjar kan det också vara bra att granska följande artiklar för att få bakgrundsinformation om Azure AD-integrering:

- [Universal Authentication med SQL Database och Azure Synapse Analytics (SSMS-stöd för MFA)](../../azure-sql/database/authentication-mfa-ssms-overview.md)
- [Konfigurera och hantera Azure Active Directory autentisering med SQL Database eller Azure Synapse Analytics](../../azure-sql/database/authentication-aad-configure.md)

SQL DB kräver unika AAD-visningsnamn. Med detta måste AAD-kontona, t. ex. användare, grupper och tjänst huvud namn (program) och virtuella dator namn som är aktiverade för hanterad identitet unikt definieras i AAD om deras visnings namn. SQL DB kontrollerar AADs visnings namn vid T-SQL-skapandet av sådana användare och om det inte är unikt, Miss lyckas begäran att tillhandahålla ett unikt AAD-visnings namn för ett angivet konto.

**Så här skapar du en innesluten användare:**

1. Starta SQL Server Management Studio.
2. I dialog rutan **Anslut till Server** anger du Server namnet i fältet **Server namn** .
3. I fältet **Autentisering** väljer du **Active Directory – Universell med stöd för MFA**.
4. I fältet **Användarnamn** anger du namnet på det Azure AD-konto som du anger som serveradministratör, till exempel helen@woodgroveonline.com
5. Klicka på **Alternativ**.
6. I fältet **Anslut till databas** anger du namnet på den icke-systembaserade databas som du vill konfigurera.
7. Klicka på **Anslut**. Slutför inloggningsprocessen.
8. I **Object Explorer** expanderar du mappen **Databaser**.
9. Högerklicka på en användardatabas och klicka på **Ny fråga**.
10. I frågefönstret anger du följande rad och klickar på **Kör** i verktygsfältet:

    > [!NOTE]
    > `VMName` i följande kommando är namnet på den virtuella datorn som du har aktiverat systemtilldelad identitet på i avsnittet om förutsättningar.

    ```sql
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```

    Kommandot bör slutföras utan problem och skapa den inneslutna användaren för den virtuella datorns systemtilldelade identitet.
11. Rensa frågefönstret, ange följande rad och klicka på **Kör** i verktygsfältet:

    > [!NOTE]
    > `VMName` i följande kommando är namnet på den virtuella datorn som du har aktiverat systemtilldelad identitet på i avsnittet om förutsättningar.

    ```sql
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    Kommandot bör slutföras utan problem och bevilja den inneslutna användaren möjligheten att läsa hela databasen.

Kod som körs i den virtuella datorn kan nu hämta en token med sin tilldelade hanterade identitet och använda token för att autentisera till servern.

## <a name="access-data"></a>Åtkomst till data

Det här avsnittet visar hur du hämtar en åtkomsttoken med den virtuella datorns systemtilldelade identitet och använder den för att anropa Azure SQL. Azure SQL har inbyggt stöd för Azure AD-autentisering, vilket gör att åtkomsttoken som hämtas med hanterade identiteter för Azure-resurser kan accepteras direkt. Du använder metoden med **åtkomsttoken** för att skapa en anslutning till SQL. Detta är en del av integreringen av Azure SQL med Azure AD, och skiljer sig från att ange autentiseringsuppgifter i anslutningssträngen.

Här är ett .NET-kod exempel för att öppna en anslutning till SQL med hjälp av en åtkomsttoken. Koden måste köras på den virtuella datorn för att kunna få åtkomst till den virtuella datorns slut punkt för hanterad identitet. **.NET Framework 4,6** eller högre eller **.net Core 2,2** eller högre krävs för att använda åtkomsttoken. Ersätt värdena för AZURE-SQL-SERVERNAME och DATABASE i enlighet med detta. Observera resurs-ID: t för Azure SQL `https://database.windows.net/` .

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
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
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Du kan snabbt testa konfigurationen av slutpunkt till slutpunkt utan att behöva skriva och distribuera en app på den virtuella datorn med hjälp av PowerShell.

1. Gå till **Virtuella datorer** på portalen och sedan till den virtuella Windows-datorn. Under **Översikt** klickar du på **Anslut**.
2. Ange ditt **användarnamn** och **lösenord** som du lade till när du skapade den virtuella Windows-datorn.
3. Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn öppnar du **PowerShell** i fjärrsessionen.
4. Använd PowerShells `Invoke-WebRequest` och skicka en begäran till den lokala hanterade identitetens slutpunkt för att hämta en åtkomsttoken för Azure SQL.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```

    Konvertera svaret från ett JSON-objekt till ett PowerShell-objekt.

    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Extrahera åtkomsttoken från svaret.

    ```powershell
    $AccessToken = $content.access_token
    ```

5. Öppna en anslutning till servern. Kom ihåg att ersätta värdena för AZURE-SQL-SERVERNAME och DATABASE.

    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Sedan skapar du och skickar en fråga till servern. Kom ihåg att ersätta värdet för TABLE.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Kontrollera värdet på `$DataSet.Tables[0]` för att visa resultatet av frågan.

## <a name="disable"></a>Inaktivera

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder en systemtilldelad hanterad identitet för att få åtkomst till Azure SQL Database. Mer information om Azure SQL Database finns i:

> [!div class="nextstepaction"]
> [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md)
