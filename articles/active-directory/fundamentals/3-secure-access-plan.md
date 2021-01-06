---
title: Skapa ett säkerhets plan för extern åtkomst till Azure Active Directory
description: Planera säkerheten för extern åtkomst till organisationens resurser..
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd04d24e0e46b053381858be037a009b1987f8e9
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97932528"
---
# <a name="3-create-a-security-plan-for-external-access"></a>3. skapa ett säkerhets plan för extern åtkomst 

Nu när du har [fastställt den önskade säkerhets position Security position för extern åtkomst](1-secure-access-posture.md) och [upptäckt ditt nuvarande samarbets tillstånd](2-secure-access-current-state.md), kan du skapa en extern användar säkerhets-och styrnings plan. 

Den här planen bör dokumentera följande:

* De program och andra resurser som ska grupperas för åtkomst.

* Lämpliga inloggnings villkor för externa användare. Dessa kan omfatta enhets tillstånd, inloggnings plats, krav på klient program och användar risk.

* Affärs principer på när du ska granska och ta bort åtkomst. 

* Användar populationer som ska grupperas och behandlas på samma sätt.

När dessa områden har dokumenterats kan du använda principer för identitets-och åtkomst hantering från Microsoft eller någon annan identitets leverantör (IdP) för att implementera den här planen.

## <a name="document-resources-to-be-grouped-for-access"></a>Dokument resurser som ska grupperas för åtkomst

Det finns flera sätt att gruppera resurser för åtkomst. 

* Microsoft Teams grupperar filer, konversations trådar och andra resurser på ett och samma ställe. Du bör formulera en extern åtkomst strategi för Microsoft Teams. Se [säker åtkomst till Teams, OneDrive och SharePoint](9-secure-access-teams-sharepoint.md).

* Med åtkomst paketen för rättighets hantering kan du skapa ett enda paket med program och andra resurser som du kan bevilja åtkomst till. 

* Principer för villkorlig åtkomst kan tillämpas på upp till 250 program med samma åtkomst krav.

Men du kommer att hantera åtkomst. du måste dokumentera vilka program som ska grupperas tillsammans. Överväganden bör innefatta:

* **Risk profil**. Vad är risken för verksamheten om en felaktig aktör har fått åtkomst till ett program? Överväg att koda varje program som hög, medel eller låg risk. Var försiktig med att gruppera program med hög risk med låg risk. 

   * Dokument program som aldrig ska delas med externa användare.

* **Ramverk för efterlevnad**. Vad händer om det krävs ett program för att uppfylla eventuella ramverk för efterlevnad? Vilka är kraven för åtkomst och granskning?

* **Program för vissa jobb roller eller avdelningar**. Finns det program som ska grupperas eftersom alla användare i en speciell jobb roll eller avdelning behöver åtkomst?

* **Program som fokuserar på samarbete**. Vilka samarbets program bör externa användare kunna komma åt? Microsoft Teams och SharePoint kan behöva nås. För produktivitets program i Office 365, som Word och Excel, får externa användare sina egna licenser eller behöver du licens för dem och ge åtkomst?

Dokumentera följande för varje gruppering av program och resurser som du vill göra tillgängliga för externa användare:

* Ett beskrivande namn för gruppen, till exempel *High_Risk_External_Access_Finance*. 

* Fullständig lista över alla program och resurser i gruppen.

* Program-och resurs ägare och kontakt information.

* Om åtkomsten styrs av eller delegeras till företags ägaren.

* Alla krav, till exempel för att slutföra en bakgrunds kontroll eller en utbildning, för åtkomst.

* Eventuella krav på efterlevnad för åtkomst till resurserna.

* Eventuella ytterligare utmaningar, till exempel kräver Multi-Factor Authentication för särskilda resurser.

* Hur ofta åtkomst ska granskas, av vem och var den ska dokumenteras.

Den här typen av styrnings plan kan och bör även slutföras för intern åtkomst.

## <a name="document-sign-in-conditions-for-external-users"></a>Dokumentera inloggnings villkor för externa användare.

Som en del av din plan måste du fastställa inloggnings kraven för dina externa användare när de får åtkomst till resurser. Inloggnings kraven baseras ofta på resursens risk profil och riskbedömningen av användarnas inloggning.

Inloggnings villkor konfigureras i [villkorlig åtkomst i Azure AD](../conditional-access/overview.md) och består av ett villkor och ett resultat. Till exempel när du behöver Multi-Factor Authentication

**Resurs riskbaserade inloggnings villkor.**

| Program risk profil| Överväg följande principer för att utlösa Multi-Factor Authentication |
| - |-|
| Låg risk| Kräv MFA för specifika program uppsättningar |
| Mellanrisk| Kräv MFA när andra risker föreligger |
| Hög risk| Kräv MFA alltid för externa användare |


Idag kan du [tillämpa Multi-Factor Authentication för B2B-användare i din klient organisation](https://docs.microsoft.com/azure/active-directory/external-identities/b2b-tutorial-require-mfa). 

**Användar-och enhets beroende inloggnings villkor**.

| Användare eller inloggnings risk| Tänk på följande principer |
| - | - |
| Enhet| Kräv kompatibla enheter |
| Mobilappar| Kräv godkända appar |
| Identitets skydd visar hög risk| Kräv att användare ändrar lösen ord |
| Nätverks plats| Kräv inloggning från ett särskilt IP-adressintervall till hög konfidentiella projekt |

I dag måste enheten vara registrerad eller ansluten till din klient om du vill använda enhets status som inmatade i en princip. 

Det går att använda [riskbaserade principer för identitets skydd](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-risk) . Men problem måste vara minimerade i användarens hem klient.

För [nätverks platser](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-location)kan du begränsa åtkomsten till alla IP-adressintervall som du äger. Du kan använda det här alternativet om du bara vill att externa partners ska komma åt ett program när de finns på plats i din organisation.

[Läs mer om principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).

## <a name="document-access-review-policies"></a>Dokumentera åtkomst gransknings principer

Dokumentera affärs principerna för när du behöver granska åtkomst till resurser och när du behöver ta bort konto åtkomsten för externa användare. Inmatningar till dessa beslut kan vara:

* Krav som beskrivs i eventuella ramverk för efterlevnad.

* Interna affärs principer och processer

* Användar beteende

Tänk på följande när dina principer kommer att anpassas efter dina behov:

* **Åtkomst granskningar för rättighets hantering**. Använd funktionerna i rättighets hantering för att

   * [Upphör automatiskt att gälla åtkomst paket](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-lifecycle-policy)och därmed externa användares åtkomst till de resurser som ingår.

   * Ange en [nödvändig gransknings frekvens](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-reviews-create) för åtkomst granskningar.

   * Om du använder [anslutna organisationer](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-organization) för att gruppera alla användare från en enda partner kan du schemalägga regelbundna granskningar med företags ägaren och partner representanten.

* **Microsoft 365 grupper**. Ange en [förfallo princip för grupp](https://docs.microsoft.com/microsoft-365/solutions/microsoft-365-groups-expiration-policy?view=o365-worldwide) för Microsoft 365 grupper som externa användare ska bjudas in till. 

* **Andra alternativ**. Om externa användare har åtkomst utanför behörighets hanterings paket eller Microsoft 365 grupper, ställer du in affärs processen för att granska när konton ska göras inaktiva eller borttagna. Ett exempel:

   * Ta bort inloggnings möjligheten för alla konton som inte är inloggade på i för 90 dagar.

   * Utvärdera åtkomst behoven och vidta åtgärder i slutet av varje projekt med externa användare.

 

## <a name="determine-your-access-control-methods"></a>Fastställ dina åtkomst kontroll metoder

Nu när du vet vad du vill kontrol lera åtkomsten till, hur dessa till gångar ska grupperas för vanlig åtkomst och nödvändiga inloggnings-och åtkomst gransknings principer, kan du välja hur du vill utföra ditt abonnemang. 

Vissa funktioner, till exempel [rättighets hantering](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview), är bara tillgängliga med en Azure AD Premium 2-licenser (P2). Microsoft 365 E5-och Office 365 E5-licenser är Azure AD P2-licenser. 

Andra kombinationer av Microsoft 365, Office 365 och Azure AD möjliggör också vissa funktioner för att hantera externa användare. Se [Information Protection](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance) för mer information.

> [!NOTE]
> Licenser per användare. Därför kan du ha specifika användare, inklusive administratörer och företags ägare, delegerad åtkomst kontroll på Azure AD P2-eller Microsoft 365 E5-nivå utan att aktivera dessa licenser för alla användare. Dina första 50 000 externa användare är kostnads fria. Om du inte aktiverar P2-licenser för andra interna användare kan de inte använda funktioner för hantering av rättigheter som åtkomst paket. 


## <a name="govern-access-with-azure-ad-p2-and-microsoft--office-365-e5"></a>Styra åtkomst med Azure AD P2 och Microsoft/Office 365 E5
Azure AD P2 och Microsoft 365 E5 har en komplett uppsättning verktyg för säkerhet och styrning.

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning-bolded-entries-are-preferred-methods"></a>Etablering, inloggning, granskning av åtkomst och avetablering. Fetstilta poster är önskade metoder

| Funktion| Etablera externa användare| Framtvinga inloggning reqs.| Granska åtkomst| Avetablera åtkomst |
| - | - | - | - | - |
| Azure AD B2B-samarbete| Bjud in via e-post, eng ång slö sen ord, självbetjäning| | **Regelbunden granskning per partner**| Ta bort konto<br>Begränsa inloggning |
| Hantering av rättigheter| **Lägga till användare via tilldelning eller självbetjänings åtkomst**| | Åtkomstgranskningar|**Utgångs-eller borttagnings paket**|
| Office 365-grupper| | | Granska grupp medlemskap| Förfallo datum eller borttagning av grupp<br> Ta bort formulär grupp |
| Azure AD-säkerhetsgrupper| | **Principer för villkorlig åtkomst** (Lägg till externa användare i säkerhets grupper efter behov)| |  |



 ### <a name="access-to-resources-bolded-entries-are-preferred-methods"></a>Åtkomst till resurser. Fetstilta poster är önskade metoder

|Funktion | Åtkomst till APP &-resurs| SharePoint & OneDrive-åtkomst| Åtkomst till team| E-& dokument säkerhet |
| - |-|-|-|-|
| Hantering av rättigheter| **Lägga till användare via tilldelning eller självbetjänings åtkomst**| **Åtkomstpaket**| **Åtkomstpaket**|  |
| Office 365-grupp| | Åtkomst till-platser (och tillhör ande innehåll) som ingår i gruppen| Åtkomst till team (och tillhör ande innehåll) som ingår i gruppen|  |
| Känslighetsetiketter| | **Manuellt och klassificera och begränsa åtkomst automatiskt**| **Manuellt och klassificera och begränsa åtkomst automatiskt**| **Manuellt och klassificera och begränsa åtkomst automatiskt** |
| Azure AD-säkerhetsgrupper| **Principer för villkorlig åtkomst för åtkomst som inte ingår i åtkomst paket**| | |  |


### <a name="entitlement-management"></a>Hantering av rättigheter 

[Hanterings paket för rättighets hantering](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-create) möjliggör etablering och avetablering av åtkomst till grupper och team, program och SharePoint-webbplatser. Du kan definiera vilka anslutna organisationer som får åtkomst, om självbetjänings begär Anden tillåts och vilka arbets flöden för godkännande som krävs (om det finns några) för att bevilja åtkomst. För att säkerställa att åtkomsten inte hålls runt längre än nödvändigt kan du definiera förfallo principer och åtkomst granskningar för varje Access-paket. 

 

## <a name="govern-access-with-azure-ad-p1-and-microsoft--office-365-e3"></a>Styra åtkomst med Azure AD P1 och Microsoft/Office 365 E3
Du kan få robusta styrningar med Azure AD P1 och Microsoft 365 E3

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning"></a>Etablering, inloggning, granskning av åtkomst och avetablering


|Funktion | Etablera externa användare| Framtvinga inloggnings krav| Granska åtkomst| Avetablera åtkomst |
| - |-|-|-|-|
| Azure AD B2B-samarbete| **Bjud in via e-post, eng ång slö sen ord, självbetjäning**| Direct B2B-Federation| **Regelbunden granskning per partner**| Ta bort konto<br>Begränsa inloggning |
| Microsoft-eller Office 365-grupper| | | | Förfallo datum eller borttagning av grupp.<br>Borttagning från grupp. |
| Säkerhetsgrupper| | **Lägg till externa användare i säkerhets grupper (org, team, projekt osv.)**| |  |
| Principer för villkorlig åtkomst| | **Logga in principer för villkorlig åtkomst för externa användare**| |  |


 ### <a name="access-to-resources"></a>Åtkomst till resurser.

|Funktion | Åtkomst till APP &-resurs| SharePoint & OneDrive-åtkomst| Åtkomst till team| E-& dokument säkerhet |
| - |-|-|-|-|
| Microsoft-eller Office 365-grupper| | **Åtkomst till de-platser som ingår i gruppen (och tillhör ande innehåll)**|**Åtkomst till team som ingår i Microsoft 365s grupp (och tillhör ande innehåll)**|  |
| Känslighetsetiketter| | Klassificera och begränsa åtkomst manuellt| Klassificera och begränsa åtkomst manuellt.| Klassificera manuellt för att begränsa och kryptera |
| Villkorliga åtkomstprinciper| Principer för villkorlig åtkomst för åtkomst kontroll| | |  |
| Ytterligare metoder| | Begränsa åtkomsten till SharePoint-webbplatser i detalj med säkerhets grupper.<br>Tillåt inte direkt delning.| **Begränsa externa inbjudningar inifrån team**|  |


### <a name="next-steps"></a>Nästa steg

Se följande artiklar om hur du skyddar extern åtkomst till resurser. Vi rekommenderar att du vidtar åtgärder i den angivna ordningen.

1. [Bestäm din säkerhets position för extern åtkomst](1-secure-access-posture.md)

2. [Identifiera ditt nuvarande tillstånd](2-secure-access-current-state.md)

3. [Skapa en styrnings plan](3-secure-access-plan.md) (du är här.)

4. [Använda grupper för säkerhet](4-secure-access-groups.md)

5. [Över gång till Azure AD B2B](5-secure-access-b2b.md)

6. [Säker åtkomst med hantering av rättigheter](6-secure-access-entitlement-managment.md)

7. [Säker åtkomst med principer för villkorlig åtkomst](7-secure-access-conditional-access.md)

8. [Säker åtkomst med känslighets etiketter](8-secure-access-sensitivity-labels.md)

9. [Säker åtkomst till Microsoft Teams, OneDrive och SharePoint](9-secure-access-teams-sharepoint.md)