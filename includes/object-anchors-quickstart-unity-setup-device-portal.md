---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 44fd3b2bc89ac53e7a7c0293961098509d3f5c76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044694"
---
### <a name="set-up-the-windows-device-portal"></a>Konfigurera Windows Device Portal

Följ dessa steg om du vill ansluta till din HoloLens via WiFi:

1. [Anslut först din HoloLens till WiFi](https://docs.microsoft.com/hololens/hololens-network).

2. Hämta sedan IP-adressen på enheten under **inställningar > nätverks & Internet > Wi-Fi > avancerade alternativ**.

3. Gå till i en webbläsare på din dator `https://<YOUR_HOLOLENS_IP_ADDRESS>` . Webbläsaren visar följande meddelande: "det har uppstått ett problem med webbplatsens säkerhetscertifikat". Det här meddelandet beror på att certifikatet som utfärdats till enhets portalen är ett självsignerat certifikat. Du kan ignorera certifikat felet och fortsätta.

Mer information om hur du konfigurerar Windows Device Portal finns [här](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal) .
