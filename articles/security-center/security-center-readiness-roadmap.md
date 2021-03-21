---
title: Beredskapsöversikt för Azure Security Center | Microsoft Docs
description: I det här dokumentet finns en beredskapsöversikt för att komma igång med Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: e4f905b7a08564eadb20d65d651c137ef3741376
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100329"
---
# <a name="azure-security-center-readiness-roadmap"></a>Azure Security Center beredskaps översikt
I det här dokumentet får du en beredskapsöversikt som hjälper dig att komma igång med Azure Security Center.

## <a name="understanding-security-center"></a>Förstå Security Center
Azure Security Center har en enhetlig säkerhetshantering och avancerat skydd mot hot för arbetsbelastningar som körs i Azure, lokalt och i andra moln. 

Använd följande resurser för att komma igång med Security Center.

Artiklar
- [Introduktion till Azure Security Center](security-center-introduction.md)
- [Snabbstartsguide för Azure Security Center](security-center-get-started.md)

Videoklipp
- [Snabb introduktionsvideo](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [Översikt över Security Center-funktioner för skydd, identifiering och svar](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Planering och drift

För att få ut mesta möjliga av Security Center är det viktigt att veta hur olika medarbetare och avdelningar i organisationen kommer att använda tjänsten så att kraven på säkerhet vid drift, övervakning, styrning och incidenthantering uppfylls.

Med följande resurser får du hjälp under planerings- och driftprocesserna.

- [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-security-center"></a>Registrering av datorer till Security Center
Security Center identifierar automatiskt de Azure-prenumerationer eller arbets ytor som inte skyddas av Azure Defender. Detta inkluderar Azure-prenumerationer som använder Security Center lediga och arbets ytor som inte har säkerhets lösningen aktive rad.

Med följande resurser får du hjälp under registreringsprocesserna.

- [Publicera icke-Azure-datorer](quickstart-onboard-machines.md)
- [Azure Security Center Hybrid – Översikt](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Minimera säkerhetsproblem med Security Center
Security Center samlar automatiskt in, analyserar och integrerar loggdata från Azure-resurser, nätverket och anslutna partnerlösningar som brandväggs- och slutpunktsskyddslösningar för att identifiera verkliga hot och minimera antalet falska positiva identifieringar.

Följande resurser hjälper dig att hantera säkerhetsaviseringar och skydda dina resurser.

Artiklar    
- [Skydda nätverket i Azure Security Center](./security-center-network-recommendations.md)
- [Skydda Azure SQL-tjänsten och data i Azure Security Center](./security-center-remediate-recommendations.md)


Video    
- [Minimera säkerhetsproblem med Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Security Center för svar om incidenter
För att minska kostnaderna och skadan är det viktigt att ha en incident svars plan på plats innan en attack äger rum. Azure Security Center kan användas i olika faser av en incidenthantering.

Med följande resurser kan du lära dig hur Security Center kan införlivas i processen för incidenthantering.

Videoklipp    
* [Incidenthantering i Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Reagera snabbt på hot med nästa generations säkerhetsåtgärder och undersökning](https://youtu.be/e8iFCz5RM4g)

Artiklar    
* [Använda Azure Security Center vid incidenthantering](./tutorial-security-incident.md)
* [Använd Automation för att svara på Security Center utlösare](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Avancerat molnskydd

Virtuella Azure-datorer kan dra nytta av funktioner för avancerat molnskydd i Security Center. Dessa funktioner omfattar just-in-Time-åtkomst till virtuella datorer och anpassningsbara program kontroller.

Använd följande resurser för att lära dig mer om hur du använder funktionerna i Security Center.

Videoklipp    
* [Azure Security Center – just-in-Time VM-åtkomst](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center – Anpassningsbara programkontroller](https://youtu.be/wWWekI1Y9ck)

Artiklar    
* [Hantera åtkomst till virtuella datorer med just-in-Time](./security-center-just-in-time.md)
* [Anpassningsbara programkontroller i Azure Security Center](./security-center-adaptive-application.md)

## <a name="hands-on-activities"></a>Praktiska aktiviteter

* [Praktiska övningar i Security Center](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Rekommendationsspelbok för brandvägg för webbaserade program (WAF) i Security Center](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Spelbok om Azure Security Center: Säkerhetsaviseringar](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Ytterligare resurser
* [Dokumentationssidan om Security Center](./index.yml)
* [Dokumentationssidan om REST-API för Security Center](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Vanliga frågor och svar om Azure Security Center](./faq-general.md)
* [Prissättningssidan för Security Center](https://azure.microsoft.com/pricing/details/security-center/)
* [Metodtips för identitetssäkerhet](../security/fundamentals/identity-management-best-practices.md)
* [Metodtips för nätverkssäkerhet](../security/fundamentals/network-best-practices.md)
* [PaaS-rekommendationer](../security/fundamentals/paas-deployments.md)
* [Efterlevnad](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Log Analytics-kunder kan nu använda Azure Security Center för att skydda sina hybrid moln arbets belastningar](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)

## <a name="community-resources"></a>Community-resurser

* [Security Center UserVoice](https://feedback.azure.com/forums/347535-azure-security-center)
* [F&en sida för Security Center](/answers/topics/azure-security-center.html)