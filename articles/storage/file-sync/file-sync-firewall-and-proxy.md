---
title: Azure File Sync lokala brandväggs- och proxyinställningar | Microsoft Docs
description: Förstå Azure File Sync lokala proxy- och brandväggsinställningar. Granska konfigurationsinformationen för portar, nätverk och särskilda anslutningar till Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b2c77c20bfb6fff60f2242d1ac2dad7b3fc9f6fe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797151"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Inställningar för Azure File Sync-proxy och brandväggar
Azure File Sync ansluter dina lokala servrar till Azure Files, vilket aktiverar funktioner för synkronisering av flera platser och molnnivåindelad lagring. Därför måste en lokal server vara ansluten till Internet. En IT-administratör måste bestämma den bästa sökvägen för servern att nå till Azure-molntjänster.

Den här artikeln ger insikter om specifika krav och alternativ som är tillgängliga för att ansluta servern till Azure File Sync.

Vi rekommenderar att [du Azure File Sync nätverksöverväganden](file-sync-networking-overview.md) innan du läser den här guiden.

## <a name="overview"></a>Översikt
Azure File Sync fungerar som en orkestreringstjänst mellan Din Windows Server, din Azure-filresurs och flera andra Azure-tjänster för att synkronisera data enligt beskrivningen i synkroniseringsgruppen. För Azure File Sync fungerar korrekt måste du konfigurera servrarna så att de kommunicerar med följande Azure-tjänster:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Autentiseringstjänster

> [!Note]  
> Agenten Azure File Sync Windows Server initierar alla begäranden till molntjänster, vilket gör att du bara behöver tänka på utgående trafik ur brandväggsperspektiv. <br /> Ingen Azure-tjänst initierar en anslutning Azure File Sync agenten.

## <a name="ports"></a>Portar
Azure File Sync flyttar fildata och metadata exklusivt via HTTPS och kräver att port 443 är öppen för utgående trafik.
Därför krypteras all trafik.

## <a name="networks-and-special-connections-to-azure"></a>Nätverk och särskilda anslutningar till Azure
Agenten Azure File Sync har inga krav på särskilda kanaler som [ExpressRoute](../../expressroute/expressroute-introduction.md)osv. till Azure.

Azure File Sync fungerar med alla tillgängliga metoder som tillåter åtkomst till Azure, och anpassas automatiskt till olika nätverksegenskaper som bandbredd, svarstid och erbjuder administratörskontroll för finjustering. Alla funktioner är inte tillgängliga just nu. Om du vill konfigurera ett specifikt beteende kan du meddela oss via [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Azure File Sync har stöd för appspecifika proxyinställningar och datoromfattande proxyinställningar.

**Appspecifika proxyinställningar tillåter** konfiguration av en proxy som är specifik för Azure File Sync trafik. Appspecifika proxyinställningar stöds på agentversion 4.0.1.0 eller senare och kan konfigureras under agentinstallationen eller med hjälp av Set-StorageSyncProxyConfiguration PowerShell-cmdleten.

PowerShell-kommandon för att konfigurera appspecifika proxyinställningar:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
Om proxyservern till exempel kräver autentisering med ett användarnamn och lösenord kör du följande PowerShell-kommandon:

```powershell
# IP address or name of the proxy server.
$Address="127.0.0.1"  

# The port to use for the connection to the proxy.
$Port=8080

# The user name for a proxy.
$UserName="user_name" 

# Please type or paste a string with a password for the proxy.
$SecurePassword = Read-Host -AsSecureString

$Creds = New-Object System.Management.Automation.PSCredential ($UserName, $SecurePassword)

# Please verify that you have entered the password correctly.
Write-Host $Creds.GetNetworkCredential().Password

Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"

Set-StorageSyncProxyConfiguration -Address $Address -Port $Port -ProxyCredential $Creds
```
**Datoromfattande proxyinställningar är** transparenta för Azure File Sync agenten när hela trafiken på servern dirigeras via proxyn.

Du kan konfigurera datorövergripande proxyinställningar med hjälp av stegen nedan: 

1. Konfigurera proxyinställningar för .NET-program 

   - Redigera dessa två filer:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Lägg till <system.net> i machine.config (under avsnittet <system.serviceModel>).  Ändra 127.0.01:8888 till IP-adressen och porten för proxyservern. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Ange WinHTTP-proxyinställningar 

   > [!Note]  
   > Det finns flera metoder (WPAD, PAC-fil, netsh osv.) för att konfigurera en Windows Server att använda en proxyserver. Stegen nedan visar hur du konfigurerar proxyinställningarna med netsh, men alla metoder som anges i Konfigurera [proxyserverinställningar i Windows-dokumentationen](https://docs.microsoft.com/troubleshoot/windows-server/networking/configure-proxy-server-settings) stöds.


   - Kör följande kommando från en upphöjd kommandotolk eller PowerShell för att se den befintliga proxyinställningen:   

     netsh winhttp show proxy

   - Kör följande kommando från en upphöjd kommandotolk eller PowerShell för att ange proxyinställningen (ändra 127.0.01:8888 till IP-adressen och porten för proxyservern):  

     netsh winhttp set proxy 127.0.0.1:8888

3. Starta om tjänsten Storage Sync Agent genom att köra följande kommando från en upphöjd kommandotolk eller PowerShell: 

      net stop filesyncsvc

      Obs! Tjänsten Storage Sync Agent (filesyncsvc) startar automatiskt när den har stoppats.

## <a name="firewall"></a>Brandvägg
Som vi nämnde i föregående avsnitt måste port 443 vara öppen för utgående trafik. Baserat på principer i ditt datacenter, din gren eller region kan ytterligare begränsning av trafiken via den här porten till specifika domäner vara önskad eller nödvändig.

I följande tabell beskrivs de domäner som krävs för kommunikation:

| Tjänst | Slutpunkt för offentligt moln | Azure Government slutpunkt | Användning |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Alla användarsamtal (till exempel PowerShell) går till/via den här URL:en, inklusive det första serverregistreringssamtalet. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Azure Resource Manager-anrop måste göras av en autentiserad användare. För att lyckas används den här URL:en för användarautentisering. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | Som en del av Azure File Sync distribution skapas ett huvudnamn för tjänsten i Azure Active Directory prenumerationen. Den här URL:en används för detta. Det här huvudprincipen används för att delegera en minimal uppsättning rättigheter till Azure File Sync tjänsten. Den användare som utför den första konfigurationen av Azure File Sync måste vara en autentiserad användare med prenumerationens ägarbehörighet. |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | Använd url:en för den offentliga slutpunkten. | Den här URL:en nås av Active Directory-autentiseringsbiblioteket som Azure File Sync användargränssnittet för serverregistrering använder för att logga in administratören. |
| **Azure Storage** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | När servern laddar ned en fil utför servern dataförflyttningen effektivare när den pratar direkt med Azure-filresursen i lagringskontot. Servern har en SAS-nyckel som endast tillåter riktad filresursåtkomst. |
| **Azure File Sync** | &ast;.one.microsoft.com<br>&ast;.afs.azure.net | &ast;.afs.azure.us | Efter den första serverregistreringen tar servern emot en regional URL för Azure File Sync-tjänstinstansen i den regionen. Servern kan använda URL:en för att kommunicera direkt och effektivt med den instans som hanterar synkroniseringen. |
| **Microsoft PKI** |  https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | När agenten Azure File Sync installerats används PKI-URL:en för att ladda ned mellanliggande certifikat som krävs för att kommunicera Azure File Sync-tjänsten och Azure-filresursen. OCSP-URL:en används för att kontrollera status för ett certifikat. |
| **Microsoft Update** | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | När Azure File Sync har installerats används url Microsoft Update adresserna för att ladda ned Azure File Sync agentuppdateringar. |

> [!Important]
> När trafik tillåts &ast; till .afs.azure.net är trafik endast möjlig för synkroniseringstjänsten. Det finns inga andra Microsoft-tjänster använder den här domänen.
> När du tillåter trafik &ast; till .one.microsoft.com är trafik till mer än bara synkroniseringstjänsten möjlig från servern. Det finns många fler Microsoft-tjänster under underdomäner.

Om .afs.azure.net eller .one.microsoft.com är för bred kan du begränsa serverns kommunikation genom att tillåta kommunikation till endast explicita regionala instanser av &ast; &ast; Azure Files Sync-tjänsten. Vilka instanser som ska väljas beror på regionen för den tjänst för synkronisering av lagring som du har distribuerat och registrerat servern till. Den regionen kallas "primär slutpunkts-URL" i tabellen nedan.

För affärskontinuering och haveriberedskap (BCDR) kan du ha angett dina Azure-filresurser i ett globalt redundant (GRS)-lagringskonto. Om så är fallet redundanskopplas dina Azure-filresurser till den parkopplade regionen i händelse av ett långvarigt regionalt avbrott. Azure File Sync använder samma regionala parkopplingar som lagring. Så om du använder GRS-lagringskonton måste du aktivera ytterligare URL:er så att servern kan prata med den parkopplade regionen för Azure File Sync. I tabellen nedan anropas den här "parkopplade regionen". Dessutom finns det en Traffic Manager-profil-URL som också måste aktiveras. Detta säkerställer att nätverkstrafiken smidigt kan dirigeras om till den parkopplade regionen vid en redundans och kallas "Identifierings-URL" i tabellen nedan.

| Moln  | Region | Webbadress till primär slutpunkt | Länkad region | Identifierings-URL |
|--------|--------|----------------------|---------------|---------------|
| Offentliga |Australien, östra | https: \/ /australiaeast01.afs.azure.net<br>https: \/ /kailani-aue.one.microsoft.com | Australien, sydöstra | https: \/ /tm-australiaeast01.afs.azure.net<br>https: \/ /tm-kailani-aue.one.microsoft.com |
| Offentliga |Australien, sydöstra | https: \/ /australiasoutheast01.afs.azure.net<br>https: \/ /kailani-aus.one.microsoft.com | Australien, östra | https: \/ /tm-australiasoutheast01.afs.azure.net<br>https: \/ /tm-kailani-aus.one.microsoft.com |
| Offentliga | Brasilien, södra | https: \/ /brazilsouth01.afs.azure.net | USA, södra centrala | https: \/ /tm-brazilsouth01.afs.azure.net |
| Offentliga | Kanada, centrala | https: \/ /canadacentral01.afs.azure.net<br>https: \/ /kailani-cac.one.microsoft.com | Kanada, östra | https: \/ /tm-canadacentral01.afs.azure.net<br>https: \/ /tm-kailani-cac.one.microsoft.com |
| Offentliga | Kanada, östra | https: \/ /canadaeast01.afs.azure.net<br>https: \/ /kailani-cae.one.microsoft.com | Kanada, centrala | https: \/ /tm-canadaeast01.afs.azure.net<br>https: \/ /tm-kailani.cae.one.microsoft.com |
| Offentliga | Indien, centrala | https: \/ /centralindia01.afs.azure.net<br>https: \/ /kailani-cin.one.microsoft.com | Indien, södra | https: \/ /tm-centralindia01.afs.azure.net<br>https: \/ /tm-kailani-cin.one.microsoft.com |
| Offentliga | Central US | https: \/ /centralus01.afs.azure.net<br>https: \/ /kailani-cus.one.microsoft.com | USA, östra 2 | https: \/ /tm-centralus01.afs.azure.net<br>https: \/ /tm-kailani-cus.one.microsoft.com |
| Offentliga | Asien, östra | https: \/ /eastasia01.afs.azure.net<br>https: \/ /kailani11.one.microsoft.com | Sydostasien | https: \/ /tm-eastasia01.afs.azure.net<br>https: \/ /tm-kailani11.one.microsoft.com |
| Offentliga | East US | https: \/ /eastus01.afs.azure.net<br>https: \/ /kailani1.one.microsoft.com | USA, västra | https: \/ /tm-eastus01.afs.azure.net<br>https: \/ /tm-kailani1.one.microsoft.com |
| Offentliga | USA, östra 2 | https: \/ /eastus201.afs.azure.net<br>https: \/ /kailani-ess.one.microsoft.com | Central US | https: \/ /tm-eastus201.afs.azure.net<br>https: \/ /tm-kailani-ess.one.microsoft.com |
| Offentliga | Tyskland, norra | https: \/ /germanynorth01.afs.azure.net | Tyskland, västra centrala | https: \/ /tm-germanywestcentral01.afs.azure.net |
| Offentliga | Tyskland, västra centrala | https: \/ /germanywestcentral01.afs.azure.net | Tyskland, norra | https: \/ /tm-germanynorth01.afs.azure.net |
| Offentliga | Japan, östra | https: \/ /japaneast01.afs.azure.net | Japan, västra | https: \/ /tm-japaneast01.afs.azure.net |
| Offentliga | Japan, västra | https: \/ /japanwest01.afs.azure.net | Japan, östra | https: \/ /tm-japanwest01.afs.azure.net |
| Offentliga | Sydkorea, centrala | https: \/ /koreacentral01.afs.azure.net/ | Sydkorea, södra | https: \/ /tm-koreacentral01.afs.azure.net/ |
| Offentliga | Sydkorea, södra | https: \/ /koreasouth01.afs.azure.net/ | Sydkorea, centrala | https: \/ /tm-koreasouth01.afs.azure.net/ |
| Offentliga | USA, norra centrala | https: \/ /northcentralus01.afs.azure.net | USA, södra centrala | https: \/ /tm-northcentralus01.afs.azure.net |
| Offentliga | Europa, norra | https: \/ /northeurope01.afs.azure.net<br>https: \/ /kailani7.one.microsoft.com | Europa, västra | https: \/ /tm-northeurope01.afs.azure.net<br>https: \/ /tm-kailani7.one.microsoft.com |
| Offentliga | USA, södra centrala | https: \/ /southcentralus01.afs.azure.net | USA, norra centrala | https: \/ /tm-southcentralus01.afs.azure.net |
| Offentliga | Indien, södra | https: \/ /southindia01.afs.azure.net<br>https: \/ /kailani-sin.one.microsoft.com | Indien, centrala | https: \/ /tm-southindia01.afs.azure.net<br>https: \/ /tm-kailani-sin.one.microsoft.com |
| Offentliga | Sydostasien | https: \/ /southeastasia01.afs.azure.net<br>https: \/ /kailani10.one.microsoft.com | Asien, östra | https: \/ /tm-southeastasia01.afs.azure.net<br>https: \/ /tm-kailani10.one.microsoft.com |
| Offentliga | Schweiz, norra | https: \/ /switzerlandnorth01.afs.azure.net<br>https: \/ /tm-switzerlandnorth01.afs.azure.net | Schweiz, västra | https: \/ /switzerlandwest01.afs.azure.net<br>https: \/ /tm-switzerlandwest01.afs.azure.net |
| Offentliga | Schweiz, västra | https: \/ /switzerlandwest01.afs.azure.net<br>https: \/ /tm-switzerlandwest01.afs.azure.net | Schweiz, norra | https: \/ /switzerlandnorth01.afs.azure.net<br>https: \/ /tm-switzerlandnorth01.afs.azure.net |
| Offentliga | Storbritannien, södra | https: \/ /uksouth01.afs.azure.net<br>https: \/ /kailani-uks.one.microsoft.com | Storbritannien, västra | https: \/ /tm-uksouth01.afs.azure.net<br>https: \/ /tm-kailani-uks.one.microsoft.com |
| Offentliga | Storbritannien, västra | https: \/ /ukwest01.afs.azure.net<br>https: \/ /kailani-ukw.one.microsoft.com | Storbritannien, södra | https: \/ /tm-ukwest01.afs.azure.net<br>https: \/ /tm-kailani-ukw.one.microsoft.com |
| Offentliga | USA, västra centrala | https: \/ /westcentralus01.afs.azure.net | USA, västra 2 | https: \/ /tm-westcentralus01.afs.azure.net |
| Offentliga | Europa, västra | https: \/ /westeurope01.afs.azure.net<br>https: \/ /kailani6.one.microsoft.com | Europa, norra | https: \/ /tm-westeurope01.afs.azure.net<br>https: \/ /tm-kailani6.one.microsoft.com |
| Offentliga | USA, västra | https: \/ /westus01.afs.azure.net<br>https: \/ /kailani.one.microsoft.com | East US | https: \/ /tm-westus01.afs.azure.net<br>https: \/ /tm-kailani.one.microsoft.com |
| Offentliga | USA, västra 2 | https: \/ /westus201.afs.azure.net | USA, västra centrala | https: \/ /tm-westus201.afs.azure.net |
| Myndigheter | US Gov, Arizona | https: \/ /usgovarizona01.afs.azure.us | US Gov, Texas | https: \/ /tm-usgovarizona01.afs.azure.us |
| Myndigheter | US Gov, Texas | https: \/ /usgovtexas01.afs.azure.us | US Gov, Arizona | https: \/ /tm-usgovtexas01.afs.azure.us |

- Om du använder lokalt redundanta (LRS) eller zonredundant (ZRS) lagringskonton behöver du bara aktivera den URL som anges under "Primär slutpunkts-URL".

- Om du använder globalt redundanta (GRS) lagringskonton aktiverar du tre URL:er.

**Exempel:** Du distribuerar en tjänst för synkronisering av `"West US"` lagring i och registrerar servern med den. Url:erna som servern kan kommunicera med för det här fallet är:

> - https: \/ /westus01.afs.azure.net (primär slutpunkt: USA, västra)
> - https: \/ /eastus01.afs.azure.net (länkad redundansregion: USA, östra)
> - https: \/ /tm-westus01.afs.azure.net (identifierings-URL för den primära regionen)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Lista över tillåtna Azure File Sync IP-adresser
Azure File Sync stöd för användning av [tjänsttaggar](../../virtual-network/service-tags-overview.md), som representerar en grupp IP-adressprefix för en viss Azure-tjänst. Du kan använda tjänsttaggar för att skapa brandväggsregler som möjliggör kommunikation med Azure File Sync tjänsten. Tjänsttaggen för Azure File Sync är `StorageSyncService` .

Om du använder Azure File Sync i Azure kan du använda namnet på tjänsttaggen direkt i nätverkssäkerhetsgruppen för att tillåta trafik. Mer information om hur du gör detta finns i [Nätverkssäkerhetsgrupper.](../../virtual-network/network-security-groups-overview.md)

Om du använder en Azure File Sync lokalt kan du använda tjänsttagg-API:et för att hämta specifika IP-adressintervall för brandväggens lista över tillåtna adresser. Det finns två metoder för att hämta den här informationen:

- Den aktuella listan över IP-adressintervall för alla Azure-tjänster som stöder tjänsttaggar publiceras varje vecka på Microsoft Download Center i form av ett JSON-dokument. Varje Azure-moln har ett eget JSON-dokument med IP-adressintervall som är relevanta för det molnet:
    - [Offentliga Azure-tjänster](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure Kina](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Tyskland](https://www.microsoft.com/download/details.aspx?id=57064)
- Med API:et för identifiering av tjänsttaggar (förhandsversion) kan du hämta den aktuella listan med tjänsttaggar programmatiskt. I förhandsversionen kan API:et för identifiering av tjänsttaggar returnera information som är mindre aktuell än information som returneras från JSON-dokumenten som publicerats på Microsoft Download Center. Du kan använda API-ytan baserat på dina automatiseringsinställningar:
    - [REST-API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](/cli/azure/network#az_network_list_service_tags)

Eftersom API:et för identifiering av tjänsttaggar inte uppdateras lika ofta som JSON-dokumenten som publiceras i Microsoft Download Center rekommenderar vi att du använder JSON-dokumentet för att uppdatera listan över tillåtna brandväggar i den lokala brandväggen. Detta kan göras på följande sätt:

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

Du kan sedan använda IP-adressintervallen i `$ipAddressRanges` för att uppdatera brandväggen. Information om hur du uppdaterar brandväggen finns på din brandväggs-/nätverksinstallations webbplats.

## <a name="test-network-connectivity-to-service-endpoints"></a>Testa nätverksanslutningen till tjänstslutpunkter
När en server har registrerats med Azure File Sync-tjänsten kan Test-StorageSyncNetworkConnectivity-cmdleten och ServerRegistration.exe användas för att testa kommunikationen med alla slutpunkter (URL:er) som är specifika för den här servern. Den här cmdleten kan hjälpa dig att felsöka när ofullständig kommunikation hindrar servern från att arbeta fullt ut med Azure File Sync och den kan användas för att finjustera proxy- och brandväggskonfigurationer.

Om du vill köra testet av nätverksanslutningen installerar du Azure File Sync version 9.1 eller senare och kör följande PowerShell-kommandon:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Sammanfattning och riskbegränsning
Listorna tidigare i det här dokumentet innehåller de URL:er Azure File Sync för närvarande kommunicerar med. Brandväggar måste kunna tillåta utgående trafik till dessa domäner. Microsoft strävar efter att hålla listan uppdaterad.

Att konfigurera brandväggsregler som begränsar domänen kan vara ett mått på att förbättra säkerheten. Om dessa brandväggskonfigurationer används måste du komma ihåg att URL:er läggs till och kan till och med ändras med tiden. Kontrollera den här artikeln med jämna mellanrum.

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](file-sync-planning.md)
- [Distribuera Azure File Sync](file-sync-deployment-guide.md)
- [Övervaka Azure File Sync](file-sync-monitoring.md)
