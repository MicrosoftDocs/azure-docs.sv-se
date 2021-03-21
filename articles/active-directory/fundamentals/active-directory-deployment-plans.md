---
title: Distributions planer – Azure Active Directory | Microsoft Docs
description: Fullständig vägledning om hur du distribuerar många Azure Active Directory-funktioner.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4185ffd644d54c419f42c78326ca10bf100443c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99051438"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory-distributionsplaner
Letar du efter slut punkt till slut punkt om hur du distribuerar Azure Active Directory (Azure AD)-funktioner? Distributions planer för Azure AD vägleder dig genom affärs värde, planerings överväganden och operativa procedurer som krävs för att distribuera vanliga funktioner i Azure AD.

Från någon av plan sidorna använder du webbläsarens utskrifts-till-PDF-funktion för att skapa en uppdaterad offline-version av dokumentationen.


## <a name="deploy-authentication"></a>Distribuera autentisering

| Funktion | Beskrivning|
| -| -|
| [Multifaktorautentisering](../authentication/howto-mfa-getstarted.md)| Azure AD Multi-Factor Authentication (MFA) är Microsofts lösning för verifiering av två steg. Med hjälp av godkända autentiseringsmetoder hjälper Azure AD MFA till att skydda åtkomsten till dina data och program samtidigt som du kan uppfylla behovet av en enkel inloggnings process. Titta på den här videon om [hur du konfigurerar och framtvingar Multi-Factor Authentication i din klient](https://www.youtube.com/watch?v=qNndxl7gqVM)|
| [Villkorlig åtkomst](../conditional-access/plan-conditional-access.md)| Med villkorlig åtkomst kan du implementera automatiska åtkomst kontroll beslut för vilka som kan komma åt dina molnappar, baserat på villkor. |
| [Återställning av lösenord för självbetjäning](../authentication/howto-sspr-deployment.md)| Lösen ords återställning via självbetjäning hjälper dina användare att återställa sina lösen ord utan att administratören behöver göra det, när och var de behöver. |
| [Lösenordsfri](../authentication/howto-authentication-passwordless-deployment.md) | Implementera lösenordsbaserad autentisering med hjälp av Microsoft Authenticator-appen eller FIDO2 säkerhets nycklar i din organisation |

## <a name="deploy-application-and-device-management"></a>Distribuera hantering av program och enheter

| Funktion | Beskrivning|
| -| - |
| [Enkel inloggning](../manage-apps/plan-sso-deployment.md)| Enkel inloggning hjälper dina användare att komma åt de appar och resurser som de behöver för att göra affärer medan de bara loggar in en gång. När de har loggat in kan de gå från Microsoft Office till SalesForce till Box till interna program utan att behöva ange autentiseringsuppgifterna en andra gång. |
| [Mina appar](../manage-apps/my-apps-deployment-plan.md)| Erbjud dina användare en enkel hubb för att upptäcka och komma åt alla sina program. Gör det möjligt för dem att bli mer produktiva med självbetjänings funktioner, t. ex. att begära åtkomst till appar och grupper eller att hantera åtkomst till resurser för andras räkning. |
| [Egenskaper](../devices/plan-device-deployment.md) | Den här artikeln hjälper dig att utvärdera metoder för att integrera din enhet med Azure AD, välja implementerings plan och innehåller viktiga länkar till enhets hanterings verktyg som stöds. |


## <a name="deploy-hybrid-scenarios"></a>Distribuera hybrid scenarier

| Funktion | Beskrivning|
| -| -|
| [ADFS till hash-synkronisering av lösenord](../hybrid/plan-migrate-adfs-password-hash-sync.md)| Med hash-synkronisering av lösen ord synkroniseras hashar av användar lösen ord från lokala Active Directory till Azure AD, vilket gör att Azure AD kan autentisera användare utan interaktion med den lokala Active Directory |
| [ADFS till Pass Through Authentication](../hybrid/plan-migrate-adfs-pass-through-authentication.md) (Passeringsautentisering)| Med Azure AD-direktautentisering kan användarna logga in i både lokala och molnbaserade program med samma lösen ord. Den här funktionen ger användarna en bättre upplevelse – ett mindre lösen ord att komma ihåg – och minskar kostnaderna för IT-supportavdelningen eftersom det är mindre troligt att de glömmer att logga in. När användare loggar in med Azure AD verifierar den här funktionen användarnas lösenord direkt mot din lokala Active Directory. |
| [Azure-AD-programproxy](../manage-apps/application-proxy-deployment-plan.md) |I dag vill anställda vara produktiva var som helst, när som helst och från valfri enhet. De behöver åtkomst till SaaS-appar i molnet och i företags program lokalt. Azure AD Application Proxy ger denna robust åtkomst utan kostsamma och komplexa virtuella privata nätverk (VPN) eller demilitariserad zoner (DMZs). |
| [Sömlös enkel inloggning](../hybrid/how-to-connect-sso-quick-start.md)| Sömlös enkel inloggning med Azure Active Directory (sömlös SSO med Azure AD) loggar automatiskt in användare när de är på sina företagsenheter som är anslutna till företagsnätverket. Med den här funktionen behöver användarna inte ange sina lösen ord för att logga in på Azure AD och behöver vanligt vis inte ange sina användar namn. Den här funktionen ger auktoriserade användare enkel åtkomst till dina molnbaserade program utan att behöva ytterligare lokala komponenter. |

## <a name="deploy-user-provisioning"></a>Distribuera användar etablering

| Funktion | Beskrivning|
| -| -|
| [Användaretablering](../app-provisioning/plan-auto-user-provisioning.md)| Azure AD hjälper dig att automatisera skapande, underhåll och borttagning av användaridentiteter i molnprogram (SaaS), till exempel Dropbox, Salesforce, ServiceNow och mer. |
| [Molnets användar etablering för HR](../app-provisioning/plan-cloud-hr-provision.md)| Cloud HR User etablering till Active Directory skapar en grund för pågående identitets styrning och förbättrar kvaliteten på affärs processer som förlitar sig på auktoritativa identitets data. Med hjälp av den här funktionen med din moln produkt i molnet, t. ex. workday eller SuccessFactors, kan du sömlöst hantera identitets livs cykeln för anställda och eventualtillgångar genom att konfigurera regler som mappar antecknings processer – överlåtande processer (till exempel ny hyra, avsluta, överför) till etablerings åtgärder (till exempel skapa, aktivera, inaktivera) |

## <a name="deploy-governance-and-reporting"></a>Distribuera styrning och rapportering

| Funktion | Beskrivning|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| Azure AD Privileged Identity Management (PIM) hjälper dig att hantera privilegierade administrativa roller i Azure AD, Azure-resurser och andra Microsoft Online Services. PIM innehåller lösningar som just-in-Time-åtkomst, begär godkännande arbets flöden och fullständigt integrerade åtkomst granskningar, så att du kan identifiera, upptäcka och förhindra illvilliga aktiviteter för privilegierade roller i real tid. |
| [Rapportering och övervakning](../reports-monitoring/plan-monitoring-and-reporting.md)| Utformningen av din Azure AD-rapportering och övervaknings lösning är beroende av dina juridiska, säkerhets-och drift krav samt din befintliga miljö och befintliga processer. Den här artikeln visar de olika design alternativen och vägleder dig till rätt distributions strategi. |
| [Åtkomst granskningar](../governance/deploy-access-reviews.md) | Åtkomst granskningar är en viktig del av din styrnings strategi, så att du kan känna till och hantera vem som har åtkomst och vad de har åtkomst till. Den här artikeln hjälper dig att planera och distribuera åtkomst granskningar för att uppnå önskad säkerhets-och samarbets postures. |

## <a name="include-the-right-stakeholders"></a>Ta med rätt intressenter

När du påbörjar distributions planeringen för en ny funktion är det viktigt att inkludera viktiga intressenter i organisationen. Vi rekommenderar att du identifierar och dokumenterar den person eller de personer som uppfyller var och en av följande roller, och arbetar med dem för att fastställa deras medverkan i projektet.  

Roller kan innehålla följande 

|Roll |Beskrivning |
|-|-|
|Slutanvändare|En representativ grupp användare för vilka funktionen ska implementeras. Visar ofta ändringarna i ett pilotprogram.
|IT-support ansvarig|IT-avdelningen stöder organisations representant som kan tillhandahålla insikter om den här förändringen från ett support perspektiv.  
|Identitets arkitekt eller Global Azure-administratör|Identitets hanterings team som är ansvarig för att definiera hur den här ändringen justeras med infrastrukturen för kärn identitets hantering i din organisation.|
|Programmets företags ägare |Den övergripande företags ägaren av de berörda programmen, som kan innehålla hanterings åtkomst.Kan även ge indata om användar upplevelsen och användbarheten för den här ändringen från slutanvändarens perspektiv.
|Säkerhets ägare|En representant från säkerhets teamet som kan godkänna att planen uppfyller organisationens säkerhets krav.|
|Compliance Manager (Efterlevnadshanteraren)|Den person i organisationen som ansvarar för att säkerställa efterlevnaden av företags-, bransch-eller myndighets krav.|

**Inblandning nivåer kan vara:**

- **R**-esponsible för implementering av projekt plan och resultat 

- **En** pproval av projekt plan och resultat 

- **C**-ontributor till projekt plan och resultat 

- **I** nformed för projekt plan och resultat


## <a name="best-practices-for-a-pilot"></a>Metod tips för en pilot
Med en pilot kan du testa med en liten grupp innan du aktiverar en funktion för alla. Se till att varje användnings fall i din organisation testas grundligt under testningen. Det är bäst att rikta in sig på en bestämd grupp pilot användare innan du går över till din organisation som helhet.

I din första Wave, rikta in dig på den, användbarhet och andra lämpliga användare som kan testa och ge feedback. Den här feedbacken bör användas för att ytterligare utveckla kommunikationen och anvisningarna som du skickar till användarna och ger insikter om vilka typer av problem som support Personalen kan se. 

Att utöka distributionen till större grupper av användare bör utföras genom att utöka omfånget för de grupper som är riktade till varandra. Detta kan göras via [dynamiskt grupp medlemskap](../enterprise-users/groups-dynamic-membership.md)eller genom att manuellt lägga till användare till mål grupperna.
