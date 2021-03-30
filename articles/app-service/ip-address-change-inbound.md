---
title: Förbered för ändring av inkommande IP-adress
description: Om din inkommande IP-adress ska ändras kan du läsa vad du kan göra så att din app fortsätter att fungera efter ändringen.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 999511c4759ee761f028a61fbfaa0310ac93d9b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "74672419"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Förbereda för en ändring av en inkommande IP-adress

Om du har fått ett meddelande om att den inkommande IP-adressen för din Azure App Service-appen ändras följer du anvisningarna i den här artikeln.

## <a name="determine-if-you-have-to-do-anything"></a>Ta reda på om du behöver göra något

* Alternativ 1: om din App Service-app saknar en anpassad domän krävs ingen åtgärd.

* Alternativ 2: om bara en CNAME-post (DNS-post som pekar på en URI) har kon figurer ATS i din domän registrerings portal (tredje parts DNS-provider eller Azure DNS) krävs ingen åtgärd.

* Alternativ 3: om en post (DNS-post som pekar direkt till din IP-adress) har kon figurer ATS i din domän registrerings portal (tredjeparts-DNS-provider eller Azure DNS) ersätter du den befintliga IP-adressen med den nya. Du kan hitta den nya IP-adressen genom att följa anvisningarna i nästa avsnitt.

* Alternativ 4: om ditt program ligger bakom en belastningsutjämnare, IP-filter eller andra IP-mekanismer som kräver appens IP-adress ersätter du den befintliga IP-adressen med den nya. Du kan hitta den nya IP-adressen genom att följa anvisningarna i nästa avsnitt.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Hitta den nya inkommande IP-adressen i Azure Portal

Den nya inkommande IP-adressen som tilldelas till din app finns i portalen i fältet **virtuell IP-adress** . Både den nya IP-adressen och den gamla är anslutna till din app nu, och senare kommer den gamla att kopplas från.

1.  Öppna [Azure-portalen](https://portal.azure.com).

2.  I den vänstra navigerings menyn väljer du **app Services**.

3.  Välj din App Service-app i listan.

1.  Om appen är en Function-app, se [Function app inkommande IP-adress](../azure-functions/ip-addresses.md#function-app-inbound-ip-address).

4.  Under rubriken **Inställningar** klickar du på **Egenskaper** i det vänstra navigerings fältet och letar upp avsnittet med etiketten **virtuell IP-adress**.

5. Kopiera IP-adressen och konfigurera om domän posten eller IP-mekanismen.

## <a name="next-steps"></a>Nästa steg

Den här artikeln förklaras hur du förbereder en ändring av IP-adressen som initierades av Azure. Mer information om IP-adresser i Azure App Service finns i [inkommande och utgående IP-adresser i Azure App Service](overview-inbound-outbound-ips.md).
