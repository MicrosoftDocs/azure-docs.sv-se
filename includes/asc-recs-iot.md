---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 03/22/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 388798b9f1ada6921fb79363678ecd17a3041227
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801493"
---
Det finns **12** rekommendationer i den här kategorin.

|Rekommendation |Beskrivning |Allvarlighetsgrad |
|---|---|---|
|Standard princip för IP-filter ska nekas |Konfiguration av IP-filter måste ha definierade regler för tillåten trafik och ska neka all annan trafik som standard<br />(Ingen relaterad princip) |Medel |
|Diagnostikloggar i IoT Hub ska vara aktive rad |Aktivera loggar och behåll dem på ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras.<br />(Relaterad princip: [diagnostikloggar i IoT Hub ska vara aktive rad](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f383856f8-de7f-44a2-81fc-e5135b5c2aa4)) |Låg |
|Identiska autentiseringsuppgifter för autentisering |Identiska autentiseringsuppgifter för autentisering till IoT Hub som används av flera enheter. Detta kan tyda på att en Illegitimate-enhet personifierar en legitim enhet. Det exponerar också risken för personifiering av en angripare<br />(Ingen relaterad princip) |Högt |
|IoT-enheter – agent som skickar underutnyttjade meddelanden |IoT agent-meddelandets storleks kapacitet används för närvarande, vilket orsakar en ökning av antalet skickade meddelanden. Justera meddelande intervall för bättre användning<br />(Ingen relaterad princip) |Låg |
|IoT-enheter – gransknings processen slutade skicka händelser |Säkerhets händelser som kommer från gransknings processen tas inte längre emot från den här enheten<br />(Ingen relaterad princip) |Högt |
|IoT-enheter – öppna portar på enheten |En lyssnande slut punkt hittades på enheten<br />(Ingen relaterad princip) |Medel |
|IoT-enheter – det gick inte att verifiera operativ systemets bas linje |Identifierade säkerhetsrelaterade system konfigurations problem<br />(Ingen relaterad princip) |Medel |
|IoT-enheter – en princip för att tillåta brand vägg i en av kedjorna hittades |En tillåten brand Väggs princip hittades i de huvudsakliga brand Väggs kedjorna (indata/utdata). Principen bör neka all trafik som standard definiera regler för att tillåta nödvändig kommunikation till/från enheten<br />(Ingen relaterad princip) |Medel |
|IoT-enheter-tillåtande brand Väggs regel i indatamängden hittades |En regel i brand väggen har hittats som innehåller ett tillåtet mönster för ett brett utbud av IP-adresser eller portar<br />(Ingen relaterad princip) |Medel |
|IoT-enheter – en brand Väggs regel för att tillåtas i utmatnings kedjan hittades |En regel i brand väggen har hittats som innehåller ett tillåtet mönster för ett brett utbud av IP-adresser eller portar<br />(Ingen relaterad princip) |Medel |
|IoT-enheter-TLS cipher Suite-uppgradering krävs |Osäkra TLS-konfigurationer har identifierats. Omedelbar uppgradering av TLS cipher Suite rekommenderas<br />(Ingen relaterad princip) |Medel |
|IP-filter regel för stort IP-intervall |IP-filter regelns käll-IP-intervall är för stort. Överanvändnings regler kan exponera IoT-hubben för skadliga infaller<br />(Ingen relaterad princip) |Medel |
|||
