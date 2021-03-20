---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5a734963ced9daefda2b7b6f4a52fd9ef437eddc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "71269268"
---
Den virtuella nätverksgatewayen använder specifika undernät som kallas gateway-undernät. Gateway-undernätet ingår i det virtuella nätverkets IP-adressintervall som du anger när du konfigurerar det virtuella nätverket. Det innehåller de IP-adresser som gateway-resurserna och tjänsterna för det virtuella nätverket använder. Undernätet måste ha namnet ”GatewaySubnet' för att Azure ska kunna distribuera gateway-resurserna. Du kan inte ange ett annat undernät som gateway-resurserna ska distribueras till. Om du inte har ett undernät med namnet GatewaySubnet, kommer du inte kunna skapa din VPN-gateway.

När du skapar gatewayundernätet anger du det antal IP-adresser som undernätet innehåller. Hur många IP-adresser som behövs beror på vilken konfiguration av VPN-gatewayen som du vill skapa. Vissa konfigurationer kräver fler IP-adresser än andra. Vi rekommenderar att du skapar ett gateway-undernät som använder /27 eller /28.

Kontrollera ditt VNet-adressintervall om du ser ett fel som anger att adressutrymmet överlappar ett undernät, eller att undernätet inte ligger inom adressutrymmet för det virtuella nätverket. Du kanske inte har tillräckligt med tillgängliga IP-adresser i adressintervallet som du har skapat för ditt virtuella nätverk. Till exempel om ditt standardundernät omfattar hela adressintervallet, finns det inga IP-adresser kvar till att skapa extra undernät. Du kan antingen justera dina undernät i det befintliga adressutrymmet för att frigöra IP-adresser, eller ange ett ytterligare adressintervall och skapa gateway-undernätet där.