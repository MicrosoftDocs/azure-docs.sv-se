---
title: Så här hanterar du Azure File Sync-nivåindelade filer | Microsoft Docs
description: Tips och PowerShell-kommandon som hjälper dig att hantera nivåindelade filer
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd740c773998450ef6e8bb95c4df3a1abadaceed
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797127"
---
# <a name="how-to-manage-tiered-files"></a>Hantera nivåindelade filer

Den här artikeln innehåller vägledning för användare som har frågor om att hantera nivåindelade filer. Konceptuella frågor om molnnivåindelad lagring finns i [vanliga Azure Files vanliga frågor och svar.](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)

## <a name="how-to-check-if-your-files-are-being-tiered"></a>Så här kontrollerar du om dina filer är nivåindelade

Huruvida filer behöver nivåindelade per uppsättningsprinciper utvärderas en gång i timmen. Du kan stöta på två situationer när en ny serverslutpunkt skapas:

När du först lägger till en ny serverslutpunkt finns ofta filer på den serverplatsen. De måste laddas upp innan molnnivåindelad nivå kan börja. Principen för ledigt utrymme på volymen börjar inte sitt arbete förrän den inledande uppladdningen av alla filer har slutförts. Den valfria datumprincipen börjar dock att fungera för enskilda filer, så snart en fil har laddats upp. Entimmeintervallet gäller även här. 

När du lägger till en ny serverslutpunkt är det möjligt att du har anslutit en tom serverplats till en Azure-filresurs med dina data i den. Om du väljer att ladda ned namnområdet och återkalla innehåll under den första nedladdningen till servern kommer filerna att återkallas efter att namnområdet har laddats ned, baserat på den senast ändrade tidsstämpeln tills principen för ledigt utrymme på volymen och de valfria datumprincipgränserna har nåtts.

Det finns flera sätt att kontrollera om en fil har nivåindelats till din Azure-filresurs:
    
   *  **Kontrollera filattributen i filen.**
     Högerklicka på en fil, gå till **Information** och rulla sedan ned till **egenskapen** Attribut. En nivåindelad fil har följande attributuppsättning:     
        
        | Attributbokstav | Attribut | Definition |
        |:----------------:|-----------|------------|
        | A | Arkiv | Anger att filen ska säkerhetskopieras av programvara för säkerhetskopiering. Det här attributet anges alltid, oavsett om filen är nivåindelad eller helt lagrad på disk. |
        | P | Gles fil | Anger att filen är en gles fil. En gles fil är en specialiserad typ av fil som NTFS erbjuder för effektiv användning när filen på diskströmmen huvudsakligen är tom. Azure File Sync använder glesa filer eftersom en fil antingen är helt nivåindelad eller delvis återkallad. I en fullständigt nivåindelad fil lagras filströmmen i molnet. I en delvis återkallad fil finns den delen av filen redan på disken. Detta kan inträffa när filer delvis läses av program som multimediaspelare eller zip-verktyg. Om en fil återkallas helt till disk Azure File Sync den från en gles fil till en vanlig fil. Det här attributet anges endast på Windows Server 2016 och äldre.|
        | M | Återkalla vid dataåtkomst | Anger att filens data inte finns helt på den lokala lagringen. Om du läser filen hämtas minst en del av filinnehållet från en Azure-filresurs som serverslutpunkten är ansluten till. Det här attributet anges endast på Windows Server 2019. |
        | L | Referenspunkt | Anger att filen har en reparspunkt. Enparspunkt är en särskild pekare för användning av ett filsystemfilter. Azure File Sync använder sig avparse-punkter för att definiera Azure File Sync för filsystemfiltret (StorageSync.sys) den molnplats där filen lagras. Detta stöder sömlös åtkomst. Användarna behöver inte veta att Azure File Sync används eller hur de får åtkomst till filen i azure-filresursen. När en fil har återkallats Azure File Sync tar bortparspunkten från filen. |
        | O | Offline | Anger att en del eller allt innehåll i filen inte lagras på disken. När en fil har återkallats tar Azure File Sync bort det här attributet. |

        ![Dialogrutan Egenskaper för en fil med fliken Information markerad](../files/media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > Du kan se attributen för alla filer i en mapp genom att lägga till **fältet** Attribut i tabelldisplayen för Utforskaren. Det gör du genom att högerklicka på en befintlig kolumn (till exempel Storlek **),** välja **Mer** och sedan välja **Attribut** i listrutan.
        
        > [!NOTE]
        > Alla dessa attribut visas även för delvis återkallade filer.
        
   * **Använd `fsutil` för att söka efterparspunkter i en fil.**
       Enligt beskrivningen i föregående alternativ har en nivåindelad fil alltid en uppsättningparspunkt. Med en reparspunkt kan Azure File Sync filterdrivrutinen för filsystemet (StorageSync.sys) hämta innehåll från Azure-filresurser som inte lagras lokalt på servern. 

       Kontrollera om en fil har en reparspunkt genom att köra verktyget i en upphöjd kommandotolk eller `fsutil` PowerShell-fönster:

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       Om filen har en reparse-punkt kan du förvänta dig att **seParsa taggvärde: 0x8000001e**. Det här hexadecimala värdet är detparse-punktvärde som ägs av Azure File Sync. Utdata innehåller även de data som representerar sökvägen till filen på Azure-filresursen.
        
        > [!WARNING]  
        > `fsutil reparsepoint`Verktygskommandot kan också ta bort enparspunkt. Kör inte det här kommandot om inte Azure File Sync tekniska teamet ber dig att göra det. Om du kör det här kommandot kan det leda till dataförlust. 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>Så här undantar du program från spårning av senaste åtkomsttid för molnnivå

Med Azure File Sync agentversion 11.1 kan du nu undanta program från spårning av senaste åtkomst. När ett program kommer åt en fil uppdateras den senaste åtkomsttiden för filen i databasen för molnnivåindelning. Program som söker igenom filsystemet, t.ex. antivirus, gör att alla filer har samma senaste åtkomsttid, vilket påverkar när filerna är nivåindelade.

Om du vill undanta program från spårning av senaste åtkomsttid lägger du till processnamnet i registerinställningen HeatTrackingProcessNameExclusionList som finns under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

Exempel: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

> [!NOTE]
> Processer för datadeduplicering och Resource Manager (FSRM) undantas som standard. Ändringar i processens undantagslista respekteras av systemet var femte minut.

## <a name="how-to-access-the-heat-store"></a>Så här kommer du åt värmelagret

Molnnivåindelning använder den senaste åtkomsttiden och åtkomstfrekvensen för en fil för att avgöra vilka filer som ska nivåindelade. Filterdrivrutinen för molnnivåinloggning (storagesync.sys) spårar senaste åtkomsttid och loggar informationen i molnnivålagret för värmelagring. Du kan hämta värmelagret och spara det i en CSV-fil med hjälp av en server-lokal PowerShell-cmdlet.

Det finns ett enda värmelager för alla filer på samma volym. Värmelagret kan bli mycket stort. Om du bara behöver hämta det "coolaste" antalet objekt använder du -Limit och ett tal och överväger även filtrering efter en undersökväg jämfört med volymroten.

- Importera PowerShell-modulen:   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- LEDIGT UTRYMME FÖR VOLYM: För att få den ordning i vilken filerna nivåindelades med hjälp av principen för ledigt utrymme på volymen:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- DATUMPRINCIP: Så här hämtar du den ordning i vilken filerna nivåindelades med hjälp av datumprincipen:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- Hitta information om värmelagret för en viss fil:   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- Visa alla filer i fallande ordning efter senaste åtkomsttid:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- Se i vilken ordning nivåindelade filer kommer att återkallas av bakgrundsnedkallning eller återkallelse på begäran via PowerShell:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>Så här tvingar du en fil eller katalog att nivåindelad

> [!NOTE]
> När du väljer en katalog som ska nivåindelad nivåindelades endast de filer som för närvarande finns i katalogen. Filer som skapas efter den tiden nivåindelade inte automatiskt.

När funktionen för molnnivåindelad lagring är aktiverad nivåindelar molnnivåindelad automatiskt filer baserat på senaste åtkomst och ändrar tider för att uppnå den procentandel ledigt volymutrymme som anges på molnslutpunkten. Men ibland kanske du vill tvinga en fil att nivåindelad manuellt. Detta kan vara användbart om du sparar en stor fil som du inte planerar att använda igen under en längre tid och du vill att det lediga utrymmet på volymen nu ska användas för andra filer och mappar. Du kan tvinga fram nivåindelad lagring med hjälp av följande PowerShell-kommandon:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>Så här återkallar du en nivåindelad fil till disk

Det enklaste sättet att återkalla en fil till disk är att öppna filen. Det Azure File Sync filsystemfiltret (StorageSync.sys) laddar sömlöst ned filen från din Azure-filresurs utan att du behöver göra något själv. För filtyper som delvis kan läsas eller strömmas, till exempel multimedia- eller ZIP-filer, säkerställer det inte att hela filen laddas ned om du öppnar den.

För att säkerställa att en fil är helt nedladdad till en lokal disk måste du använda PowerShell för att tvinga en fil att återkallas helt. Det här alternativet kan också vara användbart om du vill återkalla flera filer samtidigt, till exempel alla filer i en mapp. Öppna en PowerShell-session till servernoden där Azure File Sync har installerats och kör sedan följande PowerShell-kommandon:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Valfria parametrar:
* `-Order CloudTieringPolicy` kommer att återkalla de senast ändrade eller använda filerna först och tillåts av den aktuella nivåprincipen. 
    * Om principen för ledigt utrymme på volymen har konfigurerats kommer filerna att återkallas tills principinställningen för ledigt utrymme för volymen har uppnåtts. Om den kostnadsfria volymprincipinställningen till exempel är 20 % stoppas återkallningen när det lediga utrymmet för volymen når 20 %.  
    * Om volym ledigt utrymme och datumprincip har konfigurerats, kommer filer att återkallas tills den volym lediga utrymmet eller datum principinställningen har uppnåtts. Om den volymfria principinställningen till exempel är 20 % och datumprincipen är 7 dagar stoppas återkallandet när det lediga utrymmet når 20 % eller om alla filer som nås eller ändras inom 7 dagar är lokala.
* `-ThreadCount` avgör hur många filer som kan återkallas parallellt.
* `-PerFileRetryCount`avgör hur ofta ett återkallelseförsök görs för en fil som för närvarande är blockerad.
* `-PerFileRetryDelaySeconds`bestämmer tiden i sekunder mellan återförsök att återkalla försök och bör alltid användas i kombination med föregående parameter.

Exempel:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- Om den lokala volymen som är värd för servern inte har tillräckligt med ledigt utrymme för att återkalla alla nivåindelade data misslyckas `Invoke-StorageSyncFileRecall` cmdleten.  

> [!Note]
> Nätverksbandbredden måste vara minst 1 Mbit/s för att komma ihåg filer som har nivåindelats. Om nätverksbandbredden är mindre än 1 Mbit/s kan det hända att filer inte kan återkallas med ett timeout-fel.

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar om Azure Files](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)