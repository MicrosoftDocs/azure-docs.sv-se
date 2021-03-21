---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: 922a34bd1a147d407041d2945b14df18a18d7287
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104761223"
---
När du klonar konfiguration från en annan distributions plats kan den klonade konfigurationen redige ras. Vissa konfigurations element följer innehållet i en växling (inte en viss plats), medan andra konfigurations element stannar kvar på samma plats efter en växling (plats Specific). I följande listor visas de inställningar som ändras när du byter platser.

**Inställningar som växlas**:

* Allmänna inställningar, till exempel Ramverks version, 32/64-bitars, Web Sockets
* Appinställningar (kan konfigureras för att fästa på en plats)
* Anslutnings strängar (kan konfigureras att fästa mot en plats)
* Mappningar för hanterare
* Offentliga certifikat
* WebJobs-innehåll
* Hybrid anslutningar *
* Tjänst slut punkter *
* Azure Content Delivery Network *

Funktioner som har marker ATS med en asterisk (*) planeras att inte växlas. 

**Inställningar som inte har växlats**:

* Publicerings slut punkter
* Egna domännamn
* Icke-offentliga certifikat och TLS/SSL-inställningar
* Skalnings inställningar
* Jobb scheman för WebJobs
* IP-begränsningar
* Alltid på
* Diagnostikinställningar
* Resursdelning för korsande ursprung (CORS)
* Virtual Network-integration
* Sök vägs mappningar

> [!NOTE]
> Om du vill att dessa inställningar ska växlas, lägger du till appens inställning `WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS` i varje plats i appen och anger värdet till `0` eller `false` . Dessa inställningar är antingen växlings bara eller inte alls. Du kan inte bara göra vissa inställningar utbytbara och inte andra.

> Vissa inställningar för appar som tillämpas på inställningar som inte har växlats växlas inte heller. Till exempel, eftersom diagnostikinställningar inte växlas, växlas inte relaterade appinställningar, till exempel `WEBSITE_HTTPLOGGING_RETENTION_DAYS` och `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` , även om de inte visas som plats inställningar.
>
