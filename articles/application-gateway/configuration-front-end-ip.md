---
title: Konfiguration av klient delens IP-adress för Azure Application Gateway
description: Den här artikeln beskriver hur du konfigurerar den Azure Application gatewayens frontend-IP-adress.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: eff63510f70dd7b4cdd522cc5a2a68096cda7166
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548724"
---
# <a name="application-gateway-front-end-ip-address-configuration"></a>Application Gateway IP-adresskonfiguration på klient Sidan

Du kan konfigurera programgatewayen att ha en offentlig IP-adress, en privat IP-adress eller både och. En offentlig IP-adress krävs när du är värd för en server del som klienterna måste komma åt via Internet via en virtuell IP-adress (VIP).

## <a name="public-and-private-ip-address-support"></a>Stöd för offentliga och privata IP-adresser

Application Gateway v2 stöder för närvarande inte enbart privat IP-läge. Det stöder följande kombinationer:

* Privat IP-adress och offentlig IP-adress
* Endast offentlig IP-adress

Mer information finns i [vanliga frågor och svar om Application Gateway](application-gateway-faq.yml#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address).


En offentlig IP-adress krävs inte för en intern slut punkt som inte är exponerad för Internet. Det kallas för en *intern belastningsutjämnare* (ILB) eller privat klient dels-IP. En Application Gateway-ILB är användbar för interna branschspecifika program som inte är utsatta för Internet. Det är också användbart för tjänster och nivåer i ett program med flera nivåer inom en säkerhets gränser som inte exponeras för Internet men som kräver belastnings fördelning för resursallokering, varaktighet eller TLS-avslutning.

Det finns bara stöd för en offentlig IP-adress och en privat IP-adress. Du väljer klient delens IP-adress när du skapar programgatewayen.

- För en offentlig IP-adress kan du skapa en ny offentlig IP-adress eller använda en befintlig offentlig IP-adress på samma plats som Application Gateway. Mer information finns i [statisk eller dynamisk offentlig IP-adress](./application-gateway-components.md#static-versus-dynamic-public-ip-address).

- För en privat IP-adress kan du ange en privat IP-adress från det undernät där programgatewayen skapas. Om du inte anger något väljs en godtycklig IP-adress automatiskt från under nätet. Den IP-adress typ som du väljer (statisk eller dynamisk) kan inte ändras senare. Mer information finns i [skapa en Programgateway med en intern belastningsutjämnare](./application-gateway-ilb-arm.md).

En IP-adress på klient sidan är kopplad till en *lyssnare* som söker efter inkommande begär Anden på klient delens IP.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Listener-konfiguration](configuration-listeners.md)
