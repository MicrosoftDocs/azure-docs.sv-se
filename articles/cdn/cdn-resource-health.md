---
title: Övervaka hälsan för Azure CDN resurser | Microsoft Docs
description: Lär dig hur du övervakar Azure CDN resursers hälso tillstånd med hjälp av Azure Resource Health.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 81c92b6ef5bdf89391364bc238f55200eb3679f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92778162"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Övervaka hälsotillståndet för dina Azure CDN-resurser
  
Azure CDN Resource Health är en del av [Azures resurs hälsa](../service-health/resource-health-overview.md).  Du kan använda Azure Resource Health för att övervaka hälso tillståndet för CDN-resurser och få instruktioner som kan åtgärdas för att felsöka problem.

>[!IMPORTANT] 
>Azure CDN resurs hälsa för närvarande endast för hälso tillstånd för globala CDN-leverans-och API-funktioner.  Azure CDN resurs hälsa verifierar inte enskilda CDN-slutpunkter.
>
>Signalerna som matar Azure CDN resurs hälsa kan vara upp till 15 minuter försenade.

## <a name="how-to-find-azure-cdn-resource-health"></a>Så här hittar du Azure CDN resurs hälsa

1. I [Azure Portal](https://portal.azure.com)bläddrar du till din CDN-profil.

2. Klicka på knappen **Inställningar** .

    ![Knappen Inställningar](./media/cdn-resource-health/cdn-profile-settings.png)

3. Klicka på **resurs hälsa** under *support + fel sökning*.

    ![CDN-resurs hälsa](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Du kan också hitta CDN-resurser som visas i panelen *resurs hälsa* på bladet *Hjälp + Support* .  Du kan snabbt komma till *Hjälp + Support* genom att klicka på cirkeln **?** i det övre högra hörnet i portalen.
>
> ![Hjälp + support](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Azure CDN-/regionsspecifika meddelanden

Status som är relaterade till Azure CDN resurs hälsa kan hittas nedan.

|Meddelande | Rekommenderad åtgärd |
|---|---|
|Du kan ha stoppat, tagit bort eller felaktigt konfigurerat en eller flera CDN-slutpunkter | Du kan ha stoppat, tagit bort eller felaktigt konfigurerat en eller flera CDN-slutpunkter.|
|CDN-hanteringstjänsten är tyvärr inte tillgänglig just nu | Kom tillbaka hit för status uppdateringar; Kontakta supporten om problemet kvarstår efter den förväntade lösnings tiden.|
|Ett aktuellt problem med några av våra CDN-leverantörer kan tyvärr påverka dina CDN-slutpunkter | Kom tillbaka hit för status uppdateringar; Använd fel söknings verktyget för att lära dig hur du testar din start-och CDN-slutpunkt. Kontakta supporten om problemet kvarstår efter den förväntade lösnings tiden. |
|Det tar tyvärr längre tid att sprida konfigurationsändringar i CDN-slutpunkter | Kom tillbaka hit för status uppdateringar; Kontakta supporten om konfigurations ändringarna inte är fullständigt spridda under den förväntade tiden.|
|Just nu har vi tyvärr problem med att läsa in den kompletterande portalen | Kom tillbaka hit för status uppdateringar; Kontakta supporten om problemet kvarstår efter den förväntade lösnings tiden.|
Vi har tyvärr problem med några av våra CDN-leverantörer | Kom tillbaka hit för status uppdateringar; Kontakta supporten om problemet kvarstår efter den förväntade lösnings tiden. |

## <a name="next-steps"></a>Nästa steg

- [Läs en översikt över Azure Resource Health](../service-health/resource-health-overview.md)
- [Felsök problem med CDN-komprimering](./cdn-troubleshoot-compression.md)
- [Felsök problem med 404-fel](./cdn-troubleshoot-endpoint.md)