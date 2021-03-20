---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c538c510eeafacd1596fdeb1b4593919e6236cf0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92487083"
---
1. Från [Azure Portal](https://portal.azure.com), i **Sök resurser, tjänster och dokument (G +/)** , skriver du **lokal nätverksgateway**. Leta upp **lokal** nätverksgateway under **Marketplace** i Sök resultaten och markera den. Då öppnas sidan **skapa lokal** nätverksgateway.
1. På sidan **Skapa lokal nätverksgateway** anger du värden för din lokala gateway.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-ip-empty/create-ip.png" alt-text="Skapa en lokal nätverksgateway med IP-adress":::

   * **Namn:** Ange ett namn för ditt lokala gateway-objekt.
   * **Slut punkt:** Välj typ av slut punkt för den lokala VPN-enheten – **IP-adress** eller **fullständigt domän namn (fullständigt kvalificerat domän namn)**.
      * **IP-adress**: om du har en statisk offentlig IP-adress som har tilldelats från Internet leverantören för VPN-enheten väljer du alternativet IP-adress och fyller i IP-adressen så som visas i exemplet. Det här är den offentliga IP-adressen för VPN-enheten som du vill att Azure VPN-gatewayen ska ansluta till. Om du inte har IP-adressen just nu kan du använda de värden som visas i exemplet. Du behöver dock gå tillbaka och ersätta platshållar-IP-adressen med den offentliga IP-adressen för din VPN-enhet. Azure kommer annars inte kunna ansluta.
   * **Adressutrymme** refererar till adressintervallen för det nätverk som detta lokala nätverk representerar. Du kan lägga till flera adressintervall. Kontrollera att intervallen du anger här inte överlappar intervallen för andra nätverk som du vill ansluta till. Azure vidarebefordrar det adressintervall som du anger till den lokala VPN-enhetens IP-adress. *Använd egna värden här om du vill ansluta till din lokala plats. Använd inte de värden som visas i exemplet*.
   * **Konfigurera BGP-inställningar:** Använd endast vid konfigurering av BGP. Annars ska detta inte väljas.
   * **Prenumeration:** Kontrollera att korrekt prenumeration visas.
   * **Resursgrupp:** Välj den resursgrupp som du vill använda. Du kan antingen skapa en ny resursgrupp eller välja en som du redan har skapat.
   * **Plats:** Platsen är samma som **region** i andra inställningar. Välj den plats som det här objektet kommer att skapas i. Du kanske vill välja samma plats som din VNet finns i, men du behöver inte göra det.

1. När du är klar med att ange värdena väljer du knappen **skapa** längst ned på sidan för att skapa den lokala Nätverksgatewayen.
