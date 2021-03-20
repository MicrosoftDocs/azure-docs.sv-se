---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 134f1dc7cb6e53c181b2f518055e5cb758fccf31
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91812785"
---
1. Under ditt virtuella WAN väljer du hubbar och väljer **+ nytt nav**.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.jpg" alt-text="ny hubb":::

1. Fyll i följande fält på sidan Skapa virtuellt nav.

   * **Region** – Välj den region som du vill distribuera den virtuella hubben i.
   * **Namn** – ange det namn som du vill använda för att anropa den virtuella hubben.
   * **Hubb privat adress utrymme** – hubbens adress intervall i CIDR-format.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.jpg" alt-text="Skapa virtuell hubb":::

1. På fliken punkt-till-plats fyller du i följande fält:

   * **Enheter för gateway-skalning** – som representerar den sammanställda kapaciteten för användarens VPN-gateway.
   * **Peka på plats konfiguration** – som du skapade i föregående steg.
   * **Klient-adresspool** – för fjärran vändare.
   * **Anpassad IP för DNS-Server**.

   :::image type="content" source="media/virtual-wan-p2s-hub/hub-with-p2s.png" alt-text="hubb med punkt-till-plats":::

1. Välj **Granska + skapa**.
1. På sidan **validering som skickats** väljer du **skapa**.