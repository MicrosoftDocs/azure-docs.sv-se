---
title: Vad är Custom Translator?
titleSuffix: Azure Cognitive Services
description: Custom Translator har liknande funktioner som Microsoft Translator Hub utför för statistisk maskinöversättning, men exklusivt för NMT-system (Neural Machine Translation – neural maskinöversättning).
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: lajanuar
ms.topic: overview
ms.openlocfilehash: 89ab25f45cd35a86d2ff4416ceb851ae6cd5e115
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657169"
---
# <a name="what-is-custom-translator"></a>Vad är Custom Translator?

[Custom Translator](https://portal.customtranslator.azure.ai) är en funktion i Microsoft Translator-tjänsten, där översättningsföretag, apputvecklare och leverantörer av språktjänster kan skapa anpassade NMT-system för neural maskinöversättning. De anpassade översättningssystemen integreras sömlöst i befintliga program, arbetsflöden och webbplatser.

Översättningssystem som skapats med [Custom Translator](https://portal.customtranslator.azure.ai) är tillgängliga genom samma molnbaserade, [säkra](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality), högpresterande, mycket skalbara Microsoft Translator [Text API V3](../reference/v3-0-translate.md?tabs=curl) vars funktioner används av flera miljarder översättningar varje dag.

Custom Translator stöder mer än tre dussin språk och mappas direkt till språken som är tillgängliga för neural maskinöversättning. En komplett lista finns i artikeln om [Microsoft Translator-språk](../language-support.md#customization).

Den här dokumentationen innehåller följande artikel typer:

* [**Snabb starter**](quickstart-build-deploy-custom-model.md) hjälper dig att komma igång med instruktioner för att göra förfrågningar till tjänsten.  
* [**Instruktions guider**](how-to-create-project.md) innehåller instruktioner för hur du använder funktionen på mer exakta eller anpassade sätt.  
* [**Begreppen**](workspace-and-project.md) ger djupgående förklaringar av funktions funktionerna.  


## <a name="features"></a>Funktioner

Anpassad översättare tillhandahåller olika funktioner för att bygga ett anpassat översättnings system och senare komma åt det.

|Funktion  |Beskrivning  |
|---------|---------|
|[Använd teknik för neurala-maskin Översättning](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Förbättra din översättning genom att använda NMT (neurala Machine Translation) som tillhandahålls av anpassad översättare.       |
|[Skapa system som känner till din affärsterminologi](what-are-parallel-documents.md)     |  Anpassa och skapa översättningssystem med hjälp av parallella dokument, som förstår den terminologi som används i din egen verksamhet och bransch.       |
|[Använda ordlista till att skapa dina modeller](what-is-dictionary.md)     |   Om du inte har angett träningsdata kan du träna en modell med bara ordlistedata.       |
|[Samarbeta med andra](how-to-manage-settings.md#share-your-workspace)     |   Samarbeta med teamet genom att dela ditt arbete med olika personer.     |
|[Få åtkomst till din anpassade översättningsmodell](../reference/v3-0-translate.md?tabs=curl)     |  Den anpassade översättningsmodellen kan användas när som helst av dina befintliga appar/program via Microsoft Translator Text API V3.       |

## <a name="get-better-translations"></a>Få bättre översättningar

Microsoft Translator släppte [NMT](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) 2016. NMT innebär stora framsteg i översättningskvaliteten jämfört med standardtekniken inom översättningsindustrin, [statistisk maskinöversättning](https://en.wikipedia.org/wiki/Statistical_machine_translation). Eftersom NMT fångar kontexten för fullständiga meningar på ett bättre sätt innan de översätts ger tekniken översättningar med högre kvalitet som låter mer naturligt och är mer flytande. Med [Custom Translator](https://portal.customtranslator.azure.ai) får du NMT för dina anpassade modeller, vilket ger bättre översättningskvalitet.

Du kan använda tidigare översatta dokument och bygga ett översättningssystem. De här dokumenten innehåller en domän bestämd terminologi och stil, bättre än ett standard översättnings system. Användare kan ladda upp ALIGN-, PDF-, LCL-, HTML-, HTM-, XLF-, TMX-, XLIFF-, TXT-, DOCX- och XLSX-dokument.

Custom Translator accepterar även data som är parallella på dokumentnivå för att göra datainsamling och -förberedelse effektivare. Om användare har åtkomst till versioner med samma innehåll i flera språk men i separata dokument kan Custom Translator automatiskt matcha meningar mellan olika dokument.

Om den lämpliga typen och mängden träningsdata anges är det inte ovanligt att se ett ökat [BLEU-resultat](what-is-bleu-score.md) mellan 5 och 10 poäng genom att använda Custom Translator.

## <a name="be-productive-and-cost-effective"></a>Var produktiv och kostnadseffektiv

Med [Custom Translator](https://portal.customtranslator.azure.ai) kräver träning och distribution av ett anpassat system inte några programmeringskunskaper.

Med hjälp av den säkra [Custom Translator](https://portal.customtranslator.azure.ai)-portalen kan användare ladda upp träningsdata, träna system, testa system och distribuera dem till en produktionsmiljö via ett intuitivt användargränssnitt. Systemet är sedan tillgängligt för användning skalanpassat inom några timmar (faktisk tid beror på storleken på träningsdata).

[Custom Translator](https://portal.customtranslator.azure.ai) kan även användas programmatiskt via ett [dedikerat API](https://custom-api.cognitive.microsofttranslator.com/swagger/) (för närvarande en förhandsversion). Med API: et kan användare hantera skapande eller uppdatering av utbildning via egna appar eller webbtjänster.

Kostnaden för att använda en anpassad modell till att översätta innehåll baseras på användarens prisnivå för Translator Text API. Se Cognitive Services [webbsidan med priser för Translator Text API](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) information om olika prisnivåer.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Översätt säkert när som helst, var som helst på alla dina appar och tjänster

Anpassade system kan sömlöst användas och integreras i alla produkter eller affärsarbetsflöden, och på valfri enhet, via Microsoft Translator Text API via REST-standardtekniken.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [prisinformation](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- Med [Snabbstart](quickstart-build-deploy-custom-model.md) lär du dig att skapa en översättningsmodell i Custom Translator.
