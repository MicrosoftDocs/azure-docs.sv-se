---
title: 'Azure AD Connect synkronisering: Scheduler | Microsoft Docs'
description: I det här avsnittet beskrivs den inbyggda funktionen Scheduler i Azure AD Connect Sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad7b0039602add7f4cd3cdd300bd829c4f148a79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90084744"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Azure AD Connect sync: Scheduler
I det här avsnittet beskrivs den inbyggda Schemaläggaren i Azure AD Connect Sync (Synkroniseringsmotorn).

Den här funktionen introducerades med build-1.1.105.0 (lanserades februari 2016).

## <a name="overview"></a>Översikt
Azure AD Connect synkronisera synkronisera ändringar som inträffar i din lokala katalog med hjälp av en Scheduler. Det finns två schemaläggare-processer, en för Lösenordssynkronisering och en annan för synkronisering och underhålls aktiviteter för objekt/attribut. Det här avsnittet beskriver det senare.

I tidigare versioner var Scheduler för objekt och attribut utanför motorn för synkronisering. Den använde Schemaläggaren för Windows eller en separat Windows-tjänst för att utlösa synkroniseringsprocessen. Scheduler är med 1,1 utgåvor inbyggda i Synkroniseringsmotorn och gör det möjligt att göra vissa anpassningar. Den nya standard frekvensen för synkronisering är 30 minuter.

Scheduler ansvarar för två uppgifter:

* **Synkroniseringsprocess**. Processen för att importera, synkronisera och exportera ändringar.
* **Underhålls aktiviteter**. Förnya nycklar och certifikat för lösen ords återställning och DRS (Device Registration Service). Rensa gamla poster i åtgärds loggen.

Själva Scheduler körs alltid, men den kan konfigureras för att bara köra en eller inga av dessa uppgifter. Om du till exempel behöver ha en egen process för synkronisering, kan du inaktivera den här uppgiften i Schemaläggaren men fortfarande köra underhålls aktiviteten.

>[!IMPORTANT]
>Som standard var 30: e minut en synkronisering körs. Om du har ändrat synkroniseringsfunktionen måste du kontrol lera att en synkroniseringsprocess körs minst en gång var sjunde dag. 
>
>* En Delta-synkronisering måste ske inom 7 dagar från den senaste synkroniseringen.
>* En Delta-synkronisering (efter en fullständig synkronisering) måste ske inom 7 dagar från den tidpunkt då den senaste fullständiga synkroniseringen slutfördes.
>
>Om du inte gör det kan det orsaka synkroniseringsproblem som kräver att du kör en fullständig synkronisering för att lösa problemet. Detta gäller även för servrar i mellanlagrings läge.

## <a name="scheduler-configuration"></a>Scheduler-konfiguration
Om du vill se dina aktuella konfigurations inställningar går du till PowerShell och kör `Get-ADSyncScheduler` . Du ser något som liknar den här bilden:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Om du ser **Sync-kommandot eller om cmdleten inte är tillgänglig** när du kör den här cmdleten läses PowerShell-modulen inte in. Det här problemet kan inträffa om du kör Azure AD Connect på en domänkontrollant eller på en server med högre PowerShell-restriktioner än standardinställningarna. Om du ser det här felet kör `Import-Module ADSync` du för att göra cmdleten tillgänglig.

* **AllowedSyncCycleInterval**. Det kortaste tidsintervallet mellan cykler som tillåts av Azure AD. Du kan inte synkronisera oftare än den här inställningen och stöds fortfarande.
* **CurrentlyEffectiveSyncCycleInterval**. Schemat som för närvarande används. Det har samma värde som CustomizedSyncInterval (om det är inställt) om det inte är oftare än AllowedSyncInterval. Om du använder en version innan 1.1.281 och du ändrar CustomizedSyncCycleInterval börjar ändringen gälla efter nästa synkronisering. Från build-1.1.281 börjar ändringen gälla omedelbart.
* **CustomizedSyncCycleInterval**. Om du vill att Scheduler ska köras med en annan frekvens än standard 30 minuter konfigurerar du den här inställningen. I bilden ovan har Scheduler angetts att köras varje timme i stället. Om du ställer in den här inställningen på ett värde som är lägre än AllowedSyncInterval används den senare.
* **NextSyncCyclePolicyType**. Antingen delta eller initial. Definierar om nästa körning bara ska bearbeta förändrings ändringar, eller om nästa körning ska göra en fullständig import och synkronisering. Den senare skulle också bearbeta nya eller ändrade regler.
* **NextSyncCycleStartTimeInUTC**. Nästa gången Scheduler startar nästa synkronisering.
* **PurgeRunHistoryInterval**. Tids loggen för processen bör behållas. Dessa loggar kan granskas i Synchronization Service Manager. Standardvärdet är att behålla loggarna i 7 dagar.
* **SyncCycleEnabled**. Anger om Scheduler kör processer för import, synkronisering och export som en del av åtgärden.
* **MaintenanceEnabled**. Visar om underhålls processen är aktive rad. Den uppdaterar certifikaten/nycklarna och rensar åtgärds loggen.
* **StagingModeEnabled**. Visar om [mellanlagrings läge](how-to-connect-sync-staging-server.md) är aktiverat. Om den här inställningen är aktive rad förhindras exporten från att köras men fortfarande körning av import och synkronisering.
* **SchedulerSuspended**. Ange per anslutning under en uppgradering för att tillfälligt blockera Schemaläggaren från att köras.

Du kan ändra några av de här inställningarna med `Set-ADSyncScheduler` . Följande parametrar kan ändras:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

I tidigare versioner av Azure AD Connect exponerades **isStagingModeEnabled** i set-ADSyncScheduler. Det går **inte** att ange den här egenskapen. Egenskapen **SchedulerSuspended** bör bara ändras av Connect. Det går **inte** att ange detta med PowerShell direkt.

Scheduler-konfigurationen lagras i Azure AD. Om du har en uppsamlings Server påverkar alla ändringar på den primära servern även mellanlagrings servern (förutom IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Uttryck `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d-dagar, tt-timmar, mm-minuter, ss-sekunder

Exempel: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Ändrar Schemaläggaren så att den körs var 3: e timme.

Exempel: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Ändringarna ändrar Schemaläggaren till kör varje dag.

### <a name="disable-the-scheduler"></a>Inaktivera Scheduler  
Om du behöver göra konfigurations ändringar vill du inaktivera Scheduler. Till exempel när du [konfigurerar filtrering](how-to-connect-sync-configure-filtering.md) eller [gör ändringar i regler för synkronisering](how-to-connect-sync-change-the-configuration.md).

Om du vill inaktivera Scheduler kör du `Set-ADSyncScheduler -SyncCycleEnabled $false` .

![Inaktivera Scheduler](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

När du har gjort ändringarna ska du inte glömma att aktivera Scheduler igen med `Set-ADSyncScheduler -SyncCycleEnabled $true` .

## <a name="start-the-scheduler"></a>Starta Scheduler
Scheduler körs som standard var 30: e minut. I vissa fall kanske du vill köra en synkronisering mellan schemalagda cykler eller så måste du köra en annan typ.

### <a name="delta-sync-cycle"></a>Delta-synkronisering
En Delta-synkronisering innehåller följande steg:


- Delta import på alla anslutningar
- Delta-synkronisering på alla anslutningar
- Exportera på alla kopplingar

### <a name="full-sync-cycle"></a>Fullständig synkronisering
En fullständig synkronisering innehåller följande steg:

- Fullständig import för alla anslutningar
- Fullständig synkronisering för alla anslutningar
- Exportera på alla kopplingar

Det kan bero på att du har en brådskande ändring som måste synkroniseras direkt, vilket är anledningen till att du måste köra en cykel manuellt. 

Om du behöver köra en Sync-cykel manuellt kör du PowerShell-körningen `Start-ADSyncSyncCycle -PolicyType Delta` .

Om du vill starta en fullständig synkronisering kör du `Start-ADSyncSyncCycle -PolicyType Initial` från en PowerShell-prompt.   

Det kan ta lång tid att köra en fullständig synkronisering, Läs nästa avsnitt för att läsa mer om hur du optimerar den här processen.

### <a name="sync-steps-required-for-different-configuration-changes"></a>Synkronisera steg som krävs för olika konfigurations ändringar
Olika konfigurations ändringar kräver olika synkroniseringsinställningar för att se till att ändringarna tillämpas korrekt för alla objekt.

- Fler objekt eller attribut som ska importeras från en käll katalog har lagts till (genom tillägg/ändring av regler för synkronisering)
    - En fullständig import krävs för anslutningen till käll katalogen
- Ändrade reglerna för synkronisering
    - En fullständig synkronisering krävs för anslutningen för de ändrade reglerna för synkronisering
- Filtrering av ändrade [filter](how-to-connect-sync-configure-filtering.md) så att ett annat antal objekt ska tas med
    - En fullständig import krävs för anslutningen för varje AD-anslutning, om du inte använder attributbaserade filtrering baserat på attribut som redan har importer ATS till Synkroniseringsmotorn

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>Anpassa en synkronisering kör rätt kombination av delta och fullständig synkronisering
För att undvika att köra en fullständig synkronisering kan du markera vissa kopplingar för att köra ett fullständigt steg med följande cmdletar.

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

Exempel: om du har gjort ändringar i synkroniseringsregeln för koppling "AD-skog A" som inte kräver att några nya attribut importeras kör du följande cmdlets för att köra en delta-synkroniseringsanslutning som också gjorde ett fullständigt synkroniserings steg för den anslutningen.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

Exempel: om du har ändrat synkroniseringsregeln för Connector "AD-skog A" så att de nu kräver att ett nytt attribut importeras, så kör du följande cmdlets för att köra en delta-synkroniseringsanslutning som också gjorde en fullständig import, fullständig synkronisering för den anslutningen.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>Stoppa Scheduler
Om Scheduler för närvarande kör en cykel för synkronisering kan du behöva stoppa den. Om du till exempel startar installations guiden och får det här felet:

![Skärm bild visas det går inte att ändra konfigurations fel meddelandet.](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

När en synkroniseringsanslutning körs kan du inte göra konfigurations ändringar. Du kan vänta tills Scheduler har avslutat processen, men du kan även stoppa den så att du kan göra ändringarna direkt. Att stoppa den aktuella cykeln är inte skadlig och väntande ändringar bearbetas med nästa körning.

1. Börja med att instruera Scheduler att stoppa den aktuella cykeln med PowerShell-cmdleten `Stop-ADSyncSyncCycle` .
2. Om du använder en version före 1.1.281 stoppar Scheduler inte den aktuella anslutningen från den aktuella aktiviteten. Utför följande åtgärder för att tvinga kopplingen att stoppa:

   ![Skärm bild som visar Synchronization Service Manager med valda kopplingar och en aktiv koppling är markerad med åtgärden stoppa markerad.](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)

   * Starta **synkroniseringstjänsten** från Start-menyn. Gå till **kopplingar**, markera kopplingen med det tillstånd som **körs** och välj **stoppa** från åtgärderna.

Scheduler är fortfarande aktiv och startar igen vid nästa tillfälle.

## <a name="custom-scheduler"></a>Anpassad Schemaläggaren
Cmdletarna som beskrivs i det här avsnittet är endast tillgängliga i build- [1.1.130.0](reference-connect-version-history.md) och senare.

Om den inbyggda Schemaläggaren inte uppfyller dina krav kan du schemalägga anslutningarna med hjälp av PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Du kan starta en profil för en koppling på det här sättet:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

De namn som ska användas för [anslutnings namn](how-to-connect-sync-service-manager-ui-connectors.md) och [namn på körnings profiler](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) finns i [Synchronization Service Manager användar gränssnittet](how-to-connect-sync-service-manager-ui.md).

![Anropa körnings profil](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

`Invoke-ADSyncRunProfile`Cmdleten är synkron, det vill säga den returnerar inte kontrollen förrän anslutningen har slutfört åtgärden, antingen korrekt eller med ett fel.

När du schemalägger dina anslutningar är rekommendationen att schemalägga dem i följande ordning:

1. (Fullständig/delta) Importera från lokala kataloger, till exempel Active Directory
2. (Fullständig/delta) Importera från Azure AD
3. (Fullständig/delta) Synkronisering från lokala kataloger, till exempel Active Directory
4. (Fullständig/delta) Synkronisering från Azure AD
5. Exportera till Azure AD
6. Exportera till lokala kataloger, till exempel Active Directory

Den här ordningen är hur den inbyggda Schemaläggaren kör anslutningarna.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Du kan också övervaka Synkroniseringsmotorn för att se om den är upptagen eller inaktiv. Den här cmdleten returnerar ett tomt resultat om Synkroniseringsmotorn är inaktiv och inte kör en anslutning. Om en koppling körs returneras namnet på anslutningen.

```
Get-ADSyncConnectorRunStatus
```

![Körnings status för koppling](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
I bilden ovan är den första raden från ett tillstånd där Synkroniseringsmotorn är inaktiv. Den andra raden från när Azure AD-kopplingen körs.

## <a name="scheduler-and-installation-wizard"></a>Guiden Schemaläggaren och installation
Om du startar installations guiden pausas Scheduler tillfälligt. Det här problemet beror på att du gör konfigurations ändringar och att dessa inställningar inte kan tillämpas om Synkroniseringsmotorn körs aktivt. Låt därför inte installations guiden vara öppen eftersom den stoppar Synkroniseringsmotorn från att utföra några åtgärder för synkronisering.

## <a name="next-steps"></a>Nästa steg
Läs mer om [Azure AD Connect Sync](how-to-connect-sync-whatis.md) -konfigurationen.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
