---
title: Skapa elastisk identitets-och åtkomst hantering med Azure Active Directory
description: En guide för arkitekter, IT-administratörer och utvecklare om att skapa återhämtning för att störa sina identitets system.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39021fecb79f6b8e7536daad592378ed998760a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96454354"
---
# <a name="building-resilience-into-identity-and-access-management-with-azure-active-directory"></a>Skapa återhämtning till identitets-och åtkomst hantering med Azure Active Directory

Identitets-och åtkomst hantering (IAM) är ett ramverk med processer, principer och tekniker som underlättar hanteringen av identiteter och vad de får åtkomst till. Den innehåller många komponenter som stöder autentisering och auktorisering av användare och andra konton i systemet.

IAM-återhämtning är möjligheten att göra avbrott i system komponenter och återställa med minimal påverkan på verksamheten, användare, kunder och åtgärder. Att minska beroenden, komplexitet och enstaka fel och samtidigt se till att omfattande fel hantering ökar din återhämtning.

Störningar kan komma från alla komponenter i dina IAM-system. För att bygga ett elastiskt IAM-system, antar vi att avbrott uppstår och planera för det. 

När du planerar återhämtning av din IAM-lösning bör du tänka på följande element: 

* Dina program som är beroende av ditt IAM-system.

* De offentliga infrastrukturer som dina autentisering samtal använder, inklusive tele bolag, Internet leverantörer och offentliga nyckel leverantörer.

* Dina moln-och lokala identitets leverantörer.

* Andra tjänster som är beroende av ditt IAM och de API: er som ansluter dem.

* Andra lokala komponenter i systemet.

Vad som är viktigt för att identifiera och planera för oförutsedda utgifter. Att lägga till ytterligare identitets system och deras resulterande beroenden och komplexitet kan dock minska din återhämtning snarare än att öka det.

Om du vill bygga mer återhämtning i dina system kan du läsa följande artiklar:

* [Bygg återhämtning i din IAM-infrastruktur](resilience-in-infrastructure.md)

* [Bygg IAM-återhämtning i dina program](resilience-app-development-overview.md)

* [Bygg återhämtning i dina CIAM-system (Customer Identity and Access Management)](resilience-b2c.md)
