---
title: Bädda in UI Framework-team
titleSuffix: An Azure Communication Services quickstart
description: I det här dokumentet får du lära dig hur du kan använda team Inbäddnings funktionen i Azure Communication Services UI Framework för att bygga nyckel färdiga samtals upplevelser.
author: tophpalmer
ms.author: chpalm
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 99263695aa0842daf59f23cda115dcb5b27b7add
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104804133"
---
# <a name="teams-embed"></a>Teams inbäddning

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]


Teams inbäddning är en funktion för Azure Communication Services som fokuserar på vanliga interaktioner mellan företag och företag som anropar. Kärnan i Teams Inbäddnings systemet är [video och röst samtal](../voice-video-calling/calling-sdk-features.md), men Teams inbäddnings systemet bygger på Azures anrops primitiver för att leverera en fullständig användar upplevelse baserat på Microsoft team-möten.

Teams inbäddning av klient bibliotek är stängd-källa och gör dessa funktioner tillgängliga i ett nyckel färdigt, sammansatt format. Du släpper team bädda in i appens arbets yta så genererar klient biblioteket en fullständig användar upplevelse. Eftersom den här användar upplevelsen liknar Microsoft team-möten kan du dra nytta av:

- Minskad utvecklings tid och teknik komplexitet
- Slutanvändarens bekanta med team
- Möjlighet att återanvända [Teams innehåll för slut användar utbildning](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67)

Team inbäddningen innehåller de flesta funktioner som stöds i team möten, inklusive:

- En för hands upplevelse där en användare konfigurerar sina ljud-och video enheter
- Mötes upplevelse för att konfigurera ljud-och video enheter
- [Video bakgrunder](https://support.microsoft.com/office/change-your-background-for-a-teams-meeting-f77a2381-443a-499d-825e-509a140f4780): tillåta deltagare att sudda ut eller ersätta sina bakgrunder
- [Flera alternativ för video galleriet](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae) stora gallerier, tillsammans, fokus, fästa och Spotlight
- [Innehålls delning](https://support.microsoft.comoffice/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8#ID0EABAAA=Mobile): tillåta deltagare att dela skärmen

Mer information om det här användar gränssnittet jämfört med andra Azure-kommunikations-SDK: er finns i [Introduktion till UI SDK-koncept](ui-sdk-overview.md). 
