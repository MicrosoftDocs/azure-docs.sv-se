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
ms.openlocfilehash: 92a6fef3305891b47c4dc2e0f0432e1079df5a69
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "71266527"
---
Den virtuella nätverksgatewayen använder specifika undernät som kallas gateway-undernät. Gateway-undernätet ingår i det virtuella nätverkets IP-adressintervall som du anger när du konfigurerar det virtuella nätverket. Det innehåller de IP-adresser som gateway-resurserna och tjänsterna för det virtuella nätverket använder. 


När du skapar gatewayundernätet anger du det antal IP-adresser som undernätet innehåller. Hur många IP-adresser som behövs beror på vilken konfiguration av VPN-gatewayen som du vill skapa. Vissa konfigurationer kräver fler IP-adresser än andra. Vi rekommenderar att du skapar ett gateway-undernät som använder /27 eller /28.


Kontrollera ditt VNet-adressintervall om du ser ett fel som anger att adressutrymmet överlappar ett undernät, eller att undernätet inte ligger inom adressutrymmet för det virtuella nätverket. Du kanske inte har tillräckligt med tillgängliga IP-adresser i adressintervallet som du har skapat för ditt virtuella nätverk. Till exempel om ditt standardundernät omfattar hela adressintervallet, finns det inga IP-adresser kvar till att skapa extra undernät. Du kan antingen justera dina undernät i det befintliga adressutrymmet för att frigöra IP-adresser, eller ange ett ytterligare adressintervall och skapa gateway-undernätet där.