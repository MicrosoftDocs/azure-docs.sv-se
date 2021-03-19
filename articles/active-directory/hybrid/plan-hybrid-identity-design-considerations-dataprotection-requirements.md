---
title: Hybrid Identity design – data skydds krav Azure | Microsoft Docs
description: När du planerar din hybrid identitets lösning ska du identifiera kraven för data skydd för ditt företag och vilka alternativ som är tillgängliga för att uppfylla de här kraven bäst.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 056d0caf2bfc6e99d5d659a2561bc41844feb79a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "64918783"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Planera för att öka data säkerheten via en stark identitets lösning
Det första steget i att skydda data är att identifiera vem som har åtkomst till dessa data. Du måste också ha en identitets lösning som kan integreras med systemet för att tillhandahålla funktioner för autentisering och auktorisering. Autentisering och auktorisering förväxlas ofta med varandra och deras roller är feltolkade. I verkligheten är de olika, som du ser i bilden nedan:

![livs cykel för mobila enheter](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**Livscykelstadier för hantering av mobila enheter**

När du planerar din hybrid identitets lösning måste du förstå kraven på data skydd för ditt företag och vilka alternativ som är tillgängliga för att bäst uppfylla dessa krav.

> [!NOTE]
> När du har slutfört planeringen av data säkerhet kan du granska [kraven för Multi-Factor Authentication](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) för att se till att dina val av krav på Multi-Factor Authentication inte påverkades av de beslut som du gjorde i det här avsnittet.
> 
> 

## <a name="determine-data-protection-requirements"></a>Fastställa krav för data skydd
Hos de flesta företag har de flesta företag ett gemensamt mål: gör det möjligt för användarna att vara produktiva på sina mobila enheter, samtidigt som de är lokalt, eller via fjärr anslutning från var som helst, för att öka produktiviteten. Företag som har sådana krav kommer också att bekymra sig om antalet hot som måste minskas för att skydda företagets data och upprätthålla användarens sekretess. Varje företag kan ha olika krav i detta hänseende. olika regler för efterlevnad som varierar beroende på vilken bransch företaget agerar kommer att leda till olika design beslut. 

Det finns dock vissa säkerhets aspekter som bör utforskas och verifieras, oavsett bransch.

## <a name="data-protection-paths"></a>Data skydds vägar
![data skydds vägar](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Data skydds vägar**

I diagrammet ovan är identitets komponenten den första som ska verifieras innan data nås. Dessa data kan dock finnas i olika tillstånd under den tid som den har öppnats. Varje nummer i diagrammet representerar en sökväg där data kan finnas vid en viss tidpunkt. Dessa siffror förklaras nedan:

1. Data skydd på enhets nivå.
2. Data skydd under överföring.
3. Data skydd i vila lokalt.
4. Data skydd i vila i molnet.

Det är nödvändigt att hybrid identitets lösningen kan använda både lokala och molnbaserade identitets hanterings resurser för att identifiera användaren innan den beviljar åtkomst till data. När du planerar din hybrid identitets lösning bör du se till att följande frågor besvaras enligt organisationens krav:

## <a name="data-protection-at-rest"></a>Data skydd i vila
Oavsett var datan finns i vila (enhet, moln eller lokalt) är det viktigt att utföra en utvärdering för att förstå organisationens behov i detta hänseende. Se till att följande frågor visas för det här avsnittet:

* Behöver företaget skydda data i vila?
  * Om ja, är hybrid identitets lösningen som kan integreras med din aktuella lokala infrastruktur?
  * Om ja, är hybrid identitets lösningen som kan integreras med dina arbets belastningar som finns i molnet?
* Kan moln identitets hanteringen skydda användarens autentiseringsuppgifter och andra data som lagras i molnet?

## <a name="data-protection-in-transit"></a>Data skydd under överföring
Data som överförs mellan enheten och data centret eller mellan enheten och molnet måste skyddas. Dock är det inte nödvändigt vis en kommunikations process med en komponent utanför moln tjänsten. den flyttas internt, även mellan två virtuella nätverk. Se till att följande frågor visas för det här avsnittet:

* Behöver företaget skydda data under överföring?
  * Om ja, är hybrid identitets lösningen som kan integreras med säkra kontroller som SSL/TLS?
* Håller moln identitets hanteringen trafiken till och i katalog arkivet (inom och mellan data Center) signerade?

## <a name="compliance"></a>Efterlevnad
Reglerna, lagar och regler för efterlevnad varierar beroende på vilken bransch företaget tillhör. Företag i högreglerade branscher måste hantera identitets hanterings problem som rör problem med efterlevnad. Regler som Sarbanes-Oxley (SOX), sjukförsäkrings-och ansvars Act (HIPAA), Leach-Bliley Act (GLBA) och betalnings kortet bransch data säkerhets standard (PCI DSS) är strikta för identitet och åtkomst. Den hybrid identitets lösning som företaget kommer att anta måste ha de kärn funktioner som uppfyller kraven i en eller flera av dessa regler. Se till att följande frågor visas för det här avsnittet:

* Är hybrid identitets lösningen kompatibel med myndighets krav för verksamheten?
* Har hybrid identitets lösningen skapats 
* i funktioner som gör det möjligt för företaget att vara kompatibelt med bestämmelser om krav? 

> [!NOTE]
> Skriv ner varje svar och försök förstå anledningen till svaret. [Definiera data skydds strategi](plan-hybrid-identity-design-considerations-data-protection-strategy.md) går över tillgängliga alternativ och fördelar/nack delar med varje alternativ.  Genom att ha besvarat dessa frågor väljer du det alternativ som passar dina affärs behov bäst.
> 
> 

## <a name="next-steps"></a>Nästa steg
 [Fastställa krav för innehållshantering](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över design överväganden](plan-hybrid-identity-design-considerations-overview.md)

