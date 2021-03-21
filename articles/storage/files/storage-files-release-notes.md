---
title: Viktig information för Azure File Sync agenten | Microsoft Docs
description: Läs viktig information för Azure File Sync-agenten där du kan centralisera organisationens fil resurser i Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/3/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 5549fc3b63b76c6158ae7399e6d94a43d2d4f28f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435196"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Viktig information om Azure File Sync-agenten
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Dina Windows Server-installationer omvandlas till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS). Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln innehåller viktig information om versioner av Azure File Sync-agenten som stöds.

## <a name="supported-versions"></a>Versioner som stöds
Följande Azure File Sync agent versioner stöds:

| Gränser | Agentversionsnummer | Utgivningsdatum | Status |
|----|----------------------|--------------|------------------|
| V 11.2-utgåva – [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2 februari 2021 | Stöds |
| V 11.1 release- [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 4 november 2020 | Stöds |
| V 10.1-version – [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 5 juni 2020 | Agent versionen som stöds upphör att gälla den 7 juni 2021 |
| Samlad uppdatering för 2020 maj – [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 maj 2020 | Agent versionen som stöds upphör att gälla den 7 juni 2021 |
| V10-version – [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 9 april 2020 | Agent versionen som stöds upphör att gälla den 7 juni 2021 |

## <a name="unsupported-versions"></a>Versioner som inte stöds
Följande Azure File Sync agent versioner har upphört att gälla och stöds inte längre:

| Gränser | Agentversionsnummer | Utgivningsdatum | Status |
|----|----------------------|--------------|------------------|
| V9-version | 9.0.0.0 - 9.1.0.0 | Ej tillämpligt | Stöds inte-agent versionen har upphört att gälla den 16 februari 2021 |
| V8-version | 8.0.0.0 | Ej tillämpligt | Stöds inte-agent versionen har upphört att gälla 12 januari 2021 |
| V7-version | 7.0.0.0 - 7.2.0.0 | Ej tillämpligt | Stöds inte-agent versioner har upphört att gälla den 1 september 2020 |
| V6-version | 6.0.0.0 - 6.3.0.0 | Ej tillämpligt | Stöds inte-agent versioner upphörde att gälla den 21 april 2020 |
| Version V5 | 5.0.2.0 - 5.2.0.0 | Ej tillämpligt | Stöds inte-agent versioner har upphört att gälla den 18 mars 2020 |
| V4-version | 4.0.1.0 - 4.3.0.0 | Ej tillämpligt | Stöds inte-agent versioner upphörde att gälla den 6 november 2019 |
| V3-version | 3.1.0.0 - 3.4.0.0 | Ej tillämpligt | Stöds inte-agent versioner har upphört att gälla den 19 augusti 2019 |
| För GA-agenter | 1.1.0.0 – 3.0.13.0 | Ej tillämpligt | Stöds inte-agent versioner har upphört att gälla den 1 oktober 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-11200"></a>11.2.0.0 för agent version
Följande viktig information gäller version 11.2.0.0 av Azure File Sync agent som publicerades den 2 februari 2021. De här anteckningarna är utöver de versions anteckningar som anges för version 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som åtgärdas 
- Om en synkroniseringsanslutning avbryts på grund av ett stort antal fel per objekt, kan synkroniseringen gå igenom avstämningen när en ny session startar om den Azure File Sync tjänsten fastställer en anpassad svarsomgång som krävs för att korrigera felen per objekt.
- Att registrera en server med hjälp av Register-AzStorageSyncServer cmdlet kan Miss lyckas med fel meddelandet "ohanterat undantag".
- Ny PowerShell-cmdlet (Add-StorageSyncAllowedServerEndpointPath) för att konfigurera tillåtna Sök vägar för Server slut punkter på en server. Denna cmdlet är användbar för scenarier där Azure File Sync-distributionen hanteras av en leverantör av moln lösningar (CSP) eller tjänst leverantör och kunden vill konfigurera tillåtna Sök vägar för Server slut punkter på en server. När du skapar en server slut punkt, och den angivna sökvägen inte finns i listan över tillåtna, kommer det inte att gå att skapa server slut punkten. OBS! detta är en valfri funktion och alla sökvägar som stöds tillåts som standard när du skapar en server slut punkt.  

    
    - Om du vill lägga till en sökväg för Server slut punkten som tillåts kör du följande PowerShell-kommandon på servern:

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - Om du vill hämta en lista över sökvägar som stöds kör du följande PowerShell-kommando:
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - Om du vill ta bort en sökväg kör du följande PowerShell-kommando:
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>11.1.0.0 för agent version
Följande viktig information gäller version 11.1.0.0 av Azure File Sync agent (lanserades 4 november 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som åtgärdas
- Nya alternativ för moln skiktning för att styra inledande hämtning och proaktiv återställning
    - Inledande nedladdnings läge: nu kan du välja hur du vill att filerna ska laddas ned till din nya server slut punkt. Vill du att alla filer ska skiktas eller så många filer som möjligt hämtas till servern med den senast ändrade tidsstämpeln? Det kan du göra! Kan du inte använda moln nivåer? Du kan nu välja att undvika nivåbaserade filer på systemet. Mer information finns i avsnittet [skapa en server slut punkt](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) i Deploy Azure File Sync-dokumentationen.
    - Proaktivt återställnings läge: när en fil skapas eller ändras kan du återkalla den proaktivt till servrar som du anger i samma Sync-grupp. Detta gör filen lätt att använda på varje server som du har angett. Har du flera team i hela världen som arbetar med samma data? Aktivera proaktivt återanrop så att alla filer som uppdateras av ett team i en annan tidszon hämtas och är redo att användas när teamet kommer till nästa morgon. Mer information finns i [proaktivt återkalla nya och ändrade filer från en Azure-filresurs](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) i Distribuera Azure File Sync-dokumentationen.

- Uteslut program från moln nivå senaste åtkomst tid spårning du kan nu utesluta program från senaste åtkomst tid spårningen. När ett program har åtkomst till en fil uppdateras den senaste åtkomst tiden för filen i moln skikts databasen. Program som genomsöker fil systemet, t. ex. anti-virus, gör att alla filer får samma senaste åtkomst tid som påverkar när filer skiktas.

    Om du vill undanta program från senaste åtkomst tid för spårning lägger du till process namnet i register inställningen HeatTrackingProcessNameExclusionList som finns under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

    Exempel: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

    > [!Note]  
    > Data deduplicering och FSRM-processer (hanteraren för fil server resurser) undantas som standard (hårdkodade) och process uteslutnings listan uppdateras var 5: e minut.

- Diverse förbättringar av prestanda och tillförlitlighet
    - Förbättrad prestanda för ändrings identifiering för att identifiera filer som har ändrats i Azure-filresursen.
    - Förbättrad prestanda för att ladda upp synkronisering.
    - Den inledande överföringen utförs nu från en VSS-ögonblicksbild som minskar antalet fel i objektet och synkroniseringsfel.
    - Synkronisera Tillförlitlighets förbättringar för vissa I/O-mönster.
    - En bugg har åtgärd ATS för att förhindra att Sync-databasen går in i tid på redundanskluster när en redundansväxling inträffar.
    - Bättre återställnings prestanda vid åtkomst till en nivå fil.

### <a name="evaluation-tool"></a>Utvärderings verktyg
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med systemet med hjälp av verktyget för Azure File Sync utvärdering. Det här verktyget är en Azure PowerShell-cmdlet som kontrollerar eventuella problem med fil systemet och data uppsättningen, till exempel tecken som inte stöds eller en operativ system version som inte stöds. Anvisningar för installation och användning finns i avsnittet [utvärderings verktyg](./storage-sync-files-planning.md#evaluation-cmdlet) i planerings guiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för en Azure File Sync distribution](storage-sync-files-planning.md) och [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md).

- Agent installations paketet måste installeras med utökade behörigheter (admin).
- Agenten stöds inte för distributions alternativet Nano Server.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat, ska den virtuella datorn konfigureras med minst 2048 MiB för minne. Se [rekommenderade system resurser](./storage-sync-files-planning.md#recommended-system-resources) för mer information.
- Tjänsten Storage Sync agent (FileSyncSvc) stöder inte Server slut punkter som finns på en volym som har SVI-katalogen (System Volume information) komprimerad. Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- Fil gallren i Hanteraren för fil server resurser (FSRM) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av fil gallret.
- Körning av Sysprep på en server där Azure File Sync-agenten är installerad stöds inte och kan leda till oväntade resultat. Azure File Sync agenten bör installeras efter att du har distribuerat Server avbildningen och slutfört Sysprep-miniinstallationsprogrammet.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [fel söknings guide](storage-sync-files-troubleshoot.md#handling-unsupported-characters) för en lista med tecken som inte stöds.
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
 
### <a name="server-endpoint"></a>Server slut punkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en operativ system-eller program växlings fil på en server slut punkts plats.
- Server namnet i portalen uppdateras inte om servern byter namn.

### <a name="cloud-endpoint"></a>Moln slut punkt
- Azure File Sync har stöd för att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett Azure File Sync ändrings identifierings jobb. Ett ändrings identifierings jobb initieras för en moln slut punkt var 24: e timme. Om du vill synkronisera filer som har ändrats i Azure-filresursen direkt kan du använda PowerShell-cmdleten [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) för att manuellt initiera identifieringen av ändringar i Azure-filresursen. Ändringar som görs i en Azure-filresurs via REST-protokollet kommer dessutom inte att uppdatera tidpunkten för senaste ändring av SMB och visas inte som en ändring genom synkronisering.
- Tjänsten Storage Sync och/eller lagrings kontot kan flyttas till en annan resurs grupp, prenumeration eller Azure AD-klient. När lagrings tjänsten för synkronisering eller lagrings kontot har flyttats måste du ge Microsoft. StorageSync-programmet åtkomst till lagrings kontot (se [Se till att Azure File Sync har åtkomst till lagrings kontot](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > När du skapar moln slut punkten måste Storage Sync-tjänsten och lagrings kontot finnas i samma Azure AD-klient. När moln slut punkten har skapats kan tjänsten Storage Sync och lagrings kontot flyttas till olika Azure AD-klienter.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med Robocopy kan du använda alternativet/MIR för att bevara tidsstämplar för filer. Detta säkerställer att äldre filer går fortare än vid nyligen öppnade filer.
    > [!Warning]  
    > Robocopy/B-växeln stöds inte med Azure File Sync. Om du använder Robocopy/B-växeln med en Azure File Sync Server slut punkt när källan kan leda till skadade filer.

## <a name="agent-version-10100"></a>10.1.0.0 för agent version
Följande versions information gäller version 10.1.0.0 av den Azure File Sync agenten som publicerades den 5 juni 2020. De här anteckningarna är utöver de versions anteckningar som anges för version 10.0.0.0 och 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som åtgärdas

- Stöd för privata Azure-slutpunkter
    - Synkronisering av trafik till tjänsten Storage Sync kan nu skickas till en privat slut punkt. Detta möjliggör tunnel trafik över en ExpressRoute eller VPN-anslutning. Mer information finns i [konfigurera Azure File Sync nätverks slut punkter](./storage-sync-files-networking-endpoints.md).
- Mått för synkroniserade filer visar nu förloppet medan en stor synkronisering körs, i stället för i slutet.
- Diverse Tillförlitlighets förbättringar för agent installation, moln nivåer, synkronisering och telemetri

## <a name="agent-version-10020"></a>10.0.2.0 för agent version
Följande viktiga information gäller version 10.0.2.0 av den Azure File Sync agent som lanserades den 19 maj 2020. De här anteckningarna är utöver de versions anteckningar som anges för version 10.0.0.0.

Problem korrigerat i den här versionen:  
- FileSyncSvc-agenten (Storage Sync agent) kraschar ofta när Azure File Sync v10-agenten har installerats.

> [!Note]  
>Den här versionen har inte överförts till servrar som är konfigurerade att uppdateras automatiskt när en ny version blir tillgänglig. Om du vill installera den här uppdateringen använder du Microsoft Update eller Microsoft Update katalog (se [KB4522412](https://support.microsoft.com/help/4522412) för Installationsinstruktioner).

## <a name="agent-version-10000"></a>Agent version 10.0.0.0
Följande viktiga information gäller version 10.0.0.0 av Azure File Sync agent (lanserades den 9 april 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som åtgärdas

- Förbättrat synkroniserings förlopp i portalen
    - Med v10 agent-versionen börjar Azure Portal snart att visa vilken typ av synkroniseringsanslutning som körs. T.ex. inledande hämtning, normal hämtning, bakgrunds återkallande (snabba haveri återställnings fall) och liknande. 

- Förbättrad Portal upplevelse för moln nivåer
    - Om du har filer som inte kan gå till nivån eller återkalla kan du nu visa fel skikts fel i egenskaperna för Server slut punkten.
    - Ytterligare information om moln nivåer är tillgänglig för en server slut punkt:
        - Storlek på lokal cache
        - Användnings effektivitet för cache
        - Princip information för moln skikt: volym storlek, Aktuellt ledigt utrymme eller senaste åtkomst tid för den äldsta filen i det lokala cacheminnet.
    - De här ändringarna kommer att leverera i Azure Portal strax efter den första agent versionen av v10.

- Stöd för att flytta synkroniseringstjänsten för lagring och/eller lagrings konto till en annan Azure Active Directory klient
    - Azure File Sync stöder nu att flytta synkroniseringstjänsten för lagring och/eller lagrings kontot till en annan resurs grupp, prenumeration eller Azure AD-klient.
    
- Diverse förbättringar av prestanda och tillförlitlighet
    - Ändrings identifieringen på Azure-filresursen kan Miss lyckas om det virtuella nätverket (VNET) och brand Väggs regler har kon figurer ATS på lagrings kontot.
    - Minskad minnes användning som är kopplad till återkallning. 
    - Bättre prestanda när du använder cmdleten [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) .
    - Andra förbättringar av ökad tillförlitlighet. 
    
### <a name="evaluation-tool"></a>Utvärderings verktyg
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med systemet med hjälp av verktyget för Azure File Sync utvärdering. Det här verktyget är en Azure PowerShell-cmdlet som kontrollerar eventuella problem med fil systemet och data uppsättningen, till exempel tecken som inte stöds eller en operativ system version som inte stöds. Anvisningar för installation och användning finns i avsnittet [utvärderings verktyg](./storage-sync-files-planning.md#evaluation-cmdlet) i planerings guiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för en Azure File Sync distribution](storage-sync-files-planning.md) och [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md).

- Agent installations paketet måste installeras med utökade behörigheter (admin).
- Agenten stöds inte för distributions alternativet Nano Server.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat, ska den virtuella datorn konfigureras med minst 2048 MiB för minne.
- Tjänsten Storage Sync agent (FileSyncSvc) stöder inte Server slut punkter som finns på en volym som har SVI-katalogen (System Volume information) komprimerad. Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- Fil gallren i Hanteraren för fil server resurser (FSRM) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av fil gallret.
- Körning av Sysprep på en server där Azure File Sync-agenten är installerad stöds inte och kan leda till oväntade resultat. Azure File Sync agenten bör installeras efter att du har distribuerat Server avbildningen och slutfört Sysprep-miniinstallationsprogrammet.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [fel söknings guide](storage-sync-files-troubleshoot.md#handling-unsupported-characters) för en lista med tecken som inte stöds.
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
 
### <a name="server-endpoint"></a>Server slut punkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en operativ system-eller program växlings fil på en server slut punkts plats.
- Server namnet i portalen uppdateras inte om servern byter namn.

### <a name="cloud-endpoint"></a>Moln slut punkt
- Azure File Sync har stöd för att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett Azure File Sync ändrings identifierings jobb. Ett ändrings identifierings jobb initieras för en moln slut punkt var 24: e timme. Om du vill synkronisera filer som har ändrats i Azure-filresursen direkt kan du använda PowerShell-cmdleten [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) för att manuellt initiera identifieringen av ändringar i Azure-filresursen. Ändringar som görs i en Azure-filresurs via REST-protokollet kommer dessutom inte att uppdatera tidpunkten för senaste ändring av SMB och visas inte som en ändring genom synkronisering.
- Tjänsten Storage Sync och/eller lagrings kontot kan flyttas till en annan resurs grupp, prenumeration eller Azure AD-klient. När lagrings tjänsten för synkronisering eller lagrings kontot har flyttats måste du ge Microsoft. StorageSync-programmet åtkomst till lagrings kontot (se [Se till att Azure File Sync har åtkomst till lagrings kontot](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > När du skapar moln slut punkten måste Storage Sync-tjänsten och lagrings kontot finnas i samma Azure AD-klient. När moln slut punkten har skapats kan tjänsten Storage Sync och lagrings kontot flyttas till olika Azure AD-klienter.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med Robocopy kan du använda alternativet/MIR för att bevara tidsstämplar för filer. Detta säkerställer att äldre filer går fortare än vid nyligen öppnade filer.
