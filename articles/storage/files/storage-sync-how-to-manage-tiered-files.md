---
title: Så här hanterar du Azure File Sync skiktade filer | Microsoft Docs
description: Tips och PowerShell-cmdletarna som hjälper dig att hantera filer på nivå
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dc61792da669cd5d2c928eec0fd412d86725fc8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204427"
---
# <a name="how-to-manage-tiered-files"></a>Hantera filer på nivå

Den här artikeln innehåller rikt linjer för användare som har frågor om att hantera filer på nivå. För konceptuella frågor om moln nivåer kan du läsa [Azure Files vanliga frågor och svar](storage-files-faq.md).

## <a name="how-to-check-if-your-files-are-being-tiered"></a>Så här kontrollerar du om filerna har flera nivåer

Huruvida filer måste vara nivåbaserade per uppsättnings principer utvärderas en gång i timmen. Du kan komma över två situationer när en ny server slut punkt skapas:

När du först lägger till en ny server slut punkt finns ofta filer på den server platsen. De måste laddas upp innan moln nivån kan börja. Principen för ledigt utrymme på volymen börjar inte att fungera förrän den inledande överföringen av alla filer har avslut ATS. Den valfria datum principen kommer dock att börja arbeta med en enskild fil, så snart en fil har överförts. Intervallet med en timme gäller även här. 

När du lägger till en ny server slut punkt är det möjligt att du har anslutit en tom Server plats till en Azure-filresurs med dina data. Om du väljer att ladda ned namn området och återkalla innehållet under den inledande nedladdningen till servern, kommer filerna att återkallas om de baseras på den senast ändrade tidsstämpeln till principen för ledigt utrymme på volymen och de valfria datum princip gränserna nås.

Det finns flera sätt att kontrol lera om en fil har flyttats till din Azure-fil resurs:
    
   *  **Kontrol lera filattributen på filen.**
     Högerklicka på en fil, gå till **information** och rulla ned till egenskapen **attributes** . En nivå fil har följande attribut inställda:     
        
        | Bokstav för attribut | Attribut | Definition |
        |:----------------:|-----------|------------|
        | A | Arkiv | Anger att filen ska säkerhets kopie ras av säkerhets kopierings program. Det här attributet är alltid angivet, oavsett om filen har en nivå eller lagras helt på disken. |
        | P | Sparse-fil | Anger att filen är en sparse-fil. En sparse-fil är en specialiserad typ av fil som NTFS erbjuder för effektiv användning när filen i disk strömmen huvudsakligen är tom. Azure File Sync använder sparse-filer eftersom en fil antingen är helt på nivå eller delvis återkallas. I en fil med fullständigt skikt lagras fil data strömmen i molnet. I en delvis återkallad fil är den delen av filen redan på disk. Detta kan inträffa när filer delvis läses av program som multimedie spelare eller zip-verktyg. Om en fil återställs fullständigt till disken, Azure File Sync konverterar den från en sparse-fil till en vanlig fil. Det här attributet anges bara för Windows Server 2016 och äldre.|
        | M | Återkalla data åtkomst | Anger att filens data inte är fullständigt tillgängliga på den lokala lagrings platsen. Om du läser filen kommer minst en del av fil innehållet att hämtas från en Azure-filresurs som server slut punkten är ansluten till. Det här attributet anges bara för Windows Server 2019. |
        | L | Referenspunkt | Anger att filen har en referens punkt. En referens punkt är en särskild pekare som används av ett fil system filter. Azure File Sync använder referens punkter för att definiera Azure File Sync fil system filtret (StorageSync.sys) moln platsen där filen lagras. Detta stöder sömlös åtkomst. Användarna behöver inte känna till att Azure File Sync används eller hur de får åtkomst till filen i Azure-filresursen. När en fil har återkallats fullständigt, Azure File Sync tar bort referens punkten från filen. |
        | O | Offline | Anger att en del av eller hela filens innehåll inte lagras på disken. När en fil har återkallats fullständigt, Azure File Sync tar bort det här attributet. |

        ![Dialog rutan Egenskaper för en fil med fliken information markerad](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > Du kan se attributen för alla filer i en mapp genom att lägga till fältet **attribut** i tabell visningen för Utforskaren. Om du vill göra detta högerklickar du på en befintlig kolumn (till exempel **storlek**), väljer **mer** och väljer sedan **attribut** i list rutan.
        
        > [!NOTE]
        > Alla dessa attribut visas för delvis återkallade filer.
        
   * **Används `fsutil` för att kontrol lera referens punkter för en fil.**
       Som det beskrivs i föregående alternativ har en nivå fil alltid en referens punkts uppsättning. Med en referens punkt kan Azure File Sync fil system filter driv rutin (StorageSync.sys) hämta innehåll från Azure-filresurser som inte lagras lokalt på servern. 

       Du kan kontrol lera om en fil har en referens punkt i en upphöjd kommando tolk eller PowerShell-fönster genom att köra `fsutil` verktyget:

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       Om filen har en referens punkt kan du se till att **reparse tag-värdet: 0x8000001E**. Det här hexadecimala värdet är det referens punkts värde som ägs av Azure File Sync. Utdata innehåller också de referens data som representerar sökvägen till filen på Azure-filresursen.
        
        > [!WARNING]  
        > `fsutil reparsepoint`Verktygs kommandot har också möjlighet att ta bort en referens punkt. Kör inte det här kommandot om inte Azure File Sync teknik teamet ber dig. Att köra det här kommandot kan leda till data förlust. 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>Så här undantar du program från moln nivåer senaste åtkomst tid spårning

Med Azure File Sync agent version 11,1 kan du nu utesluta program från senaste åtkomst spårning. När ett program har åtkomst till en fil uppdateras den senaste åtkomst tiden för filen i moln skikts databasen. Program som genomsöker fil systemet, t. ex. anti-virus, gör att alla filer får samma senaste åtkomst tid, vilket påverkar när filer skiktas.

Om du vill undanta program från senaste åtkomst tid för spårning lägger du till process namnet i register inställningen HeatTrackingProcessNameExclusionList som finns under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

Exempel: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

> [!NOTE]
> Processer för datadeduplicering och FSRM (hanteraren för fil server resurser) undantas som standard. Ändringar av process uteslutnings listan följs av systemet var 5: e minut.

## <a name="how-to-access-the-heat-store"></a>Så här kommer du åt värme lagret

Moln nivåer använder den senaste åtkomst tiden och åtkomst frekvensen för en fil för att avgöra vilka filer som ska nivåas. Filtret för filter driv rutinen för moln skikt (storagesync.sys) spårar senaste åtkomst tid och loggar informationen i värme lagret för moln lagring. Du kan hämta värme lagret och spara det i en CSV-fil med hjälp av en server lokal PowerShell-cmdlet.

Det finns ett enda värme lager för alla filer på samma volym. Värme lagret kan bli mycket stort. Om du bara behöver hämta det "häftigaste" antalet objekt, Använd-Limit och en siffra och fundera på att filtrera efter en under Sök väg jämfört med volym roten.

- Importera PowerShell-modulen:   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- LEDIGt utrymme på volym: för att få ordning i vilken filer kommer att skiktas med hjälp av principen för ledigt utrymme på volymen:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- DATUM princip: för att hämta i vilken ordning filerna ska bearbetas med hjälp av datum principen:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- Hitta information om värme arkivet för en viss fil:   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- Se alla filer i fallande ordning efter senaste åtkomst tid:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- Se i vilken ordning filer på nivå av filer kommer att återkallas av bakgrunds återkallande eller återkallande på begäran via PowerShell:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>Så här tvingar du en fil eller katalog att ha en nivå

> [!NOTE]
> När du väljer en katalog som ska sorteras på nivå, så är det bara filer som finns i katalogen. Filer som skapas efter den tiden sker inte automatiskt i nivå.

När funktionen för moln nivåer är aktive rad, nivårar automatiskt filer baserat på senaste åtkomst och ändrings tider för att uppnå volymens lediga utrymme i procent som anges i moln slut punkten. Ibland kanske du vill tvinga fram en fil manuellt till nivån. Detta kan vara användbart om du sparar en stor fil som du inte tänker använda igen under en längre tid, och du vill att det lediga utrymmet på volymen nu ska användas för andra filer och mappar. Du kan framtvinga en nivå genom att använda följande PowerShell-kommandon:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>Så här återkallar du en fil med flera nivåer till disk

Det enklaste sättet att återkalla en fil till disken är att öppna filen. Azure File Sync fil system filter (StorageSync.sys) laddar ned filen från Azure-filresursen utan något arbete på din sida. För filtyper som delvis kan läsas eller strömmas, t. ex. multimedie-eller zip-filer, behöver du bara öppna en fil för att se till att hela filen laddas ned.

För att säkerställa att en fil laddas ned helt till den lokala disken måste du använda PowerShell för att tvinga en fil att återkallas fullständigt. Det här alternativet kan också vara användbart om du vill återkalla flera filer samtidigt, t. ex. alla filer i en mapp. Öppna en PowerShell-session på den servernod där Azure File Sync är installerat och kör sedan följande PowerShell-kommandon:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Valfria parametrar:
* `-Order CloudTieringPolicy` kommer att återkalla de senast ändrade eller öppnade filerna först och tillåts av den aktuella nivå principen. 
    * Om principen för ledigt utrymme på volymen har kon figurer ATS, kommer filerna att återkallas tills det finns en princip inställning för ledigt utrymme på volymen. Om den kostnads fria princip inställningen till exempel är 20% stoppas återställningen när volymens lediga utrymme når 20%.  
    * Om mängden ledigt utrymme och en datum princip har kon figurer ATS kommer filerna att återkallas tills volymens lediga utrymme eller datum princip inställning nås. Om den kostnads fria princip inställningen till exempel är 20% och datum policyn är 7 dagar, stoppas återställningen när volymens lediga utrymme når 20% eller om alla filer som har öppnats eller ändrats inom 7 dagar är lokala.
* `-ThreadCount` anger hur många filer som kan återkallas parallellt.
* `-PerFileRetryCount`anger hur ofta ett återställnings försök ska göras för en fil som för närvarande är blockerad.
* `-PerFileRetryDelaySeconds`fastställer tiden i sekunder mellan försök att återkalla försök och bör alltid användas i kombination med föregående parameter.

Exempel:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- Om den lokala volym som är värd för servern inte har tillräckligt med ledigt utrymme för att återkalla alla data på nivån, `Invoke-StorageSyncFileRecall` Miss lyckas cmdleten.  

> [!Note]
> Nätverks bandbredden bör vara minst 1 Mbit/s för att återställa filer som har skiktts. Om nätverks bandbredden är mindre än 1 Mbit/s går det inte att återkalla filer med ett tids gräns fel.

## <a name="next-steps"></a>Nästa steg
* [Vanliga frågor och svar om Azure Files](storage-files-faq.md)
