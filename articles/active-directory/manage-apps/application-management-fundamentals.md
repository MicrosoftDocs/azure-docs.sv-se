---
title: 'Programhantering: Metodtips och rekommendationer för | Microsoft Docs'
description: Lär dig metodtips och rekommendationer för att hantera program i Azure Active Directory. Läs mer om att använda automatisk etablering och publicering av lokala appar med Programproxy.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9b7f312781fd4f14c5e403ad72e5978f4d01487
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379336"
---
# <a name="application-management-best-practices"></a>Metodtips för programhantering

Den här artikeln innehåller rekommendationer och metodtips för att hantera program i Azure Active Directory (Azure AD), använda automatisk etablering och publicera lokala appar med Programproxy.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Molnapp och rekommendationer för enkel inloggning
| Rekommendation | Kommentarer |
| --- | --- |
| Kontrollera apparna i Azure AD-programgalleriet  | Azure AD har ett galleri som innehåller tusentals förintegrerade program som är aktiverade med enkel inloggning (SSO) för Enterprise. Appspecifika konfigurationsguider finns i [självstudierna lista över SaaS-appar.](../saas-apps/tutorial-list.md)  | 
| Använda federerad SAML-baserad enkel inloggning  | När ett program stöder det använder du Federerad, SAML-baserad enkel inloggning med Azure AD i stället för lösenordsbaserad enkel inloggning och ADFS.  | 
| Använda SHA-256 för certifikatsignering  | Azure AD använder SHA-256-algoritmen som standard för att signera SAML-svaret. Använd SHA-256 om inte programmet kräver SHA-1 (se [Alternativ för](certificate-signing-options.md) certifikatsignering och [Program inloggningsproblem](application-sign-in-problem-application-error.md).)  | 
| Kräv användartilldelning  | Som standard kan användare komma åt dina företagsprogram utan att tilldelas till dem. Men om programmet exponerar roller, eller om du vill att programmet ska visas på en användares Mina appar kräver användartilldelning.  | 
| Distribuera Mina appar till dina användare | [Mina appar](end-user-experiences.md) på är en webbaserad portal som ger användarna en enda startpunkt `https://myapps.microsoft.com` för sina tilldelade molnbaserade program. När ytterligare funktioner som grupphantering och lösenordsåterställning via självbetjäning läggs till kan användarna hitta dem i Mina appar. Se [Planera Mina appar distribution.](my-apps-deployment-plan.md)
| Använda grupptilldelning  | Om det ingår i din prenumeration tilldelar du grupper till ett program så att du kan delegera pågående åtkomsthantering till gruppägaren.  | 
| Upprätta en process för att hantera certifikat | Den maximala livslängden för ett signeringscertifikat är tre år. Använd roller och e-postdistributionslistor för att säkerställa att certifikatrelaterade ändringsmeddelanden övervakas noggrant för att förhindra eller minimera avbrott på grund av att ett certifikat upphör att gälla. |

## <a name="provisioning-recommendations"></a>Etableringsrekommendationer
| Rekommendation | Kommentarer |
| --- | --- |
| Använd självstudier för att konfigurera etablering med molnappar | I listan [över saaS-appsjälvstudier](../saas-apps/tutorial-list.md) finns stegvisa anvisningar om hur du konfigurerar etablering för den galleriapp som du vill lägga till. |
| Använda etableringsloggar (förhandsversion) för att övervaka status | [Etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) ger information om alla åtgärder som utförs av etableringstjänsten, inklusive status för enskilda användare. |
| Tilldela en distributionsgrupp till etableringsmeddelandet via e-post | Om du vill öka synligheten för kritiska aviseringar som skickas av etableringstjänsten tilldelar du en distributionsgrupp till inställningen E-postaviseringar. |


## <a name="application-proxy-recommendations"></a>Programproxy rekommendationer
| Rekommendation | Kommentarer |
| --- | --- |
| Använda Programproxy för fjärråtkomst till interna resurser | Programproxy rekommenderas för att ge fjärranslutna användare åtkomst till interna resurser, vilket ersätter behovet av en VPN-anslutning eller omvänd proxy. Det är inte avsett för åtkomst till resurser inifrån företagsnätverket eftersom det kan ge längre svarstider.
| Använda anpassade domäner | Konfigurera anpassade domäner för dina program (se Konfigurera anpassade domäner [)](application-proxy-configure-custom-domain.md)så att URL:er för användare och mellan program fungerar inifrån eller utanför nätverket. Du kommer också att kunna kontrollera din anpassning och anpassa url:er.  När du använder anpassade domännamn bör du planera att skaffa ett offentligt certifikat från en betrodd certifikatutfärdare som inte kommer från Microsoft. Azure Application Proxy stöder[SAN-baserade](application-proxy-wildcard.md)standardcertifikat ( jokertecken). (Se [Programproxy planering](application-proxy-deployment-plan.md).) |
| Synkronisera användare innan du distribuerar Programproxy | Innan du distribuerar programproxy synkroniserar du användaridentiteter från en lokal katalog eller skapar dem direkt i Azure AD. Identitetssynkronisering gör att Azure AD kan autentisera användare i förväg innan de beviljas åtkomst till publicerade program i App Proxy. Den innehåller också nödvändig information om användaridentifierare för att utföra enkel inloggning (SSO). (Se [Programproxy planering](application-proxy-deployment-plan.md).) |
| Följ våra tips för hög tillgänglighet och belastningsutjämning | Information om hur trafik flödar mellan användare, Programproxy-anslutningsappar och backend-appservrar samt tips för att optimera prestanda och belastningsutjämning finns i Hög tillgänglighet och belastningsutjämning för [dina Programproxy-anslutningsappar](application-proxy-high-availability-load-balancing.md)och program. |
| Använda flera anslutningsappar | Använd två eller flera Programproxy anslutningsappar för bättre återhämtning, tillgänglighet och skalning (se [Programproxy anslutningsappar](application-proxy-connectors.md)). Skapa anslutningsgrupper och se till att varje anslutningsgrupp har minst två anslutningsappar (tre anslutningsappar är optimala). |
| Leta rätt på anslutningsservrarna nära programservrarna och kontrollera att de finns i samma domän | Om du vill optimera prestandan letar du fysiskt upp anslutningsservern nära programservrarna (se [Överväganden för nätverkstopologi).](application-proxy-network-topology.md) Dessutom bör anslutningsservern och webbprogramservrarna tillhöra samma Active Directory-domän, eller så bör de sträcka sig över betrodda domäner. Den här konfigurationen krävs för enkel inloggning med Integrerad Windows-autentisering (IWA) och Kerberos-begränsad delegering (KCD). Om servrarna finns i olika domäner måste du använda resursbaserad delegering för enkel inloggning (se [KCD](application-proxy-configure-single-sign-on-with-kcd.md)för enkel inloggning med Programproxy ). |
| Aktivera automatiska uppdateringar för anslutningsappar | Aktivera automatiska uppdateringar för dina anslutningsappar för de senaste funktionerna och felkorrigeringarna. Microsoft har direkt stöd för den senaste versionen av anslutningsappen och en tidigare version. (Se [Programproxy versionshistorik .)](application-proxy-release-version-history.md) |
| Kringgå din lokala proxy | För enklare underhåll konfigurerar du anslutningsappen så att den kringgår din lokala proxy så att den ansluter direkt till Azure-tjänsterna. (Se [Programproxy anslutningsappar och proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md).) |
| Använda Azure AD Programproxy över Programproxy | Använd Azure AD Programproxy för de flesta lokala scenarier. Webb Programproxy är endast att föredra i scenarier som kräver en proxyserver för AD FS och där du inte kan använda anpassade domäner i Azure Active Directory. (Se [Programproxy migrering](application-proxy-migration.md).) |