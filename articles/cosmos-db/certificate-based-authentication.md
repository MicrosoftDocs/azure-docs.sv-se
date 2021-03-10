---
title: Certifikatbaserad autentisering med Azure Cosmos DB och Active Directory
description: Lär dig hur du konfigurerar en Azure AD-identitet för certifikatbaserad autentisering för åtkomst till nycklar från Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 84cbc681d0974e91561daf8918dff389226fa7aa
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553977"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Certifikatbaserad autentisering för en Azure AD-identitet för åtkomst till nycklar från ett Azure Cosmos DB konto
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Med certifikatbaserad autentisering kan klientprogrammet autentiseras med hjälp av Azure Active Directory (Azure AD) med ett klientcertifikat. Du kan utföra certifikatbaserad autentisering på en dator där du behöver en identitet, till exempel en lokal dator eller en virtuell dator i Azure. Ditt program kan sedan läsa Azure Cosmos DB nycklar utan att ha nycklarna direkt i programmet. Den här artikeln beskriver hur du skapar ett exempel på Azure AD-program, konfigurerar det för certifikatbaserad autentisering, loggar in på Azure med den nya program identiteten och hämtar sedan nycklarna från ditt Azure Cosmos-konto. Den här artikeln använder Azure PowerShell för att ställa in identiteter och innehåller en C#-exempel app som autentiserar och använder nycklar från ditt Azure Cosmos-konto.  

## <a name="prerequisites"></a>Förutsättningar

* Installera den [senaste versionen](/powershell/azure/install-az-ps) av Azure PowerShell.

* Om du inte har en [Azure-prenumeration](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

## <a name="register-an-app-in-azure-ad"></a>Registrera en app i Azure AD

I det här steget ska du registrera ett exempel webb program i ditt Azure AD-konto. Det här programmet används senare för att läsa nycklarna från ditt Azure Cosmos DB-konto. Använd följande steg för att registrera ett program: 

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna fönstret Azure **Active Directory** , gå till **Appregistreringar** fönstret och välj **ny registrering**. 

   :::image type="content" source="./media/certificate-based-authentication/new-app-registration.png" alt-text="Ny program registrering i Active Directory":::

1. Fyll i formuläret **Registrera ett program** med följande information:  

   * **Namn** – ange ett namn för ditt program, det kan vara vilket namn som helst, till exempel "fråga".
   * **Konto typer som stöds** – Välj **konton endast i den här organisations katalogen (standard katalog)** om du vill tillåta att resurser i din aktuella katalog får åtkomst till det här programmet. 
   * **Omdirigerings-URL** – Välj program av typen **webb** och ange en URL där ditt program finns, det kan vara vilken URL som helst. I det här exemplet kan du ange en test-URL, till exempel att `https://sampleApp.com` den är OK även om appen inte finns.

   :::image type="content" source="./media/certificate-based-authentication/register-sample-web-app.png" alt-text="Registrera ett exempel webb program":::

1. Välj **Registrera** när du har fyllt i formuläret.

1. När appen har registrerats ska du anteckna **program-ID: t** och **objekt-ID**: t. du kommer att använda informationen i nästa steg. 

   :::image type="content" source="./media/certificate-based-authentication/get-app-object-ids.png" alt-text="Hämta program-och objekt-ID: n":::

## <a name="install-the-azuread-module"></a>Installera AzureAD-modulen

I det här steget ska du installera Azure AD PowerShell-modulen. Den här modulen krävs för att hämta ID: t för programmet som du registrerade i föregående steg och associera ett självsignerat certifikat till det programmet. 

1. Öppna Windows PowerShell ISE med administratörs behörighet. Om du inte redan har gjort det installerar du AZ PowerShell-modulen och ansluter till din prenumeration. Om du har flera prenumerationer kan du ställa in kontexten för den aktuella prenumerationen så som visas i följande kommandon:

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Installera och importera modulen [AzureAD](/powershell/module/azuread/)

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Logga in på Azure AD

Logga in på Azure AD där du har registrerat programmet. Använd Connect-AzureAD kommandot för att logga in på ditt konto, ange dina autentiseringsuppgifter för Azure-kontot i popup-fönstret. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

Öppna en annan instans av Windows PowerShell ISE och kör följande kommandon för att skapa ett självsignerat certifikat och läsa nyckeln som är kopplad till certifikatet:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Skapa den certifikatbaserad autentiseringsuppgiften 

Kör sedan följande kommandon för att hämta objekt-ID för ditt program och skapa den certifikatbaserade autentiseringsuppgiften. I det här exemplet ställer vi in certifikatet så att det upphör att gälla efter ett år. du kan ställa in det på alla obligatoriska slutdatum.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Kommandot ovan resulterar i utdata som liknar skärm bilden nedan:

:::image type="content" source="./media/certificate-based-authentication/certificate-based-credential-output.png" alt-text="Utdata för att skapa certifikatbaserad autentiseringsuppgift":::

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Konfigurera ditt Azure Cosmos-konto för att använda den nya identiteten

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Gå till ditt Azure Cosmos-konto och öppna bladet **åtkomst kontroll (IAM)** .

1. Välj **Lägg till** och **Lägg till roll tilldelning**. Lägg till fråga som du skapade i föregående steg med **deltagar** rollen som visas på följande skärm bild:

   :::image type="content" source="./media/certificate-based-authentication/configure-cosmos-account-with-identify.png" alt-text="Konfigurera Azure Cosmos-konto för att använda den nya identiteten":::

1. Välj **Spara** när du har fyllt i formuläret

## <a name="register-your-certificate-with-azure-ad"></a>Registrera ditt certifikat med Azure AD

Du kan associera den certifikatbaserad autentiseringsuppgiften med klient programmet i Azure AD från Azure Portal. För att associera autentiseringsuppgifterna måste du ladda upp certifikat filen med följande steg:

I Azure App-registreringen för klient programmet:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna fönstret Azure **Active Directory** , gå till fönstret **Appregistreringar** och öppna den exempel app som du skapade i föregående steg. 

1. Välj **certifikat & hemligheter** och **Ladda upp certifikat**. Bläddra till den certifikat fil som du skapade i föregående steg för att ladda upp.

1. Välj **Lägg till**. När certifikatet har laddats upp visas tumavtryck, start datum och förfallo värden.

## <a name="access-the-keys-from-powershell"></a>Åtkomst till nycklarna från PowerShell

I det här steget ska du logga in på Azure med hjälp av programmet och det certifikat som du skapade och komma åt dina Azure Cosmos-kontots nycklar. 

1. Ta först bort Azure-kontots autentiseringsuppgifter som du använde för att logga in på ditt konto. Du kan rensa autentiseringsuppgifter genom att använda följande kommando:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Sedan verifierar du att du kan logga in på Azure Portal med hjälp av programmets autentiseringsuppgifter och komma åt Azure Cosmos DB nycklarna:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

Föregående kommando visar de primära och sekundära primär nycklarna för ditt Azure Cosmos-konto. Du kan visa aktivitets loggen för ditt Azure Cosmos-konto för att verifiera att get Keys-begäran lyckades och att händelsen initieras av "fråga"-programmet.

:::image type="content" source="./media/certificate-based-authentication/activity-log-validate-results.png" alt-text="Verifiera anropet get Keys i Azure AD":::

## <a name="access-the-keys-from-a-c-application"></a>Åtkomst till nycklar från ett C#-program 

Du kan också validera det här scenariot genom att komma åt nycklar från ett C#-program. Följande C#-konsol program, som kan komma åt Azure Cosmos DB nycklar med hjälp av appen som är registrerad i Active Directory. Se till att uppdatera tenantId, clientID, certName, resurs gruppens namn, prenumerations-ID, Azure Cosmos konto namn detaljer innan du kör koden. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
    }
}
```

Det här skriptet utvärderar de primära och sekundära primär nycklarna så som visas på följande skärm bild:

:::image type="content" source="./media/certificate-based-authentication/csharp-application-output.png" alt-text="csharp programutdata":::

Precis som i föregående avsnitt kan du Visa aktivitets loggen för ditt Azure Cosmos-konto för att kontrol lera att händelsen hämta nycklar för begäran initieras av programmet "fråga". 


## <a name="next-steps"></a>Nästa steg

* [Skydda Azure Cosmos-nycklar med Azure Key Vault](access-secrets-from-keyvault.md)

* [Säkerhets bas linje för Azure Cosmos DB](security-baseline.md)