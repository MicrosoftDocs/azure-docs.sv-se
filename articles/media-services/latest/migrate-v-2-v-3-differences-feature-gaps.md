---
title: Funktions luckor mellan Azure Media Services v2 och v3
description: I den här artikeln beskrivs funktions luckor mellan Azure Media Services v2 och v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 07e43bdb0969c26120231013cc0c0354e6968ceb
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567958"
---
# <a name="feature-gaps-between-azure-media-services-v2-and-v3"></a>Funktions luckor mellan Azure Media Services v2 och v3

![logo typ för migrations guide](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![steg 2 i migreringen](./media/migration-guide/steps-2.svg)

Den här delen av vägledningen för migrering innehåller detaljerad information om skillnaderna mellan v2-och v3-API: erna.

## <a name="feature-gaps-between-v2-and-v3-apis"></a>Funktions luckor mellan v2-och v3-API: er

V3-API: et har följande funktions luckor med v2-API: et. Några av de avancerade funktionerna i Media Encoder Standard i v2 API: er är för närvarande inte tillgängliga i v3:

- Att infoga ett tyst ljud spår när indata inte har något ljud, eftersom detta inte längre krävs med Azure Media Player.

- Att infoga ett video spår när inmatningen saknar video.

- Live-händelser med omkodning stöder för närvarande inte Skriv sätts infogning mellan ström och AD-markör infogning via API-anrop.

- Det går inte längre att använda Azure Media Premium-kodare i v2. Om du använder den för 8-bitars HEVC-kodning använder du det nya HEVC-stödet i Standard-kodaren. 
    - Vi har lagt till stöd för ljud kanal mappning till Standard-kodaren.  Se [ljud i Media Services Encoding Swagger-dokumentationen](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).
    - Om du använder avancerade funktioner eller utdataformat för en licensierad produkt från tredje part, till exempel MXF eller ProRes, använder du Azure-partner lösningen från multistream, som kommer att vara transaktionell vid tiden för den 2: a tiden. Du kan också använda föreställd kommunikation eller [Bitmovin](http://bitmovin.com).

- Egenskapen "tillgänglighets uppsättning" i slut punkten för direkt uppspelning i v2 stöds inte längre. Se exempelprojektet och vägledning för VOD-leverans med [hög tillgänglighet](./media-services-high-availability-encoding.md) i v3-API: et.

- Det går inte att ange FairPlay IV i Media Services v3. Även om det inte påverkar kunder som använder Media Services för både paketering och licens leverans, kan det vara ett problem när du använder ett DRM-system från tredje part för att leverera FairPlay-licenser (hybrid läge).

- Lagrings kryptering på klient sidan för att skydda till gångar i rest har tagits bort i v3-API: et och ersatts av Storage Service Encryption för vilande data. V3-API: erna fortsätter att fungera med befintliga lagrings krypterade till gångar men tillåter inte att nya skapas.

## <a name="terminology-and-entity-changes"></a>Ändringar av terminologi och enhet

Se [terminologi och enhets](migrate-v-2-v-3-differences-terminology.md) ändringar för ytterligare ändringar i API: et.
