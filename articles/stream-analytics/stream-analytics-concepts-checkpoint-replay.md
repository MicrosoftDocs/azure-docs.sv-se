---
title: Koncept för kontroll punkter och omuppspelning av återställnings begrepp i Azure Stream Analytics
description: Den här artikeln beskriver hur du återställnings koncepten för punkt och repetition i Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: df31f8538bb9eabeca37fe4c52c4443fd447e415
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015326"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Kontroll punkts-och repetitions begrepp i Azure Stream Analytics jobb
I den här artikeln beskrivs den interna kontroll punkten och omuppspelnings koncepten i Azure Stream Analytics och hur de påverkar jobb återställningen. Varje gången ett Stream Analytics jobb körs bevaras tillståndsinformation internt. Statusinformation sparas i en kontroll punkt med jämna mellanrum. I vissa fall används kontroll punkts informationen för jobb återställning om ett jobb fel eller en uppgradering sker. I andra fall kan kontroll punkten inte användas för återställning och det krävs en omuppspelning.

## <a name="stateful-query-logic-in-temporal-elements"></a>Tillstånds känslig fråge logik i temporala element
En av de unika funktionerna för Azure Stream Analytics jobb är att utföra tillstånds känslig bearbetning, till exempel fönster mängd, temporala kopplingar och temporala analys funktioner. Var och en av dessa operatörer behåller statusinformation när jobbet körs. Maximal fönster storlek för de här fråge elementen är sju dagar. 

Det temporala fönstrets koncept visas i flera Stream Analytics frågedata:
1. Fönster mängder (Gruppera efter rullande, hoppande och glidande fönster)

2. Temporala kopplingar (förena med DATEDIFF)

3. Temporala analys funktioner (ISFIRST, LAST och fördröjning med gräns längd)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Jobb återställning från nodfel, inklusive operativ system uppgradering
Varje gången ett Stream Analytics jobb körs, skalas det internt för att fungera på flera arbetsnoder. Varje arbetsnodens tillstånd har en kontroll punkt med några minuters mellanrum, vilket hjälper systemet att återställa om ett fel uppstår.

Ibland kan en arbets nod Miss lyckas, eller så kan en uppgradering av operativ systemet ske för den arbetsnoden. För att återställa automatiskt Stream Analytics skaffar en ny felfria nod och tillståndet för den tidigare arbetsnoden återställs från den senaste tillgängliga kontroll punkten. För att återuppta arbetet krävs en liten del omuppspelning för att återställa statusen från den tidpunkt då kontroll punkten tas. Normalt är återställnings luckan bara några minuter. När tillräckligt många enheter för strömning har valts för jobbet bör uppspelningen slutföras snabbt. 

I en helt parallell fråga, hur lång tid det tar att hämta efter att ett arbetsnods-haveri är i proportion till:

[frekvensen för indata-händelser] x [längd på mellanrum]/[antal bearbetnings partitioner]

Om du har haft betydande bearbetnings fördröjning på grund av nodfel och uppgradering av operativ system, kan du överväga att göra frågan helt parallell och skala jobbet för att allokera fler enheter för strömning. Mer information finns i [skala ett Azure Stream Analytics jobb för att öka data flödet](stream-analytics-scale-jobs.md).

Den aktuella Stream Analytics visar ingen rapport när den här typen av återställnings process sker.

## <a name="job-recovery-from-a-service-upgrade"></a>Jobb återställning från en tjänst uppgradering 
Microsoft uppgraderar ibland binärfilerna som kör Stream Analytics jobb i Azure-tjänsten. Vid dessa tillfällen uppgraderas användarens jobb som körs till en nyare version och jobbet startas om automatiskt. 

För närvarande bevaras inte återställnings kontroll punkts formatet mellan uppgraderingar. Därför måste tillstånd för strömnings frågan återställas helt med hjälp av uppspelnings teknik. För att tillåta att Stream Analytics jobb spelar upp exakt samma indata från tidigare är det viktigt att ange bevarande principen för källdata till minst fönster storlekarna i frågan. Om du inte gör det kan det leda till felaktiga eller partiella resultat under uppgraderingen av tjänsten, eftersom källdata kanske inte finns tillräckligt långt för att inkludera hela fönster storleken.

I allmänhet är mängden repetition som krävs proportionell till fönstrets storlek multiplicerat med den genomsnittliga händelse frekvensen. Till exempel, för ett jobb med en intakt på 1000 händelser per sekund, anses en fönster storlek som är större än en timme ha en stor uppspelnings storlek. Upp till en timme med data kan behöva bearbetas igen för att initiera tillstånd så att den kan producera fullständiga och korrekta resultat, vilket kan orsaka fördröjd utdata (inga utdata) under en längre period. Frågor utan Windows eller andra temporala operatörer, som `JOIN` eller `LAG` , har noll repetition.

## <a name="estimate-replay-catch-up-time"></a>Omfångs fångst tid för repetition
Om du vill beräkna fördröjnings tiden på grund av en tjänst uppgradering kan du följa den här metoden:

1. Läs in indata-Händelsehubben med tillräckligt med data för att ta fram största fönster storlek i frågan, med en förväntad händelse frekvens. Händelsens tidsstämpel bör ligga nära tiden för vägg klockan under den tids perioden, precis som om det är en Live-inmatnings flöde. Om du till exempel har ett fönster med tre dagar i din fråga, skickar du händelser till Händelsehubben i tre dagar och fortsätter att skicka händelser. 

2. Starta jobbet med **nu** som start tid. 

3. Mät tiden mellan start tiden och när den första utdatan genereras. Tiden är en grov fördröjning i hur lång tid jobbet skulle ådra sig under en tjänst uppgradering.

4. Om fördröjningen är för lång, försök att partitionera ditt jobb och öka antalet SUs, så att belastningen sprids ut till fler noder. Du kan också överväga att minska fönster storlekarna i frågan och utföra ytterligare agg regering eller annan tillstånds känslig bearbetning av de utdata som genereras av Stream Analytics jobb i den underordnade sinken (till exempel med Azure SQL Database).

Överväg att köra dubbla jobb i kopplade Azure-regioner för allmän tjänst stabilitet under uppgraderingen av verksamhets kritiska jobb. Mer information finns i [garanti Stream Analytics jobb tillförlitlighet under tjänst uppdateringar](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Jobb återställning från en användare som initierats stoppa och starta
Om du vill redigera frågesyntaxen i ett strömmande jobb eller justera indata och utdata måste jobbet stoppas för att göra ändringarna och uppgradera jobb designen. I sådana fall, när en användare stoppar direkt uppspelnings jobbet och startar det igen, ser återställnings scenariot ut på samma sätt som tjänst uppgraderingen. 

Det går inte att använda kontroll punkts data för en användare som initierat jobb. Om du vill beräkna fördröjningen av utdata under en sådan omstart använder du samma procedur som beskrivs i föregående avsnitt och tillämpar liknande åtgärder om fördröjningen är för lång.

## <a name="next-steps"></a>Nästa steg
Mer information om pålitlighet och skalbarhet finns i följande artiklar:
- [Självstudie: Konfigurera aviseringar för Azure Stream Analytics jobb](stream-analytics-set-up-alerts.md)
- [Skala ett Azure Stream Analytics jobb för att öka data flödet](stream-analytics-scale-jobs.md)
- [Garantera Stream Analytics jobbets tillförlitlighet under tjänst uppdateringar](stream-analytics-job-reliability.md)
