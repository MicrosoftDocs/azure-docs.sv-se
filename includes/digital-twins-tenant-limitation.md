---
author: baanders
description: inkludera fil som beskriver begränsningen mellan klientorganisationen med Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 16684f8c5947f8b6a09a9a785968dabf3e644c18
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589387"
---
Därför kräver begäranden till Azure Digital Twins API:er en användare eller tjänstens huvudnamn som är en del av samma klientorganisation där Azure Digital Twins instansen finns. För att förhindra skadlig genomsökning Azure Digital Twins slutpunkter returneras begäranden med åtkomsttoken utanför den ursprungliga klientorganisationen ett felmeddelande om att "404 Sub-Domain hittades inte". Det här felet returneras *även* om användarens eller tjänstens huvudnamn har fått rollen Azure Digital Twins dataägare eller Azure Digital Twins [dataläsare](../articles/digital-twins/concepts-security.md) via [Azure AD B2B-samarbete.](../articles/active-directory/external-identities/what-is-b2b.md) 