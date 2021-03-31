---
title: Jämför produkt funktioner i Azure Content Delivery Network (CDN)
description: Läs mer om de funktioner som varje Azure Content Delivery Network-produkt (CDN) stöder.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 3a794d8915c820abecd530911f05ac649e5e538c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100379921"
---
# <a name="what-are-the-comparisons-between-azure-cdn-product-features"></a>Vilka är jämförelserna mellan Azure CDN produkt funktioner?

Azure Content Delivery Network (CDN) innehåller av fyra produkter: 

* **Azure CDN Standard från Microsoft**
* **Azure CDN Standard från Akamai**
* **Azure CDN Standard från Verizon**
* **Azure CDN Premium från Verizon**. 

I följande tabell jämförs de funktioner som är tillgängliga med respektive produkt.

| **Prestanda funktioner och optimeringar** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Acceleration av dynamisk webbplats](./cdn-dynamic-site-acceleration.md)  | Erbjuds via [Azures tjänst för front dörr](../frontdoor/front-door-overview.md) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Acceleration av dynamisk webbplats – adaptiv bild komprimering](./cdn-dynamic-site-acceleration.md#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Acceleration av dynamisk webbplats – för hämtning av objekt](./cdn-dynamic-site-acceleration.md#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Allmän optimering av webb leverans](./cdn-optimization-overview.md#general-web-delivery)  | **&#x2713;** | **&#x2713;** väljer du den här optimerings typen om den genomsnittliga fil storleken är mindre än 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Optimering av videoströmning](./cdn-media-streaming-optimization.md)  | via allmän webb leverans | **&#x2713;**  | via allmän webb leverans |  via allmän webb leverans |
| [Optimering av stora filer](./cdn-large-file-optimization.md)  | via allmän webb leverans | **&#x2713;** väljer du den här optimerings typen om den genomsnittliga fil storleken är större än 10 MB   | via allmän webb leverans |  via allmän webb leverans |
| Typ av ändrings optimering | |**&#x2713;** | | |
| Ursprungs port |Alla TCP-portar |[Tillåtna ursprungs portar](/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Alla TCP-portar |Alla TCP-portar |
| [Global serverbelastningsutjämning (GSLB)](../traffic-manager/traffic-manager-load-balancing-azure.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Snabbrensning](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** rensa alla och jokertecken stöds inte av Azure CDN från Akamai för närvarande |**&#x2713;** |**&#x2713;** |
| [Inläsning av tillgångar i förväg](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Inställningar för cache/huvud (med hjälp av [cachelagringsregler](cdn-caching-rules.md))  |**&#x2713;** med [standard regel motor](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Anpassningsbar, regel baserad innehålls leverans motor |**&#x2713;** med [standard regel motor](cdn-standard-rules-engine.md)  | | |**&#x2713;** med hjälp av [regel motor](./cdn-verizon-premium-rules-engine.md) |
| Inställningar för cache/huvud  |**&#x2713;** med [standard regel motor](cdn-standard-rules-engine.md) | | |**&#x2713;** med hjälp av [Premium Rules Engine](./cdn-verizon-premium-rules-engine.md) |
| URL-omdirigering/omskrivning |**&#x2713;** med [standard regel motor](cdn-standard-rules-engine.md)  | | |**&#x2713;** med hjälp av [Premium Rules Engine](./cdn-verizon-premium-rules-engine.md) |
| Regler för mobila enheter  |**&#x2713;** med [standard regel motor](cdn-standard-rules-engine.md) | | |**&#x2713;** med hjälp av [Premium Rules Engine](./cdn-verizon-premium-rules-engine.md) |
| [Cachelagring av frågesträngar](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Dual stack-IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Stöd för HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Säkerhet** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Stöd för HTTPS med CDN-slutpunkt | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Egen domän-HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** kräver direkt CNAME för att aktivera |**&#x2713;** |**&#x2713;** |
| [Stöd för anpassade domännamn](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geo-filtrering](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tokenautentisering](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS skydd](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Ha med ditt eget certifikat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| TLS-versioner som stöds | TLS 1,2, TLS 1.0/1.1- [konfigurerbart](/rest/api/cdn/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Analyser och rapporter** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Azure diagnostikloggar](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Core-rapporter från Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Anpassade rapporter från Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Avancerade HTTP-rapporter](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Realtidsstatistik](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Gränsnodsprestanda](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Realtidsaviseringar](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Enkel att använda** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Enkel integrering med Azure-tjänster, bland annat [Storage](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) och [Media Services](../media-services/previous/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Hantering via [REST API](/rest/api/cdn/), [.net](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md)eller [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Komprimering av MIME-typer](./cdn-improve-performance.md)  |Endast standard |Konfigurerbar |Konfigurerbar  |Konfigurerbar  |
| Komprimerings kodning  |gzip, brotli |gzip |gzip, deflate, bzip2, brotli  |gzip, deflate, bzip2, brotli  |

## <a name="migration"></a>Migrering

Information om hur du migrerar en **Azure CDN Standard från Verizon**-profil till **Azure CDN Premium från Verizon** finns i [Migrera en Azure CDN-profil från Standard Verizon till Premium Verizon](cdn-migrate.md). 

> [!NOTE]
> Det finns en uppgraderings väg från standard Verizon till Premium Verizon, men det finns ingen konverterings funktion mellan andra produkter för tillfället.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure CDN](cdn-overview.md).
