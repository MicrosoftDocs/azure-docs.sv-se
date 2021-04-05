---
title: Avisering om Azure AD Connect Health-hälso tjänst data är inte uppdaterade | Microsoft Docs
description: I det här dokumentet beskrivs orsaken till aviseringen om att hälso tjänst data inte är uppdaterade och hur du felsöker den.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00518eb91e57efaacb7abc63b6ad4531619be2ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012877"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Aviseringen för hälso tjänst informationen är inte uppdaterad

## <a name="overview"></a>Översikt

Agenterna på de lokala datorerna som Azure AD Connect Health övervakar regelbundet laddar upp data till Azure AD Connect Health-tjänsten. Om tjänsten inte tar emot data från en agent, kommer den information som portalen presenterar vara inaktuell. För att markera problemet kommer tjänsten att höja **hälso tillstånds tjänstens data är inte uppdaterade** . Den här aviseringen skapas när tjänsten inte har tagit emot fullständiga data under de senaste två timmarna.  

- **Varnings** status varningen utlöses om Hälsotjänst har tagit emot endast **partiella** data typer som skickats från servern under de senaste två timmarna. Varnings status varningen utlöser inte e-postaviseringar till konfigurerade mottagare. 
- **Fel** status varning utlöses om Hälsotjänst inte har tagit emot några data typer från servern under de senaste två timmarna. Aviseringen om fel status utlöser e-postaviseringar till konfigurerade mottagare.

Tjänsten hämtar data från agenter som körs på lokala datorer, beroende på tjänst typen. I följande tabell visas de agenter som körs på datorn, vad de gör och de data typer som tjänsten genererar. I vissa fall finns det flera tjänster som är inblandade i processen, så alla kan vara orsaken. 

## <a name="understanding-the-alert"></a>Förstå aviseringen

Bladet **aviserings information** visar när aviseringen inträffade och senast upptäcktes. En bakgrunds process som körs varannan timme genererar och utvärderar aviseringen igen. I följande exempel inträffade den inledande aviseringen på 03/10 kl. 9:59. Aviseringen fanns fortfarande på 03/12 kl. 10:00 när aviseringen utvärderades igen. Bladet innehåller också information om när Hälsotjänst senast fick en viss datatyp. 
 
 ![Azure AD Connect Health aviserings information](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Följande tabell mappar tjänst typer till motsvarande obligatoriska data typer:

| Typ av tjänst | Agent (Windows-tjänstens namn) | Syfte | Datatyp genererad  |
| --- | --- | --- | --- |  
| Azure AD Connect (synkronisering) | Azure AD Connect Health Sync Insights Service | Samla in AAD Connect-Specific information (kopplingar, regler för synkronisering osv.) | -AadSyncService-SynchronizationRules <br />  -AadSyncService-Connectors <br /> -AadSyncService-GlobalConfigurations  <br />  -AadSyncService-RunProfileResults <br /> -AadSyncService-ServiceConfigurations <br /> -AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring Service | Samla in AAD Connect-/regionsspecifika prestanda räknare, ETW-spår, filer | Prestanda räknare |
| AD DS | Azure AD Connect Health AD DS Insights Service | Utför syntetiska tester, samla in information om topologi, metadata för replikering |  -Lägger till-TopologyInfo-JSON <br /> -Common-TestData-JSON (skapar test resultaten)   | 
|  | Azure AD Connect Health AD DS Monitoring Service | Samla in tillägg – vissa prestanda räknare, ETW-spår, filer | – Prestanda räknare  <br /> -Common-TestData-JSON (laddar upp test resultaten)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics Service | Utför syntetiska tester | TestResult (skapar test resultaten) | 
| | Azure AD Connect Health AD FS Insights Service  | Samla in användnings statistik i ADFS | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring Service | Samla in ADFS-/regionsspecifika prestanda räknare, ETW-spår, filer | TestResult (överför test resultaten) |

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar 

De steg som krävs för att diagnostisera problemet anges nedan. Den första är en uppsättning grundläggande kontroller som är gemensamma för alla tjänst typer. 

> [!IMPORTANT] 
> Den här aviseringen följer [principen](reference-connect-health-user-privacy.md#data-retention-policy) för att ansluta hälso data

* Kontrollera att de senaste versionerna av agenterna är installerade. Visa [versionshistoriken](reference-connect-health-version-history.md). 
* Se till att Azure AD Connect Health-agenttjänsterna är **igång** i datorn. Till exempel ska Connect Health för AD FS ha tre tjänster.
  ![Verifiera Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Se till att gå över och uppfylla [avsnittet krav](how-to-connect-health-agent-install.md#requirements).
* Använd [test anslutnings verktyget](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) för att identifiera anslutnings problem.
* Följ dessa [konfigurations steg](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)om du har en http-proxy. 


## <a name="next-steps"></a>Nästa steg
Om något av ovanstående steg har identifierat ett problem, korrigerar du det och väntar på att aviseringen ska lösas. Aviserings bakgrunds processen körs var 2: e timme, så det tar upp till 2 timmar att lösa aviseringen. 

* [Azure AD Connect Health princip för data bevarande](reference-connect-health-user-privacy.md#data-retention-policy)
* [Vanliga frågor och svar om Azure AD Connect Health](reference-connect-health-faq.md)
