---
title: Ansluta säkert till ett Azure Service Fabric-kluster
description: Beskriver hur du autentiserar klient åtkomst till ett Service Fabric kluster och hur du skyddar kommunikationen mellan klienter och ett kluster.
ms.topic: conceptual
ms.date: 01/29/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a669f7400eb2daf2d71f6caed41560020699c58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791129"
---
# <a name="connect-to-a-secure-cluster"></a>Ansluta till ett säkert kluster

När en klient ansluter till en Service Fabric klusternod kan klienten autentiseras och säkra kommunikationen som etableras med hjälp av certifikat säkerhet eller Azure Active Directory (AAD). Den här autentiseringen säkerställer att endast behöriga användare kan komma åt klustret och distribuerade program och utföra hanterings uppgifter.  Certifikat-eller AAD-säkerhet måste ha Aktiver ATS tidigare på klustret när klustret skapades.  Mer information om kluster säkerhets scenarier finns i [kluster säkerhet](service-fabric-cluster-security.md). Om du ansluter till ett kluster som är skyddat med certifikat [konfigurerar du klient certifikatet](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) på den dator som ansluter till klustret. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Ansluta till ett säkert kluster med Azure Service Fabric CLI (sfctl)

Det finns flera olika sätt att ansluta till ett säkert kluster med hjälp av Service Fabric CLI (sfctl). När du använder ett klientcertifikat för autentisering måste certifikatinformationen matcha ett certifikat som distribuerats till klusternoderna. Om ditt certifikat har certifikat utfärdare måste du också ange betrodda certifikat utfärdare.

Du kan ansluta till ett kluster med hjälp av `sfctl cluster select` kommandot.

Klient certifikat kan anges på två olika sätt, antingen som ett certifikat och nyckel par, eller som en enda PFX-fil. För lösenordsskyddade PEM-filer uppmanas du att ange lösen ordet automatiskt. Om du har fått klient certifikatet som en PFX-fil måste du först konvertera PFX-filen till en PEM-fil med hjälp av följande kommando. 

```shell
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Om PFX-filen inte är lösenordsskyddad använder du-Passin pass: för den sista parametern.

Om du vill ange klient certifikatet som en PEM-fil anger du fil Sök vägen i `--pem` argumentet. Exempel:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Lösenordsskyddade PEM-filer kommer att uppmanas att ange lösen ord innan kommandot körs.

Om du vill ange ett certifikat använder nyckel paret `--cert` `--key` argumenten och för att ange sökvägar till varje respektive fil.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Ibland kan certifikat som används för att skydda test-eller dev-kluster inte verifiera certifikat. Om du vill kringgå certifikat verifieringen anger du `--no-verify` alternativet. Exempel:

> [!WARNING]
> Använd inte `no-verify` alternativet när du ansluter till produktions Service Fabric kluster.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Dessutom kan du ange sökvägar till kataloger för betrodda CA-certifikat eller enskilda certifikat. Använd argumentet för att ange dessa sökvägar `--ca` . Exempel:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

När du har anslutit bör du kunna [köra andra sfctl-kommandon](service-fabric-cli.md) för att interagera med klustret.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Ansluta till ett kluster med PowerShell
Innan du utför åtgärder på ett kluster via PowerShell upprättar du först en anslutning till klustret. Kluster anslutningen används för alla efterföljande kommandon i den aktuella PowerShell-sessionen.

### <a name="connect-to-an-unsecure-cluster"></a>Ansluta till ett oskyddat kluster

Om du vill ansluta till ett oskyddat kluster anger du klustrets slut punkts adress till kommandot **Connect-ServiceFabricCluster** :

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Ansluta till ett säkert kluster med Azure Active Directory

Om du vill ansluta till ett säkert kluster som använder Azure Active Directory för att auktorisera kluster administratörs åtkomst, anger du tumavtryck för kluster certifikat och använder flaggan *AzureActiveDirectory* .  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ansluta till ett säkert kluster med ett klient certifikat
Kör följande PowerShell-kommando för att ansluta till ett säkert kluster som använder klient certifikat för att auktorisera administratörs åtkomst. 

#### <a name="connect-using-certificate-common-name"></a>Anslut med certifikatets nätverks namn
Ange kluster certifikatets nätverks namn och det egna namnet på det klient certifikat som har beviljats behörigheter för kluster hantering. Certifikat informationen måste matcha ett certifikat på klusternoderna.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* är det gemensamma namnet på det Server certifikat som är installerat på klusternoderna. *FindValue* är det unika namnet för administratörs klient certifikatet. När parametrarna är ifyllda ser kommandot ut som i följande exempel:
```powershell
$ClusterName= "sf-commonnametest-scus.southcentralus.cloudapp.azure.com:19000"
$certCN = "sfrpe2eetest.southcentralus.cloudapp.azure.com"

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName $certCN  `
    -FindType FindBySubjectName `
    -FindValue $certCN `
    -StoreLocation CurrentUser `
    -StoreName My 
```

#### <a name="connect-using-certificate-thumbprint"></a>Anslut med tumavtryck för certifikat
Ange tumavtryck för klustrets certifikat och tumavtryck för det klient certifikat som har beviljats behörigheter för kluster hantering. Certifikat informationen måste matcha ett certifikat på klusternoderna.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* är tumavtrycket för det Server certifikat som är installerat på klusternoderna. *FindValue* är tumavtrycket för administratörs klient certifikatet.  När parametrarna är ifyllda ser kommandot ut som i följande exempel:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Ansluta till ett säkert kluster med Windows Active Directory
Om det fristående klustret distribueras med hjälp av AD-säkerhet ansluter du till klustret genom att lägga till växeln "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Ansluta till ett kluster med FabricClient-API: erna
Service Fabric SDK tillhandahåller [FabricClient](/dotnet/api/system.fabric.fabricclient) -klassen för kluster hantering. Om du vill använda FabricClient-API: erna hämtar du Microsoft. ServiceFabric NuGet-paketet.

### <a name="connect-to-an-unsecure-cluster"></a>Ansluta till ett oskyddat kluster

Om du vill ansluta till ett fjärran slutet oskyddat kluster skapar du en FabricClient-instans och anger kluster adressen:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

För kod som körs i ett kluster, till exempel i en tillförlitlig tjänst, skapar du en FabricClient *utan att* ange kluster adressen. FabricClient ansluter till den lokala hanterings-gatewayen på den nod som koden körs på, vilket undviker en extra nätverks hopp.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ansluta till ett säkert kluster med ett klient certifikat

Noderna i klustret måste ha giltiga certifikat vars nätverks namn eller DNS-namn i SAN visas i [RemoteCommonNames-egenskapen](/dotnet/api/system.fabric.x509credentials) som angetts för [FabricClient](/dotnet/api/system.fabric.fabricclient). Genom att följa den här processen möjliggörs ömsesidig autentisering mellan klienten och klusternoderna.

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Ansluta till ett säkert kluster interaktivt med hjälp av Azure Active Directory

I följande exempel används Azure Active Directory för klient identitet och Server certifikat för Server identitet.

Ett dialog fönster öppnas automatiskt för interaktiv inloggning vid anslutning till klustret.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Ansluta till ett säkert kluster icke-interaktivt med hjälp av Azure Active Directory

Följande exempel är beroende av Microsoft. IdentityModel. clients. ActiveDirectory, version: 2.19.208020213.

Mer information om hämtning av AAD-token finns i [Microsoft. IdentityModel. clients. ActiveDirectory](/dotnet/api/microsoft.identitymodel.clients.activedirectory).

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Anslut till ett säkert kluster utan kunskaper om tidigare metadata med hjälp av Azure Active Directory

I följande exempel används icke-interaktiv token-hämtning, men samma metod kan användas för att bygga en anpassad hämtnings upplevelse för interaktiva token. De Azure Active Directory metadata som krävs för hämtning av token läses från kluster konfigurationen.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Ansluta till ett säkert kluster med Service Fabric Explorer
För att uppnå [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) för ett specifikt kluster, peka din webbläsare för att:

`http://<your-cluster-endpoint>:19080/Explorer`

Den fullständiga URL: en är också tillgänglig i fönstret kluster Essentials i Azure Portal.

För att ansluta till ett säkert kluster i Windows eller OS X med en webbläsare, kan du importera klient certifikatet och webbläsaren uppmanas att ange det certifikat som ska användas för att ansluta till klustret.  På Linux-datorer måste certifikatet importeras med avancerade webb läsar inställningar (varje webbläsare har olika mekanismer) och peka på certifikat platsen på disken. Läs [Konfigurera ett klient certifikat](#connectsecureclustersetupclientcert) för mer information.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Ansluta till ett säkert kluster med Azure Active Directory

För att ansluta till ett kluster som skyddas med AAD, peka din webbläsare för att:

`https://<your-cluster-endpoint>:19080/Explorer`

Du uppmanas automatiskt att logga in med AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ansluta till ett säkert kluster med ett klient certifikat

Om du vill ansluta till ett kluster som skyddas med certifikat, kan du peka din webbläsare för att:

`https://<your-cluster-endpoint>:19080/Explorer`

Du uppmanas automatiskt att välja ett klient certifikat.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Konfigurera ett klient certifikat på fjärrdatorn

Minst två certifikat ska användas för att skydda klustret, ett för kluster-och Server certifikat och ett annat för klient åtkomst.  Vi rekommenderar att du också använder ytterligare sekundära certifikat och klient åtkomst certifikat.  För att skydda kommunikationen mellan en klient och en klusternod med hjälp av certifikat säkerhet måste du först skaffa och installera klient certifikatet. Certifikatet kan installeras i det personliga arkivet (My) på den lokala datorn eller den aktuella användaren.  Du behöver också tumavtrycket för Server certifikatet så att klienten kan autentisera klustret.

* I Windows: Dubbelklicka på PFX-filen och följ anvisningarna för att installera certifikatet i ditt personliga arkiv `Certificates - Current User\Personal\Certificates`. Du kan också använda PowerShell-kommandot:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Om det är ett självsignerat certifikat måste du importera det till din dators Arkiv "betrodda personer" innan du kan använda det här certifikatet för att ansluta till ett säkert kluster.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* I Mac: Dubbelklicka på PFX-filen och följ anvisningarna för att installera certifikatet i din nyckelring.

## <a name="next-steps"></a>Nästa steg

* [Service Fabric kluster uppgraderings process och förväntningar från dig](service-fabric-cluster-upgrade.md)
* [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Introduktion till Service Fabric hälso modell](service-fabric-health-introduction.md)
* [Program säkerhet och RunAs](service-fabric-application-runas-security.md)
* [Kom igång med Service Fabric CLI](service-fabric-cli.md)
