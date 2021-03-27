---
title: Vad är dokument Översättning?
description: En översikt över den molnbaserade batch-dokumentets översättnings tjänst och process.
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: f84dc66828f2ebf13316b3348fd93933a2223b5a
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612287"
---
# <a name="what-is-document-translation-preview"></a>Vad är dokument översättning (för hands version)?

Dokument översättning är en molnbaserad funktion i tjänsten [Azure Translator](../translator-info-overview.md) och är en del av Azure-tjänst familjen med REST-API: er. API för dokument översättning översätter dokument till och från 90 språk och dialekter samtidigt som dokument strukturen och data formatet bevaras.

Den här dokumentationen innehåller följande artikel typer:  

* [**Snabb starter**](get-started-with-document-translation.md) hjälper dig att komma igång med instruktioner för att göra förfrågningar till tjänsten.
* [**Instruktions guider**](create-sas-tokens.md) innehåller instruktioner för hur du använder funktionen på mer exakta eller anpassade sätt.  

## <a name="document-translation-key-features"></a>Nyckel funktioner för dokument Översättning

| Funktion | Beskrivning |
| ---------| -------------|
| **Översätt stora filer**| Översätt hela dokument asynkront.|
|**Översätt flera filer**|Översätt flera filer till och från 90 språk och dialekter.|
|**Bevara käll fil presentation**| Översätt filer samtidigt som du behåller den ursprungliga layouten och formatet.|
|**Använd anpassad Översättning**| Översätt dokument med hjälp av allmänna och [anpassade översättnings](../customization.md#custom-translator) modeller.|
|**Använd anpassad Glossaries**|Översätt dokument med hjälp av anpassade Glossaries.|

## <a name="how-to-get-started"></a>Hur kommer man igång?

I vår instruktions guide får du lära dig hur du snabbt kommer igång med Document Translator. För att börja måste du ha ett aktivt [Azure-konto](https://azure.microsoft.com/free/cognitive-services/).  Om du inte har någon kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free).

> [!div class="nextstepaction"]
> [Komma igång](get-started-with-document-translation.md)

## <a name="supported-document-formats"></a>Dokumentformat som stöds

Följande dokument fil typer stöds av dokument översättning:

| Filtyp| Filnamnstillägg|Description|
|---|---|--|
|Adobe PDF|.pdf|Adobe Acrobat Portable Document Format|
|HTML|.html|Språk för text märkning.|
|Lokalisering av Interchange fil format|.xlf. , XLIFF| Ett parallellt dokument format, export av översättnings minnes system. De språk som används definieras i filen.|
|Microsoft Excel|.xlsx|En kalkyl blads fil för data analys och dokumentation.|
|Microsoft Outlook|. msg|Ett e-postmeddelande som skapats eller sparats i Microsoft Outlook.|
|Microsoft PowerPoint|.pptx| En presentations fil som används för att visa innehåll i ett bild spels format.|
|Microsoft Word|.docx| En text dokument fil.|
|Tabbavgränsade värden/TAB|. tsv/. tab| En tabbavgränsad rå data fil som används av kalkyl blads program.|
|Text|.txt| Ett oformaterat text dokument.|
|Översättning av minnes utbyte|.tmx|En öppen XML-standard som används för utbyte av översättnings minne (TM)-data som skapats av Computer Aided översättning (CAT) och lokaliserings program.|

## <a name="supported-glossary-formats"></a>Ord lista som stöds

Följande ord fil typer stöds av dokument översättning:

| Filtyp| Filnamnstillägg|Description|
|---|---|--|
|Lokalisering av Interchange fil format|.xlf. , XLIFF| Ett parallellt dokument format, export av översättnings minnes system. De språk som används definieras i filen.|
|Tabbavgränsade värden/TAB|. tsv/. tab| En tabbavgränsad rå data fil som används av kalkyl blads program.|

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med dokument Översättning](get-started-with-document-translation.md)
