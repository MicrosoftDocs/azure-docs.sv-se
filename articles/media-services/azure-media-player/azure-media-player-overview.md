---
title: Översikt över Azure Media Player
description: Läs mer om Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.openlocfilehash: ec36f6bd19728d47b73a0186fb1fb192ba57f4cf
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449825"
---
# <a name="azure-media-player-overview"></a>Översikt över Azure Media Player #

Azure Media Player är en webb Videos pelare som spelar upp medie innehåll från [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) på en rad olika webbläsare och enheter. Azure Media Player använder bransch standarder, till exempel HTML5, media source Extensions (MSE) och EME (Encrypted Media Extensions) för att tillhandahålla en omfattande anpassningsbar strömmande upplevelse.  När dessa standarder inte är tillgängliga på en enhet eller i en webbläsare, använder Azure Media Player Flash och Silverlight som reserv teknik. Oavsett vilken uppspelnings teknik som används har utvecklare ett enhetligt JavaScript-gränssnitt för att få åtkomst till API: er, vilket gör att innehåll som hanteras av Azure Media Services kan spelas upp i en mängd olika enheter och webbläsare utan extra ansträngning.

Microsoft Azure Media Services gör att innehåll kan hanteras med bindestreck, Smooth Streaming och HLS strömnings format för att spela upp innehåll. Azure Media Player tar hänsyn till dessa olika format och spelar automatiskt upp den bästa länken utifrån plattforms-och webb läsar funktionerna. Microsoft Azure Media Services tillhandahåller också dynamisk kryptering av till gångar med common Encryption (PlayReady eller Widevine) eller AES-128 bitars kryptering av kuvert. Azure Media Player tillåter dekryptering av PlayReady-och AES-128-bitars krypterat innehåll när det är lämpligt konfigurerat.  Information om hur du konfigurerar spelaren finns i avsnittet [skyddat innehåll](azure-media-player-protected-content.md) .

Om du vill begära nya funktioner, ge idéer eller feedback, skicka dem till [UserVoice för Azure Media Player](https://aka.ms/ampuservoice). Om du har och specifika problem, frågor eller hittar några buggar, släpp vi oss på ampinfo@microsoft.com .

> [!NOTE]
> Observera att Azure Media Player endast stöder medie strömmar från Azure Media Services.

## <a name="license"></a>Licens ##

Azure Media Player licensieras och omfattas av villkoren som beskrivs i licens villkoren för program vara från Microsoft för Azure Media Player. Se [licens filen](/legal/azure-media-player/azure-media-player-license) för fullständiga villkor. Mer information finns i [Sekretess policyn](https://www.microsoft.com/en-us/privacystatement/default.aspx).

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>Nästa steg ##

- [Azure Media Player snabb start](azure-media-player-quickstart.md)
