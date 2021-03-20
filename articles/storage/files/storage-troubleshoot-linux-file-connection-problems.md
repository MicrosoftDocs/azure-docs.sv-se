---
title: Felsöka Azure Files problem i Linux | Microsoft Docs
description: Felsöka Azure Files problem i Linux. Se vanliga problem som rör Azure Files när du ansluter från Linux-klienter och se möjliga lösningar.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: e680ba10c507ef83591b56652ee8e95c4d665dda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96492071"
---
# <a name="troubleshoot-azure-files-problems-in-linux-smb"></a>Felsöka Azure Files problem i Linux (SMB)

Den här artikeln innehåller vanliga problem som är relaterade till Azure Files när du ansluter från Linux-klienter. Den innehåller också möjliga orsaker och lösningar på problemen. 

Förutom fel söknings stegen i den här artikeln kan du använda [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Linux) för att säkerställa att Linux-klienten har rätt krav. AzFileDiagnostics automatiserar identifieringen av de flesta problem som nämns i den här artikeln. Det hjälper dig att konfigurera din miljö för att få bästa möjliga prestanda. Du kan också hitta den här informationen i [fel sökaren Azure Files-resurser](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). Fel sökaren innehåller steg som hjälper dig med problem med att ansluta, mappa och montera Azure Files-resurser.

> [!IMPORTANT]
> Innehållet i den här artikeln gäller endast SMB-resurser. Mer information om NFS-resurser finns i [Felsöka Azure NFS-filresurser](storage-troubleshooting-files-nfs.md).

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Det går inte att ansluta till eller montera en Azure-filresurs

### <a name="cause"></a>Orsak

Vanliga orsaker till det här problemet är:

- Du använder en Linux-distributions klient som inte är kompatibel. Vi rekommenderar att du använder följande Linux-distributioner för att ansluta till en Azure-fil resurs:

|   | SMB 2.1 <br>(Monteras på virtuella datorer inom samma Azure-region) | SMB 3.0 <br>(Monteras från lokala platser och över flera regioner) |
| --- | :---: | :---: |
| **Ubuntu Server** | 14.04 + | 16.04 + |
| **RHEL** | 7 + | 7.5 + |
| **CentOS** | 7 + |  7.5 + |
| **Debian** | 8 + |   |
| **openSUSE** | 13.2 + | 42.3 + |
| **SUSE Linux Enterprise Server** | 12 | 12 SP3 + |

- CIFS-verktyg (CIFS-utils) är inte installerade på klienten.
- Lägsta SMB/CIFS-version, 2,1, är inte installerad på klienten.
- SMB 3,0-kryptering stöds inte på klienten. Tabellen ovan innehåller en lista över Linux-distributioner som stöder montering från lokala platser och över flera regioner med hjälp av kryptering. Andra distributioner kräver kernel 4.11 och senare versioner.
- Du försöker ansluta till ett lagrings konto via TCP-port 445, vilket inte stöds.
- Du försöker ansluta till en Azure-filresurs från en virtuell Azure-dator och den virtuella datorn finns inte i samma region som lagrings kontot.
- Om inställningen [Kräv säker överföring]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) är aktive rad på lagrings kontot tillåter Azure Files bara anslutningar som använder SMB 3,0 med kryptering.

### <a name="solution"></a>Lösning

Lös problemet genom att använda [fel söknings verktyget för Azure Files monterings fel på Linux](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Linux). Det här verktyget:

* Hjälper dig att verifiera klienten som kör miljön.
* Identifierar den inkompatibla klient konfigurationen som skulle orsaka åtkomst fel för Azure Files.
* Ger vägledning om själv korrigering.
* Samlar in diagnostiska spårningar.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Monterings fel (13): behörighet nekad" när du monterar en Azure-filresurs

### <a name="cause-1-unencrypted-communication-channel"></a>Orsak 1: okrypterad kommunikations kanal

Av säkerhetsskäl blockeras anslutningar till Azure-filresurser om kommunikationskanalen inte är krypterad och om anslutningsförsöket inte görs från samma datacenter där Azure-filresurserna finns. Okrypterade anslutningar inom samma datacenter kan också blockeras om inställningen [Säker överföring krävs ](../common/storage-require-secure-transfer.md) är aktiverad på lagringskontot. En krypterad kommunikationskanal tillhandahålls endast om användarens klientoperativsystem stöder SMB-kryptering.

Mer information finns i [Krav för att montera en Azure-filresurs med Linux och paketet cifs-utils](storage-how-to-use-files-linux.md#prerequisites). 

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

1. Anslut från en klient som har stöd för SMB-kryptering eller Anslut från en virtuell dator i samma data Center som det Azure Storage-konto som används för Azure-filresursen.
2. Kontrol lera att inställningen för [säker överföring](../common/storage-require-secure-transfer.md) är inaktive rad på lagrings kontot om klienten inte stöder SMB-kryptering.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Orsak 2: virtuella nätverk eller brand Väggs regler är aktiverade på lagrings kontot 

Om virtuellt nätverk (VNET) och brandväggsregler har konfigurerats på lagringskontot tillåts inte nätverkstrafik om inte åtkomst tillåts för klientens IP-adress eller virtuella nätverk.

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Kontrollera att det virtuella nätverket och brandväggsreglerna har konfigurerats korrekt på lagringskontot. Om du vill testa om det virtuella nätverket eller brandväggsreglerna som orsakar problemet kan du tillfälligt ändra inställningen på lagringskontot för att **tillåta åtkomst från alla nätverk**. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../common/storage-network-security.md).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>Disk kvoten "[behörighet nekad] överskreds" när du försöker öppna en fil

I Linux får du ett fel meddelande som liknar följande:

**\<filename> [behörighet nekad] Disk kvoten överskreds**

### <a name="cause"></a>Orsak

Du har nått den övre gränsen för samtidiga öppna referenser som tillåts för en fil eller katalog.

Det finns en kvot på 2 000 öppna referenser på en enskild fil eller katalog. När du har 2 000 öppna handtag visas ett fel meddelande om att kvoten har nåtts.

### <a name="solution"></a>Lösning

Minska antalet samtidiga öppna referenser genom att stänga några referenser och försök sedan igen.

Om du vill visa öppna referenser för en fil resurs, katalog eller fil använder du PowerShell-cmdleten [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) .  

Om du vill stänga öppna referenser för en fil resurs, katalog eller fil använder du PowerShell-cmdleten [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) .

> [!Note]  
> De Get-AzStorageFileHandle-och Close-AzStorageFileHandle-cmdletarna ingår i AZ PowerShell-modul version 2,4 eller senare. Information om hur du installerar den senaste AZ PowerShell-modulen finns i [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Långsam fil kopiering till och från Azure Files i Linux

- Om du inte har en speciell minimi krav på I/O-storlek rekommenderar vi att du använder 1 MiB som I/O-storlek för bästa prestanda.
- Använd rätt kopierings metod:
    - Använd [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för överföring mellan två fil resurser.
    - Genom att använda CP eller DD med parallell kan du förbättra kopierings hastigheten, antalet trådar beror på ditt användnings fall och arbets belastning. I följande exempel används sex: 
    - CP-exempel (CP använder standard block storleken för fil systemet som segment storlek): `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &` .
    - DD exempel (detta kommando anger explicit segment storlek till 1 MiB): `find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - Öppna käll kods verktyg från tredje part, till exempel:
        - [GNU parallellt](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) – sorterar filer och packar dem i partitioner.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) – använder Fpart och ett kopierings verktyg för att skapa flera instanser för att migrera data från src_dir till dst_url.
        - [Multi](https://github.com/pkolano/mutil) -multi-threaded CP och md5sum baserat på GNU coreutils.
- Att ange fil storleken i förväg, i stället för att göra varje skrivning till en utökad skrivning, bidrar till att förbättra kopierings hastigheten i scenarier där fil storleken är känd. Om du behöver undvika att utöka skrivningar kan du ange en mål fil storlek med `truncate - size <size><file>` kommando. Efter det `dd if=<source> of=<target> bs=1M conv=notrunc` kommer kommandot att kopiera en källfil utan att behöva uppdatera storleken på målfilen upprepade gånger. Du kan till exempel ange mål fils storleken för varje fil som du vill kopiera (anta att en resurs monteras under/mnt/share):
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - och kopierar sedan filer utan att utöka skrivningar parallellt: `$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Monterings fel (115): åtgärden pågår nu" när du monterar Azure Files med SMB 3,0

### <a name="cause"></a>Orsak

Vissa Linux-distributioner stöder inte ännu krypteringsfunktionerna i SMB 3.0. Användare kan få felmeddelandet ”115” om de försöker montera Azure Files med hjälp av SMB 3.0 på grund av att en funktion saknas. SMB 3.0 med fullständig kryptering stöds endast om du använder Ubuntu 16.04 eller senare.

### <a name="solution"></a>Lösning

Krypteringsfunktionen för SMB 3.0 för Linux introducerades i kernel 4.11. Den här funktionen möjliggör montering av en Azure-filresurs från en lokal plats eller från en annan Azure-region. Vissa Linux-distributioner kan ha omportade ändringar från 4,11-kärnan till äldre versioner av Linux-kärnan som de underhåller. För att hjälpa till att avgöra om din version av Linux stöder SMB 3,0 med kryptering, se med att [använda Azure Files med Linux](storage-how-to-use-files-linux.md). 

Om din Linux SMB-klient inte stöder kryptering monterar du Azure Files med SMB 2.1 från en virtuell Linux-dator på Azure som är i samma datacenter som filresursen. Kontrollera att inställningen [Säker överföring krävs]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) är aktiverad för lagringskontot. 

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Fel "ingen åtkomst" när du försöker komma åt eller ta bort en Azure-filresurs  
När du försöker komma åt eller ta bort en Azure-filresurs i portalen kan du få följande fel meddelande:

Ingen åtkomst  
Felkod: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Orsak 1: virtuella nätverk eller brand Väggs regler är aktiverade på lagrings kontot

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

Kontrollera att det virtuella nätverket och brandväggsreglerna har konfigurerats korrekt på lagringskontot. Om du vill testa om det virtuella nätverket eller brandväggsreglerna som orsakar problemet kan du tillfälligt ändra inställningen på lagringskontot för att **tillåta åtkomst från alla nätverk**. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../common/storage-network-security.md).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Orsak 2: ditt användar konto har inte åtkomst till lagrings kontot

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Bläddra till det lagrings konto där Azure-filresursen finns, klicka på **åtkomst kontroll (IAM)** och kontrol lera att ditt användar konto har åtkomst till lagrings kontot. Mer information finns i [så här skyddar du ditt lagrings konto med rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../blobs/security-recommendations.md#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Det går inte att ta bort en fil eller katalog i en Azure-filresurs

### <a name="cause"></a>Orsak
Det här problemet uppstår vanligt vis om filen eller katalogen har en öppen referens. 

### <a name="solution"></a>Lösning

Om SMB-klienterna har stängt alla öppna referenser och problemet fortsätter att inträffa, gör du följande:

- Använd PowerShell [-cmdleten Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) för att visa öppna referenser.

- Använd PowerShell [-cmdleten Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) för att stänga öppna referenser. 

> [!Note]  
> De Get-AzStorageFileHandle-och Close-AzStorageFileHandle-cmdletarna ingår i AZ PowerShell-modul version 2,4 eller senare. Information om hur du installerar den senaste AZ PowerShell-modulen finns i [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Dåliga prestanda för en Azure-filresurs monterad i en virtuell Linux-dator

### <a name="cause-1-caching"></a>Orsak 1: cachelagring

En möjlig orsak till långsamma prestanda är inaktive rad cachelagring. Cachelagring kan vara användbart om du använder en fil flera gånger, annars kan det vara en omkostnader. Kontrol lera att du använder cacheminnet innan du inaktiverar det.

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

Om du vill kontrol lera om cachelagring är inaktiverat söker du efter posten **cache =** .

**Cache = ingen** anger att cachelagring har inaktiverats. Montera om resursen med hjälp av standard kommandot Mount eller genom att uttryckligen lägga till alternativet **cache = Strict** i monterings kommandot för att säkerställa att standardcachelagring eller "strikt" cacheläge är aktiverat.

I vissa fall kan **serverino** Mount-alternativet orsaka att **ls** -kommandot kör stat mot varje katalog post. Det här beteendet resulterar i försämrade prestanda när du visar en stor katalog. Du kan kontrol lera monterings alternativen i din **/etc/fstab** -post:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Du kan också kontrol lera om rätt alternativ används genom att köra kommandot  **sudo Mount | grep CIFS** och kontrol lera dess utdata. Följande är exempel på utdata:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Om alternativet **cache = Strict** eller **serverino** inte finns kan du demontera och montera Azure Files igen genom att köra monterings kommandot från [dokumentationen](./storage-how-to-use-files-linux.md). Kontrol lera sedan att posten **/etc/fstab** har rätt alternativ.

### <a name="cause-2-throttling"></a>Orsak 2: begränsning

Det är möjligt att du drabbas av begränsning och att dina förfrågningar skickas till en kö. Du kan kontrol lera detta genom att dra nytta av [Azure Storage mått i Azure Monitor](../blobs/monitor-blob-storage.md).

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Se till att din app ligger inom [Azure Files skala mål](storage-files-scale-targets.md#azure-files-scale-targets).

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Tidsstämplar gick förlorade i kopiering av filer från Windows till Linux

På Linux/UNIX-plattformar fungerar inte **CP-p-** kommandot om olika användare äger fil 1 och fil 2.

### <a name="cause"></a>Orsak

Force-flaggan **f** i COPYFILE resulterar i att **CP-p-f** körs på UNIX. Det här kommandot kan inte heller bevara tidsstämpeln för filen som du inte äger.

### <a name="workaround"></a>Lösning

Använd lagringskontoanvändaren för att kopiera filerna:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: det går inte att komma åt &lt; sökvägen &gt; : indata/utdata-fel

När du försöker att lista filer i en Azure-filresurs med hjälp av ls-kommandot låser kommandot när filer visas. Du får följande fel meddelande:

**ls: det går inte att komma åt &lt; sökvägen &gt; : indata/utdata-fel**


### <a name="solution"></a>Lösning
Uppgradera Linux-kärnan till följande versioner som har en lösning på det här problemet:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Alla versioner som är större än eller lika med 4,13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Det går inte att skapa symboliska länkar-LN: det gick inte att skapa en symbolisk länk: åtgärden stöds inte

### <a name="cause"></a>Orsak
Som standard kan du montera Azure-filresurser i Linux med hjälp av CIFS stöder inte stöd för symboliska länkar (symlinks). Du ser ett fel som detta:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Lösning
Linux CIFS-klienten har inte stöd för att skapa symboliska länkar för Windows-typ via SMB 2 eller 3-protokollet. Linux-klienten stöder för närvarande en annan typ av symboliska länkar som kallas [Minshall + franska symlinks](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) för både Create och Follow-åtgärder. Kunder som behöver symboliska länkar kan använda monterings alternativet "mfsymlinks". Vi rekommenderar "mfsymlinks" eftersom det också är det format som Mac använder.

Om du vill använda symlinks lägger du till följande i slutet av ditt CIFS Mount-kommando:

```
,mfsymlinks
```

Kommandot ser alltså ut ungefär så här:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Du kan sedan skapa symlinks som det föreslås i [wikin](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Mount-fel (112): värden är inte tillgänglig på grund av en tids gräns för åter anslutning

Monteringsfel 112 uppstår på Linux-klienten när klienten har varit inaktiv under en längre tid. Tidsgränsen för anslutningen nås efter en längre tids inaktivitet och klienten kopplas från.  

### <a name="cause"></a>Orsak

Anslutningen kan vara inaktiv av följande skäl:

-   Nätverkskommunikationsfel som förhindrar att en TCP-anslutning till servern återupprättas när standardalternativet ”mjukmontering” används
-   Nya återanslutningskorrigeringar som inte finns i äldre kernlar

### <a name="solution"></a>Lösning

Det här återanslutningsproblemet i Linux-kernel har nu åtgärdats som en del av följande ändringar:

- [Korrigering av återanslutning för att inte skjuta upp smb3-sessionsåteranslutning långt efter socketåteranslutning](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Anrop av ekotjänst omedelbart efter socketåteranslutning](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: Korrigering av en möjlig minnesskada under återanslutning](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: åtgärda en möjlig dubbel låsning av mutex under åter anslutning (för kernel v 4.9 och senare)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

De här ändringarna kan emellertid inte portas ännu till alla Linux-distributioner. Om du använder en populär Linux-distribution kan du kontrol lera om du använder [Azure Files med Linux](storage-how-to-use-files-linux.md) för att se vilken version av distributionen som har nödvändiga kernel-ändringar.

### <a name="workaround"></a>Lösning

Du kan undvika det här problemet genom att ange en hårdmontering. Med en hårdmontering tvingas klienten vänta tills en anslutning har upprättats eller tills den avbryts explicit. Du kan använda den för att undvika fel på grund av att tidsgränsen nås för nätverket. Den här lösningen kan dock orsaka obestämd väntan. Var beredd på att stoppa anslutningar vid behov.

Om du inte kan uppgradera till de senaste kernel-versionerna kan du lösa problemet genom att behålla en fil i Azure-filresursen som du skriver till minst var 30:e sekund. Detta måste vara en skrivåtgärd, till exempel omskrivning av datumet (skapad eller ändrad) i filen. Annars kan du få cachelagrade resultat och åtgärden kanske inte utlöser återanslutningen.

## <a name="cifs-vfs-error--22-on-ioctl-to-get-interface-list-when-you-mount-an-azure-file-share-by-using-smb-30"></a>"CIFS VFS: error-22 on IOCTL to get Interface List" när du monterar en Azure-filresurs med SMB 3,0

### <a name="cause"></a>Orsak
Det här felet loggas eftersom Azure Files [för närvarande inte stöder SMB Multichannel](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

### <a name="solution"></a>Lösning
Detta fel kan ignoreras.


### <a name="unable-to-access-folders-or-files-which-name-has-a-space-or-a-dot-at-the-end"></a>Det går inte att komma åt mappar eller filer vars namn har ett blank steg eller en punkt i slutet

Det går inte att komma åt mappar eller filer från Azure-filresursen medan de är monterade på Linux, kommandon som du och program från tredje part kan Miss lyckas med fel meddelandet "det finns ingen sådan fil eller katalog" vid åtkomst till resursen, men du kan ladda upp filer till dessa mappar via portalen.

### <a name="cause"></a>Orsak

Mapparna eller filerna laddades upp från ett system som kodar tecknen i slutet av namnet till ett annat tecken, filer som laddats upp från en Macintosh-dator kan ha ett "0xF028"-eller "0xF029"-tecken i stället för 0x20 (blank steg) eller 0X2E (dot).

### <a name="solution"></a>Lösning

Använd alternativet mapchars på resursen när du monterar resursen på Linux: 

Istället för:

```bash
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

Använd

```bash
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino,mapchars
```


## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.