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
ms.openlocfilehash: 5915830e4521399ad322dd4a6f3926428d811455
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94941963"
---
Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

1. I portalen väljer du **+ skapa en resurs**. Skriv det **virtuella WAN-nätverket** i sökrutan och välj **RETUR**.
1. Välj **virtuellt WAN** från resultaten. På sidan virtuellt WAN väljer du **skapa** för att öppna sidan Skapa WAN.
1. På sidan **skapa WAN** , på fliken **grundläggande** , fyller du i följande fält:

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="Skärm bild som visar fliken grundläggande i fönstret Skapa WAN.":::

   * **Prenumeration** – Välj vilken prenumeration du vill använda.
   * **Resurs grupp** – skapa ny eller Använd befintlig.
   * **Resurs grupps plats** – Välj en resurs plats i list rutan. Ett WAN är en global resurs och är inte kopplad till en viss region. Du måste dock välja en region för att kunna hantera och hitta den WAN-resurs som du skapar.
   * **Namn** – ange det namn som du vill anropa ditt WAN.
   * **Typ** – Basic eller standard. Välj **standard**. Om du väljer grundläggande VWAN är det viktigt att du förstår att Basic VWANs bara kan innehålla grundläggande nav, vilket begränsar anslutnings typen till plats-till-plats.
1. När du har fyllt i fälten väljer du **Granska + skapa**.
1. När verifieringen har godkänts väljer du **skapa** för att skapa det virtuella WAN-nätverket.
