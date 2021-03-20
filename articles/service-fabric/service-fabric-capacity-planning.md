---
title: Kapacitets planering för Service Fabric appar
description: Beskriver hur du identifierar antalet Compute-noder som krävs för ett Service Fabric program
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cd5a5c55ff873e4891ac63361d0c4a0b56d70109
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75377216"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Kapacitets planering för Service Fabric program
I det här dokumentet lär du dig hur du beräknar mängden resurser (CPU: er, RAM-minne, disk lagring) som du behöver för att köra dina Azure Service Fabric-program. Det är vanligt att resurs kraven ändras med tiden. Du behöver normalt få resurser när du utvecklar/testar din tjänst och behöver sedan mer resurser när du går in i produktionen och ditt program växer i popularitet. När du utformar ditt program ska du tänka igenom de långsiktiga kraven och göra alternativ som gör att din tjänst kan skalas för att möta hög kund efter frågan.

 När du skapar ett Service Fabric kluster bestämmer du vilka typer av virtuella datorer (VM) som utgör klustret. Varje virtuell dator levereras med en begränsad mängd resurser i form av CPU: er (kärnor och hastighet), nätverks bandbredd, RAM-minne och disk lagring. När din tjänst växer med tiden kan du uppgradera till virtuella datorer som erbjuder större resurser och/eller lägga till fler virtuella datorer i klustret. Om du vill göra det senare måste du först skapa en tjänst så att den kan dra nytta av nya virtuella datorer som läggs till dynamiskt i klustret.

Vissa tjänster hanterar lite till inga data på de virtuella datorerna. Därför bör kapacitets planeringen för dessa tjänster fokuseras främst på prestanda, vilket innebär att du väljer lämpliga processorer (kärnor och hastighet) för de virtuella datorerna. Dessutom bör du tänka på nätverks bandbredd, inklusive hur ofta nätverks överföringar sker och hur mycket data som överförs. Om tjänsten behöver utföra en ökad användning av tjänsten kan du lägga till fler virtuella datorer i klustret och belastningsutjämna nätverks förfrågningarna över alla virtuella datorer.

För tjänster som hanterar stora mängder data på de virtuella datorerna bör kapacitets planeringen fokuseras främst på storlek. Därför bör du noga överväga kapaciteten för den virtuella datorns RAM-minne och disk lagring. Det virtuella minnes hanterings systemet i Windows gör att disk utrymmet ser ut som RAM till program koden. Dessutom ger den Service Fabric körnings miljön Smart växling som håller enbart in data i minnet och flyttar kall data till disk. Program kan därmed använda mer minne än vad som är fysiskt tillgängligt på den virtuella datorn. Att ha mer RAM-minne ökar helt enkelt prestanda, eftersom den virtuella datorn kan lagra mer disk utrymme i RAM-minnet. Den virtuella dator du väljer måste ha en disk som är tillräckligt stor för att lagra de data som du vill ha på den virtuella datorn. På samma sätt bör den virtuella datorn ha tillräckligt med RAM-minne för att ge dig den prestanda du önskar. Om din tjänsts data växer med tiden kan du lägga till fler virtuella datorer i klustret och partitionera data på alla virtuella datorer.

## <a name="determine-how-many-nodes-you-need"></a>Ta reda på hur många noder du behöver
Genom att partitionera tjänsten kan du skala ut dina tjänst data. Mer information om partitionering finns i [partitionering Service Fabric](service-fabric-concepts-partitioning.md). Varje partition måste rymmas inom en enskild virtuell dator, men flera (små) partitioner kan placeras på en enda virtuell dator. Därför ger mer små partitioner större flexibilitet än att ha några större partitioner. Kompromissen är att med flera partitioner ökar Service Fabric omkostnader och du kan inte utföra överförda åtgärder mellan partitioner. Det finns också mer potentiell nätverks trafik om din tjänst kod ofta behöver komma åt data mängder som är aktiva i olika partitioner. När du designar din tjänst bör du tänka igenom dessa och nack delar för att komma till en effektiv partitionerings strategi.

Vi antar att ditt program har en enda tillstånds känslig tjänst med en lagrings storlek som du förväntar dig att växa till DB_Size GB på ett år. Du är redo att lägga till fler program (och partitioner) när du upplever tillväxt under det året.  Replikeringstopologin (RF), som avgör antalet repliker för tjänsten påverkar den totala DB_Size. Den totala DB_Size över alla repliker är replikeringstrafiken multiplicerad med DB_Size.  Node_Size representerar det disk utrymme/RAM-minne per nod som du vill använda för din tjänst. För bästa prestanda bör DB_Size anpassas till minnet i klustret och en Node_Size som är runt RAM-minnet för den virtuella datorn väljs. Genom att tilldela en Node_Size som är större än RAM-kapaciteten, förlitar du dig på växlingen som tillhandahålls av Service Fabric Runtime. Därför kanske dina prestanda inte är optimala om hela data anses vara frekventa (sedan data växlas in/ut). För många tjänster där endast en bråkdel av data är frekvent är det dock mer kostnads effektivt.

Antalet noder som krävs för högsta prestanda kan beräknas på följande sätt:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Konto för tillväxt
Du kanske vill beräkna antalet noder baserat på DB_Size som du förväntar dig att din tjänst ska växa till, utöver de DB_Size som du började med. Sedan kan du öka antalet noder när tjänsten växer så att du inte överutnyttjar antalet noder. Men antalet partitioner bör baseras på antalet noder som behövs när du kör tjänsten vid maximal tillväxt.

Det är lämpligt att ha vissa extra datorer tillgängliga när som helst, så att du kan hantera oväntade toppar eller fel (till exempel om några virtuella datorer går ned).  Även om den extra kapaciteten bör fastställas med hjälp av förväntade toppar, är en start punkt att reservera några extra virtuella datorer (5-10 procent extra).

Föregående förutsätter en enda tillstånds känslig tjänst. Om du har mer än en tillstånds känslig tjänst måste du lägga till DB_Size som är associerade med de andra tjänsterna i ekvationen. Alternativt kan du beräkna antalet noder separat för varje tillstånds känslig tjänst.  Tjänsten kan ha repliker eller partitioner som inte är balanserade. Tänk på att partitioner också kan ha mer data än andra. Mer information om partitionering finns i [partitionerings artikel om metod tips](service-fabric-concepts-partitioning.md). Föregående formel är dock partition-och replik oberoende, eftersom Service Fabric säkerställer att replikerna sprids ut mellan noderna på ett optimerat sätt.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Använd ett kalkyl blad för kostnads beräkning
Nu ska vi skicka några reella tal i formeln. Ett [exempel kalkyl blad](https://github.com/Azure/service-fabric/raw/master/docs_resources/SF_VM_Cost_calculator-NEW.xlsx) visar hur du planerar kapaciteten för ett program som innehåller tre typer av data objekt. För varje objekt uppskattar vi storleken och hur många objekt vi förväntar sig att ha. Vi väljer också hur många repliker vi vill ha för varje objekt typ. Kalkyl bladet beräknar den totala mängden minne som ska lagras i klustret.

Sedan anger vi en VM-storlek och en månads kostnad. Baserat på den virtuella datorns storlek visar kalkyl bladet det minsta antalet partitioner som du måste använda för att dela upp dina data så att de kan placeras fysiskt på noderna. Du kanske vill ha ett större antal partitioner för att kunna hantera programmets specifika beräknings-och nätverks trafik behov. Kalkyl bladet visar antalet partitioner som hanterar användar profils objekt har ökat från en till sex.

Nu, baserat på all den här informationen, visar kalkyl bladet att du kan fysiskt hämta alla data med önskade partitioner och repliker i ett kluster med 26 noder. Detta kluster skulle dock vara mycket kompakt, så du kanske vill att vissa ytterligare noder ska hantera nodfel och uppgraderingar. Kalkyl bladet visar också att fler än 57 noder ger ett extra värde eftersom du skulle ha tomma noder. Återigen kanske du vill gå över 57-noderna så att de inte hanterar nodfel och uppgraderingar. Du kan anpassa kalkyl bladet så att det matchar programmets specifika behov.   

![Kalkyl blad för kostnads beräkning][Image1]

## <a name="next-steps"></a>Nästa steg
Se [partitionering Service Fabric Services][10] om du vill veta mer om att partitionera tjänsten.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
