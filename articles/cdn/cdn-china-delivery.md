---
title: Kina, innehålls leverans med Azure CDN | Microsoft Docs
description: Lär dig mer om att använda Azure Content Delivery Network (CDN) för att leverera innehåll till Kina-användare.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 599ec041837460c30b4655531b822eab5f0eafa3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92778922"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Kina, innehålls leverans med Azure CDN

Global Azure Content Delivery Network (CDN) Global kan hantera innehåll till Kina-användare med punkt-i-närvaro-platser (POP) nära Kina eller POP som ger bästa möjliga prestanda för förfrågningar från Kina. Om Kina däremot är en betydande marknad för dina kunder och de behöver snabba prestanda, bör du överväga att använda Azure CDN Kina i stället.

Azure CDN Kina skiljer sig från Azure CDN globalt i att den levererar innehåll från pop i Kina genom att partner med ett antal lokala leverantörer. På grund av kinesisk efterlevnad och reglering måste du registrera en separat prenumeration för att kunna använda Azure CDN Kina och dina webbplatser måste ha en ICP-licens. Portalen och API-upplevelsen för att aktivera och hantera innehålls leverans är identiskt mellan Azure CDN global och Azure CDN Kina.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Jämförelse av Azure CDN global och Azure CDN Kina

Azure CDN global och Azure CDN Kina har följande funktioner:

- Azure CDN global:

     - Portal https://portal.azure.com  

     - Utför innehålls leverans utanför Kina

     - Fyra pris nivåer: Microsoft standard, Verizon standard, Verizon Premium och Akamai standard

     - [Dokumentation](./index.yml)

- Azure CDN Kina:

     - Portal https://portal.azure.cn

     - Utför innehålls leverans inuti Kina

     - Två pris nivåer: standard och Premium

     - [Dokumentation](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CDN Kina finns i:

- [Content Delivery Network funktioner](https://www.azure.cn/en-us/home/features/cdn/)

- [Översikt över Azure Content Delivery Network](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Använd Azure-Content Delivery Network](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [Tillgänglighet för Azure-tjänster i Kina](/azure/china/concepts-service-availability)