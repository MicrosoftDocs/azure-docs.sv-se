---
title: Azure Firewall Manager-filtrering i nätverks regler (förhands granskning)
description: Så här använder du FQDN-filtrering i nätverks regler
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 28cd26532ca5bdf83902854b7910f7d6c18a4eab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87460158"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>FQDN-filtrering i nätverks regler (förhands granskning)

> [!IMPORTANT]
> FQDN-filtrering i nätverks regler är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ett fullständigt kvalificerat domän namn (FQDN) representerar ett domän namn för en värd eller IP-adress (er). Du kan använda FQDN: er i nätverks regler baserat på DNS-matchning i Azure brand vägg och brand Väggs princip. Med den här funktionen kan du filtrera utgående trafik med valfritt TCP/UDP-protokoll (inklusive NTP, SSH, RDP och mer). Du måste aktivera DNS-proxy för att använda FQDN i dina nätverks regler. Mer information finns i [DNS-inställningar för Azure Firewall-principer (för hands version)](dns-settings.md).

## <a name="how-it-works"></a>Så här fungerar det

När du har definierat vilken DNS-Server din organisation behöver (Azure DNS eller din egen anpassade DNS) översätter Azure-brandväggen FQDN till en IP-adress (er) baserat på den valda DNS-servern. Den här översättningen sker för bearbetning av både program-och nätverks regler.

Vad är skillnaden mellan att använda domän namn i program regler jämfört med det för nätverks regler? 

- FQDN-filtrering i program regler för HTTP/S och MSSQL baseras på en transparent proxy för program nivå och SNI-huvudet. Det kan till exempel fram mellan två FQDN som har matchats till samma IP-adress. Detta är inte fallet med FQDN-filtrering i nätverks regler. Använd alltid program regler när det är möjligt.
- I program regler kan du använda HTTP/S och MSSQL som de valda protokollen. I nätverks regler kan du använda valfritt TCP/UDP-protokoll med dina mål-FQDN.

## <a name="next-steps"></a>Nästa steg

[DNS-inställningar för Azure-brandväggen](dns-settings.md)
