---
title: Vägledning för att koda migrering
description: Den här artikeln innehåller information om kodnings scenarion som hjälper dig att migrera från Azure Media Services v2 till v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 915fdcb059d9e7bf9e1853040b90b82a0457652e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598413"
---
# <a name="encoding-scenario-based-migration-guidance"></a>Vägledning för kodnings scenario-baserad migrering

![logo typ för migrations guide](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![steg 2 i migreringen](./media/migration-guide/steps-4.svg)

Den här artikeln innehåller information om kodnings scenarion som hjälper dig att migrera från Azure Media Services v2 till v3.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ändra ditt kodnings arbets flöde rekommenderar vi att du förstår skillnaderna i hur lagringen hanteras.  I AMS v3 används Azure Storage API för att hantera de lagrings konton som är kopplade till ditt Media Services-konto.

> [!NOTE]
> Jobb och uppgifter som skapas i v2 visas inte i v3 eftersom de inte är associerade med en transformering. Rekommendationen är att växla till v3-transformeringar och-jobb.

## <a name="encoding-workflow-comparison"></a>Kodning av arbets flödes jämförelse

Ta några minuter för att titta på flödesschemana nedan för en visuell jämförelse av kodnings arbets flöden för v2 och v3.

### <a name="v2-encoding-workflow"></a>V2-kodning av arbets flöde

Klicka på bilden nedan om du vill se en större version.

[![Kodnings arbets flöde för v2 ](./media/migration-guide/V2-pretty.svg)](./media/migration-guide/V2-pretty.svg#lightbox)

1. Installation
    1. Skapa en till gång eller Använd och en befintlig till gång. Om du använder en ny till gång överför du innehåll till den till gången. Om du använder en befintlig till gång bör du koda filer som redan finns i till gången.
    2. Hämta värdena för följande objekt:
        - ID eller objekt för medie processor
        - Kodarens sträng (namn) för den kodare som du vill använda
        - Till gångs-ID för ny till gång eller till gångs-ID för den befintliga till gången
    3. För övervakning skapar du antingen en aviserings prenumeration för jobb eller uppgifts nivå eller en SDK-händelsehanterare
2. Skapa jobbet som innehåller uppgiften eller uppgifterna. Varje aktivitet ska innehålla ovanstående objekt och:
    - Ett direktiv som en utmatnings till gång måste skapas.  Utmatnings till gången skapas av systemet.
    - Valfritt namn för utmatnings till gången
3. Skicka in jobbet.
4. Övervaka jobbet.

### <a name="v3-encoding-workflow"></a>V3-kodning av arbets flöde

[![Kodnings arbets flöde för v3](./media/migration-guide/V3-pretty.svg)](./media/migration-guide/V3-pretty.svg#lightbox)

1. Konfigurera
    1. Skapa en till gång eller Använd en befintlig till gång. Om du använder en ny till gång överför du innehåll till den till gången. Om du använder en befintlig till gång bör du koda filer som redan finns i till gången. Du *bör inte ladda upp mer innehåll till denna till gång.*
    1. Skapa en utmatnings till gång.  Utmatnings till gången är den plats där kodade filer och metadata för indata och utdata ska lagras.
    1. Hämta värden för transformeringen:
        - Standard kodare för förval
        - Resurs grupp för AMS
        - AMS konto namn
    1. Skapa transformeringen eller Använd en befintlig transformering.  Transformeringar kan återanvändas. Du behöver inte skapa en ny transformering varje gången du vill skicka ett jobb.
1. Skapa ett jobb
    1. Hämta värdena för följande objekt för jobbet:
        - Namn på transformering
        - Bas-URI för SAS-URL: en för din till gång, sökvägen till HTTPs-källan för din fil resurs eller den lokala sökvägen till filerna. `JobInputAsset`Kan också använda ett till gångs namn som inmatade.
        - Fil namn
        - Utgående till gång (ar)
        - En resursgrupp
        - AMS konto namn  
1. Använd [Event Grid](monitoring/monitor-events-portal-how-to.md) för att övervaka ditt jobb.
1. Skicka in jobbet.

## <a name="custom-presets-from-v2-to-v3-encoding"></a>Anpassade för inställningar från v2 till v3-kodning

Om din v2-kod kallas Standard-kodaren med en anpassad för inställning, måste du först skapa en ny transformering med anpassad standard kodare för förval innan du skickar ett jobb.

Anpassade för inställningar är nu JSON och inte längre XML-baserade. Återskapa din för inställning i JSON efter det anpassade förinställda schemat enligt definitionen i [Swagger-dokumentationen (Transform Open API)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json) .

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>Filer för indata och utdata från ett kodnings jobb

I v2 skapas filer för XML-indata och utdata som genereras som ett resultat av ett kodnings jobb. I v3 har metadata-formatet ändrats från XML till JSON. Mer information om metadata finns i [indata-metadata](input-metadata-schema.md) och [metadata för utdata](output-metadata-schema.md).

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>Premium-kodare till v3 standard-kodare eller partnerbaserade lösningar

V2-API: t stöder inte längre Premium-kodaren. Om du tidigare använde den arbets flödes-baserade Premium-kodaren för HEVC-kodning ska du migrera till den nya v3 [Standard-kodaren](media-encoder-standard-formats.md) med stöd för hevc-kodning.

Om du behöver de avancerade arbets flödes funktionerna i Premium-kodaren rekommenderar vi att du börjar använda en Azure Advanced encoding-partner lösning från [föreställd kommunikation](https://imaginecommunications.com), [multistream](https://www.telestream.net)eller [Bitmovin](https://bitmovin.com).

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>Jobb med indata som finns på HTTPS-värdbaserade URL: er

Nu kan du skicka jobb i v3 från filer som lagras i Azure Storage, lagras lokalt eller externa webb servrar med hjälp av [stöd för http (S)-jobb indata](job-input-from-http-how-to.md).

Om du tidigare använde arbets flöden för att kopiera filer från Azure Blob-filer till tomma till gångar innan du skickar jobb, kan du förenkla arbets flödet genom att skicka en SAS-URL för filen i Azure Blob Storage direkt till jobbet.

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>Indexerare v1 ljud avskrift till det nya AudioAnalyzer "grundläggande läget"

För kunder som använder en indexerare v1-processor i v2-API: et måste du skapa en transformering som anropar det nya `AudioAnalyzer` i [Basic-läget](how-to-create-basic-audio-transform.md) innan du skickar ett jobb.

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>Kodnings-, omvandlings-och jobb koncept, självstudier och hur du går till guider

### <a name="concepts"></a>Begrepp

- [Koda video och ljud med Media Services](encoding-concept.md)
- [Standardformat för kodare och codec](media-encoder-standard-formats.md)
- [Koda med en steg för automatiskt genererad bit hastighet](autogen-bitrate-ladder.md)
- [Använd den innehålls medveten kodnings inställningen för att hitta det optimala värdet för bit hastighet för en specifik lösning](content-aware-encoding.md)
- [Enheter med reserverat medium](concept-media-reserved-units.md)
- [Inkommande metadata](input-metadata-schema.md)
- [Utgående metadata](output-metadata-schema.md)
- [Dynamisk paketering i Media Services v3: ljud-codec](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>Självstudier

- [Självstudie: koda en fjärrfil baserat på URL och strömma videon – .NET](stream-files-dotnet-quickstart.md)
- [Självstudie: Ladda upp, koda och strömma videor med Media Services v3](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>Guider

- [Skapa ett jobb inmatat från en HTTPS-URL](job-input-from-http-how-to.md)
- [Skapa ett jobb indata från en lokal fil](job-input-from-local-file-how-to.md)
- [Skapa en grundläggande ljud omvandling](how-to-create-basic-audio-transform.md)
- Med .NET
  - [Koda med en anpassad transformering – .NET](customize-encoder-presets-how-to.md)
  - [Så här skapar du ett överlägg med Media Encoder Standard](how-to-create-overlay.md)
  - [Så här genererar du miniatyrer med kodare standard med .NET](media-services-generate-thumbnails-dotnet.md)
- Med Azure CLI
  - [Koda med en anpassad transformering – Azure CLI](custom-preset-cli-howto.md)
- Med REST
  - [Koda med en anpassad transformering-REST](custom-preset-rest-howto.md)
  - [Så här genererar du miniatyr bilder med hjälp av Encoder-standarden med REST](media-services-generate-thumbnails-rest.md)
- [Under klipp en video vid kodning med Media Services-.NET](subclip-video-dotnet-howto.md)
- [Under klipp en video vid kodning med Media Services REST](subclip-video-rest-howto.md)

## <a name="samples"></a>Exempel

Du kan också [jämföra v2-och v3-koden i kod exemplen](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
