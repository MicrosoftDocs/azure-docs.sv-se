---
title: Felsöka Azure Files-problem i Windows
description: Felsöka Azure Files i Windows. Se vanliga problem som rör Azure Files du ansluter från Windows-klienter och se möjliga lösningar. Endast för SMB-resurser
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/13/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 115c083a75adab96e416fc200bf7db287a99ff4e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788429"
---
# <a name="troubleshoot-azure-files-problems-in-windows-smb"></a>Felsöka Azure Files i Windows (SMB)

Den här artikeln innehåller vanliga problem som rör Microsoft Azure Files när du ansluter från Windows-klienter. Det ger också möjliga orsaker och lösningar på dessa problem. Förutom felsökningsstegen i den här artikeln kan du också använda [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) för att säkerställa att Windows-klientmiljön har rätt förutsättningar. AzFileDiagnostics automatiserar identifieringen av de flesta symptom som nämns i den här artikeln och hjälper dig att konfigurera din miljö för att få optimala prestanda.

> [!IMPORTANT]
> Innehållet i den här artikeln gäller endast för SMB-resurser. Mer information om NFS-resurser finns i [Felsöka Azure NFS-filresurser.](storage-troubleshooting-files-nfs.md)

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Fel 5 när du monterar en Azure-filresurs

När du försöker montera en filresurs kan följande felmeddelande visas:

- Systemfel 5 har uppstått. Åtkomst nekas.

### <a name="cause-1-unencrypted-communication-channel"></a>Orsak 1: Okrypterad kommunikationskanal

Av säkerhetsskäl blockeras anslutningar till Azure-filresurser om kommunikationskanalen inte är krypterad och om anslutningsförsöket inte görs från samma datacenter där Azure-filresurserna finns. Okrypterade anslutningar inom samma datacenter kan också blockeras om inställningen [Säker överföring krävs ](../common/storage-require-secure-transfer.md) är aktiverad på lagringskontot. En krypterad kommunikationskanal tillhandahålls endast om användarens klientoperativsystem stöder SMB-kryptering.

Windows 8, Windows Server 2012 och senare versioner av respektive system förhandlar begäranden som innehåller SMB 3.0, som stöder kryptering.

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

1. Anslut från en klient som stöder SMB-kryptering (Windows 8, Windows Server 2012 eller senare) eller anslut från en virtuell dator i samma datacenter som Azure Storage-kontot som används för Azure-filresursen.
2. Kontrollera att [inställningen Säker överföring](../common/storage-require-secure-transfer.md) krävs är inaktiverad på lagringskontot om klienten inte stöder SMB-kryptering.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Orsak 2: Virtuellt nätverk eller brandväggsregler har aktiverats för lagringskontot 

Om virtuellt nätverk (VNET) och brandväggsregler har konfigurerats på lagringskontot tillåts inte nätverkstrafik om inte åtkomst tillåts för klientens IP-adress eller virtuella nätverk.

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Kontrollera att det virtuella nätverket och brandväggsreglerna har konfigurerats korrekt på lagringskontot. Om du vill testa om det virtuella nätverket eller brandväggsreglerna som orsakar problemet kan du tillfälligt ändra inställningen på lagringskontot för att **tillåta åtkomst från alla nätverk**. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../common/storage-network-security.md).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Orsak 3: Behörigheter på resursnivå är felaktiga när du använder identitetsbaserad autentisering

Om användarna använder Azure-filresursen med hjälp av Active Directory-autentisering (AD) eller Azure Active Directory Domain Services-autentisering (Azure AD DS) misslyckas åtkomsten till filresursen med felet "Åtkomst nekas" om behörigheter på resursnivå är felaktiga. 

### <a name="solution-for-cause-3"></a>Lösning för orsak 3

Kontrollera att behörigheterna har konfigurerats korrekt:

- **Active Directory (AD) finns** [i Tilldela behörigheter på resursnivå till en identitet](./storage-files-identity-ad-ds-assign-permissions.md).

    Tilldelningar av behörigheter på resursnivå stöds för grupper och användare som har synkroniserats från Active Directory (AD) till Azure Active Directory (Azure AD) med hjälp av Azure AD Connect.  Bekräfta att grupper och användare som tilldelas behörigheter på resursnivå inte stöds av "endast moln"-grupper.
- **Azure Active Directory Domain Services (Azure AD DS) finns** i [Tilldela åtkomstbehörigheter till en identitet](./storage-files-identity-auth-active-directory-domain-service-enable.md?tabs=azure-portal#assign-access-permissions-to-an-identity).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Fel 53, Fel 67 eller Fel 87 när du monterar eller demonterar en Azure-filresurs

När du försöker montera en filresurs från en lokal plats eller från ett annat datacenter kan följande fel visas:

- Systemfel 53 har uppstått. Det gick inte att hitta nätverkssökvägen.
- Systemfel 67 har uppstått. Nätverksnamnet kan inte hittas.
- Systemfel 87 har uppstått. Parametern är felaktig.

### <a name="cause-1-port-445-is-blocked"></a>Orsak 1: Port 445 blockeras

Systemfel 53 eller systemfel 67 kan inträffa om utgående kommunikation via port 445 till ett Azure Files datacenter blockeras. En översikt över Internetleverantörer som tillåter och inte tillåter åtkomst från port 445 finns på [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Om du vill kontrollera om brandväggen eller Internetleverantören blockerar port 445 använder du [verktyget AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) eller `Test-NetConnection` cmdleten . 

Om du vill `Test-NetConnection` använda cmdleten måste Azure PowerShell vara installerad. Mer information [finns i Installera Azure PowerShell modulen.](/powershell/azure/install-Az-ps) Kom ihåg att ersätta `<your-storage-account-name>` och `<your-resource-group-name>` med gällande namn för ditt lagringskonto.

   
```azurepowershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
# $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
# or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
```
    
Om en anslutning upprättades bör du se följande utdata:
    
  
```azurepowershell
ComputerName     : <your-storage-account-name>
RemoteAddress    : <storage-account-ip-address>
RemotePort       : 445
InterfaceAlias   : <your-network-interface>
SourceAddress    : <your-ip-address>
TcpTestSucceeded : True
```
 

> [!Note]  
> Ovanstående kommando returnerar den aktuella IP-adressen för lagringskontot. Det är inte säkert att IP-adressen förblir densamma, och den kan ändras när som helst. Hårdkoda inte den här IP-adressen i några skript eller i en brandväggskonfiguration.

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

#### <a name="solution-1---use-azure-file-sync"></a>Lösning 1 – Använd Azure File Sync
Azure File Sync kan omvandla din lokala Windows Server till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt, inklusive SMB, NFS och FTPS. Azure File Sync fungerar via port 443 och kan därför användas som en lösning för att få åtkomst till Azure Files från klienter som har port 445 blockerad. [Lär dig att konfigurera Azure File Sync](../file-sync/file-sync-extend-servers.md).

#### <a name="solution-2---use-vpn"></a>Lösning 2 – Använd VPN
Genom att konfigurera ett VPN till ditt specifika lagringskonto går trafiken genom en säker tunnel i stället för via Internet. Följ [instruktionerna för att konfigurera VPN](storage-files-configure-p2s-vpn-windows.md) för att få åtkomst till Azure Files från Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Lösning 3 – Avblockera port 445 med hjälp av Internetleverantören/IT-administratören
Arbeta med IT-avdelningen eller Internetleverantören för att öppna port 445 för utgående trafik till [Azure IP-intervall.](https://www.microsoft.com/download/details.aspx?id=41653)

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Lösning 4 – Använd REST API-baserade verktyg som Storage Explorer/Powershell
Azure Files har även stöd för REST utöver SMB. REST-åtkomst fungerar via port 443 (standard-TCP). Det finns olika verktyg som skrivs med REST API som möjliggör mer omfattande användargränssnittsfunktioner. [Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) är en av dem. [Ladda ned och installera Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) och anslut till din filresurs som backas av Azure Files. Du kan också använda [PowerShell som](./storage-how-to-use-files-powershell.md) även REST API.

### <a name="cause-2-ntlmv1-is-enabled"></a>Orsak 2: NTLMv1 är aktiverat

Systemfel 53 eller systemfel 87 kan inträffa om NTLMv1-kommunikation är aktiverad på klienten. Azure Files stöder endast NTLMv2-autentisering. När NTLMv1 är aktiverad är klienten mindre säker. Därför blockeras kommunikation för Azure Files. 

Kontrollera att följande registerundernyckel inte har angetts till ett värde som är mindre än 3 för att avgöra om detta är orsaken till felet:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Mer information finns i ämnet [LmCompatibilityLevel](/previous-versions/windows/it-pro/windows-2000-server/cc960646(v=technet.10)) på TechNet.

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Återställ värdet **LmCompatibilityLevel** till standardvärdet 3 i följande registerundernyckel:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>Fel 1816 – Det finns inte tillräckligt med kvot för att bearbeta det här kommandot

### <a name="cause"></a>Orsak

Fel 1816 inträffar när du når den övre gränsen för samtidiga öppna referenser som tillåts för en fil eller katalog på Azure-filresursen. Läs mer i informationen om [skalningsmål för Azure Files](./storage-files-scale-targets.md#azure-files-scale-targets).

### <a name="solution"></a>Lösning

Minska antalet samtidiga öppna referenser genom att stänga några referenser och försök sedan igen. Mer information finns i Microsoft Azure Storage [checklista för prestanda och skalbarhet.](../blobs/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

Om du vill visa öppna referenser för en filresurs, katalog eller fil använder du [PowerShell-cmdleten Get-AzStorageFileHandle.](/powershell/module/az.storage/get-azstoragefilehandle)  

Om du vill stänga öppna referenser för en filresurs, katalog eller fil använder du [PowerShell-cmdleten Close-AzStorageFileHandle.](/powershell/module/az.storage/close-azstoragefilehandle)

> [!Note]  
> Cmdletarna Get-AzStorageFileHandle och Close-AzStorageFileHandle ingår i Az PowerShell-modul version 2.4 eller senare. Information om hur du installerar den senaste Az PowerShell-modulen [finns i Installera Azure PowerShell modulen](/powershell/azure/install-az-ps).

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Felet "Ingen åtkomst" när du försöker komma åt eller ta bort en Azure-filresurs  
När du försöker komma åt eller ta bort en Azure-filresurs i portalen kan följande felmeddelande visas:

Ingen åtkomst  
Felkod: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Orsak 1: Virtuella nätverk eller brandväggsregler är aktiverade på lagringskontot

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

Kontrollera att det virtuella nätverket och brandväggsreglerna har konfigurerats korrekt på lagringskontot. Om du vill testa om det virtuella nätverket eller brandväggsreglerna som orsakar problemet kan du tillfälligt ändra inställningen på lagringskontot för att **tillåta åtkomst från alla nätverk**. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../common/storage-network-security.md).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Orsak 2: Ditt användarkonto har inte åtkomst till lagringskontot

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Bläddra till lagringskontot där Azure-filresursen finns, klicka på Åtkomstkontroll **(IAM)** och kontrollera att ditt användarkonto har åtkomst till lagringskontot. Mer information finns i [Skydda ditt lagringskonto med rollbaserad åtkomstkontroll i Azure (Azure RBAC).](../blobs/security-recommendations.md#data-protection)

<a id="open-handles"></a>
## <a name="unable-to-modify-moverename-or-delete-a-file-or-directory"></a>Det går inte att ändra, flytta/byta namn på eller ta bort en fil eller katalog
Ett av de viktigaste syftena med en filresurs är att flera användare och program kan interagera samtidigt med filer och kataloger i resursen. För att underlätta den här interaktionen tillhandahåller filresurser flera olika sätt att medla åtkomst till filer och kataloger.

När du öppnar en fil från en monterad Azure-filresurs via SMB begär programmet/operativsystemet en filreferens, som är en referens till filen. Bland annat anger ditt program ett fildelningsläge när det begär en fil referens, som anger nivån av exlusivitet för din åtkomst till filen framtvingas av Azure Files: 

- `None`: du har exklusiv åtkomst. 
- `Read`: andra kan läsa filen medan du har den öppen.
- `Write`: andra kan skriva till filen medan du har den öppen. 
- `ReadWrite`: en kombination av både `Read` lägena `Write` och .
- `Delete`: andra kan ta bort filen medan du har den öppen. 

Även om FileREST-protokollet som ett tillståndslöst protokoll inte har ett begrepp för filreferenser tillhandahåller det en liknande mekanism för att hantera åtkomst till filer och mappar som ditt skript, ditt program eller din tjänst kan använda: fillån. När en fil lånas behandlas den som likvärdig med en filhanterare med fildelningsläget `None` . 

Även om filreferenser och lån har ett viktigt syfte kan ibland filreferenser och lån bli överblivna. När detta inträffar kan detta orsaka problem med att ändra eller ta bort filer. Du kan se felmeddelanden som:

- Processen kan inte komma åt filen eftersom den används i en annan process.
- Åtgärden kan inte slutföras eftersom filen är öppen i ett annat program.
- Dokumentet är låst för redigering av en annan användare.
- Den angivna resursen har markerats för borttagning av en SMB-klient.

Lösningen på det här problemet beror på om detta orsakas av en överbliven fil referens eller lån. 

### <a name="cause-1"></a>Orsak 1
En filhanterare förhindrar att en fil/katalog ändras eller tas bort. Du kan använda [PowerShell-cmdleten Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) för att visa öppna referenser. 

Om alla SMB-klienter har stängt sina öppna referenser på en fil/katalog och problemet kvarstår kan du tvinga fram stängning av en filreferens.

### <a name="solution-1"></a>Lösning 1
Om du vill tvinga en filhanterare att stängas använder du [PowerShell-cmdleten Close-AzStorageFileHandle.](/powershell/module/az.storage/close-azstoragefilehandle) 

> [!Note]  
> Cmdletarna Get-AzStorageFileHandle och Close-AzStorageFileHandle ingår i Az PowerShell-modul version 2.4 eller senare. Information om hur du installerar den senaste Az PowerShell-modulen [finns i Installera Azure PowerShell modulen](/powershell/azure/install-az-ps).

### <a name="cause-2"></a>Orsak 2
Ett fillån förhindrar att en fil ändras eller tas bort. Du kan kontrollera om en fil har ett fillån med följande PowerShell och ersätta `<resource-group>` , , och med lämpliga värden för din `<storage-account>` `<file-share>` `<path-to-file>` miljö:

```PowerShell
# Set variables 
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$fileShareName = "<file-share>"
$fileForLease = "<path-to-file>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName

# Get reference to file
$file = Get-AzStorageFile `
        -Context $storageAccount.Context `
        -ShareName $fileShareName `
        -Path $fileForLease

$fileClient = $file.ShareFileClient

# Check if the file has a file lease
$fileClient.GetProperties().Value
```

Om en fil har ett lån, måste det returnerade objektet innehålla följande egenskaper:

```Output
LeaseDuration         : Infinite
LeaseState            : Leased
LeaseStatus           : Locked
```

### <a name="solution-2"></a>Lösning 2
Om du vill ta bort ett lån från en fil kan du frigöra lånet eller avbryta lånet. Om du vill frigöra lånet behöver du lånets LeaseId, vilket du anger när du skapar lånet. Du behöver inte något LeaseId för att avbryta lånet.

I följande exempel visas hur du bryter lånet för filen som anges i orsak 2 (det här exemplet fortsätter med PowerShell-variablerna från orsak 2):

```PowerShell
$leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($fileClient)
$leaseClient.Break() | Out-Null
```

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Långsam filkopiering till och från Azure Files i Windows

Du kan få långsamma prestanda när du försöker överföra filer till Azure File-tjänsten.

- Om du inte har ett specifikt minimikrav för I/O-storlek rekommenderar vi att du använder 1 MiB som I/O-storlek för optimal prestanda.
-   Om du vet den slutliga storleken på en fil som du utökar med skrivningar, och programvaran inte har kompatibilitetsproblem när det oskrivna slutet på filen innehåller nollor, anger du filstorleken i förväg i stället för att göra varje skrivning till en utökad skrivning.
-   Använd rätt kopieringsmetod:
    -   Använd [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för all överföring mellan två filresurser.
    -   Använd [Robocopy](./storage-how-to-create-file-share.md) mellan filresurser på en lokal dator.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Överväganden för Windows 8.1 eller Windows Server 2012 R2

För klienter som kör Windows 8.1 eller Windows Server 2012 R2 kontrollerar du att [snabbkorrigeringen KB3114025](https://support.microsoft.com/help/3114025) är installerad. Den här snabbkorrigeringen förbättrar prestandan för att skapa och stänga referenser.

Du kan köra följande skript för att kontrollera om snabbkorrigeringen har installerats:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Om snabbkorrigeringar har installerats visas följande utdata:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Windows Server 2012 R2-avbildningar i Azure Marketplace har snabbkorrigeringar KB3114025 installerade som standard, från och med december 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Ingen mapp med en enhetsbeteckning i "Min dator" eller "Den här datorn"

Om du mappar en Azure-filresurs som administratör med hjälp av net use verkar resursen saknas.

### <a name="cause"></a>Orsak

Som standard körs Windows Utforskaren inte som administratör. Om du kör net use från en administrativ kommandotolk mappar du nätverksenhet som administratör. Eftersom mappade enheter är användarcentrerade visar användarkontot som är inloggat inte enheterna om de är monterade under ett annat användarkonto.

### <a name="solution"></a>Lösning
Montera resursen från en kommandorad som inte är administratör. Du kan också följa det här [TechNet-ämnet för](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee844140(v=ws.10)) att konfigurera **registervärdet EnableLinkedConnections.**

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Kommandot Net use misslyckas om lagringskontot innehåller ett snedstreck

### <a name="cause"></a>Orsak

Kommandot net use tolkar ett snedstreck (/) som ett kommandoradsalternativ. Om namnet på ditt användarkonto börjar med ett snedstreck misslyckas enhetsmappningen.

### <a name="solution"></a>Lösning

Du kan använda något av följande steg för att lösa problemet:

- Kör följande PowerShell-kommando:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Från en batchfil kan du köra kommandot på det här sättet:

  `Echo new-smbMapping ... | powershell -command –`

- Placera dubbla citattecken runt nyckeln för att lösa det här problemet – såvida inte snedstrecket är det första tecknet. Om det är det använder du antingen det interaktiva läget och anger ditt lösenord separat eller återskapar dina nycklar för att hämta en nyckel som inte börjar med ett snedstreck.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Program eller tjänst kan inte komma åt en monterad Azure Files enhet

### <a name="cause"></a>Orsak

Enheter monteras per användare. Om ditt program eller din tjänst körs under ett annat användarkonto än det som monterade enheten visas inte enheten i programmet.

### <a name="solution"></a>Lösning

Använd någon av följande lösningar:

-   Montera enheten från samma användarkonto som innehåller programmet. Du kan använda ett verktyg som PsExec.
- Skicka lagringskontots namn och nyckel i parametrarna användarnamn och lösenord för kommandot net use.
- Använd kommandot cmdkey för att lägga till autentiseringsuppgifterna i Autentiseringshanteraren. Utför detta från en kommandorad under tjänstkontots kontext, antingen via en interaktiv inloggning eller med hjälp av `runas` .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mappa resursen direkt utan att använda en mappad enhetsbeteckning. Vissa program kanske inte återansluter till enhetsbeteckningen på rätt sätt, så det kan vara mer tillförlitligt att använda den fullständiga UNC-sökvägen. 

  `net use * \\storage-account-name.file.core.windows.net\share`

När du har följt de här anvisningarna kan du få följande felmeddelande när du kör net use för system-/nätverkstjänstkontot: "Systemfel 1312 har uppstått. Det finns ingen angiven inloggningssession. Det kan redan ha avslutats." Om detta inträffar kontrollerar du att användarnamnet som skickas till net innehåller domäninformation (till exempel: "[lagringskontots namn].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Felet "Du kopierar en fil till ett mål som inte stöder kryptering"

När en fil kopieras över nätverket dekrypteras filen på källdatorn, överförs i klartext och krypteras på nytt på målet. Följande felmeddelande kan dock visas när du försöker kopiera en krypterad fil: "Du kopierar filen till ett mål som inte stöder kryptering."

### <a name="cause"></a>Orsak
Det här problemet kan inträffa om du använder krypterande filsystem (EFS). BitLocker-krypterade filer kan kopieras till Azure Files. Men Azure Files inte NTFS EFS.

### <a name="workaround"></a>Lösning
Om du vill kopiera en fil över nätverket måste du först dekryptera den. Använd någon av följande metoder:

- Använd kommandot **copy /d.** Det gör att krypterade filer kan sparas som dekrypterade filer på målet.
- Ange följande registernyckel:
  - Sökväg = HKLM\Software\Policies\Microsoft\Windows\System
  - Värdetyp = DWORD
  - Namn = CopyFileAllowDecryptedRemoteDestination
  - Värde = 1

Tänk på att inställningen av registernyckeln påverkar alla kopieringsåtgärder som görs till nätverksresurser.

## <a name="slow-enumeration-of-files-and-folders"></a>Långsam uppräkning av filer och mappar

### <a name="cause"></a>Orsak

Det här problemet kan inträffa om det inte finns tillräckligt med cacheminne på klientdatorn för stora kataloger.

### <a name="solution"></a>Lösning

Lös problemet genom att justera registervärdet **DirectoryCacheEntrySizeMax** för att tillåta cachelagring av större kataloglistor på klientdatorn:

- Plats: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Värdemane: DirectoryCacheEntrySizeMax 
- Värdetyp:DWORD
 
 
Du kan till exempel ställa in den på 0x100000 och se om prestandan blir bättre.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-azure-ad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Felet AadDsTenantNotFound vid aktivering av Azure Active Directory Domain Service-autentisering (Azure AD DS) för Azure Files "Det gick inte att hitta aktiva klienter med klientorganisations-ID aad-tenant-id"

### <a name="cause"></a>Orsak

Felet AadDsTenantNotFound inträffar när du försöker aktivera [Azure Active Directory Domain Services-autentisering (Azure AD DS)](storage-files-identity-auth-active-directory-domain-service-enable.md) på Azure Files på ett lagringskonto där [Azure AD Domain Service(Azure AD DS)](../../active-directory-domain-services/overview.md) inte har skapats i Azure AD-klientorganisationen för den associerade prenumerationen.  

### <a name="solution"></a>Lösning

Aktivera Azure AD DS azure AD-klientorganisationen för prenumerationen som ditt lagringskonto har distribuerats till. Du behöver administratörsbehörighet för Azure AD-klientorganisationen för att skapa en hanterad domän. Om du inte är administratör för Azure AD-klienten kontaktar du administratören och följer den stegvisa vägledningen för att skapa och konfigurera en [Azure Active Directory Domain Services hanterad domän.](../../active-directory-domain-services/tutorial-create-instance.md)

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Det går inte att montera Azure Files med AD-autentiseringsuppgifter 

### <a name="self-diagnostics-steps"></a>Självdiagnostiksteg
Kontrollera först att du har följt alla fyra stegen för att aktivera [Azure Files AD-autentisering](./storage-files-identity-auth-active-directory-enable.md).

Prova sedan att [montera Azure-filresursen med lagringskontonyckeln](./storage-how-to-use-files-windows.md). Om du inte kunde montera laddar du [ nedAzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) för att hjälpa dig att verifiera klienten som kör miljön, identifiera den inkompatibla klientkonfigurationen som skulle orsaka åtkomstfel för Azure Files, ge vägledning om självkorrigering och samla in diagnostikspårningar.

För det tredje kan du köra Debug-AzStorageAccountAuth cmdlet för att utföra en uppsättning grundläggande kontroller i AD-konfigurationen med den inloggade AD-användaren. Den här cmdleten stöds i [versionen AzFilesHybrid v0.1.2+](https://github.com/Azure-Samples/azure-files-samples/releases). Du måste köra denna cmdlet med en AD-användare som har ägarbehörighet på mållagringskontot.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
Cmdleten utför dessa kontroller nedan i följd och ger vägledning för fel:
1. CheckADObjectPasswordIsCorrect: Kontrollera att lösenordet som konfigurerats på den AD-identitet som representerar lagringskontot matchar lagringskontots kerb1- eller kerb2-nyckel. Om lösenordet är felaktigt kan du köra [Update-AzStorageAccountADObjectPassword](./storage-files-identity-ad-ds-update-password.md) för att återställa lösenordet. 
2. CheckADObject: Bekräfta att det finns ett objekt i Active Directory som representerar lagringskontot och har rätt SPN (tjänstens huvudnamn). Om SPN inte är korrekt konfigurerat kör du den Set-AD-cmdlet som returnerades i felsöknings-cmdleten för att konfigurera SPN.
3. CheckDomainJoined: Verifiera att klientdatorn är domänansluten till AD. Om datorn inte är domän-ansluten till AD läser du den här artikeln [för](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain) anvisningar om domänkoppling.
4. CheckPort445Anslutning: Kontrollera att port 445 är öppen för SMB-anslutning. Om den nödvändiga porten inte är öppen kan du gå till felsökningsverktyget [ ochAzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) anslutningsproblem med Azure Files.
5. CheckSidHasAadUser: Kontrollera att den inloggade AD-användaren är synkroniserad med Azure AD. Om du vill kontrollera om en specifik AD-användare har synkroniserats med Azure AD kan du ange -UserName och -Domain i indataparametrarna. 
6. CheckGetKerberosTicket: Försök att hämta en Kerberos-biljett för att ansluta till lagringskontot. Om det inte finns någon giltig Kerberos-token kör du cmdleten klist get cifs/storage-account-name.file.core.windows.net och undersöker felkoden för att rotorsaken till biljetthämtningsfelet.
7. CheckStorageAccountDomainJoined: Kontrollera om AD-autentiseringen har aktiverats och kontots AD-egenskaper fylls i. Om inte, se instruktionen här [för att](./storage-files-identity-ad-ds-enable.md) aktivera AD DS-autentisering på Azure Files. 
8. CheckUserRbacAssignment: Kontrollera om AD-användaren har rätt RBAC-rolltilldelning för att ge åtkomstbehörighet på Azure Files. Om inte, se instruktionen här [för](./storage-files-identity-ad-ds-assign-permissions.md) att konfigurera behörighet på resursnivå. (Stöds på AzFilesHybrid v0.2.3+-versionen)
9. CheckUserFileAccess: Kontrollera om AD-användaren har rätt katalog-/filbehörighet (Windows-ACL:er) för att komma åt Azure Files. Om inte, se instruktionen här [för](./storage-files-identity-ad-ds-configure-permissions.md) att konfigurera behörighet på katalog-/filnivå. (Stöds på AzFilesHybrid v0.2.3+ version)

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Det går inte att konfigurera behörigheter på katalog-/filnivå (Windows-ACL:er) med Windows Utforskaren

### <a name="symptom"></a>Symptom

Du kan uppleva något av de symptom som beskrivs nedan när du försöker konfigurera Windows-ACL:er Utforskaren på en monterad filresurs:
- När du klickar på Redigeringsbehörighet under fliken Säkerhet läses inte behörighetsguiden in. 
- När du försöker välja en ny användare eller grupp visas inte rätt AD DS-domän på domänplatsen. 

### <a name="solution"></a>Lösning

Vi rekommenderar att du använder [icacls-verktyget](/windows-server/administration/windows-commands/icacls) för att konfigurera behörigheter på katalog-/filnivå som en lösning. 

## <a name="errors-when-running-join-azstorageaccountforauth-cmdlet"></a>Fel vid körning av Join-AzStorageAccountForAuth cmdlet

### <a name="error-the-directory-service-was-unable-to-allocate-a-relative-identifier"></a>Fel: "Katalogtjänsten kunde inte allokera en relativ identifierare"

Det här felet kan inträffa om en domänkontrollant som innehåller FSMO-rollen för RID-hanterare inte är tillgänglig eller har tagits bort från domänen och återställts från säkerhetskopian.  Kontrollera att alla domänkontrollanter körs och är tillgängliga.

### <a name="error-cannot-bind-positional-parameters-because-no-names-were-given"></a>Fel: ”Det går inte att binda positionsparametrar eftersom inget namn angavs”

Det här felet utlöses troligen av ett syntaxfel i kommandot Join-AzStorageAccountforAuth.  Kontrollera om det finns felstavningar eller syntaxfel i kommandot och kontrollera att den senaste versionen av AzFilesHybrid-modulen ( https://github.com/Azure-Samples/azure-files-samples/releases) är installerad.  

## <a name="azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption"></a>Azure Files lokal AD DS-autentiseringsstöd för AES 256 Kerberos-kryptering

Vi införde stöd för AES 256 Kerberos-kryptering för Azure Files en on-prem AD DS-autentisering [med AzFilesHybrid-modulen v0.2.2.](https://github.com/Azure-Samples/azure-files-samples/releases) Om du har aktiverat AD DS-autentisering med en modulversion som är lägre än v0.2.2 måste du ladda ned den senaste AzFilesHybrid-modulen (v0.2.2+) och köra PowerShell nedan. Om du inte har aktiverat AD DS-autentisering på ditt lagringskonto ännu kan du följa den här [vägledningen](./storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module) för att aktivera. 

```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName
```


## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.
