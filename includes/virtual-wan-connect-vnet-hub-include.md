---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/09/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 999f3fb054eedab64a1f7bcebd9788e1edbf29f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86220627"
---
I det här steget skapar du anslutningen mellan hubben och ett VNet. Upprepa de här stegen för varje virtuellt nätverk du vill ansluta.

1. Välj **virtuella nätverks anslutningar** på sidan för ditt virtuella WAN-nätverk.
1. På sidan virtuell nätverks anslutning väljer du **+ Lägg till anslutning**.
1. Fyll i följande fält på sidan **Lägg till anslutning**:

    * **Anslutningsnamn** – Namnge anslutningen.
    * **Hubbar** – Välj den hubb du vill koppla till anslutningen.
    * **Prenumeration** – Kontrollera prenumerationen.
    * **Virtuellt nätverk** – Välj det virtuella nätverk du vill ansluta till hubben. Det virtuella nätverket får inte ha någon befintlig gateway för virtuellt nätverk.
1. Välj **OK** för att skapa anslutningen.