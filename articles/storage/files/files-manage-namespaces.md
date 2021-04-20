---
title: Använda DFS-N med Azure Files
description: Vanliga DFS-N-användningsfall med Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a8f1b8c54ad4fd42cc8ebda4965aaf1233143f39
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741989"
---
# <a name="how-to-use-dfs-namespaces-with-azure-files"></a>Använda DFS-namnrymder med Azure Files
[Namnrymder](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)för distribuerade filsystem, som ofta kallas DFS-namnrymder eller DFS-N, är en Windows Server-serverroll som ofta används för att förenkla distribution och underhåll av SMB-filresurser i produktion. DFS-namnrymder är en teknik för virtualisering av lagringsnamnrymd, vilket innebär att du kan tillhandahålla ett indirekt lager mellan UNC-sökvägen för dina filresurser och själva filresurser. DFS-namnrymder fungerar med SMB-filresurser, är agnostiska för dessa filresurser värdbaserade: det kan användas med SMB-resurser som finns på en lokal Windows-filserver med eller utan Azure File Sync, Azure-filresurser direkt, SMB-filresurser som finns i Azure NetApp Files eller andra erbjudanden från tredje part, och även med filresurser som finns i andra moln. 

I grunden tillhandahåller DFS-namnrymder en mappning mellan en användarvänlig UNC-sökväg, som och den underliggande UNC-sökvägen för `\\contoso\shares\ProjectX` SMB-resursen som `\\Server01-Prod\ProjectX` eller `\\storageaccount.file.core.windows.net\projectx` . När slutanvändaren vill navigera till sin filresurs skriver de in den användarvänliga UNC-sökvägen, men deras SMB-klient kommer åt den underliggande SMB-sökvägen för mappningen. Du kan också utöka det här grundläggande konceptet för att ta över ett befintligt filservernamn, till exempel `\\MyServer\ProjectX` . Du kan använda den här funktionen för att uppnå följande scenarier:

- Ange ett migreringssäkert namn för en logisk datauppsättning. I det här exemplet har du en mappning som `\\contoso\shares\Engineering` den som mappar till `\\OldServer\Engineering` . När du har slutfört migreringen till Azure Files kan du ändra mappningen så att din användarvänliga UNC-sökväg pekar på `\\storageaccount.file.core.windows.net\engineering` . När en slutanvändare får åtkomst till den användarvänliga UNC-sökvägen omdirigeras de sömlöst till sökvägen till Azure-filresursen.

- Upprätta ett gemensamt namn för en logisk datauppsättning som distribueras till flera servrar på olika fysiska platser, till exempel via Azure File Sync. I det här exemplet mappas ett namn `\\contoso\shares\FileSyncExample` som till flera UNC-sökvägar som `\\FileSyncServer1\ExampleShare` , , `\\FileSyncServer2\DifferentShareName` `\\FileSyncServer3\ExampleShare` . När användaren får åtkomst till det användarvänliga UNC får de en lista över möjliga UNC-sökvägar och väljer den som är närmast dem baserat på ad-Windows Server Active Directory (ad)-webbplatsdefinitioner.

- Utöka en logisk uppsättning data över storlek, I/O eller andra skalningströsklar. Detta är vanligt när du hanterar användarkataloger, där varje användare får sin egen mapp på en resurs eller med tillfälliga resurser, där användarna får godtyckligt utrymme för att hantera tillfälliga databehov. Med DFS-namnrymder sammanfogar du flera mappar i ett sammanhängande namnområde. Till exempel `\\contoso\shares\UserShares\user1` mappar till , mappar till och så `\\storageaccount.file.core.windows.net\user1` `\\contoso\shares\UserShares\user2` `\\storageaccount.file.core.windows.net\user2` vidare.  

Du kan se ett exempel på hur du använder DFS-namnrymder med Azure Files distribution i följande videoöversikt.

[![Demo om hur du ställer in DFS-N med Azure Files – klicka för att spela upp!](./media/files-manage-namespaces/video-snapshot-dfsn.png)](https://www.youtube.com/watch?v=jd49W33DxkQ)
> [!NOTE]  
> Gå vidare till 10:10 i videon för att se hur du ställer in DFS-namnrymder.

Om du redan har ett DFS-namnområde på plats krävs inga särskilda steg för att använda det med Azure Files och File Sync. Om du använder azure-filresursen från en lokal plats gäller vanliga nätverksöverväganden. Se [Azure Files nätverksöverväganden](./storage-files-networking-overview.md) för mer information.

## <a name="namespace-types"></a>Namnområdestyper
DFS-namnrymder innehåller två huvudnamnrymdstyper:

- **Domänbaserat namnområde:** Ett namnområde som finns som en del av din Windows Server AD domän. Namnrymder som finns som en del av AD har en UNC-sökväg som innehåller namnet på din domän, till exempel `\\contoso.com\shares\myshare` , om din domän är `contoso.com` . Domänbaserade namnrymder stöder större skalningsgränser och inbyggd redundans via AD. Domänbaserade namnområden kan inte vara en klustrad resurs i ett redundanskluster. 
- **Fristående namnområde:** Ett namnområde som finns på en enskild server och som inte finns som en del av Windows Server AD. Fristående namnrymder har ett namn baserat på namnet på den fristående servern, till exempel , där `\\MyStandaloneServer\shares\myshare` den fristående servern heter `MyStandaloneServer` . Fristående namnområden har stöd för mål med lägre skalning än domänbaserade namnrymder, men de kan finnas som en klustrad resurs i ett redundanskluster.

## <a name="requirements"></a>Krav
Om du vill använda DFS-Azure Files och File Sync måste du ha följande resurser:

- En Active Directory-domän. Den kan finnas var du vill, till exempel lokalt, på en virtuell Azure-dator (VM) eller till och med i ett annat moln.
- En Windows Server som kan vara värd för namnområdet. Ett vanligt mönster för distribution av DFS-namnrymder är att använda Active Directory-domänkontrollanten som värd för namnrymderna, men namnrymderna kan konfigureras från valfri server med serverrollen DFS-namnrymder installerad. DFS-namnrymder är tillgängliga i alla Windows Server-versioner som stöds.
- En SMB-filresurs som finns i en domän-ansluten miljö, till exempel en Azure-filresurs som finns i ett domän-ansluten lagringskonto eller en filresurs som finns på en domän-ansluten Windows-filserver med Azure File Sync. Mer information om domän-anslutning till ditt lagringskonto finns [i Identitetsbaserad autentisering.](storage-files-active-directory-overview.md) Windows-filservrar är domän-ansluten på samma sätt oavsett om du använder Azure File Sync.
- De SMB-filresurser som du vill använda med DFS-namnrymder måste kunna nås från dina lokala nätverk. Detta är främst ett problem för Azure-filresurser, men tekniskt gäller det för alla filresurser som finns i Azure eller andra moln. Mer information om nätverk finns i [Nätverksöverväganden för direktåtkomst.](storage-files-networking-overview.md)

## <a name="install-the-dfs-namespaces-server-role"></a>Installera serverrollen DFS-namnrymder
Om du redan använder DFS-namnrymder, eller om du vill konfigurera DFS-namnrymder på domänkontrollanten, kan du hoppa över dessa steg på ett säkert sätt.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill installera serverrollen DFS-namnrymder öppnar Serverhanteraren på servern. Välj **Hantera** och välj sedan Lägg **till roller och funktioner.** Den resulterande guiden vägleder dig genom installationen av de Windows-komponenter som krävs för att köra och hantera DFS-namnrymder. 

I avsnittet **Installationstyp** i installationsguiden väljer du alternativknappen Rollbaserad eller **funktionsbaserad installation** och väljer **Nästa.** I **avsnittet Serverval** väljer du de servrar som du vill installera serverrollen DFS-namnrymder på och väljer **Nästa.** 

I avsnittet **Serverroller** väljer du och kontrollerar **DFS-namnrymdsrollen** från rolllistan. Du hittar detta under **Fil- och lagringstjänster**  >  **fil- och ISCSI-tjänster.** När du väljer serverrollen DFS-namnrymder kan den även lägga till nödvändiga stödserverroller eller funktioner som du inte redan har installerat.

![En skärmbild av guiden **Lägg till roller och funktioner** med rollen **DFS-namnrymder** vald.](./media/files-manage-namespaces/dfs-namespaces-install.png)

När du har kontrollerat **DFS-namnrymdsrollen** kan du välja  Nästa på alla efterföljande skärmar och välja Installera så snart guiden aktiverar knappen.  När installationen är klar kan du konfigurera ditt namnområde.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Från en upphöjd PowerShell-session (eller med PowerShell-fjärrkommunikation) kör du följande kommandon.

```PowerShell
Install-WindowsFeature -Name "FS-DFS-Namespace", "RSAT-DFS-Mgmt-Con"
```
---

## <a name="take-over-existing-server-names-with-root-consolidation"></a>Ta över befintliga servernamn med rotkonsolidering
En viktig användning för DFS-namnrymder är att ta över ett befintligt servernamn för att omstrukturera den fysiska layouten för filresurser. Du kanske till exempel vill konsolidera filresurser från flera gamla filservrar tillsammans på en enda filserver under en moderniseringsmigrering. Traditionellt begränsar slutanvändares kunskaper och dokumentlänkning möjligheten att konsolidera filresurser från olika filservrar tillsammans på en värd, men med rotkonsolideringsfunktionen i DFS-namnrymder kan du ställa upp en enskild server till flera servernamn och dirigera till lämpligt resursnamn.

Även om det är användbart för olika scenarier för datacentermigrering är rotkonsolidering särskilt användbart för att använda molnbaserade Azure-filresurser eftersom:

- Med Azure-filresurser kan du inte behålla befintliga lokala servernamn.
- Azure-filresurser måste nås via det fullständiga domännamnet (FQDN) för lagringskontot. Till exempel kan en Azure-filresurs `share` som heter i `storageaccount` lagringskontot alltid nås via `\\storageaccount.file.core.windows.net\share` UNC-sökvägen. Detta kan vara förvirrande för slutanvändare som förväntar sig ett kort namn (t.ex. `\\MyServer\share`) eller ett namn som är en underdomän till organisationens domännamn (t.ex. `\\MyServer.contoso.com\share`).

Rotkonsolidering kan bara användas med fristående namnrymder. Om du redan har ett befintligt domänbaserat namnområde för dina filresurser behöver du inte skapa ett rot konsoliderat namnområde.

### <a name="enabling-root-consolidation"></a>Aktivera rotkonsolidering
Rotkonsolidering kan aktiveras genom att ange följande registernycklar från en upphöjd PowerShell-session (eller via PowerShell-fjärrkommunikation).

```PowerShell
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Type Registry `
    -ErrorAction SilentlyContinue
Set-ItemProperty `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Name "ServerConsolidationRetry" `
    -Value 1
```

### <a name="creating-dns-entries-for-existing-file-server-names"></a>Skapa DNS-poster för befintliga filservernamn
För att DFS-namnrymder ska svara på befintliga filnamn skapar du aliasposter (CNAME) för dina befintliga filservrar som pekar på DFS-namnrymdens servernamn. Den exakta proceduren för att uppdatera DINA DNS-poster kan bero på vilka servrar din organisation använder och om din organisation använder anpassade verktyg för att automatisera hanteringen av DNS. Följande steg visas för DNS-servern som ingår i Windows Server och används automatiskt av Windows AD.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Öppna DNS-hanteringskonsolen på en Windows DNS-server. Du hittar detta genom att välja **knappen Start** och skriva **DNS**. Gå till zonen för sökning framåt för din domän. Om din domän till exempel är finns zonen för sökning framåt `contoso.com` under Forward **Lookup Zones (Forward Lookup**  >  **`contoso.com`** Zones) i hanteringskonsolen. Den exakta hierarkin som visas i den här dialogrutan beror på NÄTVERKETs DNS-konfiguration.

Högerklicka på din zon för sökning framåt och välj **Nytt alias (CNAME).** I dialogrutan som visas anger du kortnamnet för den filserver som du ersätter (det fullständigt kvalificerade domännamnet fylls i automatiskt i textrutan med namnet **Fullständigt kvalificerat domännamn**). I textrutan med namnet Fullständigt kvalificerat domännamn **(FQDN)** för målvärden anger du namnet på den DFS-N-server som du har ställt in. Du kan använda knappen **Bläddra** för att välja server om du vill. Välj **OK** för att skapa CNAME-posten för servern.

![En skärmbild som visar **Ny resurspost** för en CNAME DNS-post.](./media/files-manage-namespaces/root-consolidation-cname.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
På en Windows DNS-server öppnar du en PowerShell-session (eller använder PowerShell-fjärrkommunikation) för att köra följande kommandon och fylla i och , med relevanta värden för din miljö ( fylls i automatiskt med domännamnet, men du kan även skriva ut detta `$oldServer` `$dfsnServer` `$domain` manuellt).

```PowerShell
# Variables
$oldServer = "MyServer"
$domain = Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
$dfsnServer = "CloudDFSN.$domain"

# Create CNAME record
Import-Module -Name DnsServer
Add-DnsServerResourceRecordCName `
    -Name $oldServer `
    -HostNameAlias $dfsnServer `
    -ZoneName $domain
```

---

## <a name="create-a-namespace"></a>Skapa ett namnområde
Den grundläggande hanteringsenheten för DFS-namnrymder är namnområdet. Namnområdesroten, eller namnet, är startpunkten för namnområdet, så att i UNC-sökvägen `\\contoso.com\Public\` är namnområdesroten `Public` . 

Om du använder DFS-namnrymder för att ta över ett befintligt servernamn med rotkonsolidering bör namnet på namnområdet vara namnet på det servernamn som du vill ta över, med tecknet `#` i slutet. Om du till exempel vill ta över en befintlig server med namnet `MyServer` skapar du ett DFS-N-namnområde med namnet `#MyServer` . PowerShell-avsnittet nedan tar hand om förestående , men om du skapar via DFS-hanteringskonsolen måste du `#` förbereda efter behov. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill skapa ett nytt namnområde öppnar du **DFS-hanteringskonsolen.** Du hittar detta genom att välja **knappen Start** och skriva **DFS Management**. Den resulterande hanteringskonsolen **har** två avsnitt namnrymder och **replikering**, som refererar till DFS-DFS Replication (DFS-R) respektive . Azure File Sync en modern replikerings- och synkroniseringsmekanism som kan användas i stället för DFS-R om replikering också önskas.

Välj **avsnittet Namnområden** och välj knappen **Nytt namnområde** (du kan också högerklicka på avsnittet **Namnområden).** Guiden Nytt **namnområde visar hur** du skapar ett namnområde. 

Det första avsnittet i guiden kräver att du väljer DFS-namnområdesservern som värd för namnområdet. Flera servrar kan vara värdar för ett namnområde, men du måste konfigurera DFS-namnrymder med en server i taget. Ange namnet på den önskade DFS-namnrymdsservern och välj **Nästa.** I **avsnittet Namnområdesnamn och** inställningar kan du ange önskat namn för namnområdet och välja **Nästa.** 

I **avsnittet Typ av** namnområde kan du välja mellan ett **domänbaserat namnområde** och ett **fristående namnområde**. Om du tänker använda DFS-namnrymder för att bevara ett befintligt filserver-/NAS-enhetsnamn bör du välja alternativet för fristående namnområde. För andra scenarier bör du välja ett domänbaserat namnområde. Mer information [om hur du väljer](#namespace-types) mellan namnområdestyper finns i namnområdestyperna ovan.

![En skärmbild av hur du väljer mellan ett domänbaserat namnområde och ett fristående namnområde i guiden **Nytt namnområde**.](./media/files-manage-namespaces/dfs-namespace-type.png)

Välj önskad namnområdestyp för din miljö och välj **Nästa.** Guiden sammanfattar sedan det namnområde som ska skapas. Välj **Skapa** för att skapa namnområdet **och Stäng** när dialogrutan är klar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Från en PowerShell-session på DFS-namnområdesservern kör du följande PowerShell-kommandon och fyller i , och med `$namespace` relevanta värden för din `$type` `$takeOverName` miljö.

```PowerShell
# Variables
$namespace = "Public"
$type = "DomainV2" # "Standalone"
$takeOverName = $false # $true

$namespace = if ($takeOverName -and $type -eq "Standalone" -and $namespace[0] -ne "#") { 
    "#$namespace" 
} else { $namespace }
$dfsnServer = $env:ComputerName
$namespaceServer = if ($type -eq "DomainV2") { 
    Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
} else { $dfsnServer }

# Create share for DFS-N namespace
$smbShare = "C:\DFSRoots\$namespace"
if (!(Test-Path -Path $smbShare)) { New-Item -Path $smbShare -ItemType Directory }
New-SmbShare -Name $namespace -Path $smbShare -FullAccess Everyone

# Create DFS-N namespace
Import-Module -Name DFSN
$namespacePath = "\\$namespaceServer\$namespace"
$targetPath = "\\$dfsnServer\$namespace"
New-DfsnRoot -Path $namespacePath -TargetPath $targetPath -Type $type
```
---

## <a name="configure-folders-and-folder-targets"></a>Konfigurera mappar och mappmål
För att ett namnområde ska vara användbart måste det ha mappar och mappmål. Varje mapp kan ha ett eller flera mappmål, som är pekare till de SMB-filresurs(er) som är värdar för innehållet. När användare bläddrar i en mapp med mappmål får klientdatorn en referens som transparent omdirigerar klientdatorn till något av mappmålen. Du kan också ha mappar utan mappmål för att lägga till struktur och hierarki i namnområdet.

Du kan tänka på DFS-namnrymdsmappar som analoga med filresurser. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
I DFS-hanteringskonsolen väljer du det namnområde som du nyss skapade och väljer **Ny mapp.** I dialogrutan **Ny** mapp kan du skapa både mappen och dess mål.

![En skärmbild av dialogrutan **Ny mapp**.](./media/files-manage-namespaces/dfs-folder-targets.png)

I textrutan med namnet **Namn** anger du namnet på mappen. Välj **Lägg till...** för att lägga till mappmål för den här mappen. Den resulterande **dialogrutan Lägg till** mappmål innehåller en textruta med etiketten Sökväg till **mappmål** där du kan ange UNC-sökvägen till önskad mapp. Välj **OK** i dialogrutan **Lägg till mappmål.** Om du lägger till en UNC-sökväg till en Azure-filresurs kan du få ett meddelande om att servern `storageaccount.file.core.windows.net` inte kan vara kontakter. Detta är förväntat; välj **Ja för** att fortsätta. Välj slutligen **OK i** dialogrutan Ny mapp **för** att skapa mapp- och mappmålen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Variables
$shareName = "MyShare"
$targetUNC = "\\storageaccount.file.core.windows.net\myshare"

# Create folder and folder targets
$sharePath = "$namespacePath\$shareName"
New-DfsnFolder -Path $sharePath -TargetPath $targetUNC
```

---

Nu när du har skapat ett namnområde, en mapp och ett mappmål bör du kunna montera filresursen via DFS-namnrymder. Om du använder ett domänbaserat namnområde ska den fullständiga sökvägen för resursen vara `\\<domain-name>\<namespace>\<share>` . Om du använder ett fristående namnområde ska den fullständiga sökvägen för resursen vara `\\<DFS-server>\<namespace>\<share>` . Om du använder ett fristående namnområde med rotkonsolidering kan du komma åt direkt via ditt gamla servernamn, till exempel `\\<old-server>\<share>` .

## <a name="see-also"></a>Se även
- Distribuera en Azure-filresurs: [Planera för en Azure Files-distribution](storage-files-planning.md) [och Så här skapar du en filresurs.](storage-how-to-create-file-share.md)
- Konfigurera filresursåtkomst: [Identitetsbaserad autentisering och](storage-files-active-directory-overview.md) [nätverksöverväganden för direktåtkomst.](storage-files-networking-overview.md)
- [Namnrymder Distributed File System Windows Server](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)