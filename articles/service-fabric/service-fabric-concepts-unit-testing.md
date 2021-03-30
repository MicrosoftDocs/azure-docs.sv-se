---
title: Enhets testning tillstånds känsliga tjänster i Azure Service Fabric
description: Lär dig mer om begreppen och metoderna för enhets testning Service Fabric tillstånds känsliga tjänster.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 12e8a47d9685dee12594f4e2afaa848d9688d185
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75433913"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Enhets testning tillstånds känsliga tjänster i Service Fabric

Den här artikeln beskriver begreppen och metoderna för enhets testning Service Fabric tillstånds känsliga tjänster. Enhets testning inom Service Fabric förtjänar egna överväganden på grund av det faktum att program koden körs aktivt under flera olika kontexter. I den här artikeln beskrivs de metoder som används för att säkerställa att program koden omfattas av de olika kontexterna som den kan köra.

## <a name="unit-testing-and-mocking"></a>Enhets testning och skisser
Enhets testning i samband med den här artikeln är automatiserad testning som kan utföras inom ramen för en testlöpare, till exempel MSTest eller NUnit. Enhets testerna i den här artikeln utför inte åtgärder mot en fjär resurs, till exempel en databas eller RESTFul-API. Dessa fjär resurser bör vara fördelade. Att modellera i samband med den här artikeln kommer att falska, registrera och kontrol lera retur värden för fjär resurser.

### <a name="service-fabric-considerations"></a>Service Fabric överväganden
Enhets testning av en Service Fabric tillstånds känslig tjänst har flera överväganden. För det första körs Service koden på flera noder, men under olika roller. Enhets tester bör utvärdera koden under varje roll för att uppnå fullständig täckning. De olika rollerna skulle vara primära, aktiva sekundär, inaktiv sekundär och okänd. Ingen av rollerna behöver vanligt vis någon speciell täckning eftersom Service Fabric anser att den här rollen inte är void-eller null-tjänst. För det andra kommer varje nod att ändra sin roll vid en viss tidpunkt. För att uppnå fullständig täckning ska kod körningens sökväg testas med roll ändringar som inträffar.

## <a name="why-unit-test-stateful-services"></a>Varför enhets test tillstånds känsliga tjänster? 
Enhets testning tillstånds känsliga tjänster kan hjälpa dig att få en del vanliga misstag som inte behöver fångas upp av konventionell program-eller domänbaserad enhets testning. Om till exempel den tillstånds känsliga tjänsten har ett minnes intern tillstånd, kan den här typen av testning verifiera att det här InMemory-läget hålls synkroniserat över varje replik. Den här typen av testning kan också kontrol lera att en tillstånds känslig tjänst svarar på avbrutna token som skickas i Service Fabric Orchestration på lämpligt sätt. När annulleringar utlöses bör tjänsten stoppa alla tids krävande och/eller asynkrona åtgärder.  

## <a name="common-practices"></a>Vanliga metoder

I följande avsnitt beskrivs de vanligaste metoderna för enhets testning av en tillstånds känslig tjänst. Den aviserar även vad ett modell lager måste ha för att anpassa sig till Service Fabric dirigering och tillstånds hantering. [ServiceFabric. skisser](https://www.nuget.org/packages/ServiceFabric.Mocks/) från och med 3.3.0 eller senare är ett bibliotek som tillhandahåller de skiss funktioner som rekommenderas och följer de metoder som beskrivs nedan.

### <a name="arrangement"></a>Märke

#### <a name="use-multiple-service-instances"></a>Använd flera tjänst instanser
Enhets test bör köra flera instanser av en tillstånds känslig tjänst. Detta simulerar vad som faktiskt händer i klustret där Service Fabric etablerar flera repliker som kör tjänsten på olika noder. Var och en av instanserna körs under en annan kontext. När du kör testet bör varje instans definieras med den roll konfiguration som förväntas i klustret. Om tjänsten till exempel förväntas ha mål replik storleken 3, etablerar Service Fabric tre repliker på olika noder. En som är primär och de andra två som är aktiva som sekundär.

I de flesta fall kan tjänstens körnings Sök väg variera något för var och en av dessa roller. Om tjänsten t. ex. inte accepterar begär Anden från en aktiv sekundär, kan tjänsten ha en kontroll för det här ärendet för att återställa ett informativt undantag som anger att en begäran har gjorts på en sekundär. Om du har flera instanser kan den här situationen testas.

Om du dessutom har flera instanser kan testerna växla rollerna för var och en av dessa instanser för att kontrol lera att svaren är konsekventa trots att rollen ändras.

#### <a name="mock-the-state-manager"></a>Modellera tillstånds chefen
Tillstånds hanteraren bör behandlas som en fjär resurs och därför modelleras. När du modellerar tillstånds hanteraren måste det finnas en underliggande minnes lagring för att spåra vad som sparas i tillstånds hanteraren så att det kan läsas och verifieras. Ett enkelt sätt att åstadkomma detta är att skapa blå instanser av var och en av typerna av pålitliga samlingar. I dessa modeller använder du en datatyp som stämmer med de åtgärder som utförs mot den samlingen. Följande är några föreslagna data typer för varje tillförlitlig samling

- IReliableDictionary<TKey, TValue>-> system. Collections. samtidig. ConcurrentDictionary<TKey, TValue>
- IReliableQueue \<T> -> system. Collections. Generic. Queue\<T>
- IReliableConcurrentQueue \<T> -> system. Collections. samtidig. ConcurrentQueue\<T>

#### <a name="many-state-manager-instances-single-storage"></a>Många tillstånds hanterarens instanser, enkel lagring
Som nämnts tidigare bör tillstånds hanteraren och pålitliga samlingar behandlas som en fjär resurs. Dessa resurser bör därför vara och de kommer att bli blå inom enhets testen. Men när du kör flera instanser av en tillstånds känslig tjänst är det en utmaning att hålla alla modellerade tillstånds hanterare synkroniserade över olika tillstånds känsliga tjänst instanser. När den tillstånds känsliga tjänsten körs på klustret tar Service Fabric hand om att hålla varje sekundär repliks tillstånds hanterare konsekvent med den primära repliken. Därför bör testerna beter sig på samma sätt så att de kan simulera roll ändringar.

Ett enkelt sätt att utföra den här synkroniseringen kan vara att använda ett singleton-mönster för det underliggande objektet som lagrar data som skrivits till varje tillförlitlig samling. Till exempel om en tillstånds känslig tjänst använder en `IReliableDictionary<string, string>` . Den modellerande tillstånds hanteraren ska returnera en skiss av `IReliableDictionary<string, string>` . Den här skissen kan använda en `ConcurrentDictionary<string, string>` för att hålla reda på nyckel/värde-par som skrivits. `ConcurrentDictionary<string, string>`Ska vara en singleton som används av alla instanser av tillstånds hanterare som skickas till tjänsten.

#### <a name="keep-track-of-cancellation-tokens"></a>Håll koll på inställda token
Avbrutna token är en viktig men ofta överblickad aspekt av tillstånds känsliga tjänster. När Service Fabric startar en primär replik för en tillstånds känslig tjänst tillhandahålls en avbrotts-token. Denna uppsägnings-token är avsedd att signalera till tjänsten när den tas bort eller nedgraderas till en annan roll. Den tillstånds känsliga tjänsten bör stoppa eventuella tids krävande eller asynkrona åtgärder så att Service Fabric kan slutföra arbets flödet för roll ändringar.

När du kör enhets testerna ska alla avbrutna token som anges för RunAsync, ChangeRoleAsync, openAsync och CloseAsync hållas kvar under test körningen. Om du använder dessa token kan testet simulera en tjänst avstängning eller degradering och kontrol lera att tjänsten svarar på rätt sätt.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Testa slut punkt till slut punkt med modellerade fjär resurser
Enhets test bör köras så mycket som möjligt av program koden som kan ändra statusen för den tillstånds känsliga tjänsten som möjligt. Vi rekommenderar att testerna är mer heltäckande. De enda skisser som finns är att registrera, simulera och/eller verifiera Fjärran slutna resurs interaktioner. Detta inkluderar interaktioner med tillstånds hanteraren och pålitliga samlingar. Följande kodfragment är ett exempel på Gherkin för ett test som visar testning från slut punkt till slut punkt:

```
    Given stateful service named "fabric:/MyApp/MyService" is created
    And a new replica is created as "Primary" with id "111"
    And a new replica is created as "IdleSecondary" with id "222"
    And a new replica is created as "IdleSecondary" with id "333"
    And all idle secondary replicas are promoted to active secondary
    When a request is made to add the an employee "John Smith"
    And the active secondary replica "222" is promoted to primary
    And a request is made to get all employees
    Then the request should should return the "John Smith" employee
```

Det här testet kontrollerar att data som fångas på en replik är tillgängliga för en sekundär replik när de befordras till primär. Om vi antar att en tillförlitlig samling är lagrings platsen för de anställdas data, kan ett potentiellt fel som kan fångas upp med det här testet vara om program koden inte kördes `CommitAsync` på transaktionen för att spara den nya medarbetaren. I så fall skulle den andra begäran att hämta medarbetare inte returnera medarbetare som lagts till av den första begäran.

### <a name="acting"></a>Reagera
#### <a name="mimic-service-fabric-replica-orchestration"></a>Imitera Service Fabric replik dirigering
Vid hantering av flera tjänst instanser bör testerna initiera och riva av dessa tjänster på samma sätt som Service Fabric-dirigering. När en tjänst till exempel skapas på en ny primär replik, kommer Service Fabric att anropa CreateServiceReplicaListener, openAsync, ChangeRoleAsync och RunAsync. Livs cykel händelser dokumenteras i följande artiklar:

- [Start av tillstånds känslig tjänst](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Avstängning av tillstånds känslig tjänst](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Primär växlingar för tillstånds känslig tjänst](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Kör ändringar av replik rollen
Enhets testen bör ändra rollerna för tjänst instanserna på samma sätt som Service Fabric Orchestration. Roll tillstånds datorn dokumenteras i följande artikel:

[Dator för replik roll tillstånd](service-fabric-concepts-replica-lifecycle.md#replica-role)

Att simulera roll ändringar är en av de mer kritiska aspekterna av testning och kan få problem om replikens status inte stämmer överens med varandra. Inkonsekvent replik status kan uppstå på grund av lagrings tillstånd i minnet på statiska eller på klass nivå av instans-variabler. Exempel på detta kan vara avbrutna token, uppräkningar och konfigurations objekt/värden. Detta säkerställer också att tjänsten respekterar de token för uppsägning som anges under RunAsync så att roll ändringen kan utföras. Att simulera roll ändringar kan också få problem som kan uppstå om kod inte skrivs för att tillåta ett anrop av RunAsync flera gånger.

#### <a name="cancel-cancellation-tokens"></a>Avbryt token för annullering
Det bör finnas enhets test där token för annullering som tillhandahölls till RunAsync avbryts. Detta gör att testet kan verifiera att tjänsten stängs av på ett smidigt sätt. Under den här avstängningen ska alla tids krävande eller asynkrona åtgärder stoppas. Exempel på en tids krävande process som kan finnas på en tjänst är en som lyssnar efter meddelanden i en tillförlitlig kö. Detta kan förekomma direkt i RunAsync eller en bakgrunds tråd. Implementeringen bör innehålla logik för att avsluta åtgärden om denna token för annullering avbryts.

Om tillstånds känsliga tjänster använder ett cacheminne eller minnes intern status som bara ska finnas på den primära, ska den tas bort för tillfället. Detta görs för att säkerställa att det här läget är konsekvent om noden blir primär igen senare. När du avbryter testet kan testet verifiera att det här läget har tagits bort korrekt.

#### <a name="execute-requests-against-multiple-replicas"></a>Köra förfrågningar mot flera repliker
Assert-tester bör utföra samma begäran mot olika repliker. När de kombineras med roll ändringar kan konsekvens problem uppstå. Ett exempel test kan utföra följande steg:
1. Kör en Skriv förfrågan mot den aktuella primära
2. Kör en Read-begäran som returnerar de data som skrevs i steg 1 mot den aktuella primära
3. Befordra en sekundär till primär. Detta bör också nedgradera den aktuella primära till den sekundära
4. Kör samma läsbegäran från steg 2 mot den nya sekundära.

I det sista steget kan testet kontrol lera att de data som returneras är konsekventa. Ett potentiellt problem som detta kan medföra är att data som returneras av tjänsten kan finnas i minnet men som i slut ändas av en tillförlitlig samling. Att InMemory-data kanske inte synkroniseras korrekt med vad som finns i den tillförlitliga samlingen.

Minnes intern data används vanligt vis för att skapa sekundära index eller agg regeringar av data som finns i en tillförlitlig samling.

### <a name="asserting"></a>Garanterar
#### <a name="ensure-responses-match-across-replicas"></a>Se till att svaren matchar alla repliker
Enhets test bör kontrol lera att ett svar för en specifik begäran är konsekvent över flera repliker efter över gången till primär. Detta kan medföra potentiella problem där data som anges i svaret inte backas upp av en tillförlitlig samling eller hålls i minnet utan en mekanism för att synkronisera data mellan repliker. På så sätt kan du se till att tjänsten skickar tillbaka konsekventa svar när Service Fabric balanseringar eller växlar över till en ny primär replik.

#### <a name="verify-service-respects-cancellation"></a>Kontrol lera att tjänsten respekterar uppsägning
Långvariga eller asynkrona processer som ska avslutas när en token för annullering avbryts bör verifieras att de faktiskt avbröts efter annullering. Detta säkerställer att trots att replikerna byter roll, att processer som inte är avsedda att fortsätta att köras på icke-primära repliker stoppas innan över gången har slutförts. Detta kan också få problem om en sådan process blockerar en roll ändrings-eller avslutnings förfrågan från Service Fabric att slutföras.

#### <a name="verify-which-replicas-should-serve-requests"></a>Verifiera vilka repliker som ska betjäna begär Anden
Testerna bör försäkrar det förväntade beteendet om en begäran dirigeras till en icke-primär replik. Service Fabric ger möjlighet att använda sekundära repliker för att betjäna begär Anden. Skrivningar till pålitliga samlingar kan dock bara ske från den primära repliken. Om ditt program endast avser primära repliker för att hantera begär Anden eller, kan endast en delmängd av förfrågningar hanteras av en sekundär, och testerna bör påverka det förväntade beteendet för både positiva och negativa fall. Det negativa fallet dirigeras till en replik som inte ska hantera begäran och det positiva är tvärtom.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du bevarar [test tillstånds känsliga tjänster](service-fabric-how-to-unit-test-stateful-services.md).
