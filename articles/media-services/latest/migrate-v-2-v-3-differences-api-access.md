---
title: Media Services v2 vs v3 API-åtkomst
description: I den här artikeln beskrivs skillnaderna mellan Azure Media Services v2 och v3 för API-åtkomst.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 5f3c6526139389da3bfdbc3c43cf8b6d2a1dbccf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567975"
---
# <a name="api-access-differences-between-azure-media-services-v2-to-v3-api"></a>Åtkomst skillnader mellan Azure Media Services v2 och v3 API

![logo typ för migrations guide](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![steg 2 i migreringen](./media/migration-guide/steps-2.svg)

I den här artikeln beskrivs skillnaderna mellan Azure Media Services v2 och v3 för API-åtkomst.

## <a name="api-access"></a>API-åtkomst

Alla Media Services-konton kommer att ha åtkomst till v3-API: et. Men vi rekommenderar starkt att du migrerar migreringen på ett nytt konto innan du använder den uppdaterade koden på ett befintligt v2-konto. Det beror på att v3-entiteter inte är bakåtkompatibla med v2. Vissa v2-enheter som till gångar är kompatibla med v3.
Du kan fortsätta att använda befintliga konton om du inte blandar v2-och v3-API: er och försöker sedan gå tillbaka till v2, men detta rekommenderas inte.

Åtkomst till v2-API: et är tillgängligt tills den dras tillbaka om 2024.

## <a name="create-a-v3-account"></a>Skapa ett v3-konto

När du migrerar kan du skapa ett v3-konto som fortfarande har åtkomst till v2.  Du kan skapa kontot med:

- REST API och äldre version
- Markera kryss rutan i portalen.

> [!div class="mx-imgBorder"]
> [![skapa konton i portalen ](./media/migration-guide/v-3-v-2-access-account-creation-small.png)](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

Alla .NET-, CLI-och andra SDK: er är riktade mot de senaste 2020-05-01-API: erna, så du kan hitta eller konfigurera äldre API-versioner.

> [!NOTE]
> Nya konton som skapats med 2020-05-01 API kan inte använda v2-API: er.
