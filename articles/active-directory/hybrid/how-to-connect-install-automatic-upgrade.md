---
title: 'Azure AD Connect: automatisk uppgradering | Microsoft Docs'
description: I det här avsnittet beskrivs den inbyggda funktionen för automatisk uppgradering i Azure AD Connect Sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8dcc8766b21551f3cd62289805fe735ef0f333
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317624"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: Automatisk uppgradering
Den här funktionen introducerades med build- [1.1.105.0 (lanserades februari 2016)](reference-connect-version-history.md).  Funktionen uppdaterades i [build-1.1.561](reference-connect-version-history.md) och stöder nu ytterligare scenarier som tidigare inte stöds.

## <a name="overview"></a>Översikt
Att se till att din Azure AD Connect installation alltid är uppdaterad har aldrig varit enklare med funktionen för **automatisk uppgradering** . Den här funktionen är aktive rad som standard för Express installationer och DirSync-uppgraderingar. När en ny version släpps, uppgraderas installationen automatiskt.
Automatisk uppgradering är aktiverat som standard för följande:

* Express inställningar installation och DirSync-uppgraderingar.
* Använda SQL Express-LocalDB, som är de snabb inställningar som alltid används. DirSync med SQL Express använder också LocalDB.
* AD-kontot är standard MSOL_s kontot som skapats av Express inställningar och DirSync.
* Har färre än 100 000 objekt i metaversum.

Det aktuella läget för automatisk uppgradering kan visas med PowerShell-cmdleten `Get-ADSyncAutoUpgrade` . Det har följande tillstånd:

| Tillstånd | Kommentar |
| --- | --- |
| Enabled |Automatisk uppgradering har Aktiver ATS. |
| Inaktiverad |Anges endast av systemet. Systemet är **för närvarande inte** behörigt att ta emot automatiska uppgraderingar. |
| Inaktiverad |Automatisk uppgradering har inaktiverats. |

Du kan ändra mellan **aktiverat** och **inaktiverat** med `Set-ADSyncAutoUpgrade` . Endast systemet bör ställa in tillståndet **inaktiverat**.  Innan 1.1.750.0-cmdlet: Set-ADSyncAutoUpgrade en skulle blockera automatisk uppgradering om tillståndet för automatisk uppgradering var inaktive rad. Den här funktionen har nu ändrats så att den inte blockerar autouppgradering.

Automatisk uppgradering använder Azure AD Connect Health för uppgraderings infrastrukturen. För att automatisk uppgradering ska fungera kontrollerar du att du har öppnat URL: erna i proxyservern för **Azure AD Connect Health** som dokumenteras i [URL: er för Office 365 och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Om **Synchronization Service Manager** gränssnittet körs på servern pausas uppgraderingen tills användar gränssnittet stängs.

## <a name="troubleshooting"></a>Felsökning
Om din Connect-installation inte uppgraderas som förväntat följer du de här stegen för att ta reda på vad som kan vara fel.

Först bör du inte förvänta dig att den automatiska uppgraderingen ska försöka utföras första dagen som en ny version släpps. Det finns en avsiktlig slumpmässighet innan en uppgradering görs, så du får ingen varning om att installationen inte uppgraderas direkt.

Om du tror att något inte är rätt kan du först köra `Get-ADSyncAutoUpgrade` för att säkerställa att automatisk uppgradering är aktiverat.

Om tillståndet har pausats kan du använda `Get-ADSyncAutoUpgrade -Detail` för att Visa orsaken.  Stopp orsaken kan innehålla valfritt sträng värde, men innehåller vanligt vis strängvärdet för UpgradeResult, det vill säga `UpgradeNotSupportedNonLocalDbInstall` eller `UpgradeAbortedAdSyncExeInUse` .  Ett sammansatt värde kan också returneras, till exempel `UpgradeFailedRollbackSuccess-GetPasswordHashSyncStateFailed` .

Det är också möjligt att få ett resultat som inte är en UpgradeResult, t. ex. "AADHealthEndpointNotDefined" eller "DirSyncInPlaceUpgradeNonLocalDb".

Kontrol lera sedan att du har öppnat de nödvändiga URL: erna i proxyservern eller brand väggen. Automatisk uppdatering använder Azure AD Connect Health enligt beskrivningen i [översikten](#overview). Om du använder en proxyserver kontrollerar du att hälsan har kon figurer ATS för att använda en [proxyserver](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Testa också [hälso anslutningen](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) till Azure AD.

När anslutningen till Azure AD har verifierats är det dags att titta på händelse loggen. Starta logg boken och leta i **program** händelse loggen. Lägg till ett EventLog-filter för käll **Azure AD Connect uppgraderingen** och händelse-ID-intervallet **300-399**.  
![Skärm bild som visar fönstret "Filtrera aktuell logg" med "händelse källor" och händelse-ID: n för "inkludera/exkludera" markerat.](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Du kan nu se händelse loggen som är associerad med status för automatisk uppgradering.  
![EventLog-filter för automatisk uppgradering](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

Resultat koden har ett prefix med en översikt över statusen.

| Resultat kod prefix | Beskrivning |
| --- | --- |
| Klart |Installationen har uppgraderats. |
| UpgradeAborted |Ett tillfälligt tillstånd stoppade uppgraderingen. Ett nytt försök görs och förväntat är att det lyckas senare. |
| UpgradeNotSupported |Systemet har en konfiguration som hindrar systemet från att uppgraderas automatiskt. Det görs ett nytt försök att se om statusen ändras, men förväntat är att systemet måste uppgraderas manuellt. |

Här är en lista över de vanligaste meddelanden som du hittar. Ingen lista visas, men resultat meddelandet bör vara avmarkerat med det som problemet är.

| Resultat meddelande | Beskrivning |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Det gick inte att skriva till registret. |
| UpgradeAbortedInsufficientDatabasePermissions |Den inbyggda gruppen Administratörer har inte behörighet till databasen. Uppgradera manuellt till den senaste versionen av Azure AD Connect för att åtgärda problemet. |
| UpgradeAbortedInsufficientDiskSpace |Det finns inte tillräckligt med disk utrymme för att stödja en uppgradering. |
| UpgradeAbortedSecurityGroupsNotPresent |Det gick inte att hitta och lösa alla säkerhets grupper som används av Synkroniseringsmotorn. |
| UpgradeAbortedServiceCanNotBeStarted |Det gick inte att starta NT-tjänsten **Microsoft Azure AD Sync** . |
| UpgradeAbortedServiceCanNotBeStopped |Det gick inte att stoppa NT-tjänstens **Microsoft Azure AD Sync** . |
| UpgradeAbortedServiceIsNotRunning |NT-tjänsten **Microsoft Azure AD Sync** körs inte. |
| UpgradeAbortedSyncCycleDisabled |Alternativet SyncCycle i [Scheduler](how-to-connect-sync-feature-scheduler.md) har inaktiverats. |
| UpgradeAbortedSyncExeInUse |[Synchronization Service Manager-användargränssnittet](how-to-connect-sync-service-manager-ui.md) är öppet på servern. |
| UpgradeAbortedSyncOrConfigurationInProgress |Installations guiden körs eller också har synkroniseringen schemalagts utanför Scheduler. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedCustomizedSyncRules |Du har lagt till dina egna anpassade regler i konfigurationen. |
| UpgradeNotSupportedInvalidPersistedState |Installationen är inte en snabb inställning eller en DirSync-uppgradering. |
| UpgradeNotSupportedNonLocalDbInstall |Du använder inte en SQL Server Express LocalDB-databas. |
|UpgradeNotSupportedLocalDbSizeExceeded|Den lokala databas storleken är större än eller lika med 8 GB|
|UpgradeNotSupportedAADHealthUploadDisabled|Hälso data överföringar har inaktiverats från portalen|

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
