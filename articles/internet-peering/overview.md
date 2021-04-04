---
title: Konfigurera peering med Microsoft
titleSuffix: Azure
description: Översikt över peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 8f960e58d47963cbb239d498af52e7adfc77caa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586874"
---
# <a name="internet-peering-overview"></a>Översikt över Internet-peering

Peering är en sammanlänkning mellan Microsofts globala nätverk (AS8075) och ditt nätverk för att kunna utbyta Internet trafik från/till Microsoft onlinetjänster och Microsoft Azure-tjänster. Leverantörer eller tjänst leverantörer kan begära att ansluta till Microsoft på någon av våra gräns platser. Varje begäran granskas av Microsoft för att säkerställa att den följer vår peering-princip. Du kan konfigurera en peering med Microsoft Network på två sätt:

* **Direkt peering:**

    Peering har upprättats över direkta fysiska anslutningar mellan Microsoft-nätverk på Microsoft Edge och nätverket. BGP-sessioner konfigureras för de här anslutningarna enligt Routningsprincipen och enligt förhandlat avtal. Detta kallas även PNI.

* **Exchange-peering:**

    Detta avser standard-offentliga peering-anslutningar i Internet utbyten (IX). De fysiska anslutningarna mellan Microsoft-nätverk och nätverket är via växel infrastruktur som drivs av IX. BGP-sessioner konfigureras med hjälp av det IP-utrymme som tillhandahålls av IX.

## <a name="benefits-of-peering-with-microsoft"></a>Fördelar med peering med Microsoft
* Sänk dina överförings kostnader genom att leverera Microsoft-trafik med hjälp av peering med Microsoft.
* Förbättra prestanda för dina kunder genom att minska nätverks hopp och svars tid till Microsoft Edge-nätverk.
* Skydda kund trafik mot problem i nätverket eller transit leverantörens nätverk genom peering med Microsoft på redundanta platser.
* Lär dig prestanda mått om dina peering-anslutningar och Använd insikter för att felsöka nätverket.

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Fördelar med att använda Azure för att konfigurera peering

Du kan begära peering med Microsoft med hjälp av Azure PowerShell eller portalen. Peering-konfiguration på det här sättet hanteras som en Azure-resurs och ger följande fördelar:
* Förenklade och automatiserade steg för att konfigurera och hantera peering med Microsoft.
* Snabbt och enkelt sätt att visa och hantera alla dina kollegor på ett och samma ställe.
* Spåra status-och bandbredds data för alla dina anslutningar.
* Du kan använda samma prenumeration för att få åtkomst till Azure-Cloud Services.

Om du redan har upprättat peer-kopplingar med Microsoft kallas de för **äldre peer**-kopplingar. Du kan välja att hantera sådana peer-kopplingar som Azure Resource för att dra nytta av de ovannämnda fördelarna. Om du vill skicka en ny peering-begäran eller konvertera äldre peering till Azure Resource följer du länkarna i avsnittet **Nästa steg** nedan.

## <a name="peering-policy"></a>Peering-policy
Microsoft har en selektiv, men ofta öppen peering-princip. Peer-datorer väljs utifrån prestanda, kapacitet och var de har ömsesidig nytta och omfattas av vissa tekniska, kommersiella och juridiska krav. Mer information finns i [peering-principen](policy.md).

## <a name="faq"></a>Vanliga frågor
Vanliga frågor och svar om peering finns i [Internet-peering – vanliga frågor och svar](faqs.md).

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om hur du konfigurerar direkt peering med Microsoft följer du [genom gången av direkt peering](walkthrough-direct-all.md)
* Om du vill veta mer om hur du konfigurerar Exchange-peering med Microsoft följer du [genom gången av Exchange-peering](walkthrough-exchange-all.md)
* Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.