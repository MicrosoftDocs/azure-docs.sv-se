---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 81c4e95660eb2875cd1b03bdfa670aeabadedc3f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105061"
---
### <a name="set-up-the-windows-device-portal"></a>Konfigurera Windows Device Portal

Följ dessa steg om du vill ansluta till din HoloLens via WiFi:

1. [Anslut först din HoloLens till WiFi](/hololens/hololens-network).

2. Hämta sedan IP-adressen på enheten under **inställningar > nätverks & Internet > Wi-Fi > avancerade alternativ**.

3. Gå till i en webbläsare på din dator `https://<YOUR_HOLOLENS_IP_ADDRESS>` . Webbläsaren visar följande meddelande: "det har uppstått ett problem med webbplatsens säkerhetscertifikat". Det här meddelandet beror på att certifikatet som utfärdats till enhets portalen är ett självsignerat certifikat. Du kan ignorera certifikat felet och fortsätta.

Mer information om hur du konfigurerar Windows Device Portal finns [här](/windows/mixed-reality/using-the-windows-device-portal) .