---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 1166585c7291c4fe0d78cbc9540e3f08f985db6c
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107589903"
---
| Resurs | Gräns | Anteckning |
| --- | --- | --- |
| Azure Application Gateway |1 000 per prenumeration | |
| IP-konfigurationer på frontend-sidan |2 |1 offentlig och 1 privat |
| Frontend-portar |100<sup>1</sup> | |
| Backend-adresspooler |100<sup>1</sup> | |
| Backend-servrar per pool |1 200 | |
| HTTP-lyssnare |200<sup>1</sup> |Begränsat till 100 aktiva lyssnare som dirigerar trafik. Aktiva lyssnare = totalt antal lyssnare – lyssnare är inte aktiva.<br>Om en standardkonfiguration i en routningsregel är inställd på att dirigera trafik (till exempel om den har en lyssnare, en serverpool och HTTP-inställningar) räknas detta också som en lyssnare.|
| Regler för HTTP-belastningsutjämning |100<sup>1</sup> | |
| HTTP-inställningar för backend |100<sup>1</sup> | |
| Instanser per gateway |V1 SKU – 32<br>V2 SKU – 125 | |
| SSL-certifikat |100<sup>1</sup> |1 per HTTP-lyssnare |
| Maximal SSL-certifikatstorlek |V1 SKU – 10 KB<br>V2 SKU – 16 KB| |
| Autentiseringscertifikat |100 | |
| Betrodda rotcertifikat |100 | |
| Minsta timeout för begäran |1 sekund | |
| Maximal tidsgräns för begäran |24 timmar | |
| Antal platser |100<sup>1</sup> |1 per HTTP-lyssnare |
| URL-mappningar per lyssnare |1 | |
| Maximalt antal sökvägsbaserade regler per URL-mappning|100||
| Omdirigeringskonfigurationer |100<sup>1</sup>| |
| Antal regeluppsättningar för omskrivning |400| |
| Antal rubrik- eller URL-konfigurationer per omskrivningsregeluppsättning|40| |
| Antal villkor per omskrivningsregeluppsättning|40| |
| Samtidiga WebSocket-anslutningar |Medelstora gateways 20k<sup>2</sup><br> Stora gatewayer 50 000<sup>2</sup>| |
| Maximal URL-längd|32 kB| |
| Maximal rubrikstorlek för HTTP/2 |16 kB| |
| Maximal filuppladdningsstorlek, Standard |2 GB | |
| Maximal filuppladdningsstorlek FÖR WAF |V1 Medium WAF gateways, 100 MB<br>V1 Stora WAF-gatewayer, 500 MB<br>V2 WAF, 750 MB| |
| Storleksgräns för WAF-brödtext, utan filer|128 kB||
| Maximalt antal anpassade WAF-regler|100||
| Maximalt antal WAF-undantag per Application Gateway|40||

<sup>1</sup> För WAF-aktiverade SKU:er måste du begränsa antalet resurser till 40.

<sup>2</sup> Gränsen är per Application Gateway-instans som inte Application Gateway resurs.
