---
title: Kapacitets gränser i Azure Lab Services
description: Läs mer om kapacitets begränsningar (gränser för virtuella datorer) i Azure Lab Services.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 9866628cd11ec8df67e6fe16ae8806f0f30ae9a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491026"
---
# <a name="capacity-limits-in-azure-lab-services"></a>Kapacitets gränser i Azure Lab Services
Azure Lab Services har standard kapacitets gränser för Azure-prenumerationer som följer Azures begränsningar för beräknings kvoter och för att minimera bedrägerier. Alla Azure-prenumerationer får en ursprunglig kapacitets gräns, som kan variera beroende på prenumerations typ, antal standard beräknings kärnor och GPU-kärnor som är tillgängliga i Azure Lab Services. Den begränsar hur många virtuella datorer du kan skapa i labbet innan du måste begära en gräns för att öka.  

Om du är nära eller har nått gränsen för din prenumerations virtuella dator kärnor visas meddelanden från Azure Lab Services när du försöker utföra åtgärder som skapar ytterligare virtuella datorer. Exempel: 

- Skapa ett labb
- Publicera ett labb
- Justera labb kapaciteten för att lägga till fler virtuella datorer i ett befintligt labb

De här åtgärderna kan också inaktive ras om du redan har nått gränsen för kärnor. 

![Kärn gränser – varnings meddelande](./media/capacity-limits/warning-message.png)

## <a name="subscriptions-with-default-limit-of-zero-cores"></a>Prenumerationer med standard begränsning på noll kärnor
Vissa ovanliga prenumerations typer som ofta används för bedrägerier kan ha en standard gräns på 0 standard kärnor och 0 GPU-kärnor. Om du använder någon av dessa prenumerations typer måste administratören som skapar ditt labb konto begära en gräns ökning innan du kan använda Azure Lab Services. 

Administratören kan följa de här stegen för att begära en gräns ökning:  

1.  [Skapa ett labb konto](tutorial-setup-lab-account.md)i din prenumeration.
2.  På sidan **Översikt** i labb kontot klickar du på knappen för att **öka gränsen för begränsning** längst upp. 
3.  Följ stegen i formuläret för att skicka en supportbegäran för att öka gränsen.

## <a name="request-a-limit-increase"></a>Begär en gräns ökning
Om du når gränsen för kärnor kan du begära en gräns ökning för att fortsätta använda Azure Lab Services. Processen för begäran är en kontroll punkt för att säkerställa att din prenumeration inte ingår i några fall av bedrägerier eller oavsiktliga, plötsliga storskaliga distributioner.

Meddelandena om gränsen för virtuella dator kärnor i Azure Lab Services-portalen innehåller en länk för att begära en gräns ökning. Länken öppnar en ny flik i webbläsaren där du kan skapa en ny supportbegäran. Information om problem typ, prenumeration och kvot typ fylls i automatiskt för dig som du ser i följande bild: 

![Ny supportbegäran](./media/capacity-limits/new-support-request.png)


Sedan uppmanas du att ange mer information om gräns ökningen. I fältet **Beskrivning** anger du följande information:

- Vad du försöker göra (till exempel skapa ett labb för att lära en dator vetenskaps klass, köra en Hackathon och så vidare.)
- Den storlek på virtuell dator som du använder för det här labbet
- Antal virtuella datorer som du behöver

När du har skickat in support förfrågan kommer vi att granska begäran. Vid behov kommer vi att kontakta dig för att få mer information. 

## <a name="next-steps"></a>Nästa steg
Se följande artikel:
- [Administratörs guide – storlek på virtuell dator](administrator-guide.md#vm-sizing).
- [Vanliga frågor och svar](classroom-labs-faq.md).