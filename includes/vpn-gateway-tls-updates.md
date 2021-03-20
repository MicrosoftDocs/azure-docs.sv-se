---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187167"
---
>[!NOTE]
>Från och med 1 juli 2018 tas stödet för TLS 1.0 och 1.1 bort från Azure VPN Gateway. VPN Gateway kommer endast att stödja TLS 1.2. Information om hur du underhåller support finns i [uppdateringar för att aktivera stöd för TLS 1.2](#tls1).

Dessutom kommer följande äldre algoritmer också att föråldras för TLS den 1 juli 2018:

* RC4 (Rivest Cipher 4)
* DES (Data Encryption Algorithm, datakrypteringsalgoritm)
* 3DES (Triple Data Encryption Algorithm, tredubbel datakrypteringsalgoritm)
* MD5 (Message Digest 5)

### <a name="how-do-i-enable-support-for-tls-12-in-windows-7-and-windows-81"></a><a name="tls1"></a>Hur gör jag för att aktivera stöd för TLS 1,2 i Windows 7 och Windows 8,1?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
