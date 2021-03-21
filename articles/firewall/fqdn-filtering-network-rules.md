---
title: Azure Firewall FQDN-filtrering i nätverks regler
description: Använda Azure Firewall FQDN-filtrering i nätverks regler
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: f7196c7715ad5d2c02759040b780b96218e1655e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94695953"
---
# <a name="use-fqdn-filtering-in-network-rules"></a>Använd FQDN-filtrering i nätverks regler

Ett fullständigt kvalificerat domän namn (FQDN) representerar ett domän namn för en värd eller IP-adress (er). Du kan använda FQDN: er i nätverks regler baserat på DNS-matchning i Azure brand vägg och brand Väggs princip. Med den här funktionen kan du filtrera utgående trafik med valfritt TCP/UDP-protokoll (inklusive NTP, SSH, RDP och mer). Du måste aktivera DNS-proxy för att använda FQDN i dina nätverks regler. Mer information finns i [Azure Firewall DNS-inställningar](dns-settings.md).

> [!NOTE]
> Enligt design stöder FQDN-filtrering inte jokertecken.

## <a name="how-it-works"></a>Så här fungerar det

När du har definierat vilken DNS-Server din organisation behöver (Azure DNS eller din egen anpassade DNS) översätter Azure-brandväggen FQDN till en IP-adress (er) baserat på den valda DNS-servern. Den här översättningen sker för bearbetning av både program-och nätverks regler.

När en ny DNS-matchning äger rum läggs nya IP-adresser till i brand Väggs reglerna. Gamla IP-adresser som inte längre returneras av DNS-servern upphör att gälla om 15 minuter. Azure brand Väggs regler uppdateras var 15: e sekund från DNS-matchning av FQDN i nätverks regler.

### <a name="differences-in-application-rules-vs-network-rules"></a>Skillnader i program regler jämfört med nätverks regler

- FQDN-filtrering i program regler för HTTP/S och MSSQL baseras på en transparent proxy för program nivå och SNI-huvudet. Det kan till exempel fram mellan två FQDN som har matchats till samma IP-adress. Detta är inte fallet med FQDN-filtrering i nätverks regler. 

   Använd alltid program regler när det är möjligt:
     - Om protokollet är HTTP/S eller MSSQL använder du program regler för FQDN-filtrering.
   - För alla andra protokoll förutom HTTP/S eller MSSQL kan du använda program-eller nätverks regler för FQDN-filtrering.

## <a name="next-steps"></a>Nästa steg

[DNS-inställningar för Azure-brandväggen](dns-settings.md)
