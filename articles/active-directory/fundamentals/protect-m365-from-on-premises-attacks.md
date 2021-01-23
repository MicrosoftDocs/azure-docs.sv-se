---
title: Skydda Microsoft 365 från lokala attacker
description: Vägledning om hur du ser till att en lokal attack inte påverkar Microsoft 365
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
ms.openlocfilehash: ecddb950c06c9f8e61f31e104051f5e3b3640ae5
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725018"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Skydda Microsoft 365 från lokala attacker

Många kunder ansluter sina privata företags nätverk till Microsoft 365 att dra nytta av sina användare, enheter och program. Det finns dock många väldokumenterade sätt som dessa privata nätverk kan komprometteras. Eftersom Microsoft 365 fungerar som "nerv systemet" för många organisationer är det viktigt att skydda det från komprometterad lokal infrastruktur.

Den här artikeln visar hur du konfigurerar dina system för att skydda din Microsoft 365 moln miljö från lokala kompromisser. Vi fokuserar främst på konfigurations inställningar för Azure AD-klient, hur Azure AD-klienter kan anslutas till lokala system och de kompromisser som krävs för att hantera dina system på ett sätt som skyddar dina moln system från lokala kompromisser.

Vi rekommenderar starkt att du implementerar den här vägledningen för att skydda din Microsoft 365 moln miljö.
> [!NOTE]
> Den här artikeln publicerades ursprungligen som ett blogg inlägg. Den har flyttats hit för Longevity och underhåll. <br>
Om du vill skapa en offline-version av den här artikeln använder du webbläsarens utskrifts-till-PDF-funktion. Kom tillbaka hit ofta för uppdateringar.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Primära hot vektorer från komprometterade lokala miljöer


Din Microsoft 365 moln miljö har nytta av en omfattande övervaknings-och säkerhets infrastruktur. Om du använder maskin inlärning och Human Intelligence som tittar på den världs omfattande trafiken kan du snabbt upptäcka attacker och göra det möjligt att konfigurera om i nära real tid. I hybrid distributioner som ansluter lokalt-infrastruktur till Microsoft 365 kan många organisationer delegera förtroende till lokala komponenter för kritiska autentiserings-och katalog objekt tillstånds hanterings beslut.
Om den lokala miljön har komprometterats kan de här förtroende relationerna leda till angripares möjligheter att kompromettera din Microsoft 365s miljö.

De två primära hot vektorerna är **Federations förtroende relationer** och **konto synkronisering.** Båda vektorerna kan ge en angripare administrativ åtkomst till molnet.

* **Federerade förtroende relationer**, till exempel SAML-autentisering, används för att autentisera till Microsoft 365 via din lokala identitets infrastruktur. Om ett certifikat för SAML-tokensignering skadas, tillåter federationen att vem som helst med certifikatet personifierar alla användare i molnet. **Vi rekommenderar att du inaktiverar Federations förtroende relationer för autentisering till Microsoft 365 när det är möjligt.**

* **Konto synkronisering** kan användas för att ändra privilegierade användare (inklusive deras autentiseringsuppgifter) eller grupper som beviljats administratörs behörighet i Microsoft 365. **Vi rekommenderar att du ser till att synkroniserade objekt inte har några behörigheter utöver en användare i Microsoft 365,** antingen direkt eller via inkludering i betrodda roller eller grupper. Se till att dessa objekt inte har någon direkt eller kapslad tilldelning i betrodda moln roller eller grupper.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Skydda Microsoft 365 från lokal kompromiss


För att adressera de hot vektorer som beskrivs ovan rekommenderar vi att du följer de principer som illustreras nedan:

![Referens arkitektur för att skydda Microsoft 365 ](media/protect-m365/protect-m365-principles.png)

*  **Isolera Microsoft 365 administratörs konton fullständigt.** De bör vara

    * Hanterat i Azure AD.

     * Autentiserad med Multi-Factor Authentication (MFA).

     *  Skyddad av villkorlig åtkomst för Azure AD.

     *  Endast nås med hjälp av Azure Managed-arbetsstationer.

Dessa är begränsade användnings konton. **Det får inte finnas några lokala konton med administratörs behörighet i Microsoft 365.** Mer information finns i den här [översikten över Microsoft 365 administratörs roller](/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).
Se även [roller för Microsoft 365 i Azure Active Directory](../roles/m365-workload-docs.md).

*  **Hantera enheter från Microsoft 365.** Använd Azure AD Join och molnbaserad hantering av mobila enheter (MDM) för att ta bort beroenden i din lokala infrastruktur för enhets hantering, som kan kompromettera enhets-och säkerhets kontroller.

* **Inget lokalt konto har förhöjd behörighet att Microsoft 365.**
    Konton som har åtkomst till lokala program som kräver NTLM-, LDAP-eller Kerberos-autentisering behöver ett konto i organisationens lokala identitets infrastruktur. Se till att dessa konton, inklusive tjänst konton, inte ingår i privilegierade moln roller eller grupper och att ändringar i dessa konton inte påverkar moln miljöns integritet. Privilegie rad lokal program vara får inte kunna påverka Microsoft 365 privilegierade konton eller roller.

*  **Använd Azure AD Cloud Authentication** för att ta bort beroenden för dina lokala autentiseringsuppgifter. Använd alltid stark autentisering, t. ex. Windows Hello, FIDO, Microsoft Authenticator eller Azure AD MFA.

## <a name="specific-recommendations"></a>Vissa rekommendationer


I följande avsnitt finns information om hur du implementerar principerna som beskrivs ovan.

### <a name="isolate-privileged-identities"></a>Isolera privilegierade identiteter


I Azure AD är användare med privilegierade roller, till exempel Administratörer, förtroende roten för att bygga och hantera resten av miljön. Implementera följande metoder för att minimera effekten av en kompromiss.

* Använd endast moln konton för Azure AD och Microsoft 365 privilegierade roller. d

* Distribuera [privilegierade åtkomst enheter](/security/compass/privileged-access-devices#device-roles-and-profiles) för privilegie rad åtkomst för att hantera Microsoft 365 och Azure AD.

*  Distribuera [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) för just-in-Time (JIT)-åtkomst till alla mänskliga konton som har privilegierade roller och kräver stark autentisering för att aktivera roller.

* Ge administrativa roller den [minsta behörighet som krävs för att utföra sina uppgifter](../roles/delegate-by-task.md).

* Om du vill aktivera en omfattande roll tilldelnings funktion som inkluderar delegering och flera roller samtidigt kan du överväga att använda Azure AD-säkerhetsgrupper eller Microsoft 365 grupper (kollektivt "moln grupper") och [Aktivera rollbaserad åtkomst kontroll](../roles/groups-assign-role.md). Du kan också använda [administrativa enheter](../roles/administrative-units.md) för att begränsa rollens omfång till en del av organisationen.

* Distribuera [åtkomst konton för nöd situationer](../roles/security-emergency-access.md) och Använd inte lokala lösen ords valv för att lagra autentiseringsuppgifter.

Mer information finns i [skydda privilegie rad åtkomst](/security/compass/overview), som innehåller detaljerad vägledning om det här avsnittet. Se även [säker åtkomst praxis för administratörer i Azure AD](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Använd molnbaserad autentisering 

Autentiseringsuppgifterna är en primär angrepps vektor. Implementera följande metoder för att göra autentiseringsuppgifterna säkrare.

* [Distribuera lösenordsskyddad autentisering](../authentication/howto-authentication-passwordless-deployment.md): minska användningen av lösen ord så mycket som möjligt genom att distribuera lösen ords färre autentiseringsuppgifter. Autentiseringsuppgifterna hanteras och verifieras internt i molnet. Välj mellan:

   * [Windows Hello för företag](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Autentiseringsapp](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2 säkerhets nycklar](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Distribuera Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md): etablera [flera starka AUTENTISERINGSUPPGIFTER med Azure AD MFA](../fundamentals/resilience-in-credentials.md). På så sätt kräver åtkomst till moln resurser en autentiseringsuppgift som hanteras i Azure AD förutom ett lokalt lösen ord som kan ändras.

   * Mer information finns i [skapa en elastisk strategi för hantering av åtkomst kontroll med Azure Active Directory](./resilience-overview.md).

**Begränsningar och kompromisser**

* Lösen ords hantering för Hybrid konton kräver hybrid komponenter som lösen ords skydds agenter och för tillbakaskrivning av lösen ord. Om den lokala infrastrukturen har komprometterats kan angripare styra de datorer där agenterna finns. Även om detta inte äventyrar din moln infrastruktur skyddar inte dina moln konton dessa komponenter från lokala kompromisser.

*  Lokala konton som synkroniseras från Active Directory har marker ATS att aldrig upphöra att gälla i Azure AD, baserat på antagandet att lokala AD-lösenords lösen ords principer kommer att minimera detta. Om din lokala AD är komprometterad och synkronisering från AD Connect måste inaktive ras måste du ange alternativet [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="provision-user-access-from-the-cloud"></a>Etablera användar åtkomst från molnet

Etableringen avser skapandet av användar konton och grupper i program eller identitets leverantörer.

![Diagram över etablerings arkitektur](media/protect-m365/protect-m365-provision.png)

* **Etablera från molnets HR-appar till Azure AD:** Detta gör det möjligt att isolera en lokal kompromiss utan att störa din anslutning – överskrivning-cykel från dina moln-till Azure AD-appar.

* **Moln program:** Distribuera [Azure AD App etablering](../app-provisioning/user-provisioning.md) i stället för lokala etablerings lösningar om det är möjligt. Detta kommer att skydda vissa av dina SaaS-appar från att bli förgiftade med skadliga användar profiler på grund av lokala överträdelser. 

* **Externa identiteter:** Använd [Azure AD B2B-samarbete](../external-identities/what-is-b2b.md).
    Detta minskar beroendet av lokala konton för externt samarbete med partners, kunder och leverantörer. Utvärdera noga alla direkta federationer med andra identitets leverantörer. Vi rekommenderar att du begränsar B2B-gäst konton på följande sätt.

   *  Begränsa gäst åtkomsten till att bläddra bland grupper och andra egenskaper i katalogen. Använd de externa samarbets inställningarna för att begränsa gäst möjligheten att läsa grupper som de inte är medlemmar i. 

    *   Blockera åtkomst till Azure Portal. Du kan göra sällsynta nödvändiga undantag.  Skapa en princip för villkorlig åtkomst som inkluderar alla gäster och externa användare och [implementera sedan en princip för att blockera åtkomst](../../role-based-access-control/conditional-access-azure-management.md). 

* **Frånkopplade skogar:** Använd [Azure AD Cloud-etablering](../cloud-provisioning/what-is-cloud-provisioning.md). På så sätt kan du ansluta till frånkopplade skogar, vilket eliminerar behovet av att upprätta anslutningar mellan skogar eller förtroenden, vilket kan bredda påverkan av en lokal överträdelse. * 
 
**Begränsningar och kompromisser:**

* När den används för att etablera hybrid konton förlitar sig Azure AD från Cloud HR-system på lokal synkronisering för att slutföra data flödet från AD till Azure AD. Om synkronisering avbryts kommer nya medarbetar poster inte att vara tillgängliga i Azure AD.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Använd moln grupper för samarbete och åtkomst

Med moln grupper kan du frikoppla ditt samarbete och få åtkomst från din lokala infrastruktur.

* **Samarbete:** Använd Microsoft 365 grupper och Microsoft Teams för modern samarbete. Inaktivera lokala distributions listor och [Uppgradera distributions listor till Microsoft 365 grupper i Outlook](/office365/admin/manage/upgrade-distribution-lists?view=o365-worldwide).

* **Åtkomst:** Använd Azure AD-säkerhetsgrupper eller Microsoft 365 grupper för att ge åtkomst till program i Azure AD.
* **Office 365-licensiering:** Använd gruppbaserad licensiering för att etablera till Office 365 med endast grupper med moln. Detta frikopplar kontroll över grupp medlemskap från lokal infrastruktur.

Ägare av grupper som används för åtkomst bör betraktas som privilegierade identiteter för att undvika medlemskaps övertag ande från lokala kompromisser.
Ta över inkluderar direkt manipulering av grupp medlemskap lokalt eller manipulering av lokala attribut som kan påverka det dynamiska grupp medlemskapet i Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Hantera enheter från molnet


Använd Azure AD-funktioner för att hantera enheter på ett säkert sätt.

-   **Använd Windows 10-arbetsstationer:** [Distribuera Azure AD-anslutna](../devices/azureadjoin-plan.md) enheter med MDM-principer. Aktivera [Windows autopilot](/mem/autopilot/windows-autopilot) för en helt automatiserad etablerings upplevelse.

    -   Föråldrade Windows 8,1 och tidigare datorer.

    -   Distribuera inte server-OS-datorer som arbets stationer.

    -   Använd [Microsoft Intune](https://www.microsoft.com/en/microsoft-365/enterprise-mobility-security/microsoft-intune) som källa för utfärdare av alla arbets belastningar för enhets hantering.

-   [**Distribuera privilegierade åtkomst enheter**](/security/compass/privileged-access-devices#device-roles-and-profiles) för privilegie rad åtkomst för att hantera Microsoft 365 och Azure AD.

 ## <a name="workloads-applications-and-resources"></a>Arbets belastningar, program och resurser 

-   **Lokala SSO-system:** Föråldra alla lokala Federations-och hanterings infrastrukturer för webb åtkomst och konfigurera program för att använda Azure AD.  

-   **SaaS-och LOB-program som stöder moderna autentiseringsprotokoll:** [Använd Azure AD för enkel inloggning](../manage-apps/what-is-single-sign-on.md). De fler appar som du konfigurerar för att använda Azure AD för autentisering, desto mindre risk om det rör sig om en lokal kompromiss.


* **Äldre program** 

   * Autentisering, auktorisering och fjärråtkomst till äldre program som inte stöder modern autentisering kan aktive ras via [Azure AD-programproxy](../manage-apps/application-proxy.md). De kan också aktive ras via en nätverks-eller program leverans kontroll enhets lösning med hjälp av  [säker partners integrering för Hybrid åtkomst](../manage-apps/secure-hybrid-access.md).   

   * Välj en VPN-leverantör som stöder modern autentisering och integrerar dess autentisering med Azure AD. Om Anon komprometteras kan du använda Azure AD för att inaktivera eller blockera åtkomst genom att inaktivera VPN.

*  **Program-och arbets belastnings servrar**

   * Program eller resurser som krävde servrar kan migreras till Azure-IaaS och använda [Azure AD Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) för att frikoppla förtroende och beroende av AD lokalt. För att kunna åstadkomma detta kan virtuella nätverk som används för Azure AD DS inte ha anslutning till företags nätverk.

   * Följ rikt linjerna för [nivån för autentiseringsuppgifter](/security/compass/privileged-access-access-model#ADATM_BM). Program servrar betraktas vanligt vis som nivå 1-till gångar.

 ## <a name="conditional-access-policies"></a>Villkorliga åtkomstprinciper

Använd villkorlig åtkomst i Azure AD för att tolka signaler och fatta beslut om autentisering baserat på dem. Mer information finns i [distributions planen för villkorlig åtkomst.](../conditional-access/plan-conditional-access.md)

* [Bakåtkompatibla](../fundamentals/auth-sync-overview.md)autentiseringsprotokoll: Använd villkorlig åtkomst för att [blockera äldre autentiseringsprotokoll](../conditional-access/howto-conditional-access-policy-block-legacy.md) närhelst det är möjligt. Dessutom inaktiverar du äldre autentiseringsprotokoll på program nivå med programspecifik konfiguration.

   * Se detaljerad information för [Exchange Online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) och [SharePoint Online](/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps).

* Implementera de rekommenderade [konfigurationerna för identitets-och enhets åtkomst.](/microsoft-365/security/office-365-security/identity-access-policies?view=o365-worldwide)

* Om du använder en version av Azure AD som inte innehåller villkorlig åtkomst kontrollerar du att du använder [standardinställningarna för Azure AD-säkerhet](../fundamentals/concept-fundamentals-security-defaults.md).

   * Mer information om Azure AD Feature Licensing finns i [pris guiden för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitoring"></a>Övervakning 

När du har konfigurerat din miljö för att skydda dina Microsoft 365 från en lokal kompromiss kan du [proaktivt övervaka](../reports-monitoring/overview-monitoring.md) miljön.
### <a name="scenarios-to-monitor"></a>Scenarier som ska övervakas

Övervaka följande viktiga scenarier, förutom eventuella scenarier som är speciella för din organisation. Till exempel bör du proaktivt övervaka åtkomsten till dina affärs kritiska program och resurser.

* **Misstänkt aktivitet**: alla [Azure AD-risk händelser](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) bör övervakas för misstänkt aktivitet. [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) integreras internt med Azure Security Center.

   * Definiera nätverket [med namnet platser](../reports-monitoring/quickstart-configure-named-locations.md) för att undvika störningar på platsbaserade signaler. 
*  **UEBA-aviseringar (User Entity Behavior Analytics)** Använd UEBA för att få insikter om avvikelse identifiering.
   * Microsoft Cloud App Discovery (MCAS) tillhandahåller [UEBA i molnet](/cloud-app-security/tutorial-ueba).

   * Du kan [integrera lokala UEBA från Azure ATP](/defender-for-identity/install-step2). MCAS läser signaler från Azure AD Identity Protection. 

* **Aktivitet för åtkomst till nöd situationer**: all åtkomst med hjälp av [konton för nöd åtkomst](../roles/security-emergency-access.md) bör övervakas och aviseringar skapas för utredningar. Denna övervakning måste innehålla: 

   * Inloggningar. 

   * Hantering av autentiseringsuppgifter. 

   * Eventuella uppdateringar av grupp medlemskap. 

   *    Program tilldelningar. 
* **Privilegie rad roll aktivitet**: Konfigurera och granska säkerhets [aviseringar som genererats av Azure AD PIM](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts).
    Övervaka direkt tilldelning av privilegierade roller utanför PIM genom att generera aviseringar när en användare tilldelas direkt.
* **Konfigurationer för Azure AD-klient**: alla ändringar av konfigurationer för klient organisationen bör generera aviseringar i systemet. Dessa inkluderar, men är inte begränsade till
  *  Uppdaterar anpassade domäner  

  * Ändringar i listan över tillåtna/blockerade Azure AD B2B-listor.
  * Azure AD B2B tillåtna identitets leverantörer (SAML-IDP: er via direkt Federation eller sociala inloggningar).  
  * Ändringar av villkorlig åtkomst eller risk princip 

* **Program-och tjänst huvud objekt**:
   * Nya program eller tjänst huvud namn som kan kräva principer för villkorlig åtkomst. 

   * Ytterligare autentiseringsuppgifter har lagts till i tjänstens huvud namn.
   * Aktivitet för program medgivande. 

* **Anpassade roller**:
   * Uppdateringar av anpassade roll definitioner. 

   * Nya anpassade roller har skapats. 

### <a name="log-management"></a>Logg hantering

Definiera en strategi för logg lagring och bevarande, utformning och implementering för att under lätta en konsekvent uppsättning verktyg som t. ex. SIEM system som Azure Sentinel, vanliga frågor och undersökning och data utredning spel böcker.

* **Azure AD-loggar** Inmatnings loggar och-signaler som skapats efter konsekventa metod tips, inklusive diagnostikinställningar, logg kvarhållning och SIEM inmatning. Logg strategin måste omfatta följande Azure AD-loggar:
   * Inloggningsaktivitet 

   * Granskningsloggar 

   * Riskhändelser 

Azure AD tillhandahåller [Azure Monitor integrering](../reports-monitoring/concept-activity-logs-azure-monitor.md) för inloggnings aktivitets loggen och gransknings loggarna. Risk händelser kan matas in via [Microsoft Graph-API](/graph/api/resources/identityriskevent). Du kan [strömma Azure AD-loggar till Azure Monitor-loggar](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Säkerhets loggar för Hybrid infrastruktur-operativsystem.** Alla operativ system loggar för Hybrid identitets infrastruktur bör arkiveras och övervakas noga som <br>Nivå 0-system, med hänsyn till ytans konsekvenser. Du måste bland annat: 

   *  Azure AD Connect. [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md) måste distribueras för att övervaka synkronisering av identiteter.

   *  Programproxy-agenter 


   * Skriv skydds agenter för lösen ord 

   * Password Protection gateway-datorer  

   * NPS som har tillägget Azure MFA RADIUS 

## <a name="next-steps"></a>Nästa steg
* [Bygg in återhämtning i identitets- och åtkomsthanteringen med Azure AD](resilience-overview.md)

* [Säker extern åtkomst till resurser](secure-external-access-resources.md) 
* [Integrera alla dina appar med Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)