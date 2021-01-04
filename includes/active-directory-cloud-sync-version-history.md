---
ms.openlocfilehash: 77866fcb72515d5167d9f06b7d9ad91c59bc7945
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2020
ms.locfileid: "97700587"
---
Den här artikeln innehåller versioner och funktioner i Azure Active Directory Connect etablerings agent som har släppts. Azure AD-teamet uppdaterar regelbundet etablerings agenten med nya funktioner och funktioner. Etablerings agenten uppdateras automatiskt när en ny version släpps. 

Microsoft tillhandahåller direkt support för den senaste agent versionen och en version.

## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Versionsstatus

23 november 2020: släpps för hämtning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* Stöd för [gMSA](../articles/active-directory/cloud-provisioning/how-to-prerequisites.md#group-managed-service-accounts)
* Stöd för grupper upp till en storlek som är mindre än 1500 medlemmar under stegvis eller delta-synkronisering. Detta gäller när du använder grupp definitions filter
* Stöd för stora grupper med medlems storlek upp till 15 000
* Förbättringar av inledande synkronisering
* Avancerad utförlig loggning
* Introduktion av [AADCloudSyncTools PowerShell-modulen](../articles/active-directory/cloud-provisioning/reference-powershell.md)
* Fasta begränsningar för att tillåta att agenten installeras på en icke-engelsk server
* Stöd för PHS-filtrering för objekt inom omfånget (ursprungligen synkroniserade vi lösen ords hashar för alla objekt)
* Åtgärdat problem med minnes läckor i agenten
* Förbättrade etablerings loggar
* Stöd för konfigurering av [LDAP-anslutningstimeout](../articles/active-directory/cloud-provisioning/how-to-manage-registry-options.md#configure-ldap-connection-timeout) 
* Stöd för att konfigurera [referens jaga](../articles/active-directory/cloud-provisioning/how-to-manage-registry-options.md#configure-referral-chasing) 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Versionsstatus

4 december 2019: släpps för hämtning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* Innehåller stöd för [Azure AD Connect moln etablering](../articles/active-directory/cloud-provisioning/what-is-cloud-provisioning.md) för att synkronisera användare, kontakt och grupp data från lokala Active Directory till Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Versionsstatus

9 september 2019: lanseras för automatisk uppdatering

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* Möjlighet att konfigurera ytterligare spårning och loggning för fel sökning av etablerings agent problem
* Möjlighet att bara hämta de Azure AD-attribut som konfigureras i mappningen för att förbättra synkroniseringens prestanda

### <a name="fixed-issues"></a>Åtgärdade problem

* Ett fel har åtgärd ATS där agenten övergick i ett tillstånd som inte svarar om det uppstod problem med anslutnings fel i Azure AD
* En bugg har åtgärd ATS som orsakade problem när binära data lästes från Azure Active Directory
* Ett fel har åtgärd ATS där agenten inte kunde förnya förtroende med moln hybrid identitets tjänsten

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Versionsstatus

23 januari 2019: släpps för hämtning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* Förbättringar för etablerings agenten och anslutnings arkitekturen ger bättre prestanda, stabilitet och tillförlitlighet 
* Förenklad konfiguration av konfigurations agenten med hjälp av UI-driven installations guide 
* Stöd har lagts till för automatiska agent uppdateringar


