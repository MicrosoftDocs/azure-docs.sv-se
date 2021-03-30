---
title: Metod tips för avbildnings Taggar
description: Metod tips för taggning och versioner av Docker-behållar avbildningar vid överföring av bilder till och från ett Azure Container Registry
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: b483317960409fe1fbea181706f12375606fe659
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75445743"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Rekommendationer för taggning och versions behållar avbildningar

När du distribuerar behållar avbildningar till ett behållar register och sedan distribuerar dem måste du ha en strategi för avbildnings taggning och versions hantering. I den här artikeln beskrivs två metoder och var de passar under livs cykel livs cykeln:

* **Stabila Taggar** – taggar som du återanvänder, till exempel för att ange en större eller mindre version, till exempel *mycontainerimage: 1.0*.
* **Unika Taggar** – en annan tagg för varje bild som du skickar till ett register, till exempel *mycontainerimage: vi abc123*.

## <a name="stable-tags"></a>Stabila Taggar

**Rekommendation**: Använd stabila taggar för att underhålla **bas avbildningar** för dina behållar versioner. Undvik distributioner med stabila taggar, eftersom dessa taggar fortsätter att ta emot uppdateringar och kan leda till inkonsekvenser i produktions miljöer.

*Stabila Taggar* innebär en utvecklare, eller ett build-system, kan fortsätta att hämta en speciell tagg, vilket fortsätter att hämta uppdateringar. Stabilt innebär inte att innehållet är fryst. Stabilt innebär i stället att avbildningen bör vara stabil för avsikten med den versionen. För att hålla "stabil" kan det vara en service att tillämpa säkerhets korrigeringar eller Ramverks uppdateringar.

### <a name="example"></a>Exempel

Ett Ramverks team levereras version 1,0. De vet att de kommer att leverera uppdateringar, inklusive mindre uppdateringar. För att stödja stabila taggar för en specifik och mindre version, har de två uppsättningar av stabila taggar.

* `:1` – en stabil tagg för huvud versionen. `1` representerar den nyaste eller senaste 1. * versionen.
* `:1.0`– en stabil tagg för version 1,0, så att en utvecklare kan binda till uppdateringar av 1,0 och inte skickas vidare till 1,1 när den släpps.

Teamet använder också `:latest` taggen, som pekar på den senaste stabila taggen, oavsett vad den aktuella huvud versionen är.

När bas avbildnings uppdateringar är tillgängliga, eller någon typ av service version av ramverket, uppdateras bilder med de stabila taggarna till den senaste Digest som representerar den senaste stabila versionen av den versionen.

I det här fallet betjänas både huvud-och del taggarna kontinuerligt. I ett scenario med en bas avbildning kan avbildningens ägare tillhandahålla tjänst avbildningar.

### <a name="delete-untagged-manifests"></a>Ta bort otaggade manifest

Om en bild med en stabil tagg uppdateras, är den tidigare taggade bilden omärkt, vilket resulterar i en överbliven bild. Föregående bilds manifest och unika lager data finns kvar i registret. För att upprätthålla din register storlek kan du regelbundet ta bort otaggade manifest som orsakas av stabila avbildnings uppdateringar. Du kan t. ex. [Rensa](container-registry-auto-purge.md) otaggade manifest som är äldre än en angiven varaktighet eller ange en [bevarande princip](container-registry-retention-policy.md) för otaggade manifest.

## <a name="unique-tags"></a>Unika Taggar

**Rekommendation**: Använd unika taggar för **distributioner**, särskilt i en miljö som kan skalas på flera noder. Du vill förmodligen avsiktliga distributioner av en konsekvent version av komponenter. Om din behållare startar om eller om en Orchestrator skalar ut fler instanser, kan dina värdar inte oavsiktligt hämta en nyare version, inkonsekvent med de andra noderna.

Unik taggning innebär bara att varje bild som flyttas till ett register har en unik tagg. Taggar återanvänds inte. Det finns flera mönster som du kan följa för att generera unika taggar, inklusive:

* **Datum-** tidstämpel – den här metoden är ganska vanlig, eftersom du tydligt kan se när avbildningen har skapats. Men hur kan du korrelera tillbaka till ditt versions system? Måste du hitta versionen som slutfördes samtidigt? Vilken tidszon är du i? Är alla dina Bygg system kalibrerade för UTC?
* **Git-incheckning**  – den här metoden fungerar tills du har startat stöd för bas avbildnings uppdateringar. Om en bas avbildnings uppdatering sker, kommer ditt build-system att sätta igång med samma git-incheckning som den tidigare versionen. Bas avbildningen har dock nytt innehåll. I allmänhet tillhandahåller en git-incheckning en *halv* stabil tagg.
* **Manifest Sammanfattning** -varje behållar avbildning som skickas till ett behållar register är associerad med ett manifest som identifieras av en unik SHA-256-hash eller Digest. När det är unikt är sammanfattningen lång, svår att läsa och korrelerad med din build-miljö.
* **Build-ID** – det här alternativet kan vara bäst eftersom det är troligt vis stegvist, och det gör att du kan korrelera tillbaka till den specifika versionen för att hitta alla artefakter och loggar. Men liksom en manifest sammandrag kan det vara svårt för en mänsklig att läsa.

  Om din organisation har flera build-system, är prefixet med build-systemnamnet en variant av detta alternativ: `<build-system>-<build-id>` . Du kan till exempel särskilja builds från API-teamets Jenkins build-system och webb teamets Azure pipelines build system.

### <a name="lock-deployed-image-tags"></a>Lås distribuerade avbildnings Taggar

Som bästa praxis rekommenderar vi att du [låser](container-registry-image-lock.md) alla distribuerade avbildnings etiketter genom att ställa in dess `write-enabled` attribut till `false` . Den här metoden hindrar dig från att oavsiktligt ta bort en avbildning från registret och eventuellt störa dina distributioner. Du kan inkludera låsnings steget i din versions pipeline.

Genom att låsa en distribuerad avbildning kan du fortfarande ta bort andra, icke distribuerade avbildningar från registret med hjälp av Azure Container Registry funktioner för att underhålla registret. Ta till exempel [automatiskt bort](container-registry-auto-purge.md) otaggade manifest eller olåsta bilder som är äldre än en angiven varaktighet eller ange en [bevarande princip](container-registry-retention-policy.md) för otaggade manifest.

## <a name="next-steps"></a>Nästa steg

En mer detaljerad beskrivning av begreppen i den här artikeln finns i blogg inlägget [Docker-taggning: metod tips för taggning och versioner av Docker-avbildningar](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

Information om hur du maximerar prestanda och kostnads effektiv användning av Azure Container Registry finns i [metod tips för Azure Container Registry](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->

