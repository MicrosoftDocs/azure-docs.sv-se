---
title: Öka återhämtningen av autentiserings-och auktoriserings program som du utvecklar
titleSuffix: Microsoft identity platform
description: Översikt över vår återhämtnings vägledning för program utveckling med Azure Active Directory och Microsoft Identity Platform
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: d06e851390537bf94b59e656f84bf58fe7216410
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96317360"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>Öka återhämtningen av autentiserings-och auktoriserings program som du utvecklar

Microsoft Identity använder modern token-baserad autentisering och auktorisering. Det innebär att ett program hämtar token från en identitets leverantör för att autentisera användaren och för att ge programmet möjlighet att anropa skyddade API: er.

En token är giltig under en viss tids period innan appen måste hämta en ny. Ett anrop för att hämta en token kan sällan Miss lyckas på grund av ett problem som nätverks-eller infrastruktur fel eller strömavbrott i tjänsten. I det här dokumentet beskriver vi steg för steg hur en utvecklare kan ta för att öka återhämtningen i sina program om ett fel uppstår vid hämtning av token.

De här artiklarna ger vägledning om ökad återhämtning i appar med hjälp av Microsoft Identity Platform och Azure Active Directory. Det finns vägledning för både klient program som arbetar på uppdrag av en inloggad användare och program för daemon som arbetar på egen ställe. De innehåller metod tips för att använda tokens och anropa resurser.

- [Bygg återhämtning till program som loggar in användare](resilience-client-app.md)
- [Bygg återhämtning till program utan användare](resilience-daemon-app.md)
- [Bygg återhämtning i din infrastruktur för identitets-och åtkomst hantering](resilience-in-infrastructure.md)
- [Bygg återhämtning i dina CIAM-system](resilience-b2c.md)
