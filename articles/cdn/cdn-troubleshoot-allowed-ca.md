---
title: Tillåten certifikat utfärdare för att aktivera anpassad HTTPS
titleSuffix: Azure Content Delivery Network
description: Om du använder ett eget certifikat för att aktivera HTTPS på en anpassad domän, måste du använda en tillåten certifikat utfärdare (CA) för att skapa det.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f98e28c89fa70831108cfbbbaca6e2f316d1b039
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99573406"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https"></a>Tillåtna certifikat utfärdare för att aktivera anpassad HTTPS

Specifika certifikat krav krävs när du [aktiverar https-funktionen genom att använda ditt eget certifikat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) för en Azure CDN (Content Delivery Network) anpassad domän. 

* **Azure CDN Standard från Microsoft** Profile kräver ett certifikat från en av de godkända certifikat utfärdarna (ca) i följande lista. Om ett certifikat från en certifikat utfärdare som inte är godkänt eller om ett självsignerat certifikat används, avvisas begäran. 

* **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** -profiler accepterar alla giltiga certifikat från valfri giltig certifikat utfärdare. Verizon-profiler stöder inte självsignerade certifikat.

> [!NOTE]
> Alternativet att använda ditt eget certifikat för att aktivera HTTPS-funktionen för anpassade domäner är *inte* tillgänglig för **Azure CDN Standard från Akamai** -profiler. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

