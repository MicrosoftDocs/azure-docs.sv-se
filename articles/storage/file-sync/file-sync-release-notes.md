---
title: Release notes for the Azure File Sync agent | Microsoft Docs
description: Läs viktig information för Azure File Sync agenten, som gör att du kan centralisera organisationens filresurser i Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 4/7/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 9c00b2d4d30ac417d58f2b69e4ba460789cf6583
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797079"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Viktig information om Azure File Sync-agenten
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Dina Windows Server-installationer omvandlas till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS). Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln innehåller viktig information om versioner av Azure File Sync-agenten som stöds.

## <a name="supported-versions"></a>Versioner som stöds
Följande Azure File Sync agentversioner stöds:

| Milstolpe | Agentversionsnummer | Utgivningsdatum | Status |
|----|----------------------|--------------|------------------|
| V12-version – [KB4568585](https://support.microsoft.com/topic/b9605f04-b4af-4ad8-86b0-2c490c535cfd)| 12.0.0.0 | Den 26 mars 2021 | Stöds – Flighting |
| V11.3-version – [KB4539953](https://support.microsoft.com/topic/f68974f6-bfdd-44f4-9659-bf2d8a696c26)| 11.3.0.0 | Den 7 april 2021 | Stöds |
| V11.2-version – [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | Den 2 februari 2021 | Stöds |
| V11.1-version – [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | Den 4 november 2020 | Stöds |
| V10.1-version – [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | Den 5 juni 2020 | Stöds – Agentversionen upphör att gälla den 7 juni 2021 |
| Samlad uppdatering maj 2020 [– KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | Den 19 maj 2020 | Stöds – Agentversionen upphör att gälla den 7 juni 2021 |
| V10-version – [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | Den 9 april 2020 | Stöds – Agentversionen upphör att gälla den 7 juni 2021 |

## <a name="unsupported-versions"></a>Versioner som inte stöds
Följande Azure File Sync agentversioner har upphört att gälla och stöds inte längre:

| Milstolpe | Agentversionsnummer | Utgivningsdatum | Status |
|----|----------------------|--------------|------------------|
| V9-version | 9.0.0.0 - 9.1.0.0 | Ej tillämpligt | Stöds inte – Agentversionen upphörde den 16 februari 2021 |
| V8-version | 8.0.0.0 | Ej tillämpligt | Stöds inte – Agentversionen upphörde den 12 januari 2021 |
| V7-version | 7.0.0.0 - 7.2.0.0 | Ej tillämpligt | Stöds inte – Agentversioner upphörde att gälla den 1 september 2020 |
| V6-version | 6.0.0.0 - 6.3.0.0 | Ej tillämpligt | Stöds inte – Agentversioner upphörde den 21 april 2020 |
| V5-version | 5.0.2.0 - 5.2.0.0 | Ej tillämpligt | Stöds inte – Agentversioner upphörde den 18 mars 2020 |
| V4-version | 4.0.1.0 - 4.3.0.0 | Ej tillämpligt | Stöds inte – Agentversioner upphörde den 6 november 2019 |
| V3-version | 3.1.0.0 - 3.4.0.0 | Ej tillämpligt | Stöds inte – Agentversioner upphörde den 19 augusti 2019 |
| Agenter före GA | 1.1.0.0 - 3.0.13.0 | Ej tillämpligt | Stöds inte – Agentversioner upphörde den 1 oktober 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-12000"></a>Agentversion 12.0.0.0
Följande versionsanteckningar gäller version 12.0.0.0 av Azure File Sync agenten (släpptes 26 mars 2021).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som har åtgärdats
- Ny portalupplevelse för att konfigurera nätverksåtkomstprincip och privata slutpunktsanslutningar
    - Du kan nu använda portalen för att inaktivera åtkomst till den offentliga slutpunkten för tjänsten för synkronisering av lagring och för att godkänna, avvisa och ta bort privata slutpunktsanslutningar. Om du vill konfigurera nätverksåtkomstprincipen och privata slutpunktsanslutningar öppnar du portalen för tjänsten för synkronisering av lagring, går till avsnittet Inställningar och klickar på Nätverk.
 
- Stöd för molnnivåindelad volymkluster som är större än 64KiB
    - Molnnivåindelad har nu stöd för volymklusterstorlekar upp till 2MiB på Server 2019. Mer information finns i Vad [är den minsta filstorleken för en fil till nivå?](https://docs.microsoft.com/azure/storage/files/storage-sync-choose-cloud-tiering-policies#minimum-file-size-for-a-file-to-tier).
 
- Mäta bandbredd och svarstid för Azure File Sync och lagringskonto
    - Den Test-StorageSyncNetworkConnectivity cmdleten kan nu användas för att mäta svarstid och bandbredd till Azure File Sync tjänsten och lagringskontot. Svarstiden för Azure File Sync och lagringskontot mäts som standard när du kör cmdleten .  Ladda upp och ladda ned bandbredd till lagringskontot mäts när du använder parametern "-MeasureBandwidth".
 
        Om du till exempel vill mäta bandbredd och svarstid för Azure File Sync-tjänsten och lagringskontot kör du följande PowerShell-kommandon:
 
        ```powershell
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
        Test-StorageSyncNetworkConnectivity -MeasureBandwidth 
        ``` 
 
- Förbättrade felmeddelanden i portalen när det inte går att skapa serverslutpunkten
    - Vi har hört din feedback och har förbättrat felmeddelandena och vägledningen när det inte går att skapa serverslutpunkten.
 
- Diverse prestanda- och tillförlitlighetsförbättringar
    - Förbättrad prestanda för ändringsidentifiering för att identifiera filer som har ändrats i Azure-filresursen.
    - Prestandaförbättringar för synkroniseringssessioner för avstämning. 
    - Synkroniseringsförbättringar för att minska ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED och ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED fel.
    - En bugg som orsakar skadade data har åtgärdats om molnnivåindelad lagring är aktiverat och nivåindelade filer kopieras med Robocopy med parametern /B.
    - En bugg som kan göra att filer inte nivåindelads på Server 2019 har åtgärdats om Datadeduplicering är aktiverat på volymen.
    - En bugg som kan göra att AFSDiag inte kan komprimera filer har åtgärdats om en fil är större än 2GiB.

### <a name="evaluation-tool"></a>Utvärderingsverktyg
Innan du Azure File Sync bör du utvärdera om det är kompatibelt med systemet med hjälp Azure File Sync utvärderingsverktyget. Det här verktyget är Azure PowerShell cmdlet som söker efter potentiella problem med filsystemet och datauppsättningen, till exempel tecken som inte stöds eller en os-version som inte stöds. Installations- och användningsanvisningar finns [i avsnittet Utvärderingsverktyg](file-sync-planning.md#evaluation-cmdlet) i planeringsguiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i Planera för [en Azure File Sync-distribution](file-sync-planning.md) och [Så här distribuerar du Azure File Sync](file-sync-deployment-guide.md).

- En omstart krävs för servrar som har en befintlig Azure File Sync agentinstallation.
- Agentinstallationspaketet måste installeras med förhöjd behörighet (administratör).
- Agenten stöds inte i distributionsalternativet Nano Server.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat ska den virtuella datorn konfigureras med minst 2 048 MiB minne. Mer information [finns i Rekommenderade systemresurser.](file-sync-planning.md#recommended-system-resources)
- Tjänsten Storage Sync Agent (FileSyncSvc) stöder inte serverslutpunkter som finns på en volym som har SVI-katalogen (System Volume Information) komprimerad. Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [Felsöka Azure File Sync](file-sync-troubleshoot.md).
- FSRM Resource Manager filskärmar (File Server Resource Manager) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av filskärmen.
- Att köra sysprep på en server som har Azure File Sync agenten installerad stöds inte och kan leda till oväntade resultat. Agenten Azure File Sync installeras när du har distribuerat serveravbildningen och slutfört sysprep-miniinstallationen.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [Felsökningsguide](file-sync-troubleshoot.md#handling-unsupported-characters) för en lista över tecken som inte stöds.
- Filer eller kataloger som slutar med en punkt.
- Sökvägar som är längre än 2 048 tecken.
- SACL-delen av en säkerhetsbeskrivning som används för granskning.
- Utökade attribut.
- Alternativa dataströmmar.
- Referenspunkter.
- Hårda länkar.
- Komprimering (om det angetts på en serverfil) bevaras inte när ändringar synkroniseras till filen från andra slutpunkter.
- Alla filer som krypterats med EFS (eller andra typer av kryptering från användarläget) som förhindrar att tjänsten läser data.

    > [!Note]  
    > Azure File Sync krypterar alltid data under överföring. Vilande data är alltid krypterade i Azure.
 
### <a name="server-endpoint"></a>Serverslutpunkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en växlingsfil för operativsystem eller program på en plats för serverslutpunkten.
- Servernamnet i portalen uppdateras inte om servern byter namn.

### <a name="cloud-endpoint"></a>Molnslutpunkt
- Azure File Sync kan göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett Azure File Sync-jobb för ändringsidentifiering. Ett ändringsidentifieringsjobb initieras för en molnslutpunkt en gång var 24:e timme. För att omedelbart synkronisera filer som har ändrats i Azure-filresursen kan [PowerShell-cmdleten Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) användas för att manuellt initiera identifieringen av ändringar i Azure-filresursen. Dessutom kommer ändringar som görs i en Azure-filresurs via REST-protokollet inte att uppdatera tiden för senaste ändring av SMB och visas inte som en ändring genom synkronisering.
- Tjänsten för synkronisering av lagring och/eller lagringskontot kan flyttas till en annan resursgrupp, prenumeration eller Azure AD-klientorganisation. När tjänsten för synkronisering av lagring eller lagringskontot har flyttats måste du ge programmet Microsoft.StorageSync åtkomst till lagringskontot (se Kontrollera att Azure File Sync har åtkomst till [lagringskontot](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > När du skapar molnslutpunkten måste tjänsten för synkronisering av lagring och lagringskontot finnas i samma Azure AD-klientorganisation. När molnslutpunkten har skapats kan tjänsten för synkronisering av lagring och lagringskontot flyttas till olika Azure AD-klienter.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med robocopy använder du alternativet /LTE för att bevara filtidsstämplar. Detta säkerställer att äldre filer nivåindelades tidigare än nyligen använda filer.

## <a name="agent-version-11300"></a>Agentversion 11.3.0.0
Följande versionsanteckningar gäller version 11.3.0.0 av Azure File Sync agenten som gavs ut den 7 april 2021. Dessa anteckningar är utöver den versionsanteckningar som anges för version 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som har åtgärdats 
En bugg som orsakar skadade data har åtgärdats om molnnivåindelad lagring är aktiverat och nivåindelade filer kopieras med Robocopy med parametern /B.

## <a name="agent-version-11200"></a>Agentversion 11.2.0.0
Följande versionsanteckningar gäller version 11.2.0.0 av Azure File Sync agenten som släpptes den 2 februari 2021. Dessa anteckningar är utöver de versionsanteckningar som anges för version 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som har åtgärdats 
- Om en synkroniseringssession avbryts på grund av ett stort antal fel per objekt kan synkroniseringen gå igenom avstämningen när en ny session startar om Azure File Sync-tjänsten fastställer att en anpassad synkroniseringssession krävs för att åtgärda felen per objekt.
- Registrering av en server med Register-AzStorageSyncServer cmdleten kan misslyckas med felet "Ohanterat undantag".
- Ny PowerShell-cmdlet (Add-StorageSyncAllowedServerEndpointPath) för att konfigurera tillåtna sökvägar för serverslutpunkter på en server. Denna cmdlet är användbar för scenarier där Azure File Sync-distributionen hanteras av en Molnlösningsleverantör (CSP) eller tjänstleverantör och kunden vill konfigurera tillåtna serverslutpunkter på en server. Om den angivna sökvägen inte finns i listan över tillåtna när du skapar en serverslutpunkt misslyckas skapandet av serverslutpunkten. Observera att detta är en valfri funktion och alla sökvägar som stöds tillåts som standard när du skapar en serverslutpunkt.  

    
    - Om du vill lägga till en sökväg för serverslutpunkten som tillåts kör du följande PowerShell-kommandon på servern:

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - Kör följande PowerShell-kommando för att hämta listan över sökvägar som stöds:
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - Kör följande PowerShell-kommando för att ta bort en sökväg:
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>Agentversion 11.1.0.0
Följande versionsanteckningar gäller för version 11.1.0.0 Azure File Sync agenten (släpptes 4 november 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som har åtgärdats
- Nya lägen för molnnivåinitiering för att styra den första nedladdningen och proaktivt återkallande
    - Inledande nedladdningsläge: Nu kan du välja hur du vill att filerna ska hämtas till den nya serverslutpunkten. Vill du att alla dina filer ska vara nivåindelade eller så många filer som möjligt laddas ned till servern med tidsstämpeln för senaste ändring? Det kan du göra! Kan du inte använda molnnivåindelad lagring? Nu kan du välja att undvika nivåindelade filer i systemet. Mer information finns i [avsnittet Om att skapa en serverslutpunkt](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) i Azure File Sync dokumentationen.
    - Proaktivt återkallningsläge: när en fil skapas eller ändras kan du proaktivt återkalla den till servrar som du anger i samma synkroniseringsgrupp. Detta gör filen lättillgänglig för användning på varje server som du har angett. Har team över hela världen arbetat med samma data? Aktivera proaktivt återkallande så att alla filer som uppdateras av ett team i en annan tidszon laddas ned och är redo att tas bort när teamet anländer nästa morgon! Mer information finns i Proaktivt återkalla nya och ändrade filer från en [Azure-filresurs](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) i dokumentationen distribuera Azure File Sync filresurs.

- Undanta program från spårning av senaste åtkomsttid för molnNivåindelad åtkomst Du kan nu undanta program från spårning av senaste åtkomsttid. När ett program kommer åt en fil uppdateras den senaste åtkomsttiden för filen i databasen för molnnivåindelning. Program som genomsöker filsystemet, t.ex. antivirus, gör att alla filer har samma senaste åtkomsttid, vilket påverkar när filerna är nivåindelade.

    Om du vill undanta program från spårning av senaste åtkomsttid lägger du till processnamnet i registerinställningen HeatTrackingProcessNameExclusionList som finns under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

    Exempel: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

    > [!Note]  
    > Processer för datadeduplicering och Resource Manager (FSRM) undantas som standard (hårdkodad) och listan över process uteslutningar uppdateras var femte minut.

- Diverse prestanda- och tillförlitlighetsförbättringar
    - Förbättrad prestanda för ändringsidentifiering för att identifiera filer som har ändrats i Azure-filresursen.
    - Förbättrad prestanda för synkroniseringsuppladdning.
    - Den första uppladdningen utförs nu från en VSS-ögonblicksbild, vilket minskar fel per objekt och synkroniseringssessionsfel.
    - Förbättringar av synkroniseringstillförlitlighet för vissa I/O-mönster.
    - En bugg har åtgärdats som förhindrar att synkroniseringsdatabasen går bakåt i tiden i redundanskluster när en redundans inträffar.
    - Förbättrad återkallningsprestanda vid åtkomst till en nivåindelad fil.

### <a name="evaluation-tool"></a>Utvärderingsverktyg
Innan du Azure File Sync bör du utvärdera om det är kompatibelt med systemet med hjälp Azure File Sync utvärderingsverktyget. Det här verktyget är Azure PowerShell cmdlet som söker efter potentiella problem med filsystemet och datauppsättningen, till exempel tecken som inte stöds eller en os-version som inte stöds. Installations- och användningsanvisningar finns [i avsnittet Utvärderingsverktyg](../file-sync/file-sync-planning.md#evaluation-cmdlet) i planeringsguiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i Planera för [en Azure File Sync-distribution](../file-sync/file-sync-planning.md) och [Så här distribuerar du Azure File Sync](../file-sync/file-sync-deployment-guide.md).

- Agentinstallationspaketet måste installeras med förhöjd behörighet (administratör).
- Agenten stöds inte i distributionsalternativet Nano Server.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat ska den virtuella datorn konfigureras med minst 2 048 MiB minne. Mer information [finns i Rekommenderade systemresurser.](../file-sync/file-sync-planning.md#recommended-system-resources)
- Tjänsten Storage Sync Agent (FileSyncSvc) stöder inte serverslutpunkter som finns på en volym som har SVI-katalogen (System Volume Information) komprimerad. Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [Felsöka Azure File Sync](../file-sync/file-sync-troubleshoot.md).
- FSRM Resource Manager filskärmar (File Server Resource Manager) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av filskärmen.
- Att köra sysprep på en server som har Azure File Sync agenten installerad stöds inte och kan leda till oväntade resultat. Agenten Azure File Sync installeras när du har distribuerat serveravbildningen och slutfört sysprep-miniinstallationen.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [Felsökningsguide](../file-sync/file-sync-troubleshoot.md#handling-unsupported-characters) för en lista över tecken som inte stöds.
- Filer eller kataloger som slutar med en punkt.
- Sökvägar som är längre än 2 048 tecken.
- SACL-delen av en säkerhetsbeskrivning som används för granskning.
- Utökade attribut.
- Alternativa dataströmmar.
- Referenspunkter.
- Hårda länkar.
- Komprimering (om det angetts på en serverfil) bevaras inte när ändringar synkroniseras till filen från andra slutpunkter.
- Alla filer som krypterats med EFS (eller andra typer av kryptering från användarläget) som förhindrar att tjänsten läser data.

    > [!Note]  
    > Azure File Sync krypterar alltid data under överföring. Vilande data är alltid krypterade i Azure.
 
### <a name="server-endpoint"></a>Serverslutpunkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en växlingsfil för operativsystem eller program på en plats för serverslutpunkten.
- Servernamnet i portalen uppdateras inte om servern har bytt namn.

### <a name="cloud-endpoint"></a>Molnslutpunkt
- Azure File Sync kan göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett Azure File Sync-jobbet för ändringsidentifiering. Ett ändringsidentifieringsjobb initieras för en molnslutpunkt en gång var 24:e timme. För att omedelbart synkronisera filer som har ändrats i Azure-filresursen kan [PowerShell-cmdleten Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) användas för att manuellt initiera identifieringen av ändringar i Azure-filresursen. Dessutom kommer ändringar som görs i en Azure-filresurs via REST-protokollet inte att uppdatera tiden för senaste ändring av SMB och kommer inte att ses som en ändring genom synkronisering.
- Tjänsten för synkronisering av lagring och/eller lagringskontot kan flyttas till en annan resursgrupp, prenumeration eller Azure AD-klientorganisation. När tjänsten för synkronisering av lagring eller lagringskontot har flyttats måste du ge programmet Microsoft.StorageSync åtkomst till lagringskontot (se Kontrollera att Azure File Sync har åtkomst till [lagringskontot](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > När du skapar molnslutpunkten måste tjänsten för synkronisering av lagring och lagringskontot finnas i samma Azure AD-klientorganisation. När molnslutpunkten har skapats kan tjänsten för synkronisering av lagring och lagringskontot flyttas till olika Azure AD-klienter.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med robocopy använder du alternativet /ROB för att bevara filtidsstämplar. Detta säkerställer att äldre filer nivåindelades tidigare än nyligen använda filer.
    > [!Warning]  
    > Robocopy/B-växeln stöds inte med Azure File Sync. Om du använder växeln Robocopy /B med Azure File Sync serverslutpunkt eftersom källan kan leda till skadade filer.

## <a name="agent-version-10100"></a>Agentversion 10.1.0.0
Följande versionsanteckningar gäller version 10.1.0.0 av Azure File Sync agenten som släpptes den 5 juni 2020. Dessa anteckningar är utöver den versionsanteckningar som anges för version 10.0.0.0 och 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som har åtgärdats

- Stöd för privata slutpunkter i Azure
    - Synkroniseringstrafik till tjänsten för synkronisering av lagring kan nu skickas till en privat slutpunkt. Detta möjliggör tunneling via en ExpressRoute- eller VPN-anslutning. Mer information finns i [Konfigurera Azure File Sync nätverksslutpunkter](../file-sync/file-sync-networking-endpoints.md).
- Måttet Synkroniserade filer visar nu förloppet när en stor synkronisering körs i stället för i slutet.
- Diverse tillförlitlighetsförbättringar för agentinstallation, molnnivåindelad lagring, synkronisering och telemetri

## <a name="agent-version-10020"></a>Agentversion 10.0.2.0
Följande versionsanteckningar gäller version 10.0.2.0 av Azure File Sync agenten som gavs ut den 19 maj 2020. Dessa anteckningar är utöver den versionsanteckningar som anges för version 10.0.0.0.

Problemet har åtgärdats i den här versionen:  
- Storage Sync Agent (FileSyncSvc) kraschar ofta när du har installerat Azure File Sync v10-agenten.

> [!Note]  
>Den här versionen flygs inte till servrar som är konfigurerade för att uppdateras automatiskt när en ny version blir tillgänglig. Om du vill installera den här uppdateringen använder Microsoft Update eller Microsoft Update Catalog (se [KB4522412](https://support.microsoft.com/help/4522412) för installationsanvisningar).

## <a name="agent-version-10000"></a>Agentversion 10.0.0.0
Följande versionsanteckningar gäller för version 10.0.0.0 Azure File Sync agenten (släpptes den 9 april 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som har åtgärdats

- Förbättrad synkroniseringsförloppet i portalen
    - Med V10-agenten börjar Azure Portal snart att visa vilken typ av synkroniseringssession som körs. T.ex. initial download, regular download, background recall (snabb haveriberedskapsfall) och liknande. 

- Förbättrad portalupplevelse för molnnivåindelad lagring
    - Om du har filer som inte kan nivåindelats eller återkallas kan du nu visa nivåindelateringsfelen i egenskaperna för serverslutpunkten.
    - Ytterligare information om molnnivåindelad är tillgänglig för en serverslutpunkt:
        - Storlek på lokal cache
        - Cacheanvändningseffektivitet
        - Information om molnnivåprincip: volymstorlek, aktuellt ledigt utrymme eller senast använda tid för den äldsta filen i det lokala cacheminnet.
    - De här ändringarna skickas i Azure Portal kort efter den första V10-agenten.

- Stöd för att flytta tjänsten för synkronisering av lagring och/eller lagringskontot till en annan Azure Active Directory klientorganisation
    - Azure File Sync stöder nu flytt av tjänsten för synkronisering av lagring och/eller lagringskontot till en annan resursgrupp, prenumeration eller Azure AD-klientorganisation.
    
- Diverse prestanda- och tillförlitlighetsförbättringar
    - Ändringsidentifiering på Azure-filresursen kan misslyckas om det virtuella nätverket (VNET) och brandväggsreglerna har konfigurerats på lagringskontot.
    - Minskad minnesförbrukning som är associerad med återkallande. 
    - Bättre prestanda när du använder [cmdleten Invoke-AzStorageSyncChangeDetection.](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)
    - Andra diverse tillförlitlighetsförbättringar. 
    
### <a name="evaluation-tool"></a>Utvärderingsverktyg
Innan du Azure File Sync bör du utvärdera om det är kompatibelt med systemet med hjälp Azure File Sync utvärderingsverktyget. Det här verktyget är Azure PowerShell cmdlet som söker efter potentiella problem med filsystemet och datauppsättningen, till exempel tecken som inte stöds eller en os-version som inte stöds. Installations- och användningsanvisningar finns [i avsnittet Utvärderingsverktyg](../file-sync/file-sync-planning.md#evaluation-cmdlet) i planeringsguiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i Planera för [en Azure File Sync-distribution](../file-sync/file-sync-planning.md) och [Så här distribuerar du Azure File Sync](../file-sync/file-sync-deployment-guide.md).

- Agentinstallationspaketet måste installeras med förhöjd behörighet (administratör).
- Agenten stöds inte i distributionsalternativet Nano Server.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat ska den virtuella datorn konfigureras med minst 2 048 MiB minne.
- Tjänsten Storage Sync Agent (FileSyncSvc) stöder inte serverslutpunkter som finns på en volym som har SVI-katalogen (System Volume Information) komprimerad. Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [Felsöka Azure File Sync](../file-sync/file-sync-troubleshoot.md).
- FSRM Resource Manager filskärmar (File Server Resource Manager) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av filskärmen.
- Att köra sysprep på en server som har Azure File Sync agenten installerad stöds inte och kan leda till oväntade resultat. Agenten Azure File Sync installeras när du har distribuerat serveravbildningen och slutfört sysprep-miniinstallationen.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [Felsökningsguide](../file-sync/file-sync-troubleshoot.md#handling-unsupported-characters) för en lista över tecken som inte stöds.
- Filer eller kataloger som slutar med en punkt.
- Sökvägar som är längre än 2 048 tecken.
- SACL-delen av en säkerhetsbeskrivning som används för granskning.
- Utökade attribut.
- Alternativa dataströmmar.
- Referenspunkter.
- Hårda länkar.
- Komprimering (om det angetts på en serverfil) bevaras inte när ändringar synkroniseras till filen från andra slutpunkter.
- Alla filer som krypterats med EFS (eller andra typer av kryptering från användarläget) som förhindrar att tjänsten läser data.

    > [!Note]  
    > Azure File Sync krypterar alltid data under överföring. Vilande data är alltid krypterade i Azure.
 
### <a name="server-endpoint"></a>Serverslutpunkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en växlingsfil för operativsystem eller program på en plats för serverslutpunkten.
- Servernamnet i portalen uppdateras inte om servern har bytt namn.

### <a name="cloud-endpoint"></a>Molnslutpunkt
- Azure File Sync kan göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett Azure File Sync-jobbet för ändringsidentifiering. Ett ändringsidentifieringsjobb initieras för en molnslutpunkt en gång var 24:e timme. För att omedelbart synkronisera filer som har ändrats i Azure-filresursen kan [PowerShell-cmdleten Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) användas för att manuellt initiera identifieringen av ändringar i Azure-filresursen. Dessutom kommer ändringar som görs i en Azure-filresurs via REST-protokollet inte att uppdatera tiden för senaste ändring av SMB och kommer inte att ses som en ändring genom synkronisering.
- Tjänsten för synkronisering av lagring och/eller lagringskontot kan flyttas till en annan resursgrupp, prenumeration eller Azure AD-klientorganisation. När tjänsten för synkronisering av lagring eller lagringskontot har flyttats måste du ge programmet Microsoft.StorageSync åtkomst till lagringskontot (se Kontrollera att Azure File Sync har åtkomst till [lagringskontot](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > När du skapar molnslutpunkten måste tjänsten för synkronisering av lagring och lagringskontot finnas i samma Azure AD-klientorganisation. När molnslutpunkten har skapats kan tjänsten för synkronisering av lagring och lagringskontot flyttas till olika Azure AD-klienter.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med robocopy använder du alternativet /ROB för att bevara filtidsstämplar. Detta säkerställer att äldre filer nivåindelades tidigare än nyligen använda filer.
