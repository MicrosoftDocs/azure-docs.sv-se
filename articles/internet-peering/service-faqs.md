---
title: Peering-tjänst – vanliga frågor och svar
titleSuffix: Azure
description: Peering-tjänst – vanliga frågor och svar
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75775477"
---
# <a name="peering-service---faqs"></a>Peering-tjänst – vanliga frågor och svar

Du kan granska informationen nedan för allmänna frågor.

**Kan operatören använda sin befintliga direkt peering med Microsoft för att stödja peering-tjänsten?**

Ja, en operatör kan utnyttja sin befintliga PNI för att stödja peering-tjänsten. En peering-PNI kräver olikheter för att stödja HA. Om befintliga PNI redan har mångfald, krävs ingen ny infrastruktur. Om befintliga PNI behöver mångfald, kan den utökas.

**Kan en operatör använda ny direkt peering med Microsoft för att stödja peering-tjänsten?**

Ja, det är också möjligt. Microsoft samarbetar med operatör för att skapa en ny direkt peering som stöd för peering-tjänsten.  

**Varför är direkt peering ett krav för att stödja peering-tjänsten?**

En av de primära driv rutinerna bakom peering service är att tillhandahålla anslutning till Microsoft onlinetjänster via en väl ansluten SP. PNI är alltid i intervallet för Gbit/s och därför ett grundläggande Bygg block för anslutning med hög genom strömning mellan operatör och Microsoft.

**Vilka är de varierande kraven på en direkt peering för att stödja peering-tjänsten?**

En PNI måste ha stöd för lokal redundans och GEO-redundans. Lokal-redundans definieras som två olika uppsättningar av sökvägar på en viss peering-webbplats. GEO-redundans kräver att operatören har ytterligare anslutning på en annan Microsoft Edge-webbplats om den primära platsen Miss lyckas. För varaktigheten kort för kort haveri kan trafiken dirigeras via säkerhets kopierings platsen.

**Operatören erbjuder redan SLA-och Enterprise-kvalitet på Internet, hur är detta erbjudande annorlunda?**

Vissa leverantörer erbjuder SLA och företags klass på Internet på sin del av nätverket. I peering service kommer Microsoft att erbjuda trafik för SLA-erbjudandet på Microsofts del av nätverket. Genom att välja peering service-kund får du service avtal från slut punkt till slut punkt. Service avtalet från sin webbplats till Microsoft Edge på Internet leverantörens nätverk kan omfattas av Internet leverantören. Service avtal i Microsoft Global Network från Microsoft Edge to end users-programmet omfattas nu av Microsoft.

**Om en tjänste leverantör redan är peer-datorer med Microsoft med PNI så krävs vilken typ av ändringar som krävs för att stödja peering-tjänsten?**

* Program varu ändringar för att identifiera en peering service-användare och dess trafik. Kan kräva ändringar i Dirigerings principen för att byta ut en användares trafik till närmaste Microsoft Edge via peering service-anslutning.
* Se till att anslutningen har lokal redundans och GEO-redundans.
