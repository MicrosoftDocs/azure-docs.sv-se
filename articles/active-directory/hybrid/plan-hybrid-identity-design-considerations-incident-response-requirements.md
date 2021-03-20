---
title: Hybrid Identitets design – krav för incident svar Azure | Microsoft Docs
description: Fastställ övervaknings-och rapporterings funktioner för Hybrid identitets lösningen som kan utnyttjas av den för att vidta åtgärder för att identifiera och minimera potentiella hot
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c68be4e46693621f77cb25dd8f34f8e0b7d75dd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89278419"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Fastställ krav på incident svar för din hybrid identitets lösning
Stora eller medel stora organisationer kommer förmodligen att ha [svar på säkerhets incidenter](/previous-versions/tn-archive/cc700825(v=technet.10)) på plats för att hjälpa dem att vidta åtgärder i enlighet med nivån av incident. Identitets hanterings systemet är en viktig komponent i incident svars processen eftersom den kan användas för att identifiera vem som utför en speciell åtgärd mot målet. Hybrid identitets lösningen måste kunna tillhandahålla övervaknings-och rapporterings funktioner som kan utnyttjas av den för att vidta åtgärder för att identifiera och minimera ett potentiellt hot. I en typisk incident svars plan kommer du att ha följande faser som en del av planen:

1. Första utvärderingen.
2. Incident kommunikation.
3. Skada kontroll och risk minskning.
4. Identifiering av vad det var komprometterande och allvarlighets grad.
5. Bevarande av bevis.
6. Meddelande till lämpliga parter.
7. System återställning.
8. Handlingar.
9. Skada och kostnads bedömning.
10. Bearbeta och planera revision.

Under identifieringen av vad det var komprometterande och allvarlighets grad, är det nödvändigt att identifiera de system som har komprometterats, filer som har öppnats och avgör hur känsliga filerna är. Ditt hybrid identitets system ska kunna uppfylla dessa krav för att hjälpa dig att identifiera den användare som gjorde ändringarna. 

## <a name="monitoring-and-reporting"></a>Övervakning och rapportering
Ofta kan identitets systemet också hjälpa till med den inledande utvärderings fasen om systemet har skapat funktioner för granskning och rapportering. Under den inledande utvärderingen måste IT-administratören kunna identifiera en misstänkt aktivitet, eller så ska systemet kunna utlösa det automatiskt baserat på en förkonfigurerad uppgift. Många aktiviteter kan tyda på en möjlig attack, men i andra fall kan ett dåligt konfigurerat system leda till ett antal falska positiva identifieringar i ett system för intrångs avkänning. 

Identitets hanterings systemet bör hjälpa IT-administratörer att identifiera och rapportera misstänkta aktiviteter. Dessa tekniska krav kan uppfyllas genom att övervaka alla system och ha en rapporterings funktion som kan markera potentiella hot. Använd frågorna nedan för att hjälpa dig att utforma din hybrid identitets lösning och ta hänsyn till incident svars krav:

* Har företaget ett säkerhets tillbuds svar på plats?
  * Om ja, är det aktuella identitets hanterings systemet som används som en del av processen?
* Behöver företaget identifiera misstänkta inloggnings försök från användare på olika enheter?
* Behöver företaget identifiera potentiella komprometterade användares autentiseringsuppgifter?
* Behöver företaget granska användarens åtkomst och åtgärd?
* Behöver företaget veta när en användare återställer sitt lösen ord?

## <a name="policy-enforcement"></a>Policyframtvingande
Under skade kontrollen och risk minsknings fasen är det viktigt att snabbt minska de faktiska och potentiella effekterna av en attack. Den åtgärd som du vidtar vid den här punkten kan göra skillnaden mellan en mindre och en större. Det exakta svaret beror på din organisation och vilken typ av attack du möter. Om den inledande utvärderingen slutat att ett konto har komprometterats måste du tillämpa principen för att blockera det här kontot. Det är bara ett exempel där identitets hanterings systemet kommer att utnyttjas. Använd frågorna nedan för att hjälpa dig att utforma din hybrid identitets lösning och ta hänsyn till hur principerna kommer att framtvingas för att reagera på en pågående incident:

* Har ditt företag principer för att blockera användare från att få åtkomst till nätverket om det behövs?
  * Om ja, integreras den aktuella lösningen med det hybrid identitets hanterings system som du ska använda?
* Behöver företaget använda villkorlig åtkomst för användare i karantän? 

> [!NOTE]
> Skriv ner varje svar och försök förstå anledningen till svaret. [Definiera data skydds strategi](plan-hybrid-identity-design-considerations-data-protection-strategy.md) går över tillgängliga alternativ och fördelar/nack delar med varje alternativ.  Genom att ha besvarat dessa frågor väljer du det alternativ som passar dina affärs behov bäst.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Definiera strategi för data skydd](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Se även
[Översikt över design överväganden](plan-hybrid-identity-design-considerations-overview.md)