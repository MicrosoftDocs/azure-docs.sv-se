---
title: 'Azure AD Connect: msExchUserHoldPolicies och cloudMsExchUserHoldPolicies | Microsoft Docs'
description: Det här avsnittet beskriver attributets beteende för attributen msExchUserHoldPolicies och cloudMsExchUserHoldPolicies
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fbda588d99de44c77118586519055a8fc474104
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861773"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect-msExchUserHoldPolicies och cloudMsExchUserHoldPolicies
I följande referens dokument beskrivs de attribut som används av Exchange och det korrekta sättet att redigera standard reglerna för synkronisering.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Vad är msExchUserHoldPolicies och cloudMsExchUserHoldPolicies?
Det finns två typer av [undantag](/Exchange/policy-and-compliance/holds/holds) som är tillgängliga för en Exchange-Server: tvister och In-Place undantag. När tvister har Aktiver ATS spärras alla objekt i post lådan.  En In-Place-spärr används endast för att bevara de objekt som uppfyller villkoren i en Sök fråga som du har definierat med hjälp av verktyget In-Place eDiscovery.

Attributen MsExchUserHoldPolcies och cloudMsExchUserHoldPolicies tillåter lokala AD och Azure AD för att avgöra vilka användare som är under ett undantag beroende på om de använder lokalt Exchange eller Exchange Online.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>flöde för msExchUserHoldPolicies-synkronisering
Som standard synkroniseras MsExchUserHoldPolcies av Azure AD Connect direkt till msExchUserHoldPolicies-attributet i metaversum och sedan till msExchUserHoldPolicies-attributet i Azure AD

I följande tabeller beskrivs flödet:

Inkommande från lokala Active Directory:

|Active Directory attribut|Attributnamn|Flödes typ|Metaversum-attribut|Synkroniseringsregel|
|-----|-----|-----|-----|-----|
|Lokalt Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|I från AD-User Exchange|

Utgående till Azure AD:

|Metaversum-attribut|Attributnamn|Flödes typ|Azure AD-attribut|Synkroniseringsregel|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|Ut till AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>flöde för cloudMsExchUserHoldPolicies-synkronisering
Som standard synkroniseras cloudMsExchUserHoldPolicies av Azure AD Connect direkt till cloudMsExchUserHoldPolicies-attributet i metaversum. Sedan, om msExchUserHoldPolicies inte är null i metaversum, skickas attributet i utflödet till Active Directory.

I följande tabeller beskrivs flödet:

Inkommande från Azure AD:

|Active Directory attribut|Attributnamn|Flödes typ|Metaversum-attribut|Synkroniseringsregel|
|-----|-----|-----|-----|-----|
|Lokalt Active Directory|cloudMsExchUserHoldPolicies|Direct|cloudMsExchUserHoldPolicies|I från AAD – användar utbyte|

Utgående till lokal Active Directory:

|Metaversum-attribut|Attributnamn|Flödes typ|Azure AD-attribut|Synkroniseringsregel|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF (NOT NULL)|msExchUserHoldPolicies|Ut till AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Information om attributets beteende
MsExchangeUserHoldPolicies är ett enda Authority-attribut.  Ett enda Authority-attribut kan anges för ett objekt (i det här fallet användar objekt) i den lokala katalogen eller i moln katalogen.  Starten av auktoritets regler anger att om attributet synkroniseras från den lokala datorn kommer Azure AD inte att kunna uppdatera det här attributet.

Om du vill tillåta att användare anger en undantags princip för ett användar objekt i molnet används attributet cloudMSExchangeUserHoldPolicies. Det här attributet används eftersom Azure AD inte kan ställa in msExchangeUserHoldPolicies direkt baserat på de regler som beskrivs ovan.  Det här attributet synkroniseras sedan tillbaka till den lokala katalogen om, msExchangeUserHoldPolicies inte är null och ersätter det aktuella värdet för msExchangeUserHoldPolicies.

Under vissa omständigheter, till exempel, om båda har ändrats lokalt och i Azure på samma gång kan detta orsaka vissa problem.  

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
