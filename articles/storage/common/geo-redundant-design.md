---
title: Använd GEO-redundans för att skapa program med hög tillgänglighet
titleSuffix: Azure Storage
description: Lär dig hur du använder Geo-redundant lagring för att utforma ett program med hög tillgänglighet som är tillräckligt flexibelt för att hantera avbrott.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 1a07acedadfaf3d5158ba8e494d4527301655425
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035109"
---
# <a name="use-geo-redundancy-to-design-highly-available-applications"></a>Använd GEO-redundans för att skapa program med hög tillgänglighet

En vanlig funktion i molnbaserade infrastrukturer som Azure Storage är att de tillhandahåller en hög tillgänglig och varaktig plattform för att vara värd för data och program. Utvecklare av molnbaserade program måste överväga noga hur de kan utnyttja den här plattformen för att maximera de här fördelarna för sina användare. Azure Storage erbjuder Geo-redundant lagring för att säkerställa hög tillgänglighet även i händelse av ett regionalt avbrott. Lagrings konton som kon figurer ATS för Geo-redundant replikering replikeras synkront i den primära regionen och replikeras sedan asynkront till en sekundär region som är hundratals mil bort.

Azure Storage erbjuder två alternativ för Geo-redundant replikering. Den enda skillnaden mellan dessa två alternativ är hur data replikeras i den primära regionen:

* [Geo-Zone-redundant lagring (GZRS)](storage-redundancy.md): data replikeras synkront över tre tillgänglighets zoner i Azure i den primära regionen med *zon REDUNDANT lagring (ZRS)* och replikeras asynkront till den sekundära regionen. För Läs åtkomst till data i den sekundära regionen aktiverar du Läs åtkomst geo-Zone-redundant lagring (RA-GZRS).

    Microsoft rekommenderar att du använder GZRS/RA-GZRS för scenarier som kräver maximal tillgänglighet och hållbarhet.

* [Geo-redundant lagring (GRS)](storage-redundancy.md): data replikeras synkront tre gånger i den primära regionen med *lokalt REDUNDANT lagring (LRS)* och replikeras asynkront till den sekundära regionen. För Läs åtkomst till data i den sekundära regionen aktiverar du Geo-redundant lagring med Läs behörighet (RA-GRS).

Den här artikeln visar hur du utformar ditt program för att hantera ett avbrott i den primära regionen. Om den primära regionen blir otillgänglig kan programmet anpassas för att utföra Läs åtgärder mot den sekundära regionen i stället. Kontrol lera att ditt lagrings konto har kon figurer ATS för RA-GRS eller RA-GZRS innan du börjar.

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>Program design överväganden vid läsning från den sekundära

Syftet med den här artikeln är att visa hur du utformar ett program som kommer att fortsätta att fungera (låt med en begränsad kapacitet) även vid en större katastrof i det primära data centret. Du kan utforma ditt program för att hantera tillfälliga eller tids krävande problem genom att läsa från den sekundära regionen när det finns ett problem som stör läsning från den primära regionen. När den primära regionen är tillgänglig igen kan ditt program återgå till att läsa från den primära regionen.

Tänk på följande när du utvecklar ditt program för RA-GRS eller RA-GZRS:

* Azure Storage har en skrivskyddad kopia av de data som du lagrar i din primära region i en sekundär region. Som anges ovan fastställer lagrings tjänsten platsen för den sekundära regionen.

* Den skrivskyddade kopian är till slut [konsekvent](https://en.wikipedia.org/wiki/Eventual_consistency) med data i den primära regionen.

* För blobbar, tabeller och köer kan du fråga den sekundära regionen efter ett *sista synkroniseringstid* som anger när den senaste replikeringen från den primära till den sekundära regionen ägde rum. (Detta stöds inte för Azure Files, som inte har RA-GRS-redundans just nu.)

* Du kan använda lagrings klient biblioteket för att läsa och skriva data i antingen den primära eller sekundära regionen. Du kan också omdirigera Läs begär Anden automatiskt till den sekundära regionen om en Läs förfrågan till den primära regionen har nått sin tids gräns.

* Om den primära regionen blir otillgänglig kan du initiera en redundansväxling av kontot. När du växlar över till den sekundära regionen ändras de DNS-poster som pekar på den primära regionen till att peka till den sekundära regionen. När redundansväxlingen är klar återställs Skriv åtkomsten för GRS-och RA-GRS-konton. Mer information finns i [haveri beredskap och redundans för lagrings konton](storage-disaster-recovery-guidance.md).

### <a name="using-eventually-consistent-data"></a>Använda inkonsekventa data

Den föreslagna lösningen förutsätter att det är acceptabelt att returnera potentiellt inaktuella data till det anropande programmet. Eftersom data i den sekundära regionen är konsekventa, är det möjligt att den primära regionen kan bli oåtkomlig innan en uppdatering av den sekundära regionen har repliker ATS.

Anta till exempel att kunden skickar en uppdatering, men den primära regionen Miss lyckas innan uppdateringen sprids till den sekundära regionen. När kunden ber att läsa data tillbaka, får de inaktuella data från den sekundära regionen i stället för uppdaterade data. När du designar ditt program måste du bestämma om detta är acceptabelt, och i så fall hur du ska kontakta kunden. 

Senare i den här artikeln visar vi hur du kan kontrol lera den senaste synkroniseringstid för de sekundära data för att kontrol lera om den sekundära är uppdaterad.

### <a name="handling-services-separately-or-all-together"></a>Hantera tjänster separat eller tillsammans

Även om det är osannolikt är det möjligt för en tjänst att bli otillgänglig medan de andra tjänsterna fortfarande fungerar fullt ut. Du kan hantera återförsök och skrivskyddat läge för varje tjänst separat (blobbar, köer, tabeller), eller så kan du hantera försök allmänt för alla lagrings tjänster tillsammans.

Om du till exempel använder köer och blobbar i ditt program kan du välja att ange separat kod för att hantera återförsöks fel för var och en av dessa. Om du får ett nytt försök från Blob-tjänsten, men Queue Service fortfarande fungerar, kommer bara den del av programmet som hanterar blobbar att påverkas. Om du bestämmer dig för att hantera alla försök till lagrings tjänsten allmänt och ett anrop till Blob-tjänsten returnerar ett försök bara fel, kommer begäran till både Blob-tjänsten och Queue Service att påverkas.

I slut änden beror detta på programmets komplexitet. Du kan välja att inte hantera fel efter tjänst, utan i stället omdirigera Läs begär Anden för alla lagrings tjänster till den sekundära regionen och köra programmet i skrivskyddat läge när du upptäcker ett problem med lagrings tjänsten i den primära regionen.

### <a name="other-considerations"></a>Ytterligare överväganden

Det här är andra saker som vi ska diskutera i resten av den här artikeln.

* Hantera försök att läsa läsnings begär Anden med krets brytar mönstret

* Till sist-konsekventa data och senaste synkroniseringstid

* Testning

## <a name="running-your-application-in-read-only-mode"></a>Köra ditt program i skrivskyddat läge

För att effektivt förbereda för ett avbrott i den primära regionen måste du kunna hantera både misslyckade Läs begär Anden och misslyckade uppdaterings begär Anden (med uppdatering i detta fall innebär infogningar, uppdateringar och borttagningar). Om den primära regionen Miss lyckas kan Läs begär Anden omdirigeras till den sekundära regionen. Men uppdaterings begär Anden kan inte omdirigeras till den sekundära eftersom den sekundära är skrivskyddad. Därför måste du utforma ditt program så att det körs i skrivskyddat läge.

Du kan till exempel ange en flagga som kontrol leras innan en uppdaterings förfrågan skickas till Azure Storage. När en av uppdaterings förfrågningarna kommer till kan du hoppa över den och returnera ett lämpligt svar till kunden. Du kanske vill inaktivera vissa funktioner helt och hållet tills problemet är löst och meddela användare om att dessa funktioner är tillfälligt otillgängliga.

Om du bestämmer dig för att hantera fel för varje tjänst separat måste du också hantera möjligheten att köra programmet i skrivskyddat läge efter tjänst. Du kan till exempel ha skrivskyddade flaggor för varje tjänst som kan aktive ras och inaktive ras. Sedan kan du hantera flaggan på lämpliga platser i koden.

Att kunna köra ditt program i skrivskyddat läge har en annan sido förmån – det ger dig möjlighet att säkerställa begränsade funktioner under en större program uppgradering. Du kan utlösa ditt program så att det körs i skrivskyddat läge och peka på det sekundära data centret, vilket innebär att ingen kan komma åt data i den primära regionen medan du gör uppgraderingar.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Hantera uppdateringar när de körs i skrivskyddat läge

Det finns många sätt att hantera uppdaterings begär anden när de körs i skrivskyddat läge. Vi tar inte del av det här omfattande, men det finns vanligt vis några mönster som du bör tänka på.

1. Du kan svara på din användare och meddela dem att du inte accepterar uppdateringar för tillfället. Ett kontakt hanterings system kan till exempel göra det möjligt för kunder att komma åt kontakt information men inte göra uppdateringar.

2. Du kan köa dina uppdateringar i en annan region. I det här fallet skriver du väntande uppdaterings begär anden till en kö i en annan region och har sedan ett sätt att bearbeta dessa förfrågningar när det primära data centret är online igen. I det här scenariot bör du låta kunden veta att den begärda uppdateringen står i kö för senare bearbetning.

3. Du kan skriva dina uppdateringar till ett lagrings konto i en annan region. När det primära data centret är online igen kan du koppla dessa uppdateringar till primära data, beroende på data strukturen. Om du till exempel skapar separata filer med en datum-/tidsstämpel i namnet kan du kopiera filerna tillbaka till den primära regionen. Detta fungerar för vissa arbets belastningar som loggning och iOT-data.

## <a name="handling-retries"></a>Hanterings försök

Azure Storage klient biblioteket hjälper dig att avgöra vilka fel som kan göras. Till exempel går det inte att göra ett nytt försök med 404-fel (resursen hittades inte) på grund av att det inte är troligt att ett nytt försök har gjorts. Å andra sidan kan ett 500-fel göras igen eftersom det är ett Server fel och problemet kan vara ett tillfälligt problem. Mer information finns i den [Öppna käll koden för ExponentialRetry-klassen](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) i klient biblioteket för .net-lagring. (Leta efter ShouldRetry-metoden.)

### <a name="read-requests"></a>Läs begär Anden

Läs begär Anden kan omdirigeras till sekundär lagring om det är problem med den primära lagringen. Som nämnts ovan i att [använda konsekventa data](#using-eventually-consistent-data)måste det vara acceptabelt att ditt program kan läsa inaktuella data. Om du använder lagrings klient biblioteket för att komma åt data från den sekundära kan du ange ett återförsöks beteende för en läsbegäran genom att ange ett värde för egenskapen **LocationMode** till något av följande:

* **PrimaryOnly** (standard)

* **PrimaryThenSecondary**

* **SecondaryOnly**

* **SecondaryThenPrimary**

När du ställer in **LocationMode** på **PrimaryThenSecondary**, kommer klienten automatiskt att göra en begäran till den sekundära slut punkten om den inledande läsbegäran till den primära slut punkten Miss lyckas med ett fel som kan göras om. Om felet är en tids gräns för servern måste klienten vänta tills tids gränsen upphör att gälla innan den får ett nytt försök med fel från tjänsten.

Det finns i princip två scenarier som du bör tänka på när du bestämmer dig för att svara på ett återförsöks fel:

* Detta är ett isolerat problem och efterföljande begär anden till den primära slut punkten kan inte returnera ett försök till fel. Ett exempel på var detta kan inträffa är när ett tillfälligt nätverks fel inträffar.

    I det här scenariot finns det ingen betydande prestanda försämring när **LocationMode** har angetts till **PrimaryThenSecondary** eftersom detta bara sker sällan.

* Detta är ett problem med minst en av lagrings tjänsterna i den primära regionen och alla efterföljande begär anden till tjänsten i den primära regionen kan troligt vis returnera återförsöks bara fel under en viss tids period. Ett exempel på detta är om den primära regionen är helt otillgänglig.

    I det här scenariot är det en prestanda försämring eftersom alla Läs begär Anden kommer att försöka med den primära slut punkten först, vänta tills tids gränsen har gått ut och sedan växla till den sekundära slut punkten.

I dessa scenarier bör du identifiera att det finns ett pågående problem med den primära slut punkten och skicka alla Läs begär Anden direkt till den sekundära slut punkten genom att ange egenskapen **LocationMode** till **SecondaryOnly**. För närvarande bör du även ändra programmet så att det körs i skrivskyddat läge. Den här metoden kallas [krets brytar mönstret](/azure/architecture/patterns/circuit-breaker).

### <a name="update-requests"></a>Uppdaterings begär Anden

Mönstret krets brytare kan också användas för uppdaterings begär Anden. Men det går inte att omdirigera uppdaterings begär anden till sekundär lagring, som är skrivskyddad. För dessa begär Anden bör du lämna egenskapen **LocationMode** inställd på **PrimaryOnly** (standard). För att hantera dessa fel kan du tillämpa ett mått på dessa förfrågningar – till exempel 10 fel på en rad – och när ditt tröskelvärde är uppfyllt, växla programmet till skrivskyddat läge. Du kan använda samma metoder för att återgå till uppdaterings läge som beskrivs nedan i nästa avsnitt om krets brytar mönstret.

## <a name="circuit-breaker-pattern"></a>Strömbrytarmönstret

Genom att använda krets brytar mönstret i ditt program kan du förhindra att det försöker igen en åtgärd som troligen kommer att återställas upprepade gånger. Det gör att programmet kan fortsätta att köras i stället för att ta tid medan åtgärden provas exponentiellt. Den identifierar även när felet har åtgärd ATS, då programmet kan försöka utföra åtgärden igen.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Så här implementerar du krets brytar mönstret

För att identifiera att det finns ett pågående problem med en primär slut punkt kan du övervaka hur ofta klienten påträffar nya fel. Eftersom varje fall skiljer sig, måste du bestämma den tröskel som du vill använda för beslutet att växla till den sekundära slut punkten och köra programmet i skrivskyddat läge. Du kan till exempel välja att utföra växeln om det finns 10 problem i en rad utan att några lyckade. Ett annat exempel är att växla om 90% av förfrågningarna i en 2-minuters period har misslyckats.

I det första scenariot kan du bara se till att det finns ett antal misslyckade försök, och om det uppstår ett problem innan det högsta värdet uppnås, så sätt tillbaka antalet till noll. Ett sätt att implementera det i det andra scenariot är att använda MemoryCache-objektet (i .NET). För varje begäran lägger du till en CacheItem i cacheminnet, ställer in värdet på lyckades (1) eller misslyckades (0) och anger förfallo tiden till 2 minuter från nu (eller vad tids begränsningen är). När en posts förfallo tid uppnås tas posten bort automatiskt. Då får du en rullande 2-minuters period. Varje gång du gör en begäran till lagrings tjänsten använder du först en LINQ-fråga över MemoryCache-objektet för att beräkna procent andelen genom att summera värdena och dividera med antalet. När procent andelen sjunker under en viss tröskel (till exempel 10%), anger du egenskapen **LocationMode** för Läs begär anden till **SecondaryOnly** och växlar programmet till skrivskyddat läge innan du fortsätter.

Tröskelvärdet för fel som används för att fastställa när växeln kan skilja sig från tjänst till tjänst i ditt program, så du bör överväga att konfigurera parametrar. Det är också där du bestämmer dig för att hantera återförsöks fel från varje tjänst separat eller som en som beskrivs ovan.

Ett annat övervägande är hur du hanterar flera instanser av ett program, och vad du ska göra när du identifierar nya försök i varje instans. Du kan till exempel ha 20 virtuella datorer som körs med samma program inläst. Hanterar du varje instans separat? Om en instans startar med problem vill du begränsa svaret till bara en instans, eller vill du försöka att alla instanser ska reagera på samma sätt när en instans har ett problem? Att hantera instanserna separat är mycket enklare än att försöka samordna svaret på dem, men hur du gör detta beror på programmets arkitektur.

### <a name="options-for-monitoring-the-error-frequency"></a>Alternativ för att övervaka fel frekvensen

Du har tre huvud alternativ för att övervaka frekvensen för återförsök i den primära regionen för att bestämma när du ska växla över till den sekundära regionen och ändra programmet så att det körs i skrivskyddat läge.

* Lägg till en hanterare för händelsen [**försök igen**](/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) på [**OperationContext**](/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) -objektet som du skickar till dina lagrings begär anden – det här är metoden som visas i den här artikeln och används i det medföljande exemplet. De här händelserna utlöses när klienten gör en begäran så att du kan spåra hur ofta klienten påträffar nya fel på en primär slut punkt.

    # <a name="net-v12"></a>[.NET-V12](#tab/current)

    Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

    # <a name="net-v11"></a>[.NET-v11](#tab/legacy)

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```
    ---

* I metoden [**utvärdera**](/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) i en anpassad princip för återförsök kan du köra anpassad kod när ett nytt försök görs. Förutom att registrera när ett nytt försök görs, ger du också möjlighet att ändra beteendet för återförsök.

    # <a name="net-v12"></a>[.NET-V12](#tab/current)

    Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

    # <a name="net-v11"></a>[.NET-v11](#tab/legacy)

    ```csharp
    public RetryInfo Evaluate(RetryContext retryContext,
    OperationContext operationContext)
    {
        var statusCode = retryContext.LastRequestResult.HttpStatusCode;
        if (retryContext.CurrentRetryCount >= this.maximumAttempts
            || ((statusCode >= 300 && statusCode < 500 && statusCode != 408)
            || statusCode == 501 // Not Implemented
            || statusCode == 505 // Version Not Supported
            ))
        {
            // Do not retry
            return null;
        }

        // Monitor retries in the primary location
        ...

        // Determine RetryInterval and TargetLocation
        RetryInfo info =
            CreateRetryInfo(retryContext.CurrentRetryCount);

        return info;
    }
    ```
    ---

* Den tredje metoden är att implementera en anpassad övervaknings komponent i ditt program som kontinuerligt pingar din primära lagrings slut punkt med Läs begär Anden (till exempel läsning av en liten BLOB) för att fastställa dess hälsa. Detta skulle ta upp några resurser, men inte en betydande mängd. När ett problem upptäcks som når ditt tröskelvärde, utför du sedan växeln till **SecondaryOnly** och skrivskyddat läge.

Vid något tillfälle vill du växla tillbaka till med den primära slut punkten och tillåta uppdateringar. Om du använder någon av de första två metoderna som anges ovan, kan du bara växla tillbaka till den primära slut punkten och aktivera uppdaterings läget när en godtycklig vald tids period eller antal åtgärder har utförts. Du kan sedan låta den gå igenom omprövnings logiken igen. Om problemet har åtgärd ATS fortsätter det att använda den primära slut punkten och Tillåt uppdateringar. Om det fortfarande finns ett problem kommer det att gå tillbaka till den sekundära slut punkten och skrivskyddat läge efter att de villkor som du har angett har misslyckats.

För det tredje scenariot kan du aktivera växla tillbaka till **PrimaryOnly** och fortsätta att tillåta uppdateringar när pinga den primära lagrings slut punkten.

## <a name="handling-eventually-consistent-data"></a>Hantera inkonsekventa data

Geo-redundant lagring fungerar genom att replikera transaktioner från den primära till den sekundära regionen. Den här replikeringsprincipen garanterar att data i den sekundära regionen är *konsekventa*. Det innebär att alla transaktioner i den primära regionen slutligen kommer att visas i den sekundära regionen, men att det kan finnas en fördröjning innan de visas, och att det inte finns någon garanti för att transaktionerna tas emot i den sekundära regionen i samma ordning som de ursprungligen användes i den primära regionen. Om dina transaktioner kommer in i den sekundära regionen i rätt ordning *kan* du överväga att dina data i den sekundära regionen ska vara i ett inkonsekvent tillstånd tills tjänsten har fångats upp.

I följande tabell visas ett exempel på vad som kan hända när du uppdaterar information om en medarbetare så att de blir medlem i rollen *Administratörer* . För det här exemplet kräver detta att du uppdaterar den **anställdas** entitet och uppdaterar en **Administratörs roll** -entitet med ett antal av det totala antalet administratörer. Observera hur uppdateringarna tillämpas i rätt ordning i den sekundära regionen.

| **Tid** | **Transaktion**                                            | **Replikering**                       | **Tid för senaste synkronisering** | **Resultat** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transaktion A: <br> Infoga medarbetare <br> entitet i primär |                                   |                    | Transaktion A infogad till primär,<br> ännu inte repliker ATS. |
| T1       |                                                            | Transaktion A <br> replikeras till<br> alternativ | T1 | Transaktion A replikerad till sekundär. <br>Tid för senaste synkronisering uppdaterades.    |
| T2       | Transaktion B:<br>Uppdatera<br> anställd entitet<br> i primär  |                                | T1                 | Transaktion B skriven till primär,<br> ännu inte repliker ATS.  |
| T3       | Transaktion C:<br> Uppdatera <br>administratör<br>roll entitet i<br>huvud |                    | T1                 | Transaktion C skriven till primär,<br> ännu inte repliker ATS.  |
| *T4*     |                                                       | Transaktion C <br>replikeras till<br> alternativ | T1         | Transaktion C replikerad till sekundär.<br>LastSyncTime har inte uppdaterats eftersom <br>transaktion B har ännu inte repliker ATS.|
| *T*     | Läs entiteter <br>från sekundär                           |                                  | T1                 | Du får det inaktuella värdet för anställda <br> entitet eftersom transaktion B inte har <br> har repliker ATS än. Du får det nya värdet för<br> administratörs roll entitet eftersom C har<br> replikeras. Tiden för senaste synkroniseringen är fortfarande inte<br> uppdaterats eftersom transaktion B<br> har inte repliker ATS. Du kan se att<br>administratörs rollens entitet är inkonsekvent <br>eftersom entitetens datum/tid är efter <br>Tid för senaste synkronisering. |
| *T6*     |                                                      | Transaktion B<br> replikeras till<br> alternativ | T6                 | *T6* – alla transaktioner via C har <br>replikerad, tid för senaste synkronisering<br> har uppdaterats. |

I det här exemplet antar du att klienten växlar till att läsa från den sekundära regionen på T5. Det går att läsa entiteten **Administratörs roll** just nu, men entiteten innehåller ett värde för antalet administratörer som inte är konsekvent med antalet **anställdas** enheter som är markerade som administratörer i den sekundära regionen just nu. Klienten kan enkelt visa det här värdet, med risken att det är inkonsekvent information. Alternativt kan klienten försöka fastställa att **Administratörs rollen** är i ett potentiellt inkonsekvent tillstånd eftersom uppdateringarna har inträffat i rätt ordning och sedan informerar användaren om detta faktum.

För att identifiera att den har potentiellt inkonsekventa data kan klienten använda värdet för den *senaste synkroniseringstid* som du kan hämta när som helst genom att skicka en fråga till en lagrings tjänst. Detta anger den tid då data i den sekundära regionen senast var konsekventa och när tjänsten hade tillämpat alla transaktioner innan den tidpunkten. I exemplet ovan har den senaste synkroniseringstid angetts till *T1* när tjänsten infogar entiteten **anställda** i den sekundära regionen. Den finns kvar i *T1* tills tjänsten uppdaterar den **anställdas** entitet i den sekundära regionen när den är inställd på *T6*. Om klienten hämtar den senaste synkroniseringen när den läser entiteten *T5*, kan den jämföra den med tidsstämpeln för entiteten. Om tidsstämpeln i entiteten är senare än den senaste synkroniseringen, är entiteten i ett potentiellt inkonsekvent tillstånd och du kan vidta det som är lämplig åtgärd för ditt program. Om du använder det här fältet måste du känna till när den senaste uppdateringen till den primära uppdateringen slutfördes.

Information om hur du kontrollerar tidpunkten för senaste synkronisering finns i [kontrol lera den senaste synkroniseringstid-egenskapen för ett lagrings konto](last-sync-time-get.md).

## <a name="testing"></a>Testning

Det är viktigt att testa att programmet fungerar som förväntat när det påträffar nya försök att köra fel. Du måste till exempel testa att programmet växlar till den sekundära och till skrivskyddat läge när ett problem upptäcks, och växlar tillbaka när den primära regionen blir tillgänglig igen. Om du vill göra det behöver du ett sätt att simulera nya försök och kontrol lera hur ofta de inträffar.

Du kan använda [Fiddler](https://www.telerik.com/fiddler) för att avlyssna och ändra http-svar i ett skript. Det här skriptet kan identifiera svar som kommer från den primära slut punkten och ändra HTTP-statuskoden till en som lagrings klient biblioteket identifierar som ett försök till fel. Det här kodfragmentet visar ett enkelt exempel på ett Fiddler-skript som fångar upp svar på läsnings begär Anden mot **employeedata** -tabellen för att returnera en 502-status:


# <a name="java-v12"></a>[Java-V12](#tab/current)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v11"></a>[Java-v11](#tab/legacy)

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Du kan utöka det här exemplet för att fånga upp ett större antal förfrågningar och bara ändra **responseCode** på vissa av dem för att bättre simulera ett verkligt scenario. Mer information om hur du anpassar Fiddler-skript finns i [ändra en begäran eller ett svar](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) i Fiddler-dokumentationen.

Om du har gjort tröskelvärdena för att växla ditt program till skrivskyddat läge, blir det enklare att testa beteendet med icke-produktions transaktions volymer.

## <a name="next-steps"></a>Nästa steg

Ett fullständigt exempel som visar hur du ändrar fram och tillbaka mellan de primära och sekundära slut punkterna finns i Azure- [exempel – använda krets brytar mönstret med RA-GRS-lagring](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
