---
title: Azure File Sync inställningar för lokal brand vägg och proxy | Microsoft Docs
description: Förstå Azure File Sync lokala proxy-och brand Väggs inställningar. Granska konfigurations information för portar, nätverk och särskilda anslutningar till Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/30/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 01ac42cce29f941a90631936ece025f02afedeaf
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673628"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Inställningar för Azure File Sync-proxy och brandväggar
Azure File Sync ansluter dina lokala servrar till Azure Files, vilket möjliggör synkronisering av flera platser och moln nivåer. En lokal server måste därför vara ansluten till Internet. En IT-administratör måste bestämma den bästa sökvägen för att servern ska kunna komma åt Azure Cloud Services.

I den här artikeln får du information om särskilda krav och alternativ som är tillgängliga för att ansluta servern till Azure File Sync på ett säkert sätt.

Vi rekommenderar att du läser [Azure File Sync nätverks överväganden](storage-sync-files-networking-overview.md) innan du läser den här guiden.

## <a name="overview"></a>Översikt
Azure File Sync fungerar som en Orchestration-tjänst mellan Windows Server, Azure-filresursen och flera andra Azure-tjänster för att synkronisera data som beskrivs i din Sync-grupp. För att Azure File Sync ska fungera korrekt måste du konfigurera servrarna så att de kommunicerar med följande Azure-tjänster:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Authentication Services

> [!Note]  
> Azure File Sync agenten på Windows Server initierar alla förfrågningar till moln tjänster, vilket resulterar i att endast ta hänsyn till utgående trafik från ett brand Väggs perspektiv. <br /> Ingen Azure-tjänst initierar en anslutning till Azure File Sync agenten.

## <a name="ports"></a>Portar
Azure File Sync flyttar fildata och metadata exklusivt över HTTPS och kräver att port 443 är öppen för utgående data.
Det innebär att all trafik krypteras.

## <a name="networks-and-special-connections-to-azure"></a>Nätverk och särskilda anslutningar till Azure
Azure File Sync agenten har inga krav avseende särskilda kanaler som [ExpressRoute](../../expressroute/expressroute-introduction.md)osv. till Azure.

Azure File Sync kommer att fungera på något sätt som ger till gång till Azure, automatisk anpassning till olika nätverks egenskaper, t. ex. bandbredd, latens och administrations kontroll för fin justering. Alla funktioner är inte tillgängliga för tillfället. Om du vill konfigurera ett speciellt beteende kan du meddela oss via [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Azure File Sync har stöd för app-/regionsspecifika och datorövergripande proxyinställningar.

**Appar-specifika proxyinställningar** tillåter konfiguration av en proxy specifikt för Azure File Sync trafik. App-Specific proxy-inställningar stöds på agent version 4.0.1.0 eller senare och kan konfigureras under Agent installationen eller med hjälp av Set-StorageSyncProxyConfiguration PowerShell-cmdleten.

PowerShell-kommandon för att konfigurera appspecifika proxyinställningar:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
De **datorövergripande proxyinställningarna** är transparenta för Azure File Sync agenten eftersom hela trafiken på servern dirigeras via proxyservern.

Du kan konfigurera datorövergripande proxyinställningar med hjälp av stegen nedan: 

1. Konfigurera proxyinställningar för .NET-program 

   - Redigera dessa två filer:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Lägg till avsnittet <system.net> i machine.config filer (under avsnittet <system. serviceModel>).  Ändra 127.0.01:8888 till IP-adressen och porten för proxyservern. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Ange inställningar för WinHTTP-proxy 

   - Kör följande kommando från en upphöjd kommando tolk eller PowerShell för att se den befintliga inställningen för proxy:   

     Netsh WinHTTP show proxy

   - Kör följande kommando från en upphöjd kommando tolk eller PowerShell för att ange proxyinställningar (Change 127.0.01:8888 till IP-adressen och porten för proxyservern):  

     Netsh WinHTTP set proxy 127.0.0.1:8888

3. Starta om tjänsten Storage Sync agent genom att köra följande kommando från en upphöjd kommando tolk eller PowerShell: 

      net stop-filesyncsvc

      Obs! tjänsten Storage Sync agent (filesyncsvc) startar automatiskt när den har stoppats.

## <a name="firewall"></a>Brandvägg
Som anges i föregående avsnitt måste port 443 vara öppen för utgående trafik. Baserat på principer i ditt data Center, din gren eller region, kan ytterligare begränsa trafik via den här porten till vissa domäner eller obligatoriskt.

I följande tabell beskrivs de domäner som krävs för kommunikation:

| Tjänst | Offentlig moln slut punkt | Azure Government slut punkt | Användning |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Alla användar anrop (som PowerShell) går till/via denna URL, inklusive det första server registrerings anropet. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Azure Resource Manager-anrop måste göras av en autentiserad användare. För att lyckas används den här URL: en för användarautentisering. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | Som en del av distributionen Azure File Sync skapas ett huvud namn för tjänsten i prenumerationens Azure Active Directory. Den här URL: en används för det. Den här huvud gruppen används för att delegera en minimal uppsättning rättigheter till tjänsten Azure File Sync. Användaren som utför den första installationen av Azure File Sync måste vara en autentiserad användare med prenumerations ägarens behörigheter. |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | Använd URL: en för den offentliga slut punkten. | Den här URL: en används av Active Directory Authentication Library som användar gränssnittet för Azure File Sync-Server registrering använder för att logga in i administratören. |
| **Azure Storage** | &ast;. core.windows.net | &ast;. core.usgovcloudapi.net | När servern laddar ned en fil utför servern den data flyttningen mer effektivt när den pratar direkt till Azure-filresursen i lagrings kontot. Servern har en SAS-nyckel som endast tillåter åtkomst till riktad fil resurs. |
| **Azure File Sync** | &ast;. one.microsoft.com<br>&ast;. afs.azure.net | &ast;. afs.azure.us | Efter den första server registreringen får servern en regional URL för den Azure File Sync tjänst instansen i den regionen. Servern kan använda URL: en för att kommunicera direkt och effektivt med den instans som hanterar synkroniseringen. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | När Azure File Sync-agenten har installerats används PKI-URL: en för att hämta mellanliggande certifikat som krävs för att kommunicera med Azure File Sync-tjänsten och Azure-filresursen. OCSP-URL: en används för att kontrol lera status för ett certifikat. |
| **Microsoft Update** | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | När Azure File Sync-agenten har installerats används Microsoft Update URL: er för att ladda ned uppdateringar av Azure File Sync agenten. |

> [!Important]
> När trafik tillåts till &ast; . AFS.Azure.net är trafiken bara möjlig för synkroniseringstjänsten. Det finns inga andra Microsoft-tjänster som använder den här domänen.
> När trafik till &ast; . One.Microsoft.com tillåts kan trafik till mer än bara synkroniseringstjänsten vara möjlig från-servern. Det finns många fler Microsoft-tjänster som är tillgängliga under under domäner.

Om &ast; . AFS.Azure.net eller &ast; . One.Microsoft.com är för brett kan du begränsa serverns kommunikation genom att tillåta kommunikation enbart till explicita regionala instanser av tjänsten Azure Files Sync. Vilken eller vilka instanser som ska väljas beror på den region i lagrings tjänsten för synkronisering som du har distribuerat och registrerat servern på. Regionen kallas "primär slut punkts-URL" i tabellen nedan.

För affärs kontinuitet och haveri beredskap (BCDR) kan du ha angett Azure-filresurser i ett globalt redundant (GRS) lagrings konto. Om så är fallet växlar Azure-filresurserna över till den kopplade regionen vid ett varaktigt regionalt avbrott. Azure File Sync använder samma regionala par som lagring. Så om du använder GRS lagrings konton måste du aktivera ytterligare URL: er så att servern kan kommunicera med den kopplade regionen för Azure File Sync. Tabellen nedan anropar denna "parad region". Det finns dessutom en Traffic Manager-profils-URL som måste aktive ras. Detta säkerställer att nätverks trafiken kan dirigeras sömlöst till den kopplade regionen i händelse av redundans och kallas "identifierings-URL" i tabellen nedan.

| Moln  | Region | Primär slut punkts-URL | Länkad region | URL för identifiering |
|--------|--------|----------------------|---------------|---------------|
| Offentliga |Australien, östra | https: \/ /australiaeast01.AFS.Azure.net<br>https: \/ /Kailani-Aue.One.Microsoft.com | Australien, sydöstra | https: \/ /TM-australiaeast01.AFS.Azure.net<br>https: \/ /TM-Kailani-Aue.One.Microsoft.com |
| Offentliga |Australien, sydöstra | https: \/ /australiasoutheast01.AFS.Azure.net<br>https: \/ /Kailani-AUS.One.Microsoft.com | Australien, östra | https: \/ /TM-australiasoutheast01.AFS.Azure.net<br>https: \/ /TM-Kailani-AUS.One.Microsoft.com |
| Offentliga | Brasilien, södra | https: \/ /brazilsouth01.AFS.Azure.net | USA, södra centrala | https: \/ /TM-brazilsouth01.AFS.Azure.net |
| Offentliga | Kanada, centrala | https: \/ /canadacentral01.AFS.Azure.net<br>https: \/ /Kailani-CAC.One.Microsoft.com | Kanada, östra | https: \/ /TM-canadacentral01.AFS.Azure.net<br>https: \/ /TM-Kailani-CAC.One.Microsoft.com |
| Offentliga | Kanada, östra | https: \/ /canadaeast01.AFS.Azure.net<br>https: \/ /Kailani-CAE.One.Microsoft.com | Kanada, centrala | https: \/ /TM-canadaeast01.AFS.Azure.net<br>https: \/ /TM-Kailani.CAE.One.Microsoft.com |
| Offentliga | Indien, centrala | https: \/ /centralindia01.AFS.Azure.net<br>https: \/ /Kailani-CIN.One.Microsoft.com | Indien, södra | https: \/ /TM-centralindia01.AFS.Azure.net<br>https: \/ /TM-Kailani-CIN.One.Microsoft.com |
| Offentliga | Central US | https: \/ /centralus01.AFS.Azure.net<br>https: \/ /Kailani-CUS.One.Microsoft.com | USA, östra 2 | https: \/ /TM-centralus01.AFS.Azure.net<br>https: \/ /TM-Kailani-CUS.One.Microsoft.com |
| Offentliga | Asien, östra | https: \/ /eastasia01.AFS.Azure.net<br>https: \/ /kailani11.One.Microsoft.com | Sydostasien | https: \/ /TM-eastasia01.AFS.Azure.net<br>https: \/ /TM-kailani11.One.Microsoft.com |
| Offentliga | East US | https: \/ /eastus01.AFS.Azure.net<br>https: \/ /kailani1.One.Microsoft.com | USA, västra | https: \/ /TM-eastus01.AFS.Azure.net<br>https: \/ /TM-kailani1.One.Microsoft.com |
| Offentliga | USA, östra 2 | https: \/ /eastus201.AFS.Azure.net<br>https: \/ /Kailani-ess.One.Microsoft.com | Central US | https: \/ /TM-eastus201.AFS.Azure.net<br>https: \/ /TM-Kailani-ess.One.Microsoft.com |
| Offentliga | Tyskland, norra | https: \/ /germanynorth01.AFS.Azure.net | Tyskland, västra centrala | https: \/ /TM-germanywestcentral01.AFS.Azure.net |
| Offentliga | Tyskland, västra centrala | https: \/ /germanywestcentral01.AFS.Azure.net | Tyskland, norra | https: \/ /TM-germanynorth01.AFS.Azure.net |
| Offentliga | Japan, östra | https: \/ /japaneast01.AFS.Azure.net | Japan, västra | https: \/ /TM-japaneast01.AFS.Azure.net |
| Offentliga | Japan, västra | https: \/ /japanwest01.AFS.Azure.net | Japan, östra | https: \/ /TM-japanwest01.AFS.Azure.net |
| Offentliga | Sydkorea, centrala | https: \/ /koreacentral01.AFS.Azure.net/ | Sydkorea, södra | https: \/ /TM-koreacentral01.AFS.Azure.net/ |
| Offentliga | Sydkorea, södra | https: \/ /koreasouth01.AFS.Azure.net/ | Sydkorea, centrala | https: \/ /TM-koreasouth01.AFS.Azure.net/ |
| Offentliga | USA, norra centrala | https: \/ /northcentralus01.AFS.Azure.net | USA, södra centrala | https: \/ /TM-northcentralus01.AFS.Azure.net |
| Offentliga | Europa, norra | https: \/ /northeurope01.AFS.Azure.net<br>https: \/ /kailani7.One.Microsoft.com | Europa, västra | https: \/ /TM-northeurope01.AFS.Azure.net<br>https: \/ /TM-kailani7.One.Microsoft.com |
| Offentliga | USA, södra centrala | https: \/ /southcentralus01.AFS.Azure.net | USA, norra centrala | https: \/ /TM-southcentralus01.AFS.Azure.net |
| Offentliga | Indien, södra | https: \/ /southindia01.AFS.Azure.net<br>https: \/ /Kailani-sin.One.Microsoft.com | Indien, centrala | https: \/ /TM-southindia01.AFS.Azure.net<br>https: \/ /TM-Kailani-sin.One.Microsoft.com |
| Offentliga | Sydostasien | https: \/ /southeastasia01.AFS.Azure.net<br>https: \/ /kailani10.One.Microsoft.com | Asien, östra | https: \/ /TM-southeastasia01.AFS.Azure.net<br>https: \/ /TM-kailani10.One.Microsoft.com |
| Offentliga | Schweiz, norra | https: \/ /switzerlandnorth01.AFS.Azure.net<br>https: \/ /TM-switzerlandnorth01.AFS.Azure.net | Schweiz, västra | https: \/ /switzerlandwest01.AFS.Azure.net<br>https: \/ /TM-switzerlandwest01.AFS.Azure.net |
| Offentliga | Schweiz, västra | https: \/ /switzerlandwest01.AFS.Azure.net<br>https: \/ /TM-switzerlandwest01.AFS.Azure.net | Schweiz, norra | https: \/ /switzerlandnorth01.AFS.Azure.net<br>https: \/ /TM-switzerlandnorth01.AFS.Azure.net |
| Offentliga | Storbritannien, södra | https: \/ /uksouth01.AFS.Azure.net<br>https: \/ /Kailani-UKS.One.Microsoft.com | Storbritannien, västra | https: \/ /TM-uksouth01.AFS.Azure.net<br>https: \/ /TM-Kailani-UKS.One.Microsoft.com |
| Offentliga | Storbritannien, västra | https: \/ /ukwest01.AFS.Azure.net<br>https: \/ /Kailani-UKW.One.Microsoft.com | Storbritannien, södra | https: \/ /TM-ukwest01.AFS.Azure.net<br>https: \/ /TM-Kailani-UKW.One.Microsoft.com |
| Offentliga | USA, västra centrala | https: \/ /westcentralus01.AFS.Azure.net | USA, västra 2 | https: \/ /TM-westcentralus01.AFS.Azure.net |
| Offentliga | Europa, västra | https: \/ /westeurope01.AFS.Azure.net<br>https: \/ /kailani6.One.Microsoft.com | Europa, norra | https: \/ /TM-westeurope01.AFS.Azure.net<br>https: \/ /TM-kailani6.One.Microsoft.com |
| Offentliga | USA, västra | https: \/ /westus01.AFS.Azure.net<br>https: \/ /Kailani.One.Microsoft.com | East US | https: \/ /TM-westus01.AFS.Azure.net<br>https: \/ /TM-Kailani.One.Microsoft.com |
| Offentliga | USA, västra 2 | https: \/ /westus201.AFS.Azure.net | USA, västra centrala | https: \/ /TM-westus201.AFS.Azure.net |
| Myndigheter | US Gov, Arizona | https: \/ /usgovarizona01.AFS.Azure.us | US Gov, Texas | https: \/ /TM-usgovarizona01.AFS.Azure.us |
| Myndigheter | US Gov, Texas | https: \/ /usgovtexas01.AFS.Azure.us | US Gov, Arizona | https: \/ /TM-usgovtexas01.AFS.Azure.us |

- Om du använder lokalt redundant (LRS) eller zon redundant lagrings konto (ZRS) behöver du bara aktivera URL: en som anges under "primär slut punkts-URL".

- Om du använder globalt redundanta lagrings konton (GRS) aktiverar du tre URL: er.

**Exempel:** Du distribuerar en tjänst för synkronisering av lagring i `"West US"` och registrerar servern med den. URL: erna som gör att servern kan kommunicera till för det här fallet är:

> - https: \/ /westus01.AFS.Azure.net (primär slut punkt: USA, västra)
> - https: \/ /eastus01.AFS.Azure.net (parad failover-över region: USA, östra)
> - https: \/ /TM-westus01.AFS.Azure.net (identifierings-URL för den primära regionen)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Lista över tillåtna Azure File Sync IP-adresser
Azure File Sync stöder användningen av [service märken](../../virtual-network/service-tags-overview.md)som representerar en grupp IP-adressprefix för en specifik Azure-tjänst. Du kan använda service märken för att skapa brand Väggs regler som möjliggör kommunikation med tjänsten Azure File Sync. Tjänst tag gen för Azure File Sync är `StorageSyncService` .

Om du använder Azure File Sync i Azure kan du använda namnet på service tag gen direkt i nätverks säkerhets gruppen för att tillåta trafik. Mer information om hur du gör detta finns i [nätverks säkerhets grupper](../../virtual-network/network-security-groups-overview.md).

Om du använder Azure File Sync lokalt kan du använda API: et för service tag för att hämta vissa IP-adressintervall för brand väggens lista över tillåtna. Det finns två metoder för att hämta den här informationen:

- Den aktuella listan över IP-adressintervall för alla tjänster som stöder service taggar för Azure-tjänster publiceras varje vecka på Microsoft Download Center i form av ett JSON-dokument. Varje Azure-moln har sitt eget JSON-dokument med de IP-adressintervall som är relevanta för det molnet:
    - [Offentlig Azure-](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure Kina](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Tyskland](https://www.microsoft.com/download/details.aspx?id=57064)
- API: et för identifiering av service tag (för hands version) möjliggör program mässig hämtning av den aktuella listan över service märken. I för hands versionen kan service tag Discovery API returnera information som är mindre aktuell än information som returneras från JSON-dokumenten som publicerats på Microsoft Download Center. Du kan använda API-ytan baserat på inställningarna för Automation:
    - [REST-API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](/cli/azure/network#az-network-list-service-tags)

Eftersom API för service tag-identifiering inte uppdateras så ofta som JSON-dokument som publicerats till Microsoft Download Center, rekommenderar vi att du använder JSON-dokumentet för att uppdatera din lokala brand Väggs lista över tillåtna. Detta kan göras på följande sätt:

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

Du kan sedan använda IP-adressintervall i `$ipAddressRanges` för att uppdatera brand väggen. Kontrol lera brand Väggs-/nätverks enhetens webbplats för information om hur du uppdaterar brand väggen.

## <a name="test-network-connectivity-to-service-endpoints"></a>Testa nätverks anslutningen till tjänstens slut punkter
När en server har registrerats med Azure File Sync tjänsten kan Test-StorageSyncNetworkConnectivity-cmdlet och ServerRegistration.exe användas för att testa kommunikation med alla slut punkter (URL) som är speciella för den här servern. Den här cmdleten kan hjälpa till att felsöka när ofullständig kommunikation förhindrar att servern fungerar fullt ut med Azure File Sync och den kan användas för att finjustera proxy-och brand Väggs konfigurationerna.

Om du vill köra testet för nätverks anslutning installerar du Azure File Sync agent version 9,1 eller senare och kör följande PowerShell-kommandon:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Sammanfattning och risk begränsning
Listorna ovan i det här dokumentet innehåller de URL: er Azure File Sync som för närvarande kommunicerar med. Brand väggar måste kunna tillåta utgående trafik till dessa domäner. Microsoft strävar efter att hålla listan uppdaterad.

Det kan vara ett mått för att förbättra säkerheten för att konfigurera domän begränsning av brand Väggs regler. Om dessa brand Väggs konfigurationer används måste en vara i åtanke att URL: er läggs till och kan ändras med tiden. Läs den här artikeln regelbundet.

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
