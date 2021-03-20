---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: f37876a0b6b7e8b520b5b6428381ebaf7b5b5c24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93042592"
---
Du kan använda samma konfigurations paket för VPN-klienten på varje Windows-klientdator, förutsatt att versionen överensstämmer med arkitekturen för-klienten. En lista över klient operativ system som stöds finns i avsnittet punkt-till-plats i [vanliga frågor och svar om VPN gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Du måste ha administratörs behörighet på den Windows-klientdator från vilken du vill ansluta.
>

Använd följande steg för att konfigurera den interna Windows VPN-klienten för certifikatautentisering:

1. Välj de VPN-klientkonfigurationsfiler som motsvarar Windows-datorns arkitektur. Välj ”VpnClientSetupAmd64”-installationspaketet för en 64-bitars processorarkitektur. Välj ”VpnClientSetupX86”-installationspaketet för en 32-bitars processorarkitektur. 
1. Dubbelklicka på paketet för att installera det. Om du ser ett SmartScreen-fönster klickar du på **mer info** och **kör ändå**.
1. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till.
1. Innan du försöker ansluta kontrollerar du att du har installerat ett klientcertifikat på klientdatorn. Ett klientcertifikat krävs för autentisering när du använder den interna Azure-certifikatautentiseringstypen.