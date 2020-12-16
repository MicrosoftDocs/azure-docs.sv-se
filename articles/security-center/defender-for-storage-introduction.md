---
title: Azure Defender för lagring – fördelar och funktioner
description: Lär dig mer om fördelarna och funktionerna i Azure Defender för lagring.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 8979e315f188a5c21cce206c24f195f72096d438
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516508"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Introduktion till Azure Defender för Storage


**Azure Defender för lagring** är ett Azure-ursprungligt informations lager som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja dina lagrings konton. Den använder avancerade funktioner i Security AI och [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) för att tillhandahålla sammanhangsbaserade säkerhets aviseringar och rekommendationer.

Säkerhets varningar utlöses när avvikelser i aktivitet inträffar. Dessa aviseringar är integrerade med Azure Security Center och skickas också via e-post till prenumerations administratörer med information om misstänkt aktivitet och rekommendationer om hur du undersöker och reparerar hot.


## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmänt tillgänglig (GA)|
|Priset|**Azure Defender för lagring** faktureras så som visas på [sidan med priser](security-center-pricing.md)|
|Skyddade lagrings typer:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Moln|![Yes](./media/icons/yes-icon.png) Kommersiella moln<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Kina gov, andra gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Vilken typ av aviseringar tillhandahåller Azure Defender för lagring?

Säkerhets aviseringar utlöses när det finns:

- **Misstänkta åtkomst mönster** – till exempel lyckad åtkomst från en Tor-nod eller från en IP-adress som anses misstänkt av Microsoft Threat Intelligence
- **Misstänkta aktiviteter** – till exempel avvikande data extrahering eller ovanliga ändringar av åtkomst behörigheter
- **Överföring av skadligt innehåll** – till exempel potentiellt skadliga filer (baserat på hash-rykte) eller värdskap för nät fiske innehåll

Aviseringar innehåller information om den incident som utlöste dem, samt rekommendationer om hur du undersöker och åtgärdar hot. Aviseringar kan exporteras till Azure Sentinel eller från tredje parts SIEM eller andra externa verktyg.

> [!TIP]
> Vi rekommenderar att du [konfigurerar Azure Defender för lagring](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center) på prenumerations nivån, men du kan också [Konfigurera det på enskilda lagrings konton](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Vad är hash-ryktes analys för skadlig kod?

För att avgöra om en uppladdad fil är misstänkt använder Azure Defender för lagring hash-ryktes analys som stöds av [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). Skydds verktygen genomsöker inte de överförda filerna, i stället för att kontrol lera lagrings loggarna och jämföra hash-värdena för nyligen överförda filer med kända virus, trojanska hästar, spionprogram och utpressnings tro Jan. 

När en fil misstänks innehålla skadlig kod visar Security Center en avisering och kan välja att e-posta lagrings ägaren för godkännande för att ta bort den misstänkta filen. Om du vill ställa in automatisk borttagning av filer som visar att hash-ryktes analys innehåller skadlig kod kan du distribuera en [arbets flödes automatisering för att utlösa aviseringar som innehåller "potentiell skadlig kod som överförs till ett lagrings konto"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Om du vill aktivera Security Center hot skydds funktioner måste du aktivera Azure Defender på prenumerationen som innehåller de aktuella arbets belastningarna.
>
> Du kan aktivera **Azure Defender för lagring** på antingen prenumerations nivån eller resurs nivån.



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Azure Defender för lagring.

Information om relaterade material finns i följande artiklar: 

- Om en avisering genereras av Security Center eller tas emot av Security Center från en annan säkerhets produkt, kan du exportera den. Om du vill exportera aviseringar till Azure Sentinel, SIEM eller andra externa verktyg, följer du anvisningarna i [Exportera aviseringar till en Siem](continuous-export.md).
- [Så här aktiverar du avancerad Defender för lagring](../storage/common/azure-defender-storage-configure.md)
- [Listan över Azure Defender för lagrings aviseringar](alerts-reference.md#alerts-azurestorage)
- [Microsofts funktioner för hot information](https://go.microsoft.com/fwlink/?linkid=2128684)
