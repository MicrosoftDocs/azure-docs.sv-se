---
title: Azure Defender för lagring – fördelar och funktioner
description: Lär dig mer om fördelarna och funktionerna i Azure Defender för lagring.
author: memildin
ms.author: memildin
ms.date: 02/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 42aa07ccf8d886dc7eb7109bc405c730331b2c3b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095637"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Introduktion till Azure Defender för Storage

**Azure Defender för lagring** är ett Azure-ursprungligt informations lager som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja dina lagrings konton. Den använder avancerade funktioner i Security AI och [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) för att tillhandahålla sammanhangsbaserade säkerhets aviseringar och rekommendationer.

Säkerhetsaviseringar utlöses när avvikelser i en aktivitet inträffar. Dessa aviseringar är integrerade med Azure Security Center och skickas också via e-post till prenumerations administratörer med information om misstänkt aktivitet och rekommendationer om hur du undersöker och reparerar hot.

## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|**Azure Defender för lagring** faktureras enligt [Security Center prissättning](https://azure.microsoft.com/pricing/details/security-center/)|
|Skyddade lagrings typer:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) US Gov<br>![Inga](./media/icons/no-icon.png) Kina gov, andra gov|
|||


## <a name="what-are-the-benefits-of-azure-defender-for-storage"></a>Vilka är fördelarna med Azure Defender för lagring?

Azure Defender för lagring tillhandahåller:

- **Azure-ursprunglig säkerhet** – med 1 – Klicka på Aktivera, Defender för lagring skyddar data som lagras i Azure Blob, Azure Files och data sjöar. Som en Azure-inbyggd tjänst ger Defender för lagring centraliserad säkerhet för alla data till gångar som hanteras av Azure och är integrerade med andra Azure-säkerhetstjänster som Azure Sentinel.
- **Rich Detection Suite** – drivs av Microsoft Threat Intelligence, identifieringarna i Defender för lagring tar upp de högsta lagrings hoten, till exempel Anonym åtkomst, komprometterade autentiseringsuppgifter, social teknik, missbruk av privilegier och skadligt innehåll.
- **Svar vid skalning** – Security Center Automation-verktyg gör det lättare att förhindra och svara på identifierade hot. Läs mer i [automatisera svar på Security Center utlösare](workflow-automation.md).

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="Översikt över funktionerna i Azure Defender för lagring på hög nivå":::


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Vilken typ av aviseringar tillhandahåller Azure Defender för lagring?

Säkerhets aviseringar utlöses när det finns:

- **Misstänkta åtkomst mönster** – till exempel lyckad åtkomst från en Tor-nod eller från en IP-adress som anses misstänkt av Microsoft Threat Intelligence
- **Misstänkta aktiviteter** – till exempel avvikande data extrahering eller ovanliga ändringar av åtkomst behörigheter
- **Överföring av skadligt innehåll** – till exempel potentiellt skadliga filer (baserat på hash-rykte) eller värdskap för nät fiske innehåll

Aviseringar innehåller information om den incident som utlöste dem, samt rekommendationer om hur du undersöker och åtgärdar hot. Aviseringar kan exporteras till Azure Sentinel eller från tredje parts SIEM eller andra externa verktyg.

> [!TIP]
> Vi rekommenderar att du [konfigurerar Azure Defender för lagring](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center) på prenumerations nivån, men du kan också [Konfigurera det på enskilda lagrings konton](../storage/common/azure-defender-storage-configure.md?tabs=azure-portal).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Vad är hash-ryktes analys för skadlig kod?

För att avgöra om en uppladdad fil är misstänkt använder Azure Defender för lagring hash-ryktes analys som stöds av [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). Skydds verktygen genomsöker inte de överförda filerna, i stället för att kontrol lera lagrings loggarna och jämföra hash-värdena för nyligen överförda filer med kända virus, trojanska hästar, spionprogram och utpressnings tro Jan. 

När en fil misstänks innehålla skadlig kod visar Security Center en avisering och kan välja att e-posta lagrings ägaren för godkännande för att ta bort den misstänkta filen. Om du vill ställa in automatisk borttagning av filer som visar att hash-ryktes analys innehåller skadlig kod kan du distribuera en [arbets flödes automatisering för att utlösa aviseringar som innehåller "potentiell skadlig kod som överförs till ett lagrings konto"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Om du vill aktivera Security Center hot skydds funktioner måste du aktivera Azure Defender på prenumerationen som innehåller de aktuella arbets belastningarna.
>
> Du kan aktivera **Azure Defender för lagring** på antingen prenumerations nivån eller resurs nivån.

## <a name="trigger-a-test-alert-for-azure-defender-for-storage"></a>Utlös en test avisering för Azure Defender för lagring

Om du vill testa säkerhets aviseringarna från Azure Defender för lagring i din miljö genererar du aviseringen "åtkomst från en Tor-nod till ett lagrings konto" med följande steg:

1. Öppna ett lagrings konto med Azure Defender för lagring aktiverat.
1. Välj behållare på sid panelen och öppna en befintlig behållare eller skapa en ny.

    :::image type="content" source="media/defender-for-storage-introduction/opening-storage-container.png" alt-text="Öppna en BLOB-behållare från ett Azure Storage konto" lightbox="media/defender-for-storage-introduction/opening-storage-container.png":::

1. Ladda upp en fil till containern.

    > [!CAUTION]
    > Ladda inte upp en fil som innehåller känsliga data.

1. Använd snabb menyn på den överförda filen för att välja "generera SAS".

    :::image type="content" source="media/defender-for-storage-introduction/generate-sas.png" alt-text="Alternativet Skapa SAS för en fil i en BLOB-behållare":::

1. Lämna standard alternativen och välj **generera SAS-token och URL**.

1. Kopiera den genererade SAS-webbadressen.

1. Öppna Tor-webbläsaren på den lokala datorn.

    > [!TIP]
    > Du kan ladda ned Tor från webbplatsen Tor-projekt [https://www.torproject.org/download/](https://www.torproject.org/download/) .

1. I webbläsaren Tor navigerar du till SAS-URL: en.

1. Ladda ned filen som du laddade upp i steg 3.

    Inom två timmar får du följande säkerhets avisering från Security Center:

    :::image type="content" source="media/defender-for-storage-introduction/tor-access-alert-storage.png" alt-text="Säkerhets avisering om åtkomst från en Tor-stängningsmodul":::

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Azure Defender för lagring.

Information om relaterade material finns i följande artiklar: 

- Om en avisering genereras av Security Center eller tas emot av Security Center från en annan säkerhets produkt, kan du exportera den. Om du vill exportera aviseringar till Azure Sentinel, SIEM eller andra externa verktyg, följer du anvisningarna i [Exportera aviseringar till en Siem](continuous-export.md).
- [Så här aktiverar du avancerad Defender för lagring](../storage/common/azure-defender-storage-configure.md)
- [Listan över Azure Defender för lagrings aviseringar](alerts-reference.md#alerts-azurestorage)
- [Microsofts funktioner för hot information](https://go.microsoft.com/fwlink/?linkid=2128684)