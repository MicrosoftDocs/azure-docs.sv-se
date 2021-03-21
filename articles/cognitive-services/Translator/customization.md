---
title: Översättnings anpassning – översättare
titleSuffix: Azure Cognitive Services
description: Använd Microsoft Translator Hub för att bygga ett eget maskin översättnings system med önskad terminologi och stil.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 0930024a791fe8b76c90e8ab4249a070b22a1c16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898044"
---
# <a name="customize-your-text-translations"></a>Anpassa dina text översättningar

Den anpassade översättaren är en funktion i Translator-tjänsten, vilket gör att användare kan anpassa Microsoft translators avancerade neurala för maskin översättning vid översättning av text med hjälp av Translator (endast version 3).

Funktionen kan också användas för att anpassa tal översättning när den används med [Cognitive Services tal](../speech-service/index.yml).

## <a name="custom-translator"></a>Custom Translator

Med anpassad översättare kan du bygga neurala översättnings system som förstår terminologin som används i din egen verksamhet och bransch. Det anpassade översättnings systemet integreras sedan i befintliga program, arbets flöden och webbplatser.

### <a name="how-does-it-work"></a>Hur fungerar det?

Använd dina tidigare översatt dokument (broschyrer, webb sidor, dokumentation osv.) för att bygga ett översättnings system som motsvarar din domän specifika terminologi och stil, bättre än ett standard översättnings system. Användare kan ladda upp TMX-, XLIFF-, TXT-, DOCX-och XLSX-dokument.  

Systemet accepterar också data som är parallella på dokument nivån, men som ännu inte har justerats på menings nivå. Om användarna har till gång till versioner av samma innehåll på flera språk, men i separata dokument kan anpassade översättare automatiskt matcha meningar i dokument.  Systemet kan också använda monolingual-data i antingen eller båda språken för att komplettera de parallella tränings data för att förbättra översättningarna.

Det anpassade systemet är sedan tillgängligt via ett vanligt anrop till Translator med hjälp av parametern Category.

Med hänsyn till lämplig typ och mängd av utbildnings data är det inte ovanligt att förväntas vinster mellan 5 och 10, eller till och med fler BLEU punkter om översättnings kvalitet med hjälp av anpassad översättare.

Mer information om olika anpassnings nivåer som baseras på tillgängliga data finns i [användar handboken för anpassad översättare](./custom-translator/overview.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera ett anpassat språk system med anpassad översättare](./custom-translator/overview.md)
