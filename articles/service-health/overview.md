---
title: Vad är Azure Service Health?
description: Anpassad information om hur dina Azure-appar påverkas av aktuella och framtida problem och underhåll av Azure-tjänsten.
ms.topic: overview
ms.date: 05/10/2019
ms.openlocfilehash: cb1eb8eb75a18a73e8f024f02217df37f45981fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87499780"
---
# <a name="what-is-azure-service-health"></a>Vad är Azure Service Health?

Azure erbjuder en serie upplevelser för att hålla dig informerad om hälso tillståndet för dina moln resurser. Den här informationen omfattar aktuella och kommande problem som tjänst påverkan på händelser, planerat underhåll och andra ändringar som kan påverka tillgängligheten.

Azure Service Health är en kombination av tre separata mindre tjänster.

[Azure-status](azure-status-overview.md) informerar dig om service avbrott i Azure på **[sidan Azure-status](https://status.azure.com)**. Sidan är en global vy över hälsan för alla Azure-tjänster i alla Azure-regioner. Sidan status är en referens för incidenter med omfattande påverkan, men vi rekommenderar starkt att aktuella Azure-användare utnyttjar Azures hälso tillstånd för att hålla dig informerad om Azure-incidenter och underhåll.

[Tjänstens hälsa](service-health-overview.md) ger en anpassad vy över hälso tillståndet för de Azure-tjänster och regioner som du använder. Detta är den bästa platsen för att söka efter tjänster som påverkar kommunikation om avbrott, planerade underhålls aktiviteter och andra hälso rådgivare, eftersom den autentiserade Service Healthen känner till vilka tjänster och resurser som du för närvarande använder. Det bästa sättet att använda Service Health är genom att konfigurera Service Health-aviseringar som informerar dig om problem med tjänsten, planerat underhåll eller andra ändringar som kan påverka dina Azure-tjänster och -regioner via de kommunikationskanaler som du själv valt.

[Resource Health](resource-health-overview.md) innehåller information om hälso tillståndet för dina enskilda moln resurser, till exempel en viss virtuell dator instans. Med hjälp av Azure Monitor kan du även konfigurera aviseringar som informerar dig om förändringar för dina molnresursers tillgänglighet. Resource Health tillsammans med Azure Monitor-aviseringar hjälper dig att hålla dig informerad om tillgängligheten för dina resurs minuter per minut och snabbt bedöma om ett problem beror på ett problem på sidan eller relaterat till en Azure Platform-händelse.

Sammantaget ger dessa upplevelser dig en heltäckande översikt över hälsotillståndet för Azure, med den precision som är mest relevant för dig.

**Titta på en översikt över Azure-Status-sidan, Azure Service Health och Azure Resource Health**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]