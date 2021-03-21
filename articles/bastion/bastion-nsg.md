---
title: 'Arbeta med virtuella datorer och NSG: er i Azure skydds'
description: Du kan använda nätverks säkerhets grupper med Azure skydds. Läs om de undernät som krävs för den här konfigurationen.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: b6a0dee4c3fef1be4f4b9f910b4c6256b4924a2d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700226"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Arbeta med NSG-åtkomst och Azure skydds

När du arbetar med Azure-skydds kan du använda nätverks säkerhets grupper (NSG: er). Mer information finns i [säkerhets grupper](../virtual-network/network-security-groups-overview.md).

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="NSG":::

I det här diagrammet:

* Skydds-värden har distribuerats till det virtuella nätverket.
* Användaren ansluter till Azure Portal med valfri HTML5-webbläsare.
* Användaren navigerar till RDP/SSH för den virtuella Azure-datorn.
* Ansluta integrering – RDP/SSH-session med enkel klickning i webbläsaren
* Ingen offentlig IP-adress krävs på den virtuella Azure-datorn.

## <a name="network-security-groups"></a><a name="nsg"></a>Nätverks säkerhets grupper

I det här avsnittet visas nätverks trafiken mellan användaren och Azure-skydds och för virtuella datorer i det virtuella nätverket:

> [!IMPORTANT]
> Om du väljer att använda en NSG med din Azure skydds-resurs **måste** du skapa alla följande ingångs-och utgående trafik regler. Om du utelämnar någon av följande regler i NSG kommer din Azure skydds-resurs att blockeras från att ta emot nödvändiga uppdateringar i framtiden och därmed öppna din resurs för framtida säkerhets sårbarheter.
> 

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Azure-skydds distribueras specifikt till ***AzureBastionSubnet***.

* **Ingress trafik:**

   * **Ingress trafik från offentlig Internet:** Azure-skydds skapar en offentlig IP-adress som behöver port 443 aktiverat på den offentliga IP-adressen för inkommande trafik. Port 3389/22 behöver inte öppnas på AzureBastionSubnet.
   * **Ingress trafik från Azure skydds Control plan:** För kontroll Plans anslutning aktiverar du port 443 inkommande från **GatewayManager** service tag. Detta gör det möjligt för kontroll planet, det vill säga att Gateway Manager kan kommunicera med Azure-skydds.
   * **Ingress trafik från Azure Dataskyddss data plan:** För data Plans kommunikation mellan de underliggande komponenterna i Azure-skydds aktiverar du portarna 8080, 5701 inkommande från **VirtualNetwork** -tjänst tag gen till **VirtualNetwork** -tjänst tag gen. Detta gör att komponenterna i Azure-skydds kan kommunicera med varandra.
   * **Ingress trafik från Azure Load Balancer:** För hälso avsökningar aktiverar du port 443 inkommande från **AzureLoadBalancer** -tjänst tag gen. Detta gör att Azure Load Balancer kan identifiera anslutningen


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="Skärm bild som visar inkommande säkerhets regler för Azure skydds-anslutning.":::

* **Utgående trafik:**

   * **Utgående trafik till virtuella mål datorer:** Azure-skydds kommer att nå de virtuella mål datorerna via privat IP. NSG: er måste tillåta utgående trafik till andra mål-VM-undernät för port 3389 och 22.
   * **Utgående trafik till Azure Dataskyddss data plan:** För data Plans kommunikation mellan de underliggande komponenterna i Azure-skydds aktiverar du portarna 8080 och 5701 utgående från **VirtualNetwork** -tjänst tag gen till **VirtualNetwork** -tjänst tag gen. Detta gör att komponenterna i Azure-skydds kan kommunicera med varandra.
   * **Utgående trafik till andra offentliga slut punkter i Azure:** Azure-skydds måste kunna ansluta till olika offentliga slut punkter i Azure (till exempel för lagring av diagnostikloggar och avläsnings loggar). Av den anledningen behöver Azure skydds utgående till 443 till **AzureCloud** service tag.
   * **Utgående trafik till Internet:** Azure-skydds måste kunna kommunicera med Internet för session-och certifikat verifiering. Därför rekommenderar vi att du aktiverar port 80 utgående till **Internet.**


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="Skärm bild som visar utgående säkerhets regler för Azure skydds-anslutning.":::

### <a name="target-vm-subnet"></a>Mål för virtuellt dator under nät
Det här är under nätet som innehåller den virtuella mål datorn som du vill ha RDP/SSH till.

   * **Ingress trafik från Azure skydds:** Azure-skydds kommer att nå den virtuella mål datorn via privat IP. RDP/SSH-portar (portarna 3389/22) måste öppnas på den virtuella mål dator sidan över privat IP. Vi rekommenderar att du lägger till IP-adressintervallet för Azure skydds-undernätet i den här regeln för att bara tillåta skydds att kunna öppna portarna på de virtuella mål datorerna i det virtuella mål dator under nätet.


## <a name="next-steps"></a>Nästa steg

Mer information om Azure skydds finns i [vanliga frågor och svar](bastion-faq.md).
