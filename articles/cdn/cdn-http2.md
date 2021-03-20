---
title: Stöd för HTTP/2 i Azure CDN | Microsoft Docs
description: Azure Content Delivery Network stöder HTTP/2, som har fördelar över HTTP/1, till exempel multiplexering & samtidighet, huvud komprimering och strömmande beroenden.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: ril
ms.openlocfilehash: d4ed04185da251d9042f6b54a192d0e49ef20ede
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92778540"
---
# <a name="http2-support-in-azure-cdn"></a>Stöd för HTTP/2 i Azure CDN

HTTP/2 är en större revidering av HTTP/1.1 \. Det ger snabbare webb prestanda, minskad svars tid och bättre användar upplevelse, samtidigt som de välkända HTTP-metoderna, status koderna och semantiken bibehålls. Även om HTTP/2 är utformad för att fungera med HTTP och HTTPS, stöder många klient webbläsare bara HTTP/2 över TLS.

### <a name="http2-benefits"></a>HTTP/2-fördelar

Fördelarna med HTTP/2 är:

*   **Multiplexering och samtidighet**

    Med hjälp av HTTP 1,1 krävs flera TCP-anslutningar för att göra flera resurs begär Anden, och varje anslutning har en associerad prestanda. HTTP/2 gör att flera resurser kan begäras på en enda TCP-anslutning.

*   **Sidhuvud komprimering**

    Genom att komprimera HTTP-huvudena för att hantera resurser, minskas tiden på kabeln avsevärt.

*   **Strömma beroenden**

    Stream-beroenden gör att klienten kan ange för servern vilka resurser har prioritet.


## <a name="http2-browser-support"></a>Stöd för HTTP/2-webbläsare

Alla större webbläsare har implementerat HTTP/2-stöd i sina aktuella versioner. Webbläsare som inte stöds återgår automatiskt till HTTP/1.1.

|Webbläsare|Lägsta version|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Aktivera stöd för HTTP/2 i Azure CDN

HTTP/2-stöd är för närvarande aktivt för alla Azure CDN profiler. Ingen ytterligare åtgärd krävs från kunderna.

## <a name="next-steps"></a>Nästa steg

Om du vill se fördelarna med HTTP/2 i praktiken kan du läsa [den här demon från Akamai](https://http2.akamai.com/demo).

Om du vill veta mer om HTTP/2 kan du besöka följande resurser:

*   [Start sida för HTTP/2-specifikation](https://http2.github.io/)
*   [Vanliga frågor och svar om HTTP/2](https://http2.github.io/faq/)
*   [Akamai HTTP/2-information](https://http2.akamai.com/)

Mer information om tillgängliga funktioner i Azure CDN finns i [Översikt över Azure CDN](./cdn-overview.md).