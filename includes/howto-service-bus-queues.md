---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: d3d33c87dc1adf65a53b71cc4c833e7f4a191670
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187583"
---
## <a name="what-are-service-bus-queues"></a>Vad är Service Bus-köer?
Service Bus-köer stöder kommunikation med hjälp av en **asynkron meddelandetjänst**. När du använder köer kommunicerar komponenter i ett distribuerat program inte direkt med varandra. De utbyter istället meddelanden via en kö som fungerar som en mellanhand (asynkron meddelandekö). En meddelandeproducent (avsändare) lämnar meddelandet till kön och fortsätter sedan dess bearbetning. En meddelandekonsument (mottagare) hämtar asynkront meddelandet från kön och bearbetar det. Producenten behöver inte vänta på ett svar från konsumenten för att kunna fortsätta bearbetningen och skicka fler meddelanden. Köer erbjuder **FIFO (First in, first out)**-leverans av meddelanden till en eller flera konkurrerande konsumenter. Det betyder att meddelanden mottas och bearbetas vanligtvis av mottagarna i den ordning som de lagts till i kön, och varje meddelande tas bara emot och bearbetas av en meddelandekonsument.

![QueueConcepts](./media/howto-service-bus-queues/sb-queues-08.png)

Service Bus-köer är en mångsidig teknologi som kan användas för en mängd olika scenarier:

* Kommunikation mellan webb- och arbetsroller i ett Azure-program med flera nivåer.
* Kommunikation mellan lokala appar och appar med Azure som värd, i en hybridlösning.
* Kommunikation mellan komponenter i ett distribuerat program som körs lokalt i olika organisationer eller avdelningar i en organisation.

Med hjälp av köer kan du skala ditt program enklare och med bättre återhämtning i din arkitektur.


