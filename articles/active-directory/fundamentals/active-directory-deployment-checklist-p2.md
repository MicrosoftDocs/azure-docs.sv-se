---
title: Checklista för Azure AD-distribution
description: Check lista för Azure Active Directory funktions distribution
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92d1e5b8ac6492b0b1d819431e4616d32a092cc8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836928"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Distributionsguide för Azure Active Directory-funktion

Det kan verka avskräckande för att distribuera Azure Active Directory (Azure AD) för din organisation och hålla den säker. I den här artikeln beskrivs vanliga uppgifter som kunder kan få hjälp av i faser, under den 30, 60, 90 dagar eller mer, för att förbättra sina säkerhets position. Även organisationer som redan har distribuerat Azure AD kan använda den här guiden för att se till att de får ut mesta möjliga av sina investeringar.

En välplanerad och utförd identitets infrastruktur paves det sätt för säker åtkomst till dina produktivitets arbets belastningar och data genom kända användare och enheter.

Dessutom kan kunderna kontrol lera sina [identiteter](identity-secure-score.md) för att se hur justerade de är till Microsofts bästa praxis. Kontrol lera dina säkra poäng innan och efter att du har implementerat rekommendationerna för att se hur väl du jämför med andra i din bransch och andra organisationer av din storlek.

## <a name="prerequisites"></a>Förutsättningar

Många av rekommendationerna i den här hand boken kan implementeras med Azure AD Free eller ingen licens alls. Om licenserna krävs får vi tillstånd för vilken licens som krävs för att utföra uppgiften minst.

Ytterligare information om licensiering finns på följande sidor:

* [Azure AD-licensiering](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Företag](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Priser för externa Azure AD-identiteter](../external-identities/external-identities-pricing.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Fas 1: Bygg grunden för säkerhet

I den här fasen aktiverar administratörer grundläggande säkerhets funktioner för att skapa en säkrare och lättanvänd grund i Azure AD innan vi importerar eller skapar vanliga användar konton. Den här grundläggande fasen säkerställer att du befinner dig i ett säkrare tillstånd från start och att dina slutanvändare bara behöver introduceras för nya koncept en gång.

| Uppgift | Detalj | Nödvändig licens |
| ---- | ------ | ---------------- |
| [Ange mer än en global administratör](../roles/security-emergency-access.md) | Tilldela minst två permanenta globala administratörs konton för enbart molnet som ska användas om det finns en nöd situation. Dessa konton används inte dagligen och bör ha långa och komplexa lösen ord. | Azure AD Kostnadsfri |
| [Använd icke-globala administrativa roller där det är möjligt](../roles/permissions-reference.md) | Ge dina administratörer bara den åtkomst som de behöver för att komma åt de områden som de behöver åtkomst till. Alla administratörer behöver inte vara globala administratörer. | Azure AD Kostnadsfri |
| [Aktivera Privileged Identity Management för spårning av administratörs roll användning](../privileged-identity-management/pim-getting-started.md) | Aktivera Privileged Identity Management för att börja spåra användning av administrativ roll. | Azure AD Premium P2 |
| [Lansera självåterställning av lösenord](../authentication/howto-sspr-deployment.md) | Minska support samtalen för lösen ords återställning genom att låta personalen återställa sina egna lösen ord med hjälp av principer som administratörs behörighet. | |
| [Skapa en anpassad lista över blockerade lösen ord för organisationen](../authentication/tutorial-configure-custom-password-protection.md) | Hindra användare från att skapa lösen ord som innehåller vanliga ord eller fraser från din organisation eller ditt områden. | |
| [Aktivera lokal integrering med Azure AD Password Protection](../authentication/concept-password-ban-bad-on-premises.md) | Utöka listan över förbjudna lösen ord till din lokala katalog för att säkerställa att lösen ord som angetts lokalt också är kompatibla med listorna globala och klient-/regionsspecifika förbjudna lösen ord. | Azure AD Premium P1 |
| [Aktivera Microsofts lösen Ords vägledning](https://www.microsoft.com/research/publication/password-guidance/) | Sluta kräva att användarna ändrar sitt lösen ord enligt ett schema, inaktiverar komplexitets kraven och att användarna är mer Apta för att komma ihåg sina lösen ord och se till att de är säkra. | Azure AD Kostnadsfri |
| [Inaktivera regelbunden återställning av lösen ord för molnbaserade användar konton](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Regelbundna lösen ords återställning uppmanar användarna att öka sina befintliga lösen ord. Använd rikt linjerna i Microsofts rikt linjer för lösen Ords vägledning och spegla din lokala princip till endast molnbaserade användare. | Azure AD Kostnadsfri |
| [Anpassa Azure Active Directory Smart utelåsning](../authentication/howto-password-smart-lockout.md) | Stoppa utelåsning från molnbaserade användare från att replikeras till lokala Active Directory användare | |
| [Aktivera smart utelåsning för extra nät för AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | AD FS extra näts utelåsning skyddar mot brute force lösen ords gissning, samtidigt som giltiga AD FS användare fortsätter att använda sina konton. | |
| [Blockera äldre autentisering till Azure AD med villkorlig åtkomst](../conditional-access/block-legacy-authentication.md) | Blockera äldre autentiseringsprotokoll som POP, SMTP, IMAP och MAPI som inte kan genomdriva Multi-Factor Authentication, vilket gör dem till en önskad start punkt för angripare. | Azure AD Premium P1 |
| [Distribuera Azure AD Multi-Factor Authentication att använda principer för villkorlig åtkomst](../authentication/howto-mfa-getstarted.md) | Kräv att användarna utför tvåstegsverifiering vid åtkomst till känsliga program med hjälp av principer för villkorlig åtkomst. | Azure AD Premium P1 |
| [Aktivera Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) | Aktivera spårning av riskfyllda inloggningar och komprometterade autentiseringsuppgifter för användare i din organisation. | Azure AD Premium P2 |
| [Använda risk identifieringar för att utlösa Multi-Factor Authentication-och lösen ords ändringar](../authentication/tutorial-risk-based-sspr-mfa.md) | Aktivera automatisering som kan utlösa händelser, till exempel Multi-Factor Authentication, återställning av lösen ord och blockering av inloggningar baserat på risk. | Azure AD Premium P2 |
| [Aktivera kombinerad registrering för självbetjäning för återställning av lösen ord och Azure AD Multi-Factor Authentication](../authentication/concept-registration-mfa-sspr-combined.md) | Tillåt att användarna registrerar sig från en vanlig upplevelse för både Azure AD-Multi-Factor Authentication och lösen ords återställning via självbetjäning. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Fas 2: importera användare, aktivera synkronisering och hantera enheter

Därefter lägger vi till den grund som anges i fas 1 genom att importera våra användare och aktivera synkronisering, planera för gäst åtkomst och förbereda stöd för ytterligare funktioner.

| Uppgift | Detalj | Nödvändig licens |
| ---- | ------ | ---------------- |
| [Installera Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md) | Förbered för att synkronisera användare från din befintliga lokala katalog till molnet. | Azure AD Kostnadsfri |
| [Implementera hash-synkronisering av lösen ord](../hybrid/how-to-connect-password-hash-synchronization.md) | Synkronisera lösen ords-hashar för att tillåta att lösen ords ändringar replikeras, felaktig identifiering av lösen ord och reparation och läckta autentiseringsuppgifter rapporteras. | Azure AD Kostnadsfri |
| [Implementera tillbakaskrivning av lösen ord](../authentication/tutorial-enable-sspr-writeback.md) | Tillåt att lösen ords ändringar i molnet skrivs tillbaka till en lokal Windows Server Active Directory-miljö. | Azure AD Premium P1 |
| [Implementera Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) | Aktivera övervakning av statistik för viktiga hälso tillstånd för dina Azure AD Connect-servrar, AD FS-servrar och domänkontrollanter. | Azure AD Premium P1 |
| [Tilldela licenser till användare efter grupp medlemskap i Azure Active Directory](../enterprise-users/licensing-groups-assign.md) | Spara tid och ansträngning genom att skapa licens grupper som aktiverar eller inaktiverar funktioner efter grupp i stället för att ställa in per användare. | |
| [Skapa en plan för åtkomst till gäst användare](../external-identities/what-is-b2b.md) | Samar beta med gäst användare genom att låta dem logga in på dina appar och tjänster med sina egna arbets-, skol-eller sociala identiteter. | [Priser för externa Azure AD-identiteter](../external-identities/external-identities-pricing.md) |
| [Bestäm strategi för enhets hantering](../devices/overview.md) | Bestäm vad din organisation tillåter för enheter. Registrera vs-anslutning, ta med din egen enhet kontra företaget. | |
| [Distribuera Windows Hello för företag i din organisation](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Förbered för lösenordsbaserad autentisering med Windows Hello | |
| [Distribuera autentiseringsmetoder för lösen ord för dina användare](../authentication/concept-authentication-passwordless.md) | Ge dina användare med praktiska metoder för lösen ords kryptering | Azure AD Premium P1 |

## <a name="phase-3-manage-applications"></a>Fas 3: hantera program

Som vi fortsätter att bygga på föregående faser identifierar vi kandidat program för migrering och integrering med Azure AD och slutför installationen av programmen.

| Uppgift | Detalj | Nödvändig licens |
| ---- | ------ | ---------------- |
| Identifiera dina program | Identifiera program som används i din organisation: lokalt, SaaS program i molnet och andra branschspecifika program. Ta reda på om dessa program kan och bör hanteras med Azure AD. | Ingen licens krävs |
| [Integrera SaaS-program som stöds i galleriet](../manage-apps/add-application-portal.md) | Azure AD har ett galleri som innehåller tusentals förintegrerade program. Några av de program som organisationen använder är förmodligen i galleriet tillgängligt direkt från Azure Portal. | Azure AD Kostnadsfri |
| [Använd Application Proxy för att integrera lokala program](../manage-apps/application-proxy-add-on-premises-application.md) | Med Application Proxy kan användare komma åt lokala program genom att logga in med sitt Azure AD-konto. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Fas 4: granska privilegierade identiteter, slutför en åtkomst granskning och hantera användar livs cykeln

Fas 4 ser att administratörer tillämpar principer för minsta behörighet för administration, slutför sina första åtkomst granskningar och aktiverar automatisering av vanliga aktiviteter för användar livs cykeln.

| Uppgift | Detalj | Nödvändig licens |
| ---- | ------ | ---------------- |
| [Använd Privileged Identity Management](../privileged-identity-management/pim-security-wizard.md) | Ta bort administrativa roller från vanliga dagar till dags användar konton. Gör administrativa användare berättigade att använda sina roller efter att ha slutfört en Multi-Factor Authentication-kontroll, ange en affärs motivering eller begära godkännande från angivna god kännare. | Azure AD Premium P2 |
| [Slutför en åtkomst granskning för Azure AD Directory-roller i PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Arbeta med dina säkerhets-och ledarskaps grupper för att skapa en åtkomst gransknings princip för att granska administrativ åtkomst baserat på organisationens principer. | Azure AD Premium P2 |
| [Implementera principer för dynamisk grupp medlemskap](../enterprise-users/groups-dynamic-membership.md) | Använd dynamiska grupper för att automatiskt tilldela användare till grupper baserat på deras attribut från HR (eller din källa för sanningen), till exempel avdelning, titel, region och andra attribut. |  |
| [Implementera etablering av gruppbaserad applikation](../manage-apps/what-is-access-management.md) | Använd gruppbaserad åtkomst hanterings etablering för att automatiskt etablera användare för SaaS-program. |  |
| [Automatisera användar etablering och avetablering](../app-provisioning/user-provisioning.md) | Ta bort manuella steg från livs cykeln för ditt medarbetar konto för att förhindra obehörig åtkomst. Synkronisera identiteter från din källa för sanningen (HR system) till Azure AD. |  |

## <a name="next-steps"></a>Nästa steg

[Azure AD-licensiering och pris information](https://azure.microsoft.com/pricing/details/active-directory/)

[Konfigurationer för identitets- och enhetsåtkomst](/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Vanliga rekommenderade identitets-och enhets åtkomst principer](/microsoft-365/enterprise/identity-access-policies)