---
title: Elastisk slut användar upplevelse med Azure AD B2C | Microsoft Docs
description: Metoder för att bygga återhämtning i slut användar upplevelse med hjälp av Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf49b1a2b7178800966342a3d52ed49450f3d97c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226692"
---
# <a name="resilient-end-user-experience"></a>Elastisk slut användar upplevelse

Användar upplevelsen för registrering och inloggning består av följande element:

- De gränssnitt som användaren interagerar med – till exempel CSS, HTML och Java Script

- Användaren flödar och anpassade principer som du skapar – till exempel registrering, inloggning och profil redigering

- Identitets leverantörer (IDP: er) för ditt program – till exempel lokalt konto användar namn/lösen ord, Outlook, Facebook och Google

![Bild som visar komponenter för slut användar upplevelse](media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>Välj mellan användar flöde och anpassad princip  

För att hjälpa dig att skapa de vanligaste identitets uppgifterna tillhandahåller Azure AD B2C inbyggda [användar flöden](../../active-directory-b2c/user-flow-overview.md)som kan konfigureras. Du kan också bygga egna [anpassade principer](../../active-directory-b2c/custom-policy-overview.md)som ger dig maximal flexibilitet. Vi rekommenderar dock att du bara använder anpassade principer för att hantera komplexa scenarier.

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>Bestämma mellan användar flöden och anpassade principer

Välj inbyggda användar flöden om dina affärs behov kan uppfyllas av dem. Eftersom vi har testat av Microsoft kan du minimera den testning som krävs för att verifiera funktions-, prestanda-eller skalnings funktioner på princip nivå för dessa identitets användar flöden. Du behöver fortfarande testa dina program för funktioner, prestanda och skalning.

Om du [väljer anpassade principer](../../active-directory-b2c/custom-policy-get-started.md) på grund av dina affärs behov bör du kontrol lera att du utför tester på princip nivå för att fungera, prestanda eller skala utöver program nivå testning.

Se artikeln som [Jämför användar flöden och anpassade principer](../../active-directory-b2c/user-flow-overview.md#comparing-user-flows-and-custom-policies) som hjälper dig att bestämma.

## <a name="choose-multiple-idps"></a>Välj flera IDP: er

När du använder en [extern identitetsprovider](../../active-directory-b2c/technical-overview.md#external-identity-providers) , till exempel Facebook, måste du ha en reserv plan om den externa providern blir otillgänglig.

### <a name="how-to-set-up-multiple-idps"></a>Så här konfigurerar du flera IDP: er

Som en del av registrerings processen för extern identitetsprovider, inkludera ett verifierat identitets anspråk som användarens mobiltelefon nummer eller e-postadress. Genomför de verifierade anspråken till den underliggande Azure AD B2C Directory-instansen. Om den externa providern inte är tillgänglig återgår du till det verifierade identitets anspråket och återgår till telefonnumret som en autentiseringsmetod. Ett annat alternativ är att skicka användaren en eng ång slö sen ord så att användaren kan logga in.

 Följ dessa steg om du vill [bygga alternativa sökvägar för autentisering](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter):

 1. Konfigurera din registrerings princip för att tillåta registrering via lokalt konto och extern IDP: er.

 2. Konfigurera en profil princip så att användare kan [koppla den andra identiteten till sitt konto](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking) när de loggar in.

 3. Meddela och Tillåt användare att [Växla till en alternativ IDP](../../active-directory-b2c/customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) under ett avbrott.

## <a name="availability-of-multi-factor-authentication"></a>Tillgänglighet för Multi-Factor Authentication

När du använder en [telefon tjänst för Multi-Factor Authentication (MFA)](../../active-directory-b2c/phone-authentication-user-flows.md)ser du till att du funderar på en alternativ tjänst leverantör. Den lokala Telco eller telefon tjänst leverantören kan uppleva avbrott i tjänsten.

### <a name="how-to-choose-an-alternate-mfa"></a>Så här väljer du ett alternativt MFA  

Tjänsten Azure AD B2C använder en inbyggd telefonbaserad MFA-Provider för att leverera tidsbaserade eng ång slö sen ord (OTPs). Det är i form av ett röst samtal och SMS till användarens förregistrerade telefonnummer. Följande alternativa metoder är tillgängliga för olika scenarier:

När du använder **användar flöden** finns det två metoder för att bygga återhämtning:

- **Ändra användar flödes konfiguration**: när du upptäcker ett avbrott i den telefonbaserade eng ång slö sen ordet, ändrar du leverans metoden för eng ång slö sen ord från telefonbaserade till e-postbaserad och distribuerar om användar flödet och lämnar programmen oförändrade.

![skärm bild som visar inloggnings registrering](media/resilient-end-user-experiences/create-sign-in.png)

- **Ändra program**: för varje identitets aktivitet, till exempel registrering och inloggning, definierar du två uppsättningar användar flöden. Konfigurera första uppsättningen till att använda telefonbaserad eng ång slö sen ord och den andra till e-postbaserad eng ång slö sen ord. När du har identifierat ett avbrott i den telefonbaserade eng ång slö sen ordet, ändrar du och distribuerar om programmen så att de växlar från den första uppsättningen användar flöden till den andra, så att användarna inte kan ändra dem.  

När du använder **anpassade principer** finns det fyra metoder för att bygga återhämtning. Listan nedan är i komplexitets ordningen och du måste distribuera om uppdaterade principer.

- **Aktivera användar val av antingen telefonbaserade eng ång slö sen ord eller e-postbaserat eng ång slö sen ord**: Visa båda alternativen för användarna och gör det möjligt för användare att själv välja ett av alternativen. Du behöver inte göra några ändringar i principerna eller programmen.

- **Växla dynamiskt mellan telefonbaserad eng ång slö sen ord och e-postbaserad eng ång slö sen ord**: samla in information om både telefoner och e-post. Definiera en anpassad princip i förväg för att villkorligt växla under ett telefon avbrott, från telefonbaserad till e-postbaserad eng ång slö sen ord. Du behöver inte göra några ändringar i principerna eller programmen.

- **Använd en Authenticator-app**: uppdatera anpassad princip om du vill använda en [Authenticator-app](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-mfa-totp). Om din normala MFA är antingen telefonbaserad eller e-postbaserad eng ång slö sen ord, distribuera sedan om dina anpassade principer för att växla till att använda Authenticator-appen.

>[!Note]
>Användare måste konfigurera autentiserare för program varu integrering under registreringen.

- **Använd säkerhets frågor**: om ingen av metoderna ovan gäller implementerar du säkerhets frågor som en säkerhets kopia. Ställ in säkerhets frågor för användare under onboarding eller profil redigering och lagra svaren i en annan databas än katalogen. Den här metoden uppfyller MFA-kravet för "något du har", till exempel telefon, men erbjuder ett sekundärt "något som du känner".

## <a name="use-a-content-delivery-network"></a>Använd ett Content Delivery Network

Content Delivery Network (CDN) fungerar bättre och billigare än BLOB-Arkiv för lagring av anpassat användar flödes gränssnitt. Webb sidans innehåll levereras snabbare från ett geografiskt distribuerat nätverk med hög tillgängliga servrar.  

Testa regelbundet din CDN tillgänglighet och prestanda för innehålls distribution genom ett slut punkt till slut punkts scenario och belastnings testning. Om du planerar för en kommande överspänning på grund av befordran eller semester trafik kan du ändra beräkningarna för belastnings testning.
  
## <a name="next-steps"></a>Nästa steg

- [Återhämtnings resurser för Azure AD B2C utvecklare](resilience-b2c.md)
  
  - [Elastiska gränssnitt med externa processer](resilient-external-processes.md)
  - [Återhämtning genom bästa praxis för utvecklare](resilience-b2c-developer-best-practices.md)
  - [Återhämtning genom övervakning och analys](resilience-with-monitoring-alerting.md)
- [Bygg återhämtning i din infrastruktur för autentisering](resilience-in-infrastructure.md)
- [Öka återhämtningen av autentisering och auktorisering i dina program](resilience-app-development-overview.md)