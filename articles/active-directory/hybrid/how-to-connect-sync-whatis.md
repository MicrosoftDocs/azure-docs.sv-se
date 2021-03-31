---
title: 'Azure AD Connect synkronisering: förstå och anpassa synkronisering | Microsoft Docs'
description: Förklarar hur Azure AD Connect Sync fungerar och hur du anpassar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cea26cb119f64679807bc6c5eaadb41b341e5d5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89662385"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD Connect-synkronisering: Förstå och anpassa synkronisering
Azure Active Directory Connect Synchronization Services (Azure AD Connect Sync) är en huvud del av Azure AD Connect. Det tar hand om alla åtgärder som är relaterade till synkronisering av identitets data mellan din lokala miljö och Azure AD. Azure AD Connect Sync är efterföljaren till DirSync, Azure AD Sync och Forefront Identity Manager med den Azure Active Directory-anslutning som kon figurer ATS.

Det här avsnittet är start för **Azure AD Connect Sync** (kallas även **Synkroniseringsmotorn**) och listar länkar till alla andra avsnitt som är relaterade till det. Länkar till Azure AD Connect finns i [integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

Synkroniseringstjänsten består av två komponenter, den lokala **Azure AD Connect Sync** -komponenten och tjänst sidan i Azure AD som kallas **Azure AD Connect Sync-tjänst**.

## <a name="azure-ad-connect-sync-topics"></a>Avsnitt om Azure AD Connect synkronisering
| Avsnitt | Vad det täcker och när du ska läsa |
| --- | --- |
| **Grundläggande Azure AD Connect synkronisering** | |
| [Förstå arkitekturen](concept-azure-ad-connect-sync-architecture.md) |För dem som är nya för Synkroniseringsmotorn och vill lära dig mer om arkitekturen och de termer som används. |
| [Tekniska begrepp](how-to-connect-sync-technical-concepts.md) |En kort version av arkitektur ämnet och beskriver kortfattat de termer som används. |
| [Topologier för Azure AD Connect](plan-connect-topologies.md) |Beskriver de olika topologier och scenarier som stöds av Sync-motorn. |
| **Anpassad konfiguration** | |
| [Köra installations guiden igen](how-to-connect-installation-wizard.md) |Förklarar vilka alternativ som finns tillgängliga när du kör installations guiden för Azure AD Connect igen. |
| [Förstå deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md) |Beskriver konfigurations modellen med namnet deklarativ etablering. |
| [Förstå uttryck för deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |Beskriver syntaxen för det uttrycks språk som används i deklarativ etablering. |
| [Förstå standardkonfigurationen](concept-azure-ad-connect-sync-default-configuration.md) |Beskriver de färdiga reglerna och standard konfigurationen. Beskriver också hur reglerna fungerar tillsammans för de färdiga scenarierna som fungerar. |
| [Förstå användare och kontakter](concept-azure-ad-connect-sync-user-and-contacts.md) |Fortsätter i föregående avsnitt och beskriver hur konfigurationen för användare och kontakter fungerar tillsammans, särskilt i en miljö med flera skogar. |
| [Så här gör du en ändring i standard konfigurationen](how-to-connect-sync-change-the-configuration.md) |Vägleder dig genom hur du gör en gemensam konfigurations ändring i Attribute-flöden. |
| [Metodtips för att ändra standardkonfigurationen](how-to-connect-sync-best-practices-changing-default-configuration.md) |Support begränsningar och för att göra ändringar i den inbyggda konfigurationen. |
| [Konfigurera filtrering](how-to-connect-sync-configure-filtering.md) |Beskriver de olika alternativen för hur du begränsar vilka objekt som synkroniseras till Azure AD och steg för steg hur du konfigurerar dessa alternativ. |
| **Funktioner och scenarier** | |
| [Förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md) |Beskriver funktionen *förhindra oavsiktliga borttagningar* och hur du konfigurerar den. |
| [Scheduler](how-to-connect-sync-feature-scheduler.md) |Beskriver den inbyggda Schemaläggaren, som importerar, synkroniserar och exporterar data. |
| [Implementera hash-synkronisering av lösen ord](how-to-connect-password-hash-synchronization.md) |Beskriver hur Lösenordssynkronisering fungerar, hur du implementerar och hur du hanterar och felsöker. |
| [Tillbakaskrivning av enheter](how-to-connect-device-writeback.md) |Beskriver hur enhets tillbakaskrivning fungerar i Azure AD Connect. |
| [Katalogtillägg](how-to-connect-sync-feature-directory-extensions.md) |Beskriver hur du utökar Azure AD-schemat med dina egna anpassade attribut. |
| [Microsoft 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) |Beskriver hur du sätter användarens Microsoft 365 resurser i samma region som användaren. |
| **Synkroniseringstjänsten** | |
| [Tjänstfunktioner för Azure AD Connect-synkronisering](how-to-connect-syncservice-features.md) |Beskriver sidan Sync service och hur du ändrar synkroniseringsinställningar i Azure AD. |
| [Återhämtning av duplicerat attribut](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |Beskriver hur du aktiverar och använder **userPrincipalName** och **proxyAddresses** av duplicerade attributvärden. |
| **Åtgärder och användar gränssnitt** | |
| [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) |Beskriver Synchronization Service Manager användar gränssnitt, inklusive [åtgärder](how-to-connect-sync-service-manager-ui-operations.md), [kopplingar](how-to-connect-sync-service-manager-ui-connectors.md), [metaversum designer](how-to-connect-sync-service-manager-ui-mvdesigner.md)och sökflikar för [metaversum](how-to-connect-sync-service-manager-ui-mvsearch.md) . |
| [Drift uppgifter och överväganden](./how-to-connect-sync-staging-server.md) |Beskriver drift problem, till exempel haveri beredskap. |
| **Hur...** | |
| [Återställa Azure AD-kontot](how-to-connect-azureadaccount.md) |Så här återställer du autentiseringsuppgifterna för det tjänst konto som används för att ansluta från Azure AD Connect synkronisera till Azure AD. |
| **Mer information och referenser** | |
| [Portar](reference-connect-ports.md) |Visar vilka portar du måste öppna mellan Synkroniseringsmotorn och dina lokala kataloger och Azure AD. |
| [Attribut som synkroniseras till Azure Active Directory](reference-connect-sync-attributes-synchronized.md) |Visar en lista över alla attribut som synkroniseras mellan lokala AD och Azure AD. |
| [Referens för funktioner](reference-connect-sync-functions-reference.md) |Visar en lista över alla funktioner som är tillgängliga i deklarativ etablering. |

## <a name="additional-resources"></a>Ytterligare resurser
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)