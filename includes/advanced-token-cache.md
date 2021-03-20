---
title: ta med fil
description: ta med fil
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 11/05/2020
ms.author: kkrishna
ms.openlocfilehash: 174946667885debc348370ef2c6f93206890e9c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94628018"
---
Du kan använda MSAL för att tillåta att huvudappar, API: er och tjänster använder sig av Access token cache för att fortsätta att agera för användarnas räkning i sin frånvaro. Detta är särskilt användbart om huvudappar och-tjänster måste fortsätta att fungera för användarens räkning när användaren har avslutat frontend-webbappen.

Idag använder de flesta bakgrunds processer [program behörigheter](/graph/auth/auth-concepts#microsoft-graph-permissions) när de behöver arbeta med användarens data utan att de är tillgängliga för autentisering eller omautentisering. Eftersom program behörigheter ofta kräver administratörs medgivande, vilket kräver rättighets ökning, påträffas onödig friktion eftersom utvecklaren inte avsåg att få behörighet utöver den som användaren ursprungligen godkände till för appen.

Det här kod exemplet på GitHub visar hur du undviker denna onödiga friktion genom att komma åt MSAL ' token cache från huvudappar:

 [Åtkomst till den inloggade användarens token-cache från bakgrunds program, API: er och tjänster](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)