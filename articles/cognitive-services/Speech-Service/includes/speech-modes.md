---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: dc569050b78a5797808f2e2e000019ba516ba22e
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739250"
---
**Interaktiv**
- Avsedd för kommando-och kontroll scenarier.
- Har ett timeout-värde för segmentering på X.
- I slutet av en känd uttryck slutar tjänsten att bearbeta ljud från det begär ande-ID: t och avslutar turn. Anslutningen är inte stängd.
- Max gränsen för igenkänning är 20s.
- Ett typiskt kol samtal till Invoke är `RecognizeOnceAsync` .

**Konversation**
- Avsedd för längre körning av igenkänning.
- Har ett timeout-värde för segmentering av Y. (Y! = X)
- Kommer att bearbeta flera fullständiga yttranden utan att avsluta turn.
- Avslutar inaktive ras för hög tystnad.
- Kol kommer att fortsätta med ett nytt ID för begäran och spela upp ljud efter behov.
- Tjänsten tvingas att koppla från efter 10 minuter tal igenkänning.
- Kol kommer att återansluta och spela upp ej godkänt ljud.
- Anropades under kol with `StartContinuousRecognition` .

**Diktering**
- Tillåter användare att ange interpunktion genom att tala.
- Anropas i kol genom `EnableDictation` att ange på `SpeechConfig` objektet oavsett vilket API-anrop som påbörjar igenkänningen.
- 1<sup>St</sup> part-klustret returnerar `speech.fragment` meddelanden om mellanliggande resultat, och de tre<sup>RD</sup> -parternas retur `speech.hypothesis` meddelanden.