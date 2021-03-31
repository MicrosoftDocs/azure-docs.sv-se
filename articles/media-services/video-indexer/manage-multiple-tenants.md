---
title: Hantera flera klienter med Video Indexer – Azure
description: Den här artikeln föreslår olika integrerings alternativ för att hantera flera klienter med Video Indexer.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: 18f2cf3daa281400151ba223e1735e7138d97e8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "76990512"
---
# <a name="manage-multiple-tenants"></a>Hantera flera klientorganisationer

I den här artikeln beskrivs olika alternativ för att hantera flera klienter med Video Indexer. Välj den metod som passar bäst för ditt scenario:

* Video Indexer-konto per klientorganisation
* Ett Video Indexer-konto för alla klientorganisationer
* En Azure-prenumeration per klientorganisation

## <a name="video-indexer-account-per-tenant"></a>Video Indexer-konto per klientorganisation

När du använder den här arkitekturen skapas ett Video Indexer-konto för varje klient. Klienterna har fullständig isolering i beständiga och Compute-skiktet.  

![Video Indexer-konto per klientorganisation](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Överväganden

* Kunder delar inte lagrings konton (såvida de inte har kon figurer ATS manuellt av kunden).
* Kunder delar inte Compute (reserverade enheter) och påverkar inte bearbetnings jobb tiderna för varandra.
* Du kan enkelt ta bort en klient från systemet genom att ta bort Video Indexer kontot.
* Det finns ingen möjlighet att dela anpassade modeller mellan klienter.

    Se till att det inte finns något affärs krav för att dela anpassade modeller.
* Svårare att hantera på grund av flera Video Indexer-konton (och tillhör ande Media Services) per klient.

> [!TIP]
> Skapa en administratörs användare för ditt system i [video Indexer Developer-portalen](https://api-portal.videoindexer.ai/) och Använd API: et för auktorisering för att tillhandahålla klienterna relevant [konto åtkomst-token](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token).

## <a name="single-video-indexer-account-for-all-users"></a>Enkelt Video Indexer konto för alla användare

När du använder den här arkitekturen är kunden ansvarig för isolering av klient organisationer. Alla klienter måste använda ett enda Video Indexer konto med ett enda Azure Media Service-konto. När du laddar upp, söker eller tar bort innehåll måste kunden filtrera rätt resultat för den klienten.

![Enkelt Video Indexer konto för alla användare](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Med det här alternativet kan anpassnings modeller (person, språk och varumärken) delas eller isoleras mellan klienter genom att filtrera modeller efter klient.

När du [laddar upp videor](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)kan du ange ett annat partition-attribut per klient. Detta gör det möjligt att isolera i [Sök-API: et](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?). Genom att ange attributet partition i Sök-API: t får du bara resultat från den angivna partitionen. 

### <a name="considerations"></a>Överväganden

* Möjlighet att dela innehålls-och anpassnings modeller mellan klienter.
* En klient som påverkar prestanda för andra klienter.
* Kunden måste bygga ett komplext hanterings lager ovanpå Video Indexer.

> [!TIP]
> Du kan använda [prioritets](upload-index-videos.md) -attributet för att prioritera klient jobb.

## <a name="azure-subscription-per-tenant"></a>En Azure-prenumeration per klientorganisation 

När du använder den här arkitekturen får varje klient organisation sin egen Azure-prenumeration. För varje användare kommer du att skapa ett nytt Video Indexer-konto i klient prenumerationen.

![En Azure-prenumeration per klientorganisation](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Överväganden

* Detta är det enda alternativet som möjliggör fakturerings separation.
* Den här integrationen har fler hanterings kostnader än Video Indexer konto per klient. Om fakturering inte är ett krav rekommenderar vi att du använder något av de andra alternativen som beskrivs i den här artikeln.

## <a name="next-steps"></a>Nästa steg

[Översikt](video-indexer-overview.md)
