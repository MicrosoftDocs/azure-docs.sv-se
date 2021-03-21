---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 8c77efe9e3e301573b032d1dc1dd32bb4a5ab1a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417760"
---
Hantering av komprimerat ljud implementeras med [gstreamer](https://gstreamer.freedesktop.org). Av licens skäl GStreamer binärfiler inte kompileras och länkas till tal-SDK: n. Utvecklare måste installera flera beroenden och plugin-program, se [installera på Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) eller [Installera i Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). GStreamer-binärfiler måste finnas i System Sök vägen, så att tal-SDK kan läsa in binärfilerna under körningen. Om exempelvis talet SDK kan hitta `libgstreamer-1.0-0.dll` eller `gstreamer-1.0-0.dll` (för senaste GStreamer) under körningen i Windows, innebär det att gstreamer-binärfilerna finns i System Sök vägen.

