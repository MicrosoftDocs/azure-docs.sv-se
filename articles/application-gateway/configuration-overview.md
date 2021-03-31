---
title: Översikt över Azure Application Gateway-konfiguration
description: Den här artikeln beskriver hur du konfigurerar komponenterna i Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 0012ac99600c77dce5940387e1da54f29c3f6ab7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89652004"
---
# <a name="application-gateway-configuration-overview"></a>Översikt över Application Gateway konfiguration

Azure Application Gateway består av flera komponenter som du kan konfigurera på olika sätt för olika scenarier. Den här artikeln visar hur du konfigurerar varje komponent.

![Flödes diagram för Application Gateway-komponenter](./media/configuration-overview/configuration-overview1.png)

Den här bilden illustrerar ett program som har tre lyssnare. De första två är lyssnare för flera platser för `http://acme.com/*` respektive `http://fabrikam.com/*` . Båda lyssnar på port 80. Den tredje är en grundläggande lyssnare som har slut punkt till slut punkt Transport Layer Security (TLS), tidigare kallat Secure Sockets Layer (SSL) avslutades.

## <a name="infrastructure"></a>Infrastruktur

Application Gateway-infrastrukturen omfattar det virtuella nätverket, undernät, nätverks säkerhets grupper och användardefinierade vägar.

Mer information finns i [Application Gateway infrastruktur konfiguration](configuration-infrastructure.md).



## <a name="front-end-ip-address"></a>Klientdelens IP-adress

Du kan konfigurera programgatewayen att ha en offentlig IP-adress, en privat IP-adress eller både och. En offentlig IP-adress krävs när du är värd för en server del som klienterna måste komma åt via Internet via en virtuell IP-adress (VIP).

Mer information finns i [Application Gateway klient delens IP-adresskonfiguration](configuration-front-end-ip.md).

## <a name="listeners"></a>Lyssnare

En lyssnare är en logisk entitet som söker efter inkommande anslutnings begär Anden med hjälp av port, protokoll, värd och IP-adress. När du konfigurerar lyssnaren måste du ange värden för de som matchar motsvarande värden i den inkommande begäran på gatewayen.

Mer information finns i [Application Gateway Listener-konfiguration](configuration-listeners.md).

## <a name="request-routing-rules"></a>Begär routningsregler

När du skapar en Programgateway med hjälp av Azure Portal skapar du en standard regel (*regel 1*). Den här regeln binder standard avlyssningen (*appGatewayHttpListener*) med standard server delen (*appGatewayBackendPool*) och standardvärdet för Server delens http-inställningar (*appGatewayBackendHttpSettings*). När du har skapat gatewayen kan du redigera inställningarna för standard regeln eller skapa nya regler.

Mer information finns i [Application Gateway regler för routning av förfrågningar](configuration-request-routing-rules.md).

## <a name="http-settings"></a>HTTP-inställningar

Application Gateway dirigerar trafik till backend-servrarna med hjälp av den konfiguration som du anger här. När du har skapat en HTTP-inställning måste du associera den med en eller flera regler för begäran-routning.

Mer information finns i [Application Gateway konfiguration av HTTP-inställningar](configuration-http-settings.md).

## <a name="back-end-pool"></a>Serverdelspool

Du kan peka en backend-pool till fyra typer av Server dels medlemmar: en viss virtuell dator, en skalnings uppsättning för virtuella datorer, en IP-adress/FQDN eller en app service. 

När du har skapat en backend-pool måste du associera den med en eller flera regler för begäran-routning. Du måste också konfigurera hälso avsökningar för varje backend-pool på din Application Gateway. När ett villkor för en regel för anslutningsbegäran uppfylls, vidarebefordrar Application Gateway trafiken till de felfria servrarna (som fastställs av hälso avsökningarna) i motsvarande backend-pool.

## <a name="health-probes"></a>Hälsotillståndsavsökningar

En Programgateway övervakar hälsan för alla resurser i Server delen som standard. Men vi rekommenderar starkt att du skapar en anpassad avsökning för varje server dels-HTTP-inställning för att få bättre kontroll över hälso övervakningen. Information om hur du konfigurerar en anpassad avsökning finns i [Inställningar för anpassade hälso avsökningar](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> När du har skapat en anpassad hälso avsökning måste du koppla den till en HTTP-inställning på Server sidan. En anpassad avsökning övervakar inte hälsan för backend-poolen om inte den motsvarande HTTP-inställningen uttryckligen är associerad med en lyssnare med hjälp av en regel.

## <a name="next-steps"></a>Nästa steg

Nu när du känner till Application Gateway-komponenter kan du:

- [Skapa en Application Gateway i Azure Portal](quick-create-portal.md)
- [Skapa en Application Gateway med PowerShell](quick-create-powershell.md)
- [Skapa en Programgateway med hjälp av Azure CLI](quick-create-cli.md)
