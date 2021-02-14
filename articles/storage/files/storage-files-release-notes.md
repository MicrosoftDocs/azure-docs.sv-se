---
title: Viktig information för Azure File Sync agenten | Microsoft Docs
description: Läs viktig information för Azure File Sync-agenten där du kan centralisera organisationens fil resurser i Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 2/11/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 4e1b5828e096d3d712def5420fd846d65b22e696
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366117"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Viktig information om Azure File Sync-agenten
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Dina Windows Server-installationer omvandlas till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS). Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln innehåller viktig information om versioner av Azure File Sync-agenten som stöds.

## <a name="supported-versions"></a>Versioner som stöds
Följande Azure File Sync agent versioner stöds:

| Gränser | Agentversionsnummer | Utgivningsdatum | Status |
|----|----------------------|--------------|------------------|
| V 11.2-utgåva – [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2 februari 2021 | Support – flyg |
| V 11.1 release- [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 4 november 2020 | Stöds |
| V 10.1-version – [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 5 juni 2020 | Stöds |
| Samlad uppdatering för 2020 maj – [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 maj 2020 | Stöds |
| V10-version – [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 9 april 2020 | Stöds |
| Samlad uppdatering från december 2019 – [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12 december 2019 | Agent versionen som stöds upphör att gälla den 16 februari 2021 |
| V9-version – [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2 december 2019 | Agent versionen som stöds upphör att gälla den 16 februari 2021 |

## <a name="unsupported-versions"></a>Versioner som inte stöds
Följande Azure File Sync agent versioner har upphört att gälla och stöds inte längre:

| Gränser | Agentversionsnummer | Utgivningsdatum | Status |
|----|----------------------|--------------|------------------|
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

## <a name="agent-version-9100"></a>9.1.0.0 för agent version
Följande viktiga information gäller version 9.1.0.0 av Azure File Sync agent som lanserades den 12 december 2019. De här anteckningarna är utöver de versions anteckningar som anges för version 9.0.0.0.

Problem korrigerat i den här versionen:  
- Synkroniseringen Miss lyckas med ett av följande fel efter uppgraderingen till Azure File Sync agent version 9,0:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>9.0.0.0 för agent version
Följande viktig information gäller version 9.0.0.0 av Azure File Sync agent (lanserades 2 december 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som åtgärdas

- Support för självbetjänings återställning
    - Användare kan nu också återställa nivåbaserade filer (tillsammans med filer på disk) med hjälp av funktionen för tidigare versioner, från de VSS-ögonblicksbilder som skapas när funktionen för självbetjänings återställning har Aktiver ATS på volymen. Innan v9-versionen stöds inte den tidigare versions funktionen för nivåbaserade filer. Den här funktionen måste vara aktive rad för varje volym separat, på vilken en slut punkt med aktive rad moln nivå finns. Mer information finns i  
[Återställning via självbetjäning genom tidigare versioner och VSS (tjänsten Volume Shadow Copy)](./storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Stöd för större fil resurs storlekar 
    - Azure File Sync stöder nu upp till 64TiB-och 100 000 000-filer i ett enda, syncing-namnområde.  
 
- Stöd för datadeduplicering på Server 2019 
    - Datadeduplicering stöds nu (oavsett om moln skiktning är aktiverat eller inaktiverat på en eller flera Server slut punkter på volymen) på Windows Server 2016 och Windows Server 2019. För att stödja datadeduplicering på volymer med moln skiktning på Server 2019 måste Windows Update [KB4520062](https://support.microsoft.com/help/4520062) installeras. 
 
- Förbättrad minsta fil storlek för en fil till nivå 
    - Den minsta fil storleken för en fil till-nivån baseras nu på fil systemets kluster storlek (dubbelt fil systemets kluster storlek). Som standard är till exempel fil systemets NTFS-storlek 4KB, den resulterande minsta fil storleken för en fil till nivån är 8 KB. 
 
- Test-cmdlet för nätverks anslutning 
    - Som en del av Azure File Sync konfiguration måste flera tjänst slut punkter kontaktas. De har sina egna DNS-namn som måste vara tillgängliga för-servern. Dessa URL: er är också speciella för regionen som en server är registrerad på. När en server har registrerats kan anslutnings test cmdlet (PowerShell och Server registrerings verktyg) användas för att testa kommunikationen med alla URL: er som är kopplade till den här servern. Den här cmdleten kan hjälpa till att felsöka när ofullständig kommunikation förhindrar att servern fungerar fullt ut med Azure File Sync och kan användas för att finjustera konfigurationer för proxy och brand väggar.  
 
        Kör följande PowerShell-kommandon för att köra testet för nätverks anslutning: 
 
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Ta bort Server slut punkts förbättring när moln skiktning är aktiverat 
    - Som tidigare resulterar det inte i att ta bort filer i Azure-filresursen om du tar bort en server slut punkt. Beteendet för referens punkter på den lokala servern har dock ändrats. Referens punkter (pekare till filer som inte är lokala på servern) tas nu bort när du tar bort en server slut punkt. De fullständigt cachelagrade filerna är kvar på servern. Den här förbättringen gjordes för att förhindra [överblivna filer på nivån](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) när en server slut punkt togs bort. Om Server slut punkten återskapas, kommer referens punkterna för de nivåbaserade filerna att återskapas på servern.  
 
- Förbättringar av prestanda och tillförlitlighet 
    - Minskat återställnings problem. Åter kallelse storleken justeras nu automatiskt baserat på nätverks bandbredden. 
    - Förbättrad nedladdnings prestanda när du lägger till en ny server i en Sync-grupp. 
    - Mindre filer synkroniseras inte på grund av begränsnings konflikter. 
    - Filerna går inte att flytta till nivån eller återkallas i vissa fall, om Server slut punkten är en volym monterings punkt.
    
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
- DACL-delen av en säkerhetsbeskrivning om den är större än 2 kB. (Det här är endast ett problem om du har mer än ca 40 åtkomstkontrollposter på ett enskilt objekt.)
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
- Nivåbaserade filer blir otillgängliga om filerna inte återkallas innan Server slut punkten tas bort. Återskapar Server slut punkten för att återställa åtkomsten till filerna. Om 30 dagar har passerat sedan Server slut punkten togs bort, eller om moln slut punkten togs bort, kommer filer som inte återkallats att bli oanvändbara. Mer information finns i [skiktade filer är inte tillgängliga på servern när du har tagit bort en server slut punkt](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en operativ system-eller program växlings fil på en server slut punkts plats.
- Server namnet i portalen uppdateras inte om servern byter namn.

### <a name="cloud-endpoint"></a>Moln slut punkt
- Azure File Sync har stöd för att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett Azure File Sync ändrings identifierings jobb. Ett ändrings identifierings jobb initieras för en moln slut punkt var 24: e timme. Om du vill synkronisera filer som har ändrats i Azure-filresursen direkt kan du använda PowerShell-cmdleten [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) för att manuellt initiera identifieringen av ändringar i Azure-filresursen. Ändringar som görs i en Azure-filresurs via REST-protokollet kommer dessutom inte att uppdatera tidpunkten för senaste ändring av SMB och visas inte som en ändring genom synkronisering.
- Tjänsten för synkronisering av lagring och/eller lagrings kontot kan flyttas till en annan resurs grupp eller prenumeration i den befintliga Azure AD-klienten. Om lagrings kontot har flyttats måste du ge hybrid File Sync tjänst åtkomst till lagrings kontot (se [Se till att Azure File Sync har åtkomst till lagrings kontot](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync har inte stöd för att flytta prenumerationen till en annan Azure AD-klient.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med Robocopy kan du använda alternativet/MIR för att bevara tidsstämplar för filer. Detta säkerställer att äldre filer går fortare än vid nyligen öppnade filer.
- Filer kan Miss lyckas på nivån om pagefile.sys finns på en volym som har aktiverat moln skiktning. pagefile.sys bör finnas på en volym där moln nivå inaktive ras.
