---
title: Skydda Microsoft 365 från lokala attacker
description: Vägledning om hur du ser till att en lokal attack inte påverkar Microsoft 365.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9913132ca6ff3ce2840b55aa771f7bd9be2404ea
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650982"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Skydda Microsoft 365 från lokala attacker

Många kunder ansluter sina privata företags nätverk till Microsoft 365 att dra nytta av sina användare, enheter och program. Dessa privata nätverk kan dock komprometteras på många väl dokumenterade sätt. Eftersom Microsoft 365 fungerar som en sorts nerv system för många organisationer är det viktigt att skydda det från komprometterad lokal infrastruktur.

Den här artikeln visar hur du konfigurerar dina system för att skydda din Microsoft 365 moln miljö från lokala kompromisser. Vi fokuserar främst på: 

- Konfigurations inställningar för Azure Active Directory (Azure AD).
- Hur Azure AD-klienter kan anslutas till lokala system på ett säkert sätt.
- De kompromisser som krävs för att hantera dina system på ett sätt som skyddar dina moln system från lokala kompromisser.

Vi rekommenderar starkt att du implementerar den här vägledningen för att skydda din Microsoft 365 moln miljö.
> [!NOTE]
> Den här artikeln publicerades ursprungligen som ett blogg inlägg. Den har flyttats till den aktuella platsen för Longevity och underhåll.
>
> Om du vill skapa en offline-version av den här artikeln använder du webbläsarens utskrifts-till-PDF-funktion. Kom tillbaka hit ofta för uppdateringar.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Primära hot vektorer från komprometterade lokala miljöer


Din Microsoft 365 moln miljö har nytta av en omfattande övervaknings-och säkerhets infrastruktur. Med hjälp av maskin inlärning och Human Intelligence, Microsoft 365 utseende över hela världen. Den kan snabbt identifiera attacker och göra det möjligt att konfigurera om nästan i real tid. 

I hybrid distributioner som ansluter lokalt-infrastruktur till Microsoft 365 kan många organisationer delegera förtroende till lokala komponenter för kritiska autentiserings-och katalog objekt tillstånds hanterings beslut.
Om den lokala miljön har komprometterats blir dessa förtroende relationer tyvärr en angripares möjligheter att kompromettera din Microsoft 365s miljö.

De två primära hot vektorerna är *Federations förtroende relationer* och *konto synkronisering.* Båda vektorerna kan ge en angripare administrativ åtkomst till molnet.

* **Federerade förtroende relationer**, till exempel SAML-autentisering, används för att autentisera till Microsoft 365 via din lokala identitets infrastruktur. Om ett SAML-certifikat för tokensignering skadas tillåter federationen att alla som har certifikatet att personifiera en användare i molnet. *Vi rekommenderar att du inaktiverar Federations förtroende relationer för autentisering till Microsoft 365 när det är möjligt.*

* **Konto synkronisering** kan användas för att ändra privilegierade användare (inklusive deras autentiseringsuppgifter) eller grupper som har administratörs behörighet i Microsoft 365. *Vi rekommenderar att du ser till att synkroniserade objekt inte har några behörigheter utöver en användare i Microsoft 365,* antingen direkt eller genom att ingå i betrodda roller eller grupper. Se till att dessa objekt inte har någon direkt eller kapslad tilldelning i betrodda moln roller eller grupper.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Skydda Microsoft 365 från lokal kompromiss


För att adressera de hot vektorer som beskrivs tidigare rekommenderar vi att du följer de principer som illustreras i följande diagram:

![Referens arkitektur för att skydda Microsoft 365.](media/protect-m365/protect-m365-principles.png)

1. **Isolera Microsoft 365 administratörs konton fullständigt.** De bör vara:

    * Hanterat i Azure AD.

     * Autentiseras med hjälp av multifaktorautentisering.

     *  Skyddad av villkorlig åtkomst för Azure AD.

     *  Endast via Azure-hanterade arbets stationer.

    Dessa administratörs konton är begränsade – Använd-konton. *Inga lokala konton måste ha administratörs behörighet i Microsoft 365.* 

    Mer information finns i [Översikt över Microsoft 365 administratörs roller](/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide). Se även [roller för Microsoft 365 i Azure AD](../roles/m365-workload-docs.md).

1. **Hantera enheter från Microsoft 365.** Använd Azure AD Join och molnbaserad hantering av mobila enheter (MDM) för att ta bort beroenden i din lokala infrastruktur för enhets hantering. Dessa beroenden kan kompromettera enhets-och säkerhets kontroller.

1. **Se till att inget lokalt konto har förhöjd behörighet att Microsoft 365.**
    Vissa konton har åtkomst till lokala program som kräver NTLM-, LDAP-eller Kerberos-autentisering. Dessa konton måste finnas i organisationens lokala identitets infrastruktur. Se till att dessa konton, inklusive tjänst konton, inte ingår i privilegierade moln roller eller grupper. Se också till att ändringar av dessa konton inte kan påverka moln miljöns integritet. Privilegie rad lokal program vara får inte kunna påverka Microsoft 365 privilegierade konton eller roller.

1. **Använd Azure AD Cloud Authentication** för att ta bort beroenden för dina lokala autentiseringsuppgifter. Använd alltid stark autentisering, t. ex. Windows Hello, FIDO, Microsoft Authenticator eller Azure AD multifaktor-autentisering.

## <a name="specific-security-recommendations"></a>Vissa säkerhets rekommendationer


I följande avsnitt finns information om hur du implementerar principerna som beskrivs ovan.

### <a name="isolate-privileged-identities"></a>Isolera privilegierade identiteter


I Azure AD är användare som har privilegierade roller, till exempel Administratörer, förtroende roten för att bygga och hantera resten av miljön. Implementera följande metoder för att minimera effekterna av en kompromiss.

* Använd endast moln konton för Azure AD och Microsoft 365 privilegierade roller.

* Distribuera [privilegierade åtkomst enheter](/security/compass/privileged-access-devices#device-roles-and-profiles) för privilegie rad åtkomst för att hantera Microsoft 365 och Azure AD.

*  Distribuera [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) för just-in-Time-åtkomst (JIT) till alla mänskliga konton som har privilegierade roller. Kräv stark autentisering för att aktivera roller.

* Ge administrativa roller som tillåter den [minsta behörighet som krävs för att utföra obligatoriska uppgifter](../roles/delegate-by-task.md).

* Om du vill aktivera en omfattande roll tilldelnings upplevelse som innehåller delegering och flera roller samtidigt kan du överväga att använda Azure AD-säkerhetsgrupper eller Microsoft 365 grupper. Dessa grupper kallas gemensamt för *moln grupper*. [Aktivera även rollbaserad åtkomst kontroll](../roles/groups-assign-role.md). Du kan använda [administrativa enheter](../roles/administrative-units.md) för att begränsa rollens omfång till en del av organisationen.

* Distribuera [nöd åtkomst konton](../roles/security-emergency-access.md). Använd *inte* lokala lösen ords valv för att lagra autentiseringsuppgifter.

Mer information finns i [skydda privilegie rad åtkomst](/security/compass/overview). Se även [säker åtkomst praxis för administratörer i Azure AD](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Använd molnbaserad autentisering 

Autentiseringsuppgifterna är en primär angrepps vektor. Implementera följande metoder för att göra autentiseringsuppgifterna säkrare:

* [Distribuera lösenordsskyddad autentisering](../authentication/howto-authentication-passwordless-deployment.md). Minska användningen av lösen ord så mycket som möjligt genom att distribuera lösen ords färre autentiseringsuppgifter. Autentiseringsuppgifterna hanteras och verifieras internt i molnet. Välj bland dessa autentiseringsmetoder:

   * [Windows Hello för företag](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Microsoft Authenticator-appen](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2 säkerhets nycklar](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Distribuera multifaktorautentisering](../authentication/howto-mfa-getstarted.md). Etablera [flera starka autentiseringsuppgifter med Azure AD](../fundamentals/resilience-in-credentials.md)multifaktor-autentisering. På så sätt kräver åtkomst till moln resurser en autentiseringsuppgift som hanteras i Azure AD förutom ett lokalt lösen ord som kan ändras. Mer information finns i [skapa en elastisk strategi för åtkomst kontroll hantering med hjälp av Azure AD](./resilience-overview.md).

### <a name="limitations-and-tradeoffs"></a>Begränsningar och kompromisser

* Lösen ords hantering för Hybrid konton kräver hybrid komponenter som lösen ords skydds agenter och för tillbakaskrivning av lösen ord. Om den lokala infrastrukturen har komprometterats kan angripare styra de datorer där agenterna finns. Säkerhets problemet äventyrar inte moln infrastrukturen. Men dina moln konton skyddar inte dessa komponenter från lokala kompromisser.

*  Lokala konton som synkroniseras från Active Directory markeras att aldrig upphöra att gälla i Azure AD. Den här inställningen begränsas ofta av lokala Active Directory lösen ords inställningar. Om din lokala instans av Active Directory komprometteras och synkroniseringen är inaktive rad måste du dock ange alternativet [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md) för att tvinga lösen ords ändringar.

## <a name="provision-user-access-from-the-cloud"></a>Etablera användar åtkomst från molnet

*Etableringen* avser skapandet av användar konton och grupper i program eller identitets leverantörer.

![Diagram över etablerings arkitektur.](media/protect-m365/protect-m365-provision.png)

Vi rekommenderar följande etablerings metoder:

* **Etablera från molnets HR-appar till Azure AD**: den här etableringen gör det möjligt att isolera en lokal kompromiss, utan att störa din anslutning – överskrivning-cykel från dina moln-till Azure AD-appar.

* **Moln program**: där det är möjligt kan du distribuera [Azure AD App-etablering](../app-provisioning/user-provisioning.md) i stället för lokala etablerings lösningar. Den här metoden skyddar några av dina SaaS-appar (program vara som en tjänst) från att påverkas av skadliga hackare profiler i lokala överträdelser. 

* **Externa identiteter**: Använd [Azure AD B2B-samarbete](../external-identities/what-is-b2b.md).
    Den här metoden minskar beroendet av lokala konton för externt samarbete med partners, kunder och leverantörer. Utvärdera noga alla direkta federationer med andra identitets leverantörer. Vi rekommenderar att du begränsar B2B-gäst konton på följande sätt:

   *  Begränsa gäst åtkomsten till att bläddra bland grupper och andra egenskaper i katalogen. Använd de externa samarbets inställningarna för att begränsa gästernas möjlighet att läsa grupper som de inte är medlemmar i. 

    *   Blockera åtkomst till Azure Portal. Du kan göra sällsynta nödvändiga undantag.  Skapa en princip för villkorlig åtkomst som omfattar alla gäster och externa användare. [Implementera sedan en princip för att blockera åtkomst](../../role-based-access-control/conditional-access-azure-management.md). 

* **Frånkopplade skogar**: Använd [Azure AD Cloud-etablering](../cloud-sync/what-is-cloud-sync.md). Med den här metoden kan du ansluta till frånkopplade skogar, vilket eliminerar behovet av att upprätta anslutningar mellan olika skogar eller förtroenden, vilket kan leda till att en lokal överträdelse ökar. 
 
### <a name="limitations-and-tradeoffs"></a>Begränsningar och kompromisser

När du använder för att etablera hybrid konton använder Azure-AD-från-Cloud-HR-systemet den lokala synkroniseringen för att slutföra data flödet från Active Directory till Azure AD. Om synkronisering avbryts kommer nya medarbetar poster inte att vara tillgängliga i Azure AD.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Använd moln grupper för samarbete och åtkomst

Med moln grupper kan du frikoppla ditt samarbete och få åtkomst från din lokala infrastruktur.

* **Samarbete**: Använd Microsoft 365 grupper och Microsoft Teams för modern samarbete. Inaktivera lokala distributions listor och [Uppgradera distributions listor till Microsoft 365 grupper i Outlook](/office365/admin/manage/upgrade-distribution-lists?view=o365-worldwide).

* **Åtkomst**: Använd Azure AD-säkerhetsgrupper eller Microsoft 365 grupper för att ge åtkomst till program i Azure AD.
* **Office 365-licensiering**: Använd gruppbaserad licensiering för att etablera till Office 365 med hjälp av grupper med enbart moln. Med den här metoden frikopplas kontroll av grupp medlemskap från lokal infrastruktur.

Ägare av grupper som används för åtkomst bör betraktas som privilegierade identiteter för att undvika medlemskaps övertag Ande i en lokal kompromiss.
En övertag ande skulle omfatta direkt manipulering av grupp medlemskap lokalt eller manipulering av lokala attribut som kan påverka det dynamiska grupp medlemskapet i Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Hantera enheter från molnet


Använd Azure AD-funktioner för att hantera enheter på ett säkert sätt.

-   **Använd Windows 10-arbetsstationer**: [Distribuera Azure AD-anslutna](../devices/azureadjoin-plan.md) enheter med MDM-principer. Aktivera [Windows autopilot](/mem/autopilot/windows-autopilot) för en helt automatiserad etablerings upplevelse.

    -   Föråldrade datorer som kör Windows 8,1 och tidigare.

    -   Distribuera inte serverns OS-datorer som arbets stationer.

    -   Använd [Microsoft Intune](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/microsoft-intune) som auktoritets källa för alla enhets hanterings arbets belastningar.

-   [**Distribuera privilegierade åtkomst enheter**](/security/compass/privileged-access-devices#device-roles-and-profiles): Använd privilegie rad åtkomst för att hantera Microsoft 365 och Azure AD.

## <a name="workloads-applications-and-resources"></a>Arbets belastningar, program och resurser 

-   **Lokala datorer med enkel inloggning (SSO)** 

    Föråldra alla lokala Federations-och hanterings infrastrukturer för webb åtkomst. Konfigurera program för att använda Azure AD.  

-   **SaaS-och branschspecifika program som stöder moderna autentiseringsprotokoll** 

    [Använd Azure AD för SSO](../manage-apps/what-is-single-sign-on.md). De fler appar som du konfigurerar för att använda Azure AD för autentisering, desto mindre risk i en lokal kompromiss.


* **Äldre program** 

   * Du kan aktivera autentisering, auktorisering och fjärråtkomst till äldre program som inte stöder modern autentisering. Använd [Azure AD-programproxy](../manage-apps/application-proxy.md). Du kan också aktivera dem via en nätverks-eller program leverans styrenhets lösning med hjälp av [säkra hybrid åtkomst partner integrationer](../manage-apps/secure-hybrid-access.md).   

   * Välj en VPN-leverantör som stöder modern autentisering. Integrera dess autentisering med Azure AD. I en lokal kompromiss kan du använda Azure AD för att inaktivera eller blockera åtkomst genom att inaktivera VPN.

*  **Program-och arbets belastnings servrar**

   * Program eller resurser som krävde servrar kan migreras till Azure Infrastructure as a Service (IaaS). Använd [Azure AD Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) för att koppla från förtroende och beroendet på lokala instanser av Active Directory. Se till att virtuella nätverk som används för Azure AD DS inte har en anslutning till företags nätverk för att uppnå den här kopplingen.

   * Följ rikt linjerna för [nivåer av autentiseringsuppgifter](/security/compass/privileged-access-access-model#ADATM_BM). Program servrar betraktas vanligt vis som nivå 1-till gångar.

## <a name="conditional-access-policies"></a>Principer för villkorlig åtkomst

Använd villkorlig åtkomst för Azure AD för att tolka signaler och använda dem för att fatta beslut om autentisering. Mer information finns i [distributions planen för villkorlig åtkomst](../conditional-access/plan-conditional-access.md).

* Använd villkorlig åtkomst för att [blockera bakåtkompatibla autentiseringsprotokoll](../conditional-access/howto-conditional-access-policy-block-legacy.md) närhelst det är möjligt. Du kan dessutom inaktivera äldre autentiseringsprotokoll på program nivå genom att använda en programspecifik konfiguration.

   Mer information finns i [bakåtkompatibla autentiseringsprotokoll](../fundamentals/auth-sync-overview.md). Eller se information om [Exchange Online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) och [SharePoint Online](/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps).

* Implementera de rekommenderade [konfigurationerna för identitets-och enhets åtkomst](/microsoft-365/security/office-365-security/identity-access-policies?view=o365-worldwide).

* Om du använder en version av Azure AD som inte har villkorlig åtkomst kontrollerar du att du använder [standardinställningarna för Azure AD-säkerhet](../fundamentals/concept-fundamentals-security-defaults.md).

   Mer information om funktions licensiering i Azure AD finns i [pris guiden för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitor"></a>Monitor 

När du har konfigurerat din miljö för att skydda dina Microsoft 365 från en lokal kompromiss kan du [proaktivt övervaka](../reports-monitoring/overview-monitoring.md) miljön.
### <a name="scenarios-to-monitor"></a>Scenarier att övervaka

Övervaka följande viktiga scenarier, förutom eventuella scenarier som är speciella för din organisation. Till exempel bör du proaktivt övervaka åtkomsten till dina affärs kritiska program och resurser.

* **Misstänkt aktivitet** 

    Övervaka alla [Azure AD-risk händelser](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) för misstänkt aktivitet. [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) integreras internt med Azure Security Center.

    Definiera nätverket [med namnet platser](../reports-monitoring/quickstart-configure-named-locations.md) för att undvika störningar på platsbaserade signaler. 
*  **Aviseringar för beteende analys av användare och entiteter (UEBA)** 

    Använd UEBA för att få insikter om avvikelse identifiering.
    * Microsoft Cloud App Security (MCAS) tillhandahåller [UEBA i molnet](/cloud-app-security/tutorial-ueba).

    * Du kan [integrera lokala UEBA från Azure Advanced Threat Protection (ATP)](/defender-for-identity/install-step2). MCAS läser signaler från Azure AD Identity Protection. 

* **Aktivitet för nöd åtkomst konton** 

    Övervaka alla åtkomst som använder [konton för nöd åtkomst](../roles/security-emergency-access.md). Skapa aviseringar för utredningar. Denna övervakning måste innehålla: 

   * Inloggningar. 

   * Hantering av autentiseringsuppgifter. 

   * Eventuella uppdateringar av grupp medlemskap. 

   * Program tilldelningar. 
* **Privilegie rad roll aktivitet**

    Konfigurera och granska säkerhets [aviseringar som genererats av Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts).
    Övervaka direkt tilldelning av privilegierade roller utanför PIM genom att generera aviseringar när en användare tilldelas direkt.

* **Konfigurationer för hela Azure AD-klienten**

    Alla ändringar av konfigurationer för klient organisationen bör generera aviseringar i systemet. Ändringarna omfattar men är inte begränsade till:

  *  Uppdaterade anpassade domäner.  

  * Azure AD B2B ändras till allowlists och-blockeringslistor.

  * Azure AD B2B-ändringar av tillåtna identitets leverantörer (SAML Identity providers via direkt Federation eller sociala inloggningar).  

  * Villkorlig åtkomst eller risk princip ändringar. 

* **Objekt för program och tjänstens huvudnamn**
    
   * Nya program eller tjänst huvud namn som kan kräva principer för villkorlig åtkomst. 

   * Autentiseringsuppgifter har lagts till i tjänstens huvud namn.
   * Aktivitet för program medgivande. 

* **Anpassade roller**
   * Uppdateringar av anpassade roll definitioner. 

   * Nyligen skapade anpassade roller. 

### <a name="log-management"></a>Logg hantering

Definiera en logg lagring och bevarande strategi, utformning och implementering för att under lätta en enhetlig verktygs uppsättning. Du kan till exempel betrakta SIEM-system (Security information and Event Management) som Azure Sentinel, vanliga frågor och undersöknings-och data utredning spel böcker.

* **Azure AD-loggar**: mata in genererade loggar och signaler genom att konsekvent följa bästa praxis för inställningar som diagnostik, logg kvarhållning och Siem inmatning. 

    Logg strategin måste omfatta följande Azure AD-loggar:
   * Inloggningsaktivitet 

   * Granskningsloggar 

   * Riskhändelser 

    Azure AD tillhandahåller [Azure Monitor integrering](../reports-monitoring/concept-activity-logs-azure-monitor.md) för inloggnings aktivitets loggen och gransknings loggarna. Risk händelser kan matas in via [Microsoft Graph API](/graph/api/resources/identityriskevent). Du kan [strömma Azure AD-loggar till Azure Monitor loggar](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Säkerhets loggar för Hybrid infrastruktur-operativ system**: alla operativ system loggar för Hybrid identiteter bör arkiveras och övervakas noggrant som nivå 0-system, på grund av följderna av ytan. Inkludera följande element: 

   *  Azure AD Connect. [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md) måste distribueras för att övervaka synkronisering av identiteter.

   *  Programproxy-agenter 


   * Agenter för tillbakaskrivning av lösen ord 

   * Password Protection gateway-datorer  

   * Nätverks princip servrar (NPSs) som har Azure AD multiverse-autentisering med RADIUS-tillägg 

## <a name="next-steps"></a>Nästa steg
* [Bygg återhämtning till identitets-och åtkomst hantering med hjälp av Azure AD](resilience-overview.md)

* [Säker extern åtkomst till resurser](secure-external-access-resources.md) 
* [Integrera alla dina appar med Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)