---
title: Hybrid Identity design – Multi-Factor Authentication-krav Azure | Microsoft Docs
description: Med villkorlig åtkomst kontroll verifierar Azure AD de specifika villkor som du väljer när du autentiserar användaren och innan du tillåter åtkomst till programmet.
documentationcenter: ''
services: active-directory
author: billmath
manager: billmath
editor: ''
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
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
ms.openlocfilehash: 7d8ddf372e234bab242e4b28ba53dce7dd68cc89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90976047"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Fastställ krav för Multi-Factor Authentication för din hybrid identitets lösning
I den här världen av rörlighet har användare som har åtkomst till data och program i molnet och från vilken enhet som helst som skyddar den här informationen blivit ytterst stor.  Varje dag finns det en ny rubrik om en säkerhets överträdelse.  Även om det inte finns någon garanti för sådana överträdelser, använder Multi-Factor Authentication ett extra säkerhets lager för att förhindra dessa överträdelser.
Börja med att utvärdera organisations kraven för Multi-Factor Authentication. Det vill säga vad organisationen försöker skydda.  Den här utvärderingen är viktig för att definiera de tekniska kraven för att konfigurera och aktivera organisationers användare för Multi-Factor Authentication.

Se till att svara på följande:

* Försöker företaget skydda Microsoft-appar? 
* Hur de här apparna publiceras?
* Ger företaget fjärråtkomst till gång till att tillåta att anställda får åtkomst till lokala appar?

Om ja, vilken typ av fjärråtkomst? Du måste också utvärdera var de användare som har åtkomst till dessa program kommer att finnas. Den här utvärderingen är ett annat viktigt steg för att definiera rätt Multi-Factor Authentication-strategi. Se till att besvara följande frågor:

* Var kommer användarna att finnas?
* Kan de finnas var som helst?
* Vill företaget upprätta begränsningar enligt användarens plats?

När du har förstått de här kraven är det viktigt att även utvärdera användarens krav för Multi-Factor Authentication. Den här utvärderingen är viktig eftersom den definierar kraven för att distribuera Multi-Factor Authentication. Se till att besvara följande frågor:

* Är användarna bekant med Multi-Factor Authentication?
* Kommer vissa användnings områden att krävas för att tillhandahålla ytterligare autentisering?  
  * Om ja, när som helst, när de kommer från externa nätverk eller åtkomst till vissa program eller under andra omständigheter?
* Behöver användarna utbildning för att konfigurera och implementera Multi-Factor Authentication?
* Vilka är de viktigaste scenarierna som företaget vill aktivera Multi-Factor Authentication för sina användare?

När du har besvarat föregående frågor kan du förstå om det redan finns Multi-Factor Authentication lokalt. Den här utvärderingen är viktig för att definiera de tekniska kraven för att konfigurera och aktivera organisationers användare för Multi-Factor Authentication. Se till att besvara följande frågor:

* Behöver företaget skydda privilegierade konton med MFA?
* Behöver företaget aktivera MFA för vissa program för att kunna följa dessa orsaker?
* Behöver företaget aktivera MFA för alla berättigade användare av programmet eller endast administratörer?
* Behöver MFA alltid aktive ras eller bara när användarna loggas ut utanför företagets nätverk?

## <a name="next-steps"></a>Nästa steg
[Definiera en strategi för införande av hybrid identitet](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Se även
[Översikt över design överväganden](plan-hybrid-identity-design-considerations-overview.md)

