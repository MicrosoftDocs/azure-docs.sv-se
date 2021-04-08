---
title: ta med fil
description: ta med fil
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57b0bb9ab8ceb34021a38db0d0abf2c9c919e808
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94356660"
---
### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>Kan jag fortfarande distribuera flera skydds-värdar mellan peer-kopplat virtuella nätverk?

Ja. Som standard ser en användare den skydds-värd som har distribuerats i samma virtuella nätverk som den virtuella datorn finns i. Men på menyn Anslut kan en användare se flera skydds-värdar som identifieras mellan peer- **kopplade** nätverk. De kan välja den skydds-värd som de vill använda för att ansluta till den virtuella datorn som distribueras i det virtuella nätverket.

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>Om mina peer-virtuella nätverk distribueras i olika prenumerationer, kommer anslutningen via skydds fungera?

Ja, anslutningen via skydds fortsätter att fungera för peer-virtuella nätverk över olika prenumerationer för en enda klient. Prenumerationer över två olika klienter stöds inte. Om du vill se skydds i list rutan **Anslut** måste användaren välja det återbäring som de har åtkomst till i **prenumerationen > global prenumeration**.

:::image type="content" source="./media/bastion-faq-peering-include/global-subscriptions.png" alt-text="Globala prenumerations filter" lightbox="./media/bastion-faq-peering-include/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>Jag har åtkomst till det peer-virtuella nätverket, men det går inte att se den virtuella datorn som distribueras där.

Se till att användaren har **Läs** behörighet till både den virtuella datorn och det peer-virtuella VNet. Kontrol lera dessutom under IAM att användaren har **Läs** behörighet till följande resurser:

* Rollen läsare på den virtuella datorn.
* Rollen läsare på NÄTVERKSKORTet med den virtuella datorns privata IP-adress.
* Rollen läsare på Azure skydds-resursen.
* Rollen läsare i Virtual Network (behövs inte om det inte finns något peer-kopplat virtuellt nätverk).

|Behörigheter|Beskrivning|Behörighets typ|
|---|---| ---|
|Microsoft. Network/bastionHosts/Read |Hämtar en skydds-värd|Action|
|Microsoft. Network/virtualNetworks/BastionHosts/Action |Hämtar skydds värd referenser i en Virtual Network.|Action|
|Microsoft. Network/virtualNetworks/bastionHosts/default/Action|Hämtar skydds värd referenser i en Virtual Network.|Action|
|Microsoft. Network/networkInterfaces/Read|Hämtar en definition för nätverks gränssnitt.|Action|
|Microsoft. Network/networkInterfaces/ipconfigurations/Read|Hämtar en IP-konfigurationsfil för nätverks gränssnitt.|Action|
|Microsoft. Network/virtualNetworks/Read|Hämta definition av virtuellt nätverk|Action|
|Microsoft. Network/virtualNetworks/subnets/virtualMachines/Read|Hämtar referenser till alla virtuella datorer i ett undernät för virtuellt nätverk|Action|
|Microsoft. Network/virtualNetworks/virtualMachines/Read|Hämtar referenser till alla virtuella datorer i ett virtuellt nätverk|Åtgärd|