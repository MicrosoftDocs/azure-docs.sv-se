---
title: ta med fil
description: ta med fil
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 03/04/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2459cf6c5055dcde83dccf37addc160aeeaa64ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198668"
---
Händelse domäner förklaras enklast med ett exempel. Anta att du kör Contosos konstruktions maskiner, där du tillverkar traktorer, utforska-utrustning och andra tunga maskiner. Som en del av att köra verksamheten kan du skicka real tids information till kunder om utrustnings underhåll, system hälsa och kontrakt uppdateringar. All den här informationen går till olika slut punkter, inklusive din app, kund slut punkter och annan infrastruktur som kunderna har konfigurerat.

Med händelse domäner kan du modellera Contosos konstruktions maskiner som en enskild händelse enhet. Var och en av dina kunder visas som ett ämne i domänen. Autentisering och auktorisering hanteras med hjälp av Azure Active Directory. Var och en av dina kunder kan prenumerera på sitt ämne och få sina händelser levererade till dem. Hanterad åtkomst via händelse domänen säkerställer att de endast kan komma åt sina ämnen.

Du får också en enda slut punkt, som du kan publicera alla kund händelser till. Event Grid bör vara noga med att se till att varje ämne bara känner till de händelser som omfattas av klienten.

:::image type="content" source="./media/event-grid-domain-example-use-case/contoso-construction-example.png" alt-text="Exempel på Contoso-konstruktion" lightbox="./media/event-grid-domain-example-use-case/contoso-construction-example.png":::