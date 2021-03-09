---
title: Azure AD Secure hybrid-åtkomst med F5 | Microsoft Docs
description: F5 BIG-IP åtkomst Policy Manager och Azure Active Directory-integrering för säker hybrid åtkomst
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38b484bd67f3354132675f343dcc06bd7f9d48a0
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499822"
---
# <a name="f5-big-ip-access-policy-manager-and-azure-active-directory-integration-for-secure-hybrid-access"></a>F5 BIG-IP åtkomst Policy Manager och Azure Active Directory-integrering för säker hybrid åtkomst

Spridningen av mobilitets-och växande hot-landskap ger extra granskning av resurs åtkomst och styrning, och ger [inget tillförlitligt](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/) och Centrerat för alla modernisering-program.
På Microsoft och F5 inser vi att den här digitala omvandlingen vanligt vis är en resa i flera år för alla företag, vilket kan leda till att kritiska resurser exponeras tills de har gjorts tillgängliga. Genesis bakom F5 stora IP-adresser och Azure Active Directory Secure hybrid Access (SHA) är inte bara till för att förbättra fjärråtkomst till lokala program, utan även för att förstärka den övergripande säkerhets position för dessa sårbara tjänster.

I sammanhang beräknar forskningen att 60-80% av lokala program är äldre, eller att andra ord inte kan integreras direkt med Azure Active Directory (AD). Samma studie visade också att en stor del av dessa system körs på tidigare versioner av SAP, Oracle, SAGE och andra välkända arbets belastningar som tillhandahåller kritiska tjänster.

SHA åtgärdar den här hemliga punkten genom att göra det möjligt för organisationer att fortsätta använda sina F5-investeringar för överlägsen nätverks-och program leverans. Tillsammans med Azure AD är det heterogent program liggande med det moderna identitets kontroll planet.

Att ha Azure AD förautentiserad åtkomst till stora IP-publicerade tjänster ger många fördelar:

- Lösen ords lös autentisering via [Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview), [MS Authenticator](../user-help/user-help-auth-app-download-install.md), [snabb identitet Online (Fido)-nycklar](../authentication/howto-authentication-passwordless-security-key.md)och [certifikatbaserad autentisering](../authentication/active-directory-certificate-based-authentication-get-started.md)

- [Villkorlig åtkomst](../conditional-access/overview.md) för ogiltiga och [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md)

- [Identitets skydd](../identity-protection/overview-identity-protection.md) – anpassningsbar kontroll genom profilering av användar-och sessions risk


- [Identifiera läckta autentiseringsuppgifter](../identity-protection/concept-identity-protection-risks.md)

- [Lösen ords återställning via självbetjäning (SSPR)](../authentication/tutorial-enable-sspr.md)

- Samarbets [partner](../governance/entitlement-management-external-users.md) – rättighets hantering för styrd gäst åtkomst

- [Cloud App Security (CASB)](/cloud-app-security/what-is-cloud-app-security) – för fullständig identifiering och kontroll av appar

- Hot övervakning – [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) för Advanced Threat Analytics

- [Azure AD-portalen](https://azure.microsoft.com/features/azure-portal/) – ett enda kontroll plan för att övervaka identitet och åtkomst

## <a name="scenario-description"></a>Scenariobeskrivning

Som en program leverans kontroll (ADC) och SSL-VPN ger ett stort IP-system lokal och fjärråtkomst till alla typer av tjänster, inklusive:

- Moderna och äldre webb program

- Icke-webbaserade program

- REST-och SOAP-webb-API-tjänster

Den lokala Traffic Manager (LTM) möjliggör säker publicering av tjänster via funktionen för omvänd proxy. På samma gång utökar en avancerad åtkomst princip hanterare (APM) BIG-IP med en omfattande uppsättning funktioner, vilket möjliggör Federation och enkel inloggning (SSO).

Integrationen baseras på ett standard Federations förtroende mellan APM och Azure AD, som är gemensamt för de flesta SHA-användnings fall som innehåller [SSL-VPN-scenariot](f5-aad-password-less-vpn.md). Security Assertion Markup Language (SAML), OAuth-och Open ID Connect (OIDC)-resurser är inget undantag, eftersom de kan skyddas för fjärråtkomst. Det kan också finnas scenarier där en stor IP-adress blir en strypning för att få åtkomst till alla tjänster, inklusive SaaS-appar.

En stor IP-möjlighet att integrera med Azure AD är det som gör det möjligt för protokoll över gången att säkra äldre eller icke-Azure AD-integrerade tjänster med moderna kontroller, till exempel [lösen ords mindre autentisering](https://www.microsoft.com/security/business/identity/passwordless) och [villkorlig åtkomst](../conditional-access/overview.md). I det här scenariot fortsätter en stor IP att uppfylla sin roll som en omvänd proxy, samtidigt som förautentiseringen och auktoriseringen av Azure AD överförs per tjänst.

Steg 1-4 i diagrammet illustrerar förautentisering av Exchange på klient sidan mellan en användare, en stor IP-adress och Azure AD, i en Service Provider initierat flöde. Steg 5-6 visar efterföljande APM-sessioner och SSO till enskilda Server dels tjänster.

![Bild visar arkitekturen på hög nivå](./media/f5-aad-integration/integration-flow-diagram.png)

| Steg | Beskrivning |
|:------|:-----------|
| 1. | Användaren väljer en program ikon i portalen och matchar URL: en till SAML SP (BIG-IP) |
| 2. | BIG-IP omdirigerar användaren till SAML IDP (Azure AD) för förautentisering|
| 3. | Azure AD bearbetar principer för villkorlig åtkomst och [kontroll av sessioner](../conditional-access/concept-conditional-access-session.md) för auktorisering|
| 4. | Användaren omdirigeras tillbaka till BIG-IP som visar SAML-anspråk som utfärdats av Azure AD |
| 5. | BIG-IP begär ytterligare sessionsinformation som ska inkluderas i [SSO](../hybrid/how-to-connect-sso.md) och [rollbaserad åtkomst kontroll (RBAC)](../../role-based-access-control/overview.md) till den publicerade tjänsten |
| 6. | BIG-IP vidarebefordrar klient förfrågan till backend-tjänsten

## <a name="user-experience"></a>Användarupplevelse

Vare sig det är en direkt anställd, ett dotter bolag eller en konsument, är de flesta användare redan bekant med Office 365-inloggnings upplevelsen, så åtkomst till BIG-IP-tjänster via SHA förblir i stor erfarenhet.

Användarna hittar nu sina BIG-IP-publicerade tjänster konsoliderade i mina  [appar](../user-help/my-apps-portal-end-user-access.md) eller [O365](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf) -startvägar tillsammans med självbetjänings funktioner till en bredare uppsättning tjänster, oavsett typ av enhet eller plats. Användare kan även fortsätta att komma åt publicerade tjänster direkt via BIG-IPs patentskyddade Webtop-portalen, om det är lämpligt. Vid utloggning ser SHA till att en användares session avslutas i båda ändar, BIG-IP och Azure AD, vilket säkerställer att tjänsterna förblir helt skyddade från obehörig åtkomst.  

Skärm bilderna som tillhandahålls är från Azure AD-App-portalen som användarna har åtkomst till på ett säkert sätt för att hitta sina egna IP-publicerade tjänster och för att hantera deras konto egenskaper.  

![Skärm bilden visar galleriet för Sparbanken-appar](media/f5-aad-integration/woodgrove-app-gallery.png)

![Skärm bilden visar sidan med självbetjäning för Sparbankens konto](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>Insikter och analyser

En stor IP-roll är viktig för alla företag, så distribuerade BIG-IP-instanser bör övervakas för att säkerställa att publicerade tjänster är hög tillgängliga, både på SHA-nivå och även i drift.

Det finns flera alternativ för loggning av händelser antingen lokalt eller via en SIEM-lösning (Security information and Event Management) som aktiverar lagring och bearbetning av telemetri. En mycket effektiv lösning för övervakning av Azure AD och SHA-Specific-aktivitet är att använda [Azure Monitor](../../azure-monitor/overview.md) och [Azure Sentinel](../../sentinel/overview.md), tillsammans erbjuder:

- Detaljerad översikt över din organisation, potentiellt över flera moln och lokala platser, inklusive stor IP-infrastruktur

- Ett enda kontroll plan som ger en kombinerad vy över alla signaler, vilket undviker att lita på komplexa och olika verktyg

![Bilden visar övervaknings flödet](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>Förutsättningar

Att integrera F5 BIG-IP med Azure AD för SHA har följande krav:

- En F5 BIG-IP-instans som körs på någon av följande plattformar:

  - Fysisk installation

  - Hypervisor-virtuell utgåva som Microsoft Hyper-V, VMware ESXi, Linux KVM och Citrix hypervisor

  - Virtuell moln version, till exempel Azure, VMware, KVM, community-xen, MS Hyper-V, AWS, OpenStack och Google Cloud

    Den faktiska platsen för en BIG-IP-instans kan antingen vara lokal eller en moln plattform som stöds, inklusive Azure, förutsatt att den har anslutning till Internet, resurser som publiceras och andra nödvändiga tjänster som Active Directory.  

- En Active F5-licens för BIG-IP APM, via något av följande alternativ:

   - F5 BIG-IP® bästa paket (eller)

   - F5 BIG-IP Access Policy Manager™ fristående licens

   - F5-tilläggsprogram för BIG-IP Access Policy Manager™ (APM) för en befintlig BIG IP F5 BIG-IP® lokal Traffic Manager™ (LTM)

   - En [utvärderings licens](https://www.f5.com/trial/big-ip-trial.php) för en 90-dagars Big-IP Access Policy Manager™ (APM)

- Azure AD-licensiering via något av följande alternativ:

   - En [kostnads fri](/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20) Azure AD-prenumeration tillhandahåller minimi kraven för att implementera SHA med lösen ords lös autentisering

   - En [Premium-prenumeration](https://azure.microsoft.com/pricing/details/active-directory/) ger alla ytterligare värden som läggs till i förordet, inklusive [villkorlig åtkomst](../conditional-access/overview.md), [MFA](../authentication/concept-mfa-howitworks.md)och [identitets skydd](../identity-protection/overview-identity-protection.md)

Ingen tidigare erfarenhet eller F5 BIG-IP-kunskap krävs för att implementera SHA, men vi rekommenderar att du bekantar dig med F5 stor IP-terminologi. F5's Rich [Knowledge Base](https://www.f5.com/services/resources/glossary) är också en bra plats för att börja bygga stor IP-kunskap.

## <a name="deployment-scenarios"></a>Distributionsscenarier

Följande självstudier ger detaljerad vägledning om hur du implementerar några av de vanligaste mönstren för BIG-IP och Azure AD SHA:

- [F5 BIG-IP i Azure-distribution genom gång](f5-bigip-deployment-guide.md)

- [F5 BIG-IP APM och Azure AD SSO till Kerberos-program](../saas-apps/kerbf5-tutorial.md#configure-f5-single-sign-on-for-kerberos-application)

- [F5 BIG-IP APM och Azure AD SSO till Huvudbaserade program](../saas-apps/headerf5-tutorial.md#configure-f5-single-sign-on-for-header-based-application)

- [Skydda F5 BIG-IP SSL-VPN med Azure AD SHA](f5-aad-password-less-vpn.md)

## <a name="additional-resources"></a>Ytterligare resurser

- [Slutet på lösen orden, växla lösen ord](https://www.microsoft.com/security/business/identity/passwordless)

- [Azure Active Directory säker hybrid åtkomst](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [Microsoft Zero Trust Framework för att aktivera fjärran slutet](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Komma igång med Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>Nästa steg

Överväg att köra ett SHA-POC (proof of Concept) med din befintliga BIG-IP-infrastruktur eller genom att distribuera en utvärderings instans. [Distribution av en virtuell dator med stor IP-utgåva (ve) till Azure](f5-bigip-deployment-guide.md) tar cirka 30 minuter, då du har:

- En helt säker plattform för att utforma ett SHA-bevis

- En för produktions instans, helt skyddad plattform som används för att testa nya system uppdateringar och snabb korrigeringar för BIG-IP

Samtidigt bör du identifiera ett eller två program som kan användas för att publicera via BIG-IP och skydda med SHA.  

Vår rekommendation är att börja med ett program som ännu inte har publicerats via en stor IP-adress, så att du undviker potentiella avbrott i produktions tjänsterna. De rikt linjer som nämns i den här artikeln hjälper dig att bekanta dig med den allmänna proceduren för att skapa olika BIG IP-konfigurationsobjekt och att konfigurera SHA. När du är klar bör du kunna göra samma sak med andra nya tjänster, och de har också tillräckligt med kunskaper för att konvertera befintliga publicerade tjänster med stor IP till SHA med minimal ansträngning.

Den interaktiva guiden går igenom den övergripande proceduren för att implementera SHA och se slut användar upplevelsen.

[![Bilden visar interaktiv guide](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)
