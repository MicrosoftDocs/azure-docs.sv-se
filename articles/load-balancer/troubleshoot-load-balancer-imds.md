---
title: Vanliga felkoder för Azure Instance Metadata Service (IMDS)
titleSuffix: Azure Load Balancer
description: Översikt över vanliga felkoder och motsvarande minsknings metoder för Azure Instance Metadata Service (IMDS)
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: e648d71ed3c501c07a2491054b273a13d74cedaf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100418076"
---
# <a name="error-codes-common-error-codes-when-using-imds-to-retrieve-load-balancer-information"></a>Felkoder: vanliga felkoder när du använder IMDS för att hämta information om belastnings utjämning

I den här artikeln beskrivs vanliga distributions fel och hur du löser dessa fel när du använder Azure-Instance Metadata Service (IMDS).

## <a name="error-codes"></a>Felkoder

| Felkod | Felmeddelande | Information och minskning |
| --- | ---------- | ----------------- |
| 400 | Den nödvändiga parametern " \<ParameterName> " saknas. Åtgärda begäran och försök igen. | Felkoden anger en parameter som saknas. </br> Mer information om hur du lägger till den saknade parametern finns i [så här hämtar du metadata för belastningsutjämnare med hjälp av Azure instance metadata service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).
| 400 | Parametervärdet är inte tillåtet, eller parametervärdet \<ParameterValue> tillåts inte för parametern "ParameterName". Åtgärda begäran och försök igen. | Felkoden anger att formatet för begäran inte är korrekt konfigurerat. </br> Mer information finns i [hur du hämtar metadata för belastningsutjämnare med hjälp av Azure instance metadata service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) för att åtgärda begär ande texten och skicka ett nytt försök. |
| 400 | Oväntad begäran. Kontrol lera frågeparametrarna och försök igen. | Felkoden anger att formatet för begäran inte är korrekt konfigurerat. </br> Mer information finns i [hur du hämtar metadata för belastningsutjämnare med hjälp av Azure instance metadata service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) för att åtgärda begär ande texten och skicka ett nytt försök. |
| 404 | Inga metadata för belastningsutjämnare har hittats. Kontrol lera att den virtuella datorn använder en icke-grundläggande SKU-belastningsutjämnare och försök igen senare. | Felkoden anger att den virtuella datorn inte är associerad med en belastningsutjämnare eller att belastningsutjämnaren är en grundläggande SKU i stället för standard. </br> Mer information finns i [snabb start: skapa en offentlig belastningsutjämnare för att belastningsutjämna virtuella datorer med hjälp av Azure Portal](quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard) för att distribuera en standard belastningsutjämnare.|
| 404 | Det gick inte att hitta API: sökväg = " \<UrlPath> ", metod = " \<Method> " | Felkoden anger en felaktig konfiguration av sökvägen. </br> Mer information finns i [hur du hämtar metadata för belastningsutjämnare med hjälp av Azure instance metadata service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) för att åtgärda begär ande texten och skicka ett nytt försök.|
| 405 | Http-metoden tillåts inte: sökväg = " \<UrlPath> ", metod = " \<Method> " | Felkoden anger ett HTTP-verb som inte stöds. </br> Mer information finns i [Azure instance metadata service (IMDS)](/virtual-machines/windows/instance-metadata-service?tabs=windows.md#http-verbs) för verb som stöds. |
| 429 | För många begär Anden | Felkoden anger en hastighets gräns. </br> Mer information om hastighets begränsning finns i [Azure instance metadata service (IMDS)](/virtual-machines/windows/instance-metadata-service?tabs=windows#rate-limiting).|
| 400 | Begär ande texten är större än MaxBodyLength:... | Felkoden anger en begäran som är större än MaxBodyLength. </br> Mer information om längd på brödtext finns i [så här hämtar du metadata för belastningsutjämnare med hjälp av Azure instance metadata service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 400 | Parameter nyckel längden är större än MaxParameterKeyLength:... | Felkoden anger en parameter nyckel som är större än MaxParameterKeyLength. </br> Mer information om längd på brödtext finns i [så här hämtar du metadata för belastningsutjämnare med hjälp av Azure instance metadata service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response). |
| 400 | Parameter värdets längd är större än MaxParameterValueLength:... | Felkoden anger en parameter nyckel som är större än MaxParameterValueLength. </br> Mer information om värde längd finns i [hur du hämtar metadata för belastningsutjämnare med hjälp av Azure instance metadata service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 400 | Värdets längd för parameter huvudet är större än MaxHeaderValueLength:... | Felkoden anger en värde längd på parameter huvudet som är större än MaxHeaderValueLength. </br> Mer information om värde längd finns i [hur du hämtar metadata för belastningsutjämnare med hjälp av Azure instance metadata service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 404 | API för Load Balancer metadata är inte tillgängligt just nu. Försök igen senare | Felkoden anger att API: et kan vara etableringen. Försök att utföra begäran senare. |
| 404 | /metadata/Loadbalancer är för närvarande inte tillgängligt | Felkoden anger att API: t håller på att aktive ras. Försök att utföra begäran senare. |
| 503 | Den interna tjänsten är inte tillgänglig. Försök igen senare  | Felkoden anger att API: t är tillfälligt otillgängligt. Försök att utföra begäran senare. |
|  |  |

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure instance metadata service](/virtual-machines/windows/instance-metadata-service.md)

