---
title: 'Program hantering: metod tips och rekommendationer | Microsoft Docs'
description: Lär dig metod tips och rekommendationer för att hantera program i Azure Active Directory. Lär dig mer om att använda automatisk etablering och publicera lokala appar med programproxyn.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7a570fb322d24bf0d32efcb6f1a2ee515862755
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736977"
---
# <a name="application-management-best-practices"></a>Metod tips för program hantering

Den här artikeln innehåller rekommendationer och metod tips för att hantera program i Azure Active Directory (Azure AD), med automatisk etablering och publicering av lokala appar med programproxy.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Rekommendationer för Cloud App och enkel inloggning
| Rekommendation | Kommentarer |
| --- | --- |
| Kontrol lera appar i Azure AD-programgalleriet  | Azure AD har ett galleri som innehåller tusentals förintegrerade program som är aktiverade med enkel inloggning (SSO) för företag. Information om enhetsspecifika installations anvisningar finns i [listan med självstudier för SaaS-appar](../saas-apps/tutorial-list.md).  | 
| Använda federerad SAML-baserad SSO  | När ett program stöder det använder du federerad, SAML-baserad SSO med Azure AD i stället för lösenordsbaserad SSO och ADFS.  | 
| Använd SHA-256 för certifikat signering  | Azure AD använder SHA-256-algoritmen som standard för att signera SAML-svaret. Använd SHA-256 om inte programmet kräver SHA-1 (se [alternativ för certifikat signering](certificate-signing-options.md) och [program inloggnings problem](application-sign-in-problem-application-error.md).)  | 
| Kräv användar tilldelning  | Som standard kan användarna få åtkomst till dina företags program utan att de har tilldelats dem. Men om programmet visar roller, eller om du vill att programmet ska visas i användarens Mina appar, kräver du användar tilldelning.  | 
| Distribuera Mina appar till dina användare | [Mina appar](end-user-experiences.md) på `https://myapps.microsoft.com` är en webbaserad portal som förser användare med en enda post punkt för sina tilldelade molnbaserade program. Som ytterligare funktioner, t. ex. grupp hantering och lösen ords återställning via självbetjäning, kan användarna hitta dem i Mina appar. Se [Planera distribution av mina appar](access-panel-deployment-plan.md).
| Använd grupp tilldelning  | Om den ingår i din prenumeration kan du tilldela grupper till ett program så att du kan delegera kontinuerlig åtkomst hantering till grupp ägaren.  | 
| Upprätta en process för att hantera certifikat | Den maximala livs längden för ett signerings certifikat är tre år. För att förhindra eller minimera avbrott på grund av att ett certifikat upphör att gälla, använder du roller och e-postdistributionslistor för att se till att certifikat som rör ändrings aviseringar övervakas noga. |

## <a name="provisioning-recommendations"></a>Etablerings rekommendationer
| Rekommendation | Kommentarer |
| --- | --- |
| Använd Självstudier för att konfigurera etablering med molnappar | Se [listan över SaaS-självstudier](../saas-apps/tutorial-list.md) för att få stegvisa anvisningar om hur du konfigurerar etablering för den Galleri app som du vill lägga till. |
| Använd etablerings loggar (för hands version) för att övervaka status | [Etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) ger information om alla åtgärder som utförs av etablerings tjänsten, inklusive status för enskilda användare. |
| Tilldela en distributions grupp till e-postmeddelandet för etablering | Om du vill öka synligheten för kritiska aviseringar som skickas av etablerings tjänsten tilldelar du en distributions grupp till inställningen e-postaviseringar. |


## <a name="application-proxy-recommendations"></a>Rekommendationer för programproxy
| Rekommendation | Kommentarer |
| --- | --- |
| Använda Application Proxy för fjärråtkomst till interna resurser | Application Proxy rekommenderas för att ge fjärran vändare åtkomst till interna resurser, vilket ersätter behovet av en VPN-eller omvänd proxy. Den är inte avsedd för åtkomst till resurser inifrån företags nätverket eftersom det kan lägga till fördröjning.
| Använd anpassade domäner | Konfigurera anpassade domäner för dina program (se [Konfigurera anpassade domäner](application-proxy-configure-custom-domain.md)) så att webb adresser för användare och mellan program fungerar antingen i eller utanför nätverket. Du kan också styra din anpassning och anpassa dina URL: er.  När du använder anpassade domän namn bör du planera att skaffa ett offentligt certifikat från en betrodd certifikat utfärdare som inte tillhör Microsoft. Azure Application Proxy stöder standard-, ([jokertecken](application-proxy-wildcard.md)) eller San-baserade certifikat. (Mer information finns i [Application Proxy-planering](application-proxy-deployment-plan.md).) |
| Synkronisera användare innan du distribuerar programproxy | Innan du distribuerar Application Proxy ska du synkronisera användar identiteter från en lokal katalog eller skapa dem direkt i Azure AD. Identitetssynkronisering gör det möjligt för Azure AD att förautentisera användare innan de får åtkomst till publicerade program i App proxy. Den innehåller också information om den användar identifierare som krävs för att utföra enkel inloggning (SSO). (Mer information finns i [Application Proxy-planering](application-proxy-deployment-plan.md).) |
| Följ våra tips för hög tillgänglighet och belastnings utjämning | Om du vill lära dig hur trafikflödena delas mellan användare, programproxy-kopplingar och backend-appar och för att få tips om hur du optimerar prestanda och belastnings utjämning, se [hög tillgänglighet och belastnings utjämning för dina programproxy-kopplingar och-program](application-proxy-high-availability-load-balancing.md). |
| Använd flera kopplingar | Använd två eller flera Application Proxy-kopplingar för större återhämtning, tillgänglighet och skalning (se [Application Proxy-kopplingar](application-proxy-connectors.md)). Skapa anslutnings grupper och se till att varje kopplings grupp har minst två kopplingar (tre kopplingar är optimala). |
| Hitta anslutnings servrar nära program servrar och kontrol lera att de befinner sig i samma domän | Du optimerar prestanda genom att fysiskt leta upp kopplings servern nära program servrarna (se [överväganden för nätverks miljön](application-proxy-network-topology.md)). Dessutom bör kopplings servern och webb program servrarna tillhöra samma Active Directory domän, eller så bör de omfatta betrodda domäner. Den här konfigurationen krävs för enkel inloggning med integrerad Windows-autentisering (IWA) och Kerberos-begränsad delegering (KCD). Om servrarna finns i olika domäner måste du använda resurs-baserad delegering för SSO (se [KCD för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-kcd.md)). |
| Aktivera automatiska uppdateringar för anslutningar | Aktivera automatiska uppdateringar för dina anslutningar för de senaste funktionerna och fel korrigeringarna. Microsoft tillhandahåller direkt support för den senaste anslutnings versionen och en version. (Se [versions historik för programproxyn](application-proxy-release-version-history.md).) |
| Kringgå din lokala proxy | För enklare underhåll kan du konfigurera anslutningen så att den kringgår din lokala proxy så att den ansluter direkt till Azure-tjänsterna. (Se [Application Proxy-kopplingar och proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md).) |
| Använd Azure AD-programproxy över Webbprogramproxy | Använd Azure AD-programproxy för de flesta lokala scenarier. Webbprogramproxy rekommenderas bara i scenarier som kräver en proxyserver för AD FS och där du inte kan använda anpassade domäner i Azure Active Directory. (Se [programproxy-migrering](application-proxy-migration.md).) |