---
title: Hämta den aktuella POP IP-listan för Azure CDN | Microsoft Docs
description: Lär dig hur du hämtar POP-servrar med hjälp av REST API. POP-servrar gör förfrågningar till ursprungs servrar som är associerade med Azure Content Delivery Network-slutpunkter.
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
ms.topic: article
ms.date: 08/22/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 4197b1a5f047190872d055dc2ba8ccaa11efbe6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376351"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Hämta den aktuella POP IP-listan för Azure CDN

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Hämta den aktuella Verizon POP IP-listan för Azure CDN

Du kan använda REST API för att hämta en uppsättning IP-adresser för Verizons Point of Presence (POP)-servrar. Dessa POP-servrar utför förfrågningar till ursprungs servrar som är associerade med Azure Content Delivery Network-slutpunkter (CDN) i en Verizon-profil (**Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon**). Observera att den här uppsättningen IP-adresser skiljer sig från de IP-adresser som en klient kan se när de gör förfrågningar till pop. 

Syntaxen för REST API-åtgärden för att hämta POP-listan finns i [Edge Nodes-List](/rest/api/cdn/cdn/edgenodes/list).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Hämta den aktuella listan med Microsoft POP IP-adresser för Azure CDN

Om du vill låsa ditt program för att endast acceptera trafik från Azure CDN från Microsoft måste du konfigurera IP ACL: er för Server delen. Du kan också begränsa mängden godkända värden för rubriken "X-forwarded-Host" som skickas av Azure CDN från Microsoft. De här stegen beskrivs nedan:

Konfigurera IP-ACLing för dina Server delar för att acceptera trafik från Azure CDN från Microsofts server dels IP-adressutrymme och Azures infrastruktur tjänster. 

* Azure CDN från Microsofts IPv4-backend-IP-utrymme: 147.243.0.0/16
* Azure CDN från Microsofts IPv6-backend-IP-utrymme: 2a01:111:2050::/44

Om du vill använda service märken med Azure CDN från Microsoft använder du taggen för Azures frontend-dörr. IP-adressintervall och service märken för Microsoft-tjänster hittar du [här](https://www.microsoft.com/download/details.aspx?id=56519)


## <a name="typical-use-case"></a>Vanligt användningsfall

Av säkerhets synpunkt kan du använda den här IP-listan för att genomdriva att förfrågningar till din ursprungs Server endast görs från en giltig Verizon POP. Om någon till exempel har identifierat värd namnet eller IP-adressen för en CDN-slutpunkts ursprungs Server, kan en begäran skickas direkt till ursprungs servern, och därför kringgås de skalnings-och säkerhets funktioner som tillhandahålls av Azure CDN. Det här scenariot kan förhindras genom att ange IP-adresser i den returnerade listan som de enda tillåtna IP-adresserna på en ursprungs Server. Se till att du har den senaste POP-listan genom att hämta den minst en gång om dagen. 

## <a name="next-steps"></a>Nästa steg

Information om REST API finns i [Azure CDN REST API](/rest/api/cdn/).