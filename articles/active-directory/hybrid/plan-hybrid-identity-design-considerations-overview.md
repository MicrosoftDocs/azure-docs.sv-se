---
title: Design överväganden för Azure Active Directory hybrid identitet – översikt | Microsoft Docs
description: Översikt över och innehålls kartor för Hybrid identitets guide för utformning
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7f8dd49f3668b8f68753681123a04d21edac46c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997740"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Azure Active Directory Hybrid Identity designöverväganden
Konsumentbaserade enheter är av samma nivå som företags världen och SaaS-program (program vara som en tjänst) är enkla att anta. Därför är det svårt att upprätthålla kontroll över användarnas program åtkomst över interna Data Center och moln plattformar.  

Microsofts identitetslösningar omfattar både lokala och molnbaserade funktioner, skapar en enda användaridentitet för autentisering och auktorisering till alla resurser, oavsett plats. Det här begreppet kallas hybrid identitet. Det finns olika design-och konfigurations alternativ för Hybrid identitet med Microsoft-lösningar, och i vissa fall kan det vara svårt att avgöra vilken kombination som passar bäst för din organisation. 

Den här hand boken för Hybrid identitets utformning hjälper dig att förstå hur du utformar en hybrid identitets lösning som bäst passar affärs-och teknik behoven för din organisation.  Den här guiden innehåller information om en serie steg och uppgifter som du kan följa för att utforma en hybrid identitets lösning som uppfyller organisationens unika krav. I alla steg och aktiviteter visar guiden relevanta tekniker och funktions alternativ som är tillgängliga för organisationer för att uppfylla funktions-och kvalitets krav (till exempel tillgänglighet, skalbarhet, prestanda, hantering och säkerhet). 

Mer specifikt är hybrid identitys utformnings guide målen för att besvara följande frågor: 

* Vilka frågor behöver jag ställa och svara på att köra en hybrid identitets bestämd design för en teknik eller problem domän som bäst uppfyller mina krav?
* Vilken sekvens av aktiviteter bör jag slutföra för att designa en hybrid identitets lösning för teknik-eller problem domänen? 
* Vilken hybrid identitets teknik och vilka konfigurations alternativ finns tillgängliga för att hjälpa mig uppfylla kraven? Vad sker mellan dessa alternativ så att jag kan välja det bästa alternativet för mitt företag?

## <a name="who-is-this-guide-intended-for"></a>Vem riktar sig den här handboken till?
 CIO, CITO, Chief Identity Architects, Enterprise Architects och IT-arkitekter som ansvarar för att utforma en hybrid identitets lösning för medel stora eller stora organisationer.

## <a name="how-can-this-guide-help-you"></a>Hur kan den här handboken hjälpa dig?
Du kan använda den här guiden för att lära dig att utforma en hybrid identitets lösning som kan integrera ett molnbaserad identitets hanterings system med din aktuella lokala identitets lösning. 

Följande bild visar ett exempel på en hybrid identitets lösning som gör det möjligt för IT-administratörer att hantera den aktuella Windows Server Active Directory-lösningen som finns lokalt med Microsoft Azure Active Directory för att göra det möjligt för användare att använda enskilda Sign-On (SSO) över program som finns i molnet och lokalt.

![Exempel](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

Illustrationen ovan är ett exempel på en hybrid identitets lösning som använder moln tjänster för att integrera med lokala funktioner för att tillhandahålla en enskild upplevelse för processen för slutanvändare och för att under lätta hanteringen av dessa resurser. Även om det här exemplet kan vara ett vanligt scenario är alla organisationens hybrid Identitets design förmodligen annorlunda än exemplet som illustreras i bild 1 på grund av olika krav. 

Den här guiden innehåller en serie steg och uppgifter som du kan följa för att utforma en hybrid identitets lösning som uppfyller organisationens unika krav. I följande steg och uppgifter presenterar guiden de relevanta tekniker och funktions alternativ som finns tillgängliga för att uppfylla kraven på funktions-och service kvalitets nivå för din organisation.

**Antaganden**: du har viss erfarenhet av Windows Server, Active Directory Domain Services och Azure Active Directory. I det här dokumentet förutsätts att du söker efter hur dessa lösningar kan uppfylla dina affärs behov på egen hand eller i en integrerad lösning.

## <a name="design-considerations-overview"></a>Översikt över design överväganden
Det här dokumentet innehåller en uppsättning steg och uppgifter som du kan följa för att utforma en hybrid identitets lösning som bäst uppfyller dina krav. Stegen visas i tur och ordning. Design överväganden som du lär dig i senare steg kan kräva att du ändrar beslut som du har gjort i tidigare steg, på grund av motstridiga design alternativ. Varje försök görs att varna dig om potentiella design konflikter i hela dokumentet. 

Du kommer till den design som bäst uppfyller dina krav endast efter att du har gått igenom stegen så många gånger som behövs för att ta med alla överväganden i dokumentet. 

| Hybrid identitets fas | Ämnes lista |
| --- | --- |
| Fastställa identitetskrav |[Fastställa affärs behov](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Fastställa krav för katalog synkronisering](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Fastställa krav för Multi-Factor Authentication](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definiera en strategi för införande av hybrid identitet](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Planera för att öka data säkerheten via stark identitets lösning |[Fastställa krav för data skydd](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Fastställa krav för innehållshantering](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Fastställa åtkomstkontrollkrav](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Fastställa krav för incidenthantering](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definiera strategi för data skydd](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Planera för hybrididentitetslivscykeln |[Fastställa uppgifter för Hybrid identitets hantering](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Hantering av synkronisering](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Fastställ strategi för införande av hybrid identitets hantering](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Nästa steg
[Fastställa identitetskrav](plan-hybrid-identity-design-considerations-business-needs.md)

