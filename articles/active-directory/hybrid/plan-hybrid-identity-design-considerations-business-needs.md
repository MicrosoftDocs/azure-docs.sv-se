---
title: Identitets krav för Hybrid moln Identitets design Azure | Microsoft Docs
description: Identifiera företagets affärs behov som gör att du kan definiera kraven för Hybrid identitets designen.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
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
ms.openlocfilehash: 074c203a0a5688855d4f7607a877b25ce6ee6ad7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89660574"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Fastställa identitets krav för din hybrid identitets lösning
Det första steget i att designa en hybrid identitets lösning är att fastställa kraven för den affärs organisation som kommer att använda den här lösningen.  Hybrid identitet startar som en stödjande roll (den stöder alla andra moln lösningar genom att tillhandahålla autentisering) och är på för att tillhandahålla nya och intressanta funktioner som låser upp nya arbets belastningar för användare.  De arbets belastningar eller tjänster som du vill använda för dina användare avgör kraven för Hybrid identitets designen.  Dessa tjänster och arbets belastningar behöver utnyttja hybrid identitet både lokalt och i molnet.  

Du måste gå igenom dessa viktiga aspekter av verksamheten för att förstå vad det är ett krav nu och vad företaget planerar för framtiden. Om du inte har insyn i den långsiktiga strategin för Hybrid Identitets design, är det sannolikt att din lösning inte är skalbar eftersom affärs behoven växer och förändras. Diagrammet nedan visar ett exempel på en hybrid identitets arkitektur och de arbets belastningar som ska låsas upp för användare. Detta är bara ett exempel på alla nya möjligheter som kan låsas upp och levereras med en solid hybrid identitets strategi. 

Vissa komponenter som ingår i hybrid identitets arkitekturen hybrid ![ identitets arkitektur](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Fastställa affärs behov
Varje företag har olika krav, även om dessa företag ingår i samma bransch, kan de verkliga affärs kraven variera. Du kan fortfarande utnyttja bästa praxis från branschen, men i slut änden är det företagets affärs behov som kommer att leda till att du definierar kraven för Hybrid identitets designen. 

Var noga med att besvara följande frågor för att identifiera dina affärs behov:

* Vill företaget klippa ut drifts kostnaderna?
* Vill företaget se till att skydda moln till gångar (SaaS appar, infrastruktur)?
* Vill företaget modernisera din IT?
  * Är dina användare mer mobila och kräver det för att skapa undantag i din DMZ så att olika typer av trafik kan komma åt olika resurser?
  * Har företaget äldre appar som behövs för att publiceras till dessa moderna användare, men det är inte enkelt att skriva om?
  * Behöver företaget utföra alla dessa uppgifter och få dem att kontrol lera på samma tidpunkt?
* Är ditt företag som vill skydda användarnas identiteter och minska riskerna genom att införa nya verktyg som utnyttjar expert isen i Microsofts Azure-säkerhetsexpertis lokalt?
* Försöker företaget att ta bort dreaded "externa" konton lokalt och flytta dem till molnet där de inte längre är ett inaktivt hot i din lokala miljö?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analysera lokal identitets infrastruktur
Nu när du har en uppfattning om företagets affärs krav måste du utvärdera din lokala identitets infrastruktur. Den här utvärderingen är viktig för att definiera de tekniska kraven för att integrera din aktuella identitets lösning med moln identitets hanterings systemet. Se till att besvara följande frågor:

* Vilken autentiserings-och auktoriserad lösning använder företaget lokalt? 
* Har företaget för närvarande några lokala Sync-tjänster?
* Använder företaget eventuella identitets leverantörer från tredje part (IdP)?

Du måste också vara medveten om de moln tjänster som företaget kan ha. Det är mycket viktigt att utföra en utvärdering för att förstå den aktuella integrationen med SaaS-, IaaS-och PaaS-modeller i din miljö. Se till att besvara följande frågor under utvärderingen:

* Har företaget någon integrering med en moln tjänst leverantör?
* Om ja, vilka tjänster används?
* Är denna integrering för närvarande i produktion eller är den en pilot?

> [!NOTE]
> Cloud Discovery analyserar dina trafik loggar mot Microsoft Cloud App Securitys moln program katalog över 16 000-molnappar som rangordnas och poängs ätts baserat på fler än 70 riskfaktorer, för att ge dig en kontinuerlig insyn i moln användningen, skugga den och risken för att skugga den ingår i din organisation. Kom igång genom att se [konfigurera Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Utvärdera krav för identitets integrering
Sedan måste du utvärdera kraven för identitets integrering. Den här utvärderingen är viktig för att definiera de tekniska kraven för hur användarna ska autentiseras, hur organisationens närvaro kommer att se ut i molnet, hur organisationen tillåter auktorisering och vad användar upplevelsen kommer att vara. Se till att besvara följande frågor:

* Kommer din organisation att använda Federation, standard-autentisering eller både och?
* Är federationen ett krav?  På grund av följande:
  * Kerberos-baserad SSO
  * Ditt företag har ett lokalt program (antingen inbyggt eller tredje part) som använder SAML eller liknande Federations funktioner.
  * MFA via smartkort. RSA-SecurID, osv.
  * Klient åtkomst regler som åtgärdar frågorna nedan:
    1. Kan jag blockera all extern åtkomst till Microsoft 365 baserat på klientens IP-adress?
    2. Kan jag blockera all extern åtkomst till Microsoft 365, förutom Exchange ActiveSync?
    3. Kan jag blockera all extern åtkomst till Microsoft 365, förutom för webbläsarbaserade appar (OWA, SPO)
    4. Kan jag blockera all extern åtkomst till Microsoft 365 för medlemmar i angivna AD-grupper
* Säkerhet/gransknings problem
* Redan befintlig investering i federerad autentisering
* Vilket namn kommer vår organisation att använda för vår domän i molnet?
* Har organisationen en anpassad domän?
  1. Är domänen offentlig och lätt att verifiera via DNS?
  2. Om så inte är fallet har du en offentlig domän som kan användas för att registrera ett alternativt UPN i AD?
* Är användar identifierarna konsekventa för moln representation? 
* Har organisationen appar som kräver integrering med moln tjänster?
* Har organisationen flera domäner och kommer alla använda standard-eller federerad autentisering?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Utvärdera program som körs i din miljö
Nu när du har en idé om din lokala och moln infrastruktur måste du utvärdera de program som körs i dessa miljöer. Den här utvärderingen är viktig för att definiera de tekniska kraven för att integrera dessa program till moln identitets hanterings systemet. Se till att besvara följande frågor:

* Var kommer våra program att vara Live?
* Kommer användarna att komma åt lokala program?  I molnet? Eller både och?
* Finns det planer på att ta befintliga arbets belastningar för program och flytta dem till molnet?
* Finns det planer på att utveckla nya program som kommer att finnas antingen lokalt eller i molnet som ska använda molnbaserad autentisering?

## <a name="evaluate-user-requirements"></a>Utvärdera användar krav
Du måste också utvärdera användar kraven. Den här utvärderingen är viktig för att definiera de steg som behövs för att hålla igång och hjälpa användare när de övergår till molnet. Se till att besvara följande frågor:

* Kommer användarna att få åtkomst till program lokalt?
* Kommer användarna att få åtkomst till program i molnet?
* Hur loggar användare vanligt vis in på den lokala miljön?
* Hur kommer användarna att logga in i molnet?

> [!NOTE]
> Skriv ner varje svar och försök förstå anledningen till svaret. [Fastställ krav på incident svar](plan-hybrid-identity-design-considerations-incident-response-requirements.md) över tillgängliga alternativ och för-och nack delar med respektive alternativ.  Genom att ha besvarat dessa frågor väljer du det alternativ som passar dina affärs behov bäst.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställa krav för katalog synkronisering](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över design överväganden](plan-hybrid-identity-design-considerations-overview.md)

