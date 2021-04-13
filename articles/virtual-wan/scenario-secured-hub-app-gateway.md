---
title: Skydda trafik mellan Application Gateway-och backend-pooler
titleSuffix: Azure Virtual WAN
description: Scenarier för routning av säker trafik genom en Programgateway som distribueras i ett eker VNet som är anslutet till en säker virtuell WAN-hubb.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: cherylmc
ms.openlocfilehash: d9cb1251b90cf1c928f8286072bcd91e5ddf767e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315675"
---
# <a name="scenario-secure-traffic-between-application-gateway-and-backend-pools"></a>Scenario: skydda trafik mellan Application Gateway-och backend-pooler

När du arbetar med virtuell WAN-routning för virtuella WAN finns det några tillgängliga scenarier. I det här scenariot kommer en användares trafik att gå in i Azure via en Programgateway som distribuerats i ett eker VNet som är anslutet till en säker virtuell WAN-hubb (virtuell WAN-hubb med en Azure-brandvägg). Målet är att använda Azure-brandväggen i den säkra virtuella hubben för att kontrol lera trafiken mellan programgatewayen och backend-poolerna.

Det finns två specifika design mönster i det här scenariot, beroende på om programgatewayen och backend-poolerna finns i samma VNet eller olika virtuella nätverk.

* **Scenario 1:** Programgatewayen och backend-poolerna finns i samma virtuella nätverk som peer-kopplas till en virtuell WAN-hubb (separata undernät).
* **Scenario 2:** Programgatewayen och backend-poolerna finns i olika virtuella nätverk som peer-kopplas till en virtuell WAN-hubb.

## <a name="scenario-1---same-vnet"></a><a name="scenario-1"></a>Scenario 1 – samma VNet

I det här scenariot finns programgatewayen och backend-poolerna i samma virtuella nätverk som peer-kopplas till en virtuell WAN-hubb (separata undernät).

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/same-vnet.png" alt-text="Diagram för scenario 1." lightbox="./media/routing-scenarios/secured-application-gateway/same-vnet.png":::

### <a name="workflow"></a>Arbetsflöde

För närvarande tillämpas vägar som annonseras från den virtuella WAN Route-tabellen till eker-virtuella nätverk i hela det virtuella nätverket och inte i under näten i eker VNet. Därför är användardefinierade vägar nödvändiga för att aktivera det här scenariot. Information om användardefinierade vägar (UDR) finns i [Virtual Network anpassade vägar](../virtual-network/virtual-networks-udr-overview.md#user-defined).


1. I Azure Firewall Manager, i det eker-virtuella nätverk som innehåller programgatewayen och backend-pooler, väljer du **Aktivera säker Internet trafik** och **aktiverar säker privat trafik**.
1. Konfigurera användardefinierade vägar (UDR) i Application Gateway-undernätet.

   * För att säkerställa att Application Gateway kan skicka trafik direkt till Internet anger du följande UDR:

     * **Adressprefix:** 0.0.0.0.0/0
     * **Nästa hopp:** E

   * För att säkerställa att Application Gateway kan skicka trafik till backend-poolen via Azure-brandväggen i den virtuella WAN-hubben, anger du följande UDR:

      * Adressprefix **:** Undernät för backend-pool (10.2.0.0/24)
      * **Nästa hopp:** Azure Firewall privat IP

1. Konfigurera en användardefinierad väg (UDR) i under nätet för backend-poolen.

   * Adressprefix **:** Application Gateway undernät
   * **Nästa hopp:** Azure Firewall privat IP

## <a name="scenario-2---different-vnets"></a><a name="scenario-2"></a>Scenario 2 – olika virtuella nätverk

I det här scenariot finns Application Gateway-och backend-pooler i olika virtuella nätverk som är peer-kopplat till en virtuell WAN-hubb.

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/different-vnets.png" alt-text="Diagram för scenario 2." lightbox="./media/routing-scenarios/secured-application-gateway/different-vnets.png":::

### <a name="workflow"></a>Arbetsflöde

För närvarande tillämpas vägar som annonseras från den virtuella WAN Route-tabellen till eker-virtuella nätverk i hela det virtuella nätverket och inte i under näten i eker VNet. Därför är användardefinierade vägar nödvändiga för att aktivera det här scenariot. Information om användardefinierade vägar (UDR) finns i [Virtual Network anpassade vägar](../virtual-network/virtual-networks-udr-overview.md#user-defined).

1. I **Azure Firewall Manager** väljer du **Aktivera säker Internet trafik** och **aktiverar säker privat trafik** i båda de eker-virtuella nätverken.

1. Konfigurera användardefinierade vägar (UDR) i Application Gateway-undernätet. För att säkerställa att Application Gateway kan skicka trafik direkt till Internet anger du följande UDR:

   * **Adressprefix:** 0.0.0.0.0/0
   * **Nästa hopp:** E

## <a name="next-steps"></a>Nästa steg

* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
* Mer information om användardefinierade vägar finns i [Virtual Network anpassade vägar](../virtual-network/virtual-networks-udr-overview.md#user-defined).
* Information om virtuella WAN-säkra virtuella hubbar finns i [säkra virtuella hubbar](../firewall-manager/secured-virtual-hub.md).
