---
title: Design mönster för Azure Storage-tabell | Microsoft Docs
description: Granska design mönster som är lämpliga för användning med Table service lösningar i Azure. Åtgärda problem och kompromisser som beskrivs i andra artiklar.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: tamram
ms.subservice: tables
ms.custom: devx-track-csharp
ms.openlocfilehash: 2eb109078728b8a9070b3991733450c1da790d9e
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879603"
---
# <a name="table-design-patterns"></a>Mönster för tabelldesign
I den här artikeln beskrivs några mönster som lämpar sig för användning med Table service lösningar. Dessutom får du se hur du praktiskt taget kan åtgärda några av de problem och kompromisser som beskrivs i andra tabell lagrings design artiklar. Följande diagram sammanfattar relationerna mellan olika mönster:  

![så här söker du efter relaterade data](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


Mönster kartan ovan markerar några relationer mellan mönster (blå) och anti-mönster (orange) som dokumenteras i den här guiden. Det finns många andra mönster som är värda att tänka på. Ett av de viktigaste scenarierna för tabell tjänst är till exempel att använda det [materialiserade visnings mönstret](/previous-versions/msp-n-p/dn589782(v=pandp.10)) från [CQRS-mönstret (Command Query ansvars fördelning)](/previous-versions/msp-n-p/jj554200(v=pandp.10)) .  

## <a name="intra-partition-secondary-index-pattern"></a>Sekundärt index mönster för sekundär partition
Lagra flera kopior av varje entitet med olika **RowKey** -värden (i samma partition) för att möjliggöra snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika **RowKey** -värden. Uppdateringar mellan kopior kan förvaras konsekvent med EGTs.  

### <a name="context-and-problem"></a>Kontext och problem
Table service indexerar automatiskt entiteter med hjälp av värdena **PartitionKey** och **RowKey** . Detta gör att ett klient program kan hämta en entitet effektivt med dessa värden. Om du till exempel använder tabell strukturen som visas nedan kan ett klient program använda en punkt fråga för att hämta en enskild anställd entitet med hjälp av avdelnings namnet och anställnings-ID: t ( **PartitionKey** och **RowKey** ). En-klient kan också hämta entiteter sorterade efter anställnings-ID inom varje avdelning.

![Image06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Om du också vill kunna hitta en anställd entitet baserat på värdet för en annan egenskap, till exempel e-postadress, måste du använda en mindre effektiv partitions ökning för att hitta en matchning. Detta beror på att tabell tjänsten inte tillhandahåller sekundära index. Det finns dessutom inget alternativ för att begära en lista över anställda sorterad i en annan ordning än **RowKey** ordning.  

### <a name="solution"></a>Lösning
För att undvika avsaknad av sekundära index kan du lagra flera kopior av varje entitet med varje kopia med ett annat **RowKey** -värde. Om du lagrar en entitet med de strukturer som visas nedan kan du effektivt hämta personal enheter baserat på e-postadress eller medarbetar-ID. Prefixvärde för **RowKey**, "empid_" och "email_" gör att du kan fråga efter en enskild anställd eller ett antal anställda genom att använda ett intervall med e-postadresser eller anställnings-ID.  

![Personal enheter](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Följande två filter villkor (en sökning efter anställnings-ID och en sökning efter e-postadress) anger båda punkt frågor:  

* $filter = (PartitionKey EQ Sales) och (RowKey EQ empid_000223)  
* $filter = (PartitionKey EQ Sales) och (RowKey EQ email_jonesj@contoso.com )  

Om du frågar efter ett antal anställdas entiteter kan du ange ett intervall som sorterats i anställnings-ID-ordning, eller ett intervall som sorterats i e-postadressen genom att fråga efter entiteter med lämpligt prefix i **RowKey**.  

* Om du vill hitta alla anställda på försäljnings avdelningen med ett anställnings-ID inom intervallet 000100 till 000199 använder du: $filter = (PartitionKey EQ Sales) och (RowKey ge "empid_000100") och (RowKey Le ' empid_000199 ')  
* Om du vill hitta alla anställda på försäljnings avdelningen med en e-postadress som börjar med bokstaven "a", använder du $filter = (PartitionKey EQ Sales) och (RowKey ge "email_a") och (RowKey lt ' email_b ')  
  
  Den filter-syntax som används i exemplen ovan är från Table service REST API, mer information finns i [fråga entiteter](/rest/api/storageservices/Query-Entities).  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Table Storage är relativt billigt att använda så kostnaden för lagring av dubblettdata bör inte vara en stor angelägenhet. Du bör dock alltid utvärdera kostnaden för din design baserat på dina förväntade lagrings krav och bara lägga till dubbla entiteter som stöder de frågor som klient programmet ska köra.  
* Eftersom de sekundära index enheterna lagras i samma partition som de ursprungliga entiteterna, bör du se till att du inte överskrider skalbarhets målen för en enskild partition.  
* Du kan hålla dina dubbla entiteter konsekventa med varandra genom att använda EGTs för att uppdatera de två kopiorna av entiteten. Detta innebär att du bör lagra alla kopior av en entitet i samma partition. Mer information finns i avsnittet [använda enhets grupp transaktioner](table-storage-design.md#entity-group-transactions).  
* Värdet som används för **RowKey** måste vara unikt för varje entitet. Överväg att använda sammansatta nyckel värden.  
* Utfyllnad av numeriska värden i **RowKey** (till exempel anställnings-ID 000223), aktiverar korrekt sortering och filtrering baserat på övre och nedre gränser.  
* Du behöver inte duplicera alla egenskaper för entiteten. Om till exempel frågorna som söker efter entiteter som använder e-postadressen i **RowKey** aldrig behöver medarbetarens ålder, kan dessa entiteter ha följande struktur:

   ![Enhets struktur för anställd](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Det är vanligt vis bättre att lagra dubblettdata och se till att du kan hämta alla data du behöver med en enda fråga, än att använda en fråga för att hitta en entitet och en annan för att leta upp nödvändiga data.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när ditt klient program behöver hämta entiteter med olika olika nycklar, när klienten behöver hämta entiteter i olika sorterings ordningar och där du kan identifiera varje entitet med hjälp av en mängd unika värden. Du bör dock vara säker på att du inte överskrider gränserna för skalbarhet när du utför enhets uppslag med de olika **RowKey** -värdena.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sekundärt index mönster mellan partitioner](#inter-partition-secondary-index-pattern)
* [Sammansatt nyckel mönster](#compound-key-pattern)
* Enhets grupp transaktioner
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Sekundärt index mönster mellan partitioner
Lagra flera kopior av varje entitet med olika **RowKey** -värden i separata partitioner eller i separata tabeller för att möjliggöra snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika **RowKey** -värden.  

### <a name="context-and-problem"></a>Kontext och problem
Table service indexerar automatiskt entiteter med hjälp av värdena **PartitionKey** och **RowKey** . Detta gör att ett klient program kan hämta en entitet effektivt med dessa värden. Om du till exempel använder tabell strukturen som visas nedan kan ett klient program använda en punkt fråga för att hämta en enskild anställd entitet med hjälp av avdelnings namnet och anställnings-ID: t ( **PartitionKey** och **RowKey** ). En-klient kan också hämta entiteter sorterade efter anställnings-ID inom varje avdelning.  

![Anställnings-ID](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Om du också vill kunna hitta en anställd entitet baserat på värdet för en annan egenskap, till exempel e-postadress, måste du använda en mindre effektiv partitions ökning för att hitta en matchning. Detta beror på att tabell tjänsten inte tillhandahåller sekundära index. Det finns dessutom inget alternativ för att begära en lista över anställda sorterad i en annan ordning än **RowKey** ordning.  

Du förväntar dig en stor mängd transaktioner mot dessa entiteter och vill minimera risken för Table service begränsning av klienten.  

### <a name="solution"></a>Lösning
För att undvika avsaknad av sekundära index kan du lagra flera kopior av varje entitet med varje kopia med olika **PartitionKey** -och **RowKey** -värden. Om du lagrar en entitet med de strukturer som visas nedan kan du effektivt hämta personal enheter baserat på e-postadress eller medarbetar-ID. Prefixvärde för **PartitionKey**, "empid_" och "email_" gör att du kan identifiera vilket index som du vill använda för en fråga.  

![Primärt index och sekundärt index](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Följande två filter villkor (en sökning efter anställnings-ID och en sökning efter e-postadress) anger båda punkt frågor:  

* $filter = (PartitionKey EQ empid_Sales) och (RowKey EQ 000223)
* $filter = (PartitionKey EQ email_Sales) och (RowKey EQ jonesj@contoso.com )  

Om du frågar efter ett antal anställdas entiteter kan du ange ett intervall som sorterats i anställnings-ID-ordning, eller ett intervall som sorterats i e-postadressen genom att fråga efter entiteter med lämpligt prefix i **RowKey**.  

* Om du vill hitta alla anställda på försäljnings avdelningen med ett anställnings-ID i intervallet **000100** till **000199** sorterat i anställnings-ID order användning: $filter = (PartitionKey EQ Empid_Sales) och (RowKey ge "000100") och (RowKey Le ' 000199 ')  
* Om du vill hitta alla anställda på försäljnings avdelningen med en e-postadress som börjar med "a" sorterad i e-postadressen använder du: $filter = (PartitionKey EQ email_Sales) och (RowKey ge "a") och (RowKey lt ' b ')  

Den filter-syntax som används i exemplen ovan är från Table service REST API, mer information finns i [fråga entiteter](/rest/api/storageservices/Query-Entities).  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du kan se till att dina dubbla entiteter försätts i enlighet med varandra genom att använda mönstret för senare [transaktioner](#eventually-consistent-transactions-pattern) för att underhålla de primära och sekundära index enheterna.  
* Table Storage är relativt billigt att använda så kostnaden för lagring av dubblettdata bör inte vara en stor angelägenhet. Du bör dock alltid utvärdera kostnaden för din design baserat på dina förväntade lagrings krav och bara lägga till dubbla entiteter som stöder de frågor som klient programmet ska köra.  
* Värdet som används för **RowKey** måste vara unikt för varje entitet. Överväg att använda sammansatta nyckel värden.  
* Utfyllnad av numeriska värden i **RowKey** (till exempel anställnings-ID 000223), aktiverar korrekt sortering och filtrering baserat på övre och nedre gränser.  
* Du behöver inte duplicera alla egenskaper för entiteten. Om till exempel frågorna som söker efter entiteter som använder e-postadressen i **RowKey** aldrig behöver medarbetarens ålder, kan dessa entiteter ha följande struktur:
  
   ![Anställd entitet (sekundärt index)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Det är vanligt vis bättre att lagra dubblettdata och se till att du kan hämta alla data du behöver med en enda fråga än att använda en fråga för att hitta en entitet med hjälp av det sekundära indexet och en annan för att leta upp nödvändiga data i det primära indexet.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när ditt klient program behöver hämta entiteter med olika olika nycklar, när klienten behöver hämta entiteter i olika sorterings ordningar och där du kan identifiera varje entitet med hjälp av en mängd unika värden. Använd det här mönstret om du vill undvika att överskrida gränserna för skalbarhet när du utför enhets uppslag med de olika **RowKey** -värdena.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Mönster för eventuellt konsekventa transaktioner](#eventually-consistent-transactions-pattern)  
* [Sekundärt index mönster för sekundär partition](#intra-partition-secondary-index-pattern)  
* [Sammansatt nyckel mönster](#compound-key-pattern)  
* Enhets grupp transaktioner  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Mönster för eventuellt konsekventa transaktioner
Aktivera konsekvent beteende för partitionerings gränser eller lagrings system gränser med hjälp av Azure-köer.  

### <a name="context-and-problem"></a>Kontext och problem
EGTs möjliggör atomiska transaktioner över flera entiteter som delar samma partitionsnyckel. Av prestanda-och skalbarhets skäl kan du välja att lagra entiteter som har konsekvens krav i separata partitioner eller i ett separat lagrings system: i ett sådant scenario kan du inte använda EGTs för att upprätthålla konsekvens. Du kan till exempel ha ett krav på att upprätthålla eventuell konsekvens mellan:  

* Entiteter som lagras i två olika partitioner i samma tabell, i olika tabeller eller i olika lagrings konton.  
* En entitet som lagras i Table service och en blob som lagras i Blob Service.  
* En entitet som lagras i Table service och en fil i ett fil system.  
* En entitet som lagras i Table service som har indexerats med Azure Kognitiv sökning-tjänsten.  

### <a name="solution"></a>Lösning
Genom att använda Azure-köer kan du implementera en lösning som ger en eventuell konsekvens på två eller flera partitioner eller lagrings system.
För att illustrera den här metoden förutsätter vi att du har ett krav för att kunna arkivera gamla personal enheter. Gamla personal enheter frågas sällan och bör undantas från aktiviteter som hanterar aktuella anställda. För att implementera det här kravet lagrar du aktiva medarbetare i den **aktuella** tabellen och gamla anställda i tabellen **Arkiv** . Genom att arkivera en medarbetare måste du ta bort entiteten från den **aktuella** tabellen och lägga till entiteten i **Arkiv** tabellen, men du kan inte använda en EGT för att utföra dessa två åtgärder. För att undvika risken att ett fel orsakar att en entitet visas i båda eller inga tabeller, måste Arkiv åtgärden vara konsekvent. I följande sekvensdiagram beskrivs stegen i den här åtgärden. Mer information finns för undantags Sök vägar i texten som följer.  

![Lösning för Azure-köer](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

En klient initierar Arkiv åtgärden genom att placera ett meddelande i en Azure-kö, i det här exemplet för att arkivera medarbetar #456. En arbets roll avsöker kön efter nya meddelanden. När den hittar ett läser det meddelandet och lämnar en dold kopia av kön. Arbets rollen hämtar sedan en kopia av entiteten från den **aktuella** tabellen, infogar en kopia i tabellen **Arkiv** och tar sedan bort originalet från den **aktuella** tabellen. Slutligen, om det inte fanns några fel från föregående steg, tar arbets rollen bort det dolda meddelandet från kön.  

I det här exemplet infogar steg 4 medarbetaren i **Arkiv** tabellen. Den kan lägga till medarbetaren till en BLOB i Blob Service eller en fil i ett fil system.  

### <a name="recovering-from-failures"></a>Återställer från haverier
Det är viktigt att åtgärderna i steg **4** och **5** måste vara *idempotenta* om arbets rollen behöver starta om lagrings åtgärden. Om du använder Table service, för steg **4** , ska du använda en "Infoga eller Ersätt"-åtgärd. i steg **5** bör du använda åtgärden "ta bort om finns" i klient biblioteket som du använder. Om du använder ett annat lagrings system måste du använda en lämplig idempotenta-åtgärd.  

Om arbets rollen aldrig slutförs steg **6**, efter en tids gräns, visas meddelandet igen i kön redo för arbets rollen för att försöka att bearbeta det igen. Arbets rollen kan kontrol lera hur många gånger ett meddelande i kön har lästs och, om det behövs, flagga det är ett "Poison"-meddelande för undersökning genom att skicka det till en separat kö. Mer information om hur du läser Kömeddelanden och kontrollerar antalet ur kön finns i [Hämta meddelanden](/rest/api/storageservices/Get-Messages).  

Vissa fel från tabell-och Queue-tjänsterna är tillfälliga fel och klient programmet bör inkludera lämplig omprövnings logik för att hantera dem.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Den här lösningen tillhandahåller inte transaktions isolering. En-klient kan till exempel läsa **aktuella** och **arkivera** tabeller när arbets rollen var mellan steg **4** och **5** och se en inkonsekvent vy över data. Data kommer att vara konsekventa.  
* Du måste vara säker på att steg 4 och 5 är idempotenta för att säkerställa eventuell konsekvens.  
* Du kan skala lösningen genom att använda flera köer och arbets Rolls instanser.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill garantera eventuell konsekvens mellan entiteter som finns i olika partitioner eller tabeller. Du kan utöka det här mönstret för att säkerställa eventuell konsekvens för åtgärder i Table service och Blob Service och andra icke-Azure Storage data källor, till exempel databas eller fil system.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* Enhets grupp transaktioner  
* [Sammanfoga eller Ersätt](#merge-or-replace)  

> [!NOTE]
> Om transaktions isolering är viktigt för din lösning bör du överväga att designa om tabellerna så att du kan använda EGTs.  
> 
> 

## <a name="index-entities-pattern"></a>Mönster för index entiteter
Underhåll index enheter för att aktivera effektiva sökningar som returnerar listor med entiteter.  

### <a name="context-and-problem"></a>Kontext och problem
Table service indexerar automatiskt entiteter med hjälp av värdena **PartitionKey** och **RowKey** . Detta gör att ett klient program kan hämta en entitet effektivt med en punkt fråga. Med hjälp av tabell strukturen som visas nedan kan ett klient program effektivt hämta en enskild anställd entitet med hjälp av avdelnings namnet och medarbetar-ID: t ( **PartitionKey** och **RowKey**).  

![Anställd entitet](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Om du också vill kunna hämta en lista över anställdas entiteter baserat på värdet för en annan icke-unik egenskap, t. ex. efter namn, måste du använda en mindre effektiv partitions ökning för att hitta matchningar i stället för att använda ett index för att se dem direkt. Detta beror på att tabell tjänsten inte tillhandahåller sekundära index.  

### <a name="solution"></a>Lösning
Om du vill aktivera sökning efter efter namn med enhets strukturen som visas ovan måste du underhålla listor med anställnings-ID. Om du vill hämta anställdas entiteter med ett visst efter namn, till exempel Johansson, måste du först leta upp listan med anställnings-ID för anställda med Johansson som efter namn och sedan hämta de anställdas entiteter. Det finns tre huvud alternativ för att lagra listor med anställnings-ID:  

* Använd Blob Storage.  
* Skapa index enheter i samma partition som de anställdas entiteter.  
* Skapa index enheter i en separat partition eller tabell.  

<u>Alternativ #1: Använd Blob Storage</u>  

För det första alternativet skapar du en BLOB för varje unikt efter namn och i varje BLOB-Arkiv visas en lista över värdena **PartitionKey** (avdelning) och **RowKey** (medarbetar-ID) för anställda som har det efter namnet. När du lägger till eller tar bort en medarbetare bör du se till att innehållet i den relevanta blobben stämmer överens med de anställdas enheter.  

<u>Alternativ #2:</u> Skapa index enheter i samma partition  

För det andra alternativet använder du indexera entiteter som lagrar följande data:  

![Entitet för medarbetar index](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

Egenskapen **EmployeeIDs** innehåller en lista med anställnings-ID: n för anställda med efter namnet lagrat i **RowKey**.  

Följande steg beskriver processen som du bör följa när du lägger till en ny medarbetare om du använder det andra alternativet. I det här exemplet lägger vi till en anställd med ID 000152 och efter namn Jones på försäljnings avdelningen:  

1. Hämta index entiteten med **PartitionKey** -värdet "Sales" och **RowKey** -värdet "Johansson". Spara ETag för den här entiteten som ska användas i steg 2.  
2. Skapa en enhets grupp transaktion (det vill säga en batch-åtgärd) som infogar den nya personal enheten (**PartitionKey** värde "försäljning" och **RowKey** värde "000152") och uppdaterar index enheten (**PartitionKey** värde "Sales" och **RowKey** Value "Johansson") genom att lägga till det nya medarbetar-ID: t i listan i fältet EmployeeIDs. Mer information om enhets grupps transaktioner finns i enhets grupp transaktioner.  
3. Om enhets grupp transaktionen Miss lyckas på grund av ett optimistiskt samtidigt fel (någon annan har precis ändrat entiteten index) måste du börja om steg 1.  

Du kan använda en liknande metod för att ta bort en medarbetare om du använder det andra alternativet. Att ändra en anställds efter namn är något mer komplicerat eftersom du måste köra en enhets grupps transaktion som uppdaterar tre entiteter: den anställdas entitet, index-entiteten för det gamla efter namnet och entiteten index för det nya efter namnet. Du måste hämta varje entitet innan du gör några ändringar för att kunna hämta de ETag-värden som du sedan kan använda för att utföra uppdateringarna med optimistisk samtidighet.  

Följande steg beskriver processen som du bör följa när du behöver leta upp alla anställda med ett angivet efter namn på en avdelning om du använder det andra alternativet. I det här exemplet letar vi upp alla anställda med efter namn Jones på försäljnings avdelningen:  

1. Hämta index entiteten med **PartitionKey** -värdet "Sales" och **RowKey** -värdet "Johansson".  
2. Parsa listan med anställnings-ID: n i fältet EmployeeIDs.  
3. Om du behöver ytterligare information om var och en av dessa anställda (till exempel deras e-postadresser) kan du hämta var och en av de anställdas enheter med **PartitionKey** -värdet "Sales" och **RowKey** -värden från listan över medarbetare som du fick i steg 2.  

<u>Alternativ #3:</u> Skapa index enheter i en separat partition eller tabell  

För det tredje alternativet använder du indexera entiteter som lagrar följande data:  

![Entiteten för medarbetar index i en separat partition](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


Egenskapen **EmployeeIDs** innehåller en lista med anställnings-ID: n för anställda med efter namnet lagrat i **RowKey**.  

Med det tredje alternativet kan du inte använda EGTs för att upprätthålla konsekvens eftersom index entiteterna finns i en separat partition från entiteten anställda. Se till att index entiteterna är konsekventa med de anställdas entiteter.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Den här lösningen kräver minst två frågor för att hämta matchande entiteter: en för att skicka en fråga till index entiteter för att hämta listan över **RowKey** -värden och sedan frågor för att hämta varje entitet i listan.  
* Eftersom en enskild entitet har en maximal storlek på 1 MB, #2 och alternativ #3 i lösningen förutsätter vi att listan med anställnings-ID: n för ett visst efter namn aldrig är större än 1 MB. Om listan med anställnings-ID sannolikt är större än 1 MB använder du alternativet #1 och lagrar index data i Blob Storage.  
* Om du använder alternativet #2 (när du använder EGTs för att hantera att lägga till och ta bort medarbetare och ändra en anställds efter namn) måste du utvärdera om volymen av transaktionerna ska närma sig skalbarhets gränserna för en specifik partition. I så fall bör du överväga en konsekvent lösning (alternativ #1 eller alternativ #3) som använder köer för att hantera uppdaterings begär Anden och gör att du kan lagra dina index enheter i en separat partition från de anställdas enheter.  
* Om du #2 i den här lösningen förutsätter vi att du vill leta efter efter namn på en avdelning: till exempel vill du hämta en lista över anställda med efter namn Jones på försäljnings avdelningen. Om du vill kunna söka efter alla anställda med efter namn Jones i hela organisationen, använder du antingen alternativet #1 eller alternativ #3.
* Du kan implementera en Queue-baserad lösning som ger en eventuell konsekvens (mer information finns i mönster för senare [konsekventa transaktioner](#eventually-consistent-transactions-pattern) ).  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill leta upp en uppsättning entiteter som alla delar ett gemensamt egenskaps värde, till exempel alla anställda med efter namn Jones.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Mönster för eventuellt konsekventa transaktioner](#eventually-consistent-transactions-pattern)  
* Enhets grupp transaktioner  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Utnormaliserings mönster
Kombinera relaterade data tillsammans i en enda entitet så att du kan hämta alla data du behöver med en enda punkt fråga.  

### <a name="context-and-problem"></a>Kontext och problem
I en Relations databas normaliserar du vanligt vis data för att ta bort dubbletter som resulterar i frågor som hämtar data från flera tabeller. Om du normaliserar dina data i Azure-tabeller måste du göra flera tur och ingångar från klienten till servern för att hämta relaterade data. Med tabell strukturen som visas nedan behöver du till exempel två tur och svar för att hämta information om en avdelning: en för att hämta information om en avdelning: en för att hämta den avdelnings enhet som innehåller chefens ID och en annan begäran att hämta chefens information i en anställds entitet.  

![Avdelnings enhet och entitet för anställd](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Lösning
I stället för att lagra data i två separata entiteter avnormaliserar du data och behåller en kopia av chefens information i avdelnings enheten. Exempel:  

![Avdelnings enhet](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Med avdelnings enheter lagrade med dessa egenskaper kan du nu hämta all information du behöver om en avdelning med en punkt fråga.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Det finns vissa kostnads kostnader som är kopplade till att lagra vissa data två gånger. Prestanda förmånen (till följd av färre begär anden till lagrings tjänsten) väger vanligt vis att öka marginal ökningen i lagrings kostnaderna (och den här kostnaden delvis motbokas genom en minskning av antalet transaktioner som du behöver för att hämta information om en avdelning).  
* Du måste upprätthålla konsekvensen för de två entiteter som lagrar information om chefer. Du kan hantera konsekvens problemet genom att använda EGTs för att uppdatera flera entiteter i en enda Atomic-transaktion: i det här fallet avdelnings enheten och den anställdas enhet för avdelnings chefen lagras på samma partition.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du ofta behöver söka efter relaterad information. Det här mönstret minskar antalet frågor som klienten måste göra för att hämta de data som krävs.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* Enhets grupp transaktioner  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Sammansatt nyckel mönster
Använd sammansatta **RowKey** -värden för att göra det möjligt för en klient att söka efter relaterade data med en enda punkt fråga.  

### <a name="context-and-problem"></a>Kontext och problem
I en Relations databas är det naturligt att använda kopplingar i frågor för att returnera relaterade data delar till klienten i en enskild fråga. Du kan till exempel använda medarbetar-ID: t för att leta upp en lista över relaterade entiteter som innehåller prestanda och granska data för den anställda.  

Anta att du lagrar anställdas entiteter i Table service med hjälp av följande struktur:  

![Skärm bild som visar hur du kan lagra personal enheter i Table service.](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Du måste också lagra historiska data som rör granskningar och prestanda för varje år som den anställde har arbetat för din organisation och du måste kunna komma åt den här informationen per år. Ett alternativ är att skapa en annan tabell som lagrar entiteter med följande struktur:  

![Struktur för alternativ medarbetar enhet](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Observera att med den här metoden kan du välja att duplicera viss information (till exempel förnamn och efter namn) i den nya entiteten så att du kan hämta dina data med en enda begäran. Du kan dock inte upprätthålla stark konsekvens eftersom du inte kan använda en avkonsistens för att uppdatera de två entiteterna.  

### <a name="solution"></a>Lösning
Lagra en ny entitetstyp i den ursprungliga tabellen med hjälp av entiteter med följande struktur:  

![Lösning för entitets struktur för medarbetare](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Observera att **RowKey** nu är en sammansatt nyckel som består av medarbetar-ID: t och året för gransknings data som gör det möjligt att hämta den anställdas prestanda och granska data med en enda begäran för en enda enhet.  

I följande exempel beskrivs hur du kan hämta alla gransknings data för en viss medarbetare (till exempel anställda 000123 på försäljnings avdelningen):  

$filter = (PartitionKey EQ ' Sales ') och (RowKey ge ' empid_000123 ') och (RowKey lt ' 000123_2012 ') &$select = RowKey, chefs klassificering, peer-klassificering, kommentarer  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du bör använda ett lämpligt avgränsnings tecken som gör det enkelt att parsa **RowKey** -värdet: till exempel **000123_2012**.  
* Du lagrar även den här entiteten i samma partition som andra entiteter som innehåller relaterade data för samma anställd, vilket innebär att du kan använda EGTs för att upprätthålla stark konsekvens.
* Du bör fundera över hur ofta du kommer att fråga data för att avgöra om det här mönstret är lämpligt.  Om du till exempel kommer att få åtkomst till gransknings data sällan och den viktigaste informationen i den anställda bör du ha dem som separata entiteter.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra en eller flera relaterade entiteter som du frågar ofta.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* Enhets grupp transaktioner  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  
* [Mönster för eventuellt konsekventa transaktioner](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Logg änden-mönster
Hämta de *n* entiteter som senast har lagts till i en partition med hjälp av ett **RowKey** -värde som sorterar i omvänt datum-och tids ordning.  

### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt krav har kunnat hämta de senast skapade entiteterna, till exempel de 10 senaste utgifts anspråk som skickats av en medarbetare. Tabell frågor stöder en **$Top** Query-åtgärd för att returnera de första *n* entiteterna från en mängd: det finns ingen motsvarande fråga för att returnera de sista n entiteterna i en mängd.  

### <a name="solution"></a>Lösning
Lagra entiteterna med en **RowKey** som naturligt sorterar i omvänd datum-/tids ordning genom att använda så att den senaste posten alltid är den första i tabellen.  

För att till exempel kunna hämta de 10 senaste utgifts anspråk som skickats av en medarbetare, kan du använda ett omvänt skal värde som härletts från aktuellt datum/tid. I följande C#-kod exempel visas ett sätt att skapa ett lämpligt "inverterade Tick"-värde för en **RowKey** som sorterar från den senaste till det äldsta:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Du kan gå tillbaka till datum/tid-värdet med hjälp av följande kod:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Tabell frågan ser ut så här:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du måste fylla i det omvända skalet med inledande nollor för att sträng värden ska sorteras som förväntat.  
* Du måste vara medveten om skalbarhets målen på nivån för en partition. Var noga med att inte skapa varma dekor partitioner.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver åtkomst till entiteter i omvänd datum-/tids ordning eller när du behöver åtkomst till de senast tillagda entiteterna.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Lägga/Lägg till anti-mönster](#prepend-append-anti-pattern)  
* [Entiteter hämtas](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Hög volym borttagnings mönster
Aktivera borttagning av en stor mängd entiteter genom att lagra alla entiteter för samtidig borttagning i en egen separat tabell. du tar bort entiteterna genom att ta bort tabellen.  

### <a name="context-and-problem"></a>Kontext och problem
Många program tar bort gamla data som inte längre behöver vara tillgängliga för ett klient program eller som programmet har arkiverat till ett annat lagrings medium. Du kan vanligt vis identifiera sådana data med ett datum: exempelvis har du ett krav för att ta bort poster för alla inloggnings begär Anden som är äldre än 60 dagar gamla.  

En möjlig design är att använda datum och tid för inloggningsbegäran i **RowKey**:  

![Datum och tid för inloggnings försök](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Med den här metoden undviker du hotspot-filer för partitioner eftersom programmet kan infoga och ta bort inloggnings enheter för varje användare i en separat partition. Den här metoden kan dock vara kostsam och tids krävande om du har ett stort antal entiteter eftersom du först måste utföra en tabells ökning för att kunna identifiera alla entiteter som ska tas bort och sedan ta bort varje gammal entitet. Du kan minska antalet fördröjningar till servern som krävs för att ta bort de gamla entiteterna genom att gruppera flera borttagnings begär anden i EGTs.  

### <a name="solution"></a>Lösning
Använd en separat tabell för varje dag med inloggnings försök. Du kan använda entitets design ovan för att undvika hotspots när du infogar entiteter, och om du tar bort gamla entiteter är det nu bara en fråga om att ta bort en tabell varje dag (en enda lagrings åtgärd) i stället för att hitta och ta bort hundratals och tusentals enskilda inloggnings enheter varje dag.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Stöder din design andra sätt att använda data, till exempel att leta upp specifika entiteter, länka till andra data eller generera sammanställd information?  
* Undviks dina design frekventa punkter när du infogar nya entiteter?  
* Vänta en stund om du vill återanvända samma tabell namn när du har tagit bort det. Det är bättre att alltid använda unika tabell namn.  
* Vänta lite begränsning när du först använder en ny tabell medan Table service lär sig åtkomst mönstren och distribuerar partitionerna över noderna. Du bör fundera över hur ofta du behöver skapa nya tabeller.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du har en stor mängd entiteter som du måste ta bort samtidigt.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* Enhets grupp transaktioner
* [Ändra entiteter](#modifying-entities)  

## <a name="data-series-pattern"></a>Mönster för data serier
Lagra kompletta data serier i en enda entitet för att minimera antalet förfrågningar som du gör.  

### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt scenario är att ett program lagrar en serie data som vanligt vis behöver hämta alla samtidigt. Ditt program kan till exempel registrera hur många snabb meddelanden varje medarbetare skickar varje timme och sedan använda den här informationen för att rita upp hur många meddelanden varje användare skickas under de senaste 24 timmarna. En design kan vara att lagra 24 entiteter för varje anställd:  

![Lagra 24 entiteter för varje medarbetare](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Med den här designen kan du enkelt hitta och uppdatera entiteten för uppdatering för varje anställd när programmet behöver uppdatera värdet för antal meddelanden. Men för att hämta informationen för att rita ett diagram över aktiviteten under de senaste 24 timmarna måste du hämta 24 entiteter.  

### <a name="solution"></a>Lösning
Använd följande design med en separat egenskap för att lagra antalet meddelanden i varje timme:  

![Message stats-enhet](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Med den här designen kan du använda en sammanslagnings åtgärd för att uppdatera antalet meddelanden för en medarbetare under en angiven timme. Nu kan du hämta all information som du behöver för att rita diagrammet med en begäran om en enda entitet.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Om din fullständiga data serie inte passar in i en enskild entitet (en entitet kan ha upp till 252 egenskaper) använder du ett alternativt data lager, till exempel en blob.  
* Om du har flera klienter som uppdaterar en entitet samtidigt måste du använda **etag** för att implementera optimistisk samtidighet. Om du har många klienter kan du uppleva hög konkurrens.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver uppdatera och hämta en data serie som är associerad med en enskild entitet.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Mönster för stora entiteter](#large-entities-pattern)  
* [Sammanfoga eller Ersätt](#merge-or-replace)  
* [Eventuellt konsekventa transaktions mönster](#eventually-consistent-transactions-pattern) (om du lagrar data serien i en BLOB)  

## <a name="wide-entities-pattern"></a>Mönster för breda entiteter
Använd flera fysiska entiteter för att lagra logiska entiteter med fler än 252 egenskaper.  

### <a name="context-and-problem"></a>Kontext och problem
En enskild entitet får ha högst 252 egenskaper (exklusive de obligatoriska system egenskaperna) och kan inte lagra mer än 1 MB data totalt. I en Relations databas får du normalt en runda av eventuella gränser för storleken på en rad genom att lägga till en ny tabell och tvinga en 1-till-1-relation mellan dem.  

### <a name="solution"></a>Lösning
Med hjälp av Table service kan du lagra flera entiteter som representerar ett enda stort företags objekt med fler än 252 egenskaper. Om du till exempel vill lagra antalet snabb meddelanden som skickats av varje medarbetare under de senaste 365 dagarna kan du använda följande design som använder två entiteter med olika scheman:  

![Flera entiteter](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Om du behöver göra en ändring som måste uppdatera båda enheterna för att synkronisera dem med varandra, kan du använda en EGT. Annars kan du använda en enda sammanslagnings åtgärd för att uppdatera antalet meddelanden för en viss dag. Om du vill hämta alla data för en enskild medarbetare måste du hämta båda entiteterna, som du kan utföra med två effektiva begär Anden som använder både ett **PartitionKey** och ett **RowKey** -värde.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Hämtning av en fullständig logisk entitet omfattar minst två lagrings transaktioner: en för att hämta varje fysisk enhet.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra entiteter vars storlek eller antal egenskaper överskrider gränserna för en enskild entitet i Table service.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* Enhets grupp transaktioner
* [Sammanfoga eller Ersätt](#merge-or-replace)

## <a name="large-entities-pattern"></a>Mönster för stora entiteter
Använd Blob Storage för att lagra stora egenskaps värden.  

### <a name="context-and-problem"></a>Kontext och problem
En enskild entitet kan inte lagra mer än 1 MB data totalt. Om en eller flera av dina egenskaper lagrar värden som leder till att den totala storleken på din entitet överskrider det här värdet kan du inte lagra hela entiteten i Table service.  

### <a name="solution"></a>Lösning
Om din enhet överskrider 1 MB storlek eftersom en eller flera egenskaper innehåller stora mängder data, kan du lagra data i Blob Service och sedan lagra adressen för blobben i en egenskap i entiteten. Du kan till exempel lagra fotot av en medarbetare i Blob Storage och lagra en länk till fotot i egenskapen **Photo** i din personal-entitet:  

![Foto egenskap](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Om du vill behålla eventuell konsekvens mellan entiteten i Table service och data i Blob Service använder du det slutliga [transaktions mönstret](#eventually-consistent-transactions-pattern) för att underhålla dina entiteter.
* Hämtning av en fullständig entitet omfattar minst två lagrings transaktioner: en för att hämta entiteten och en för att hämta BLOB-data.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra entiteter vars storlek överskrider gränserna för en enskild entitet i Table service.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Mönster för eventuellt konsekventa transaktioner](#eventually-consistent-transactions-pattern)  
* [Mönster för breda entiteter](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Lägga/Lägg till anti-mönster
Öka skalbarheten när du har en stor mängd med infogningar genom att sprida tilläggen över flera partitioner.  

### <a name="context-and-problem"></a>Kontext och problem
Väntande eller lägga till entiteter i dina lagrade entiteter resulterar vanligt vis i att programmet lägger till nya entiteter till den första eller sista partitionen i en sekvens med partitioner. I det här fallet sker alla infogningar vid en specifik tidpunkt i samma partition, vilket skapar en hotspot som förhindrar att tabell tjänsten använder belastnings utjämning infogar på flera noder och möjligen kan orsaka att ditt program når skalbarhets målen för partition. Om du till exempel har ett program som loggar nätverks-och resurs åtkomst av medarbetare, kan en enhets struktur enligt nedan kunna resultera i att den aktuella timmens partition blir ett hotspot-område om transaktions volymen når skalbarhets målet för en enskild partition:  

![Enhets struktur](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Lösning
Följande alternativa Entity-struktur förhindrar ett hotspot-område på en viss partition som program loggar händelser:  

![Alternativ enhets struktur](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Observera i det här exemplet hur både **PartitionKey** och **RowKey** är sammansatta nycklar. **PartitionKey** använder både avdelnings-och medarbetar-ID för att distribuera loggningen över flera partitioner.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Stöder den alternativa nyckel strukturen som används för att snabbt skapa aktiva partitioner på tillägg effektivt de frågor som klient programmet gör?  
* Innebär den förväntade volymen av transaktioner att du sannolikt når skalbarhets målen för en enskild partition och att den begränsas av lagrings tjänsten?  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Undvik lägga/Lägg till anti-mönstret när din volym av transaktioner sannolikt kommer att leda till begränsning av lagrings tjänsten när du använder en aktiv partition.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Logg änden-mönster](#log-tail-pattern)  
* [Ändra entiteter](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Kant mönster för logg data
Normalt bör du använda Blob Service i stället för Table service för att lagra loggdata.  

### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt användnings fall för loggdata är att hämta ett urval av logg poster för ett visst datum-/tidsintervall: du kan till exempel hitta alla fel och viktiga meddelanden som ditt program har loggat in mellan 15:04 och 15:06 på ett visst datum. Du vill inte använda datum och tid för logg meddelandet för att bestämma vilken partition du sparar log-entiteter på: det resulterar i en aktiv partition, eftersom alla log-entiteter delar samma **PartitionKey** -värde (se avsnittet [lägga/append Anti-pattern](#prepend-append-anti-pattern)). Följande enhets schema för ett logg meddelande resulterar till exempel i en aktiv partition eftersom programmet skriver alla logg meddelanden till partitionen för aktuellt datum och timme:  

![Logga meddelande enhet](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

I det här exemplet inkluderar **RowKey** datum och tid för logg meddelandet för att säkerställa att logg meddelanden lagras sorterade i datum-/tids ordning och innehåller ett meddelande-ID om flera logg meddelanden delar samma datum och tid.  

En annan metod är att använda en **PartitionKey** som garanterar att programmet skriver meddelanden över flera olika partitioner. Om källan till logg meddelandet till exempel är ett sätt att distribuera meddelanden över flera partitioner, kan du använda följande enhets schema:  

![Alternativt entitet för logg meddelande](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

Men problemet med det här schemat är att hämta alla logg meddelanden under en angiven tids period. du måste söka igenom alla partitioner i tabellen.

### <a name="solution"></a>Lösning
I föregående avsnitt har du markerat problemet med att försöka att använda Table service för att lagra logg poster och föreslagna två, otillfredsställande och design. En lösning ledde till en aktiv partition med risken för dåliga prestanda vid skrivning av logg meddelanden. den andra lösningen resulterade i dåliga fråge prestanda på grund av kravet på att söka igenom varje partition i tabellen för att hämta logg meddelanden för ett särskilt tidsintervall. Blob Storage erbjuder en bättre lösning för den här typen av scenario och det här är hur Azure-lagringsanalys lagrar de loggdata som samlas in.  

Det här avsnittet beskriver hur Lagringsanalys lagrar loggdata i Blob Storage som en illustration av den här metoden för att lagra data som du normalt frågar efter intervall.  

Lagringsanalys lagrar logg meddelanden i ett avgränsat format i flera blobbar. Det avgränsade formatet gör det enkelt för ett klient program att parsa data i logg meddelandet.  

Lagringsanalys använder en namngivnings konvention för blobbar som gör att du kan hitta bloben (eller blobbar) som innehåller de logg meddelanden som du söker efter. Till exempel innehåller en blob med namnet "Queue/2014/07/31/1800/000001. log" logg meddelanden som relaterar till Queue Service för timme från 18:00 den 31 juli 2014. "000001" visar att det här är den första logg filen för den här perioden. Lagringsanalys registrerar också tidsstämplar för de första och sista logg meddelandena som lagras i filen som en del av blobens metadata. Med API: et för Blob Storage kan du hitta blobbar i en behållare baserat på ett namn prefix: om du vill hitta alla blobbar som innehåller kös logg data för timmen från och med 18:00 kan du använda prefixet "Queue/2014/07/31/1800".  

Lagringsanalys buffrar logg meddelanden internt och uppdaterar sedan regelbundet rätt BLOB eller skapar en ny med den senaste batchen med logg poster. Detta minskar antalet skrivningar som måste utföras till Blob-tjänsten.  

Om du implementerar en liknande lösning i ditt eget program måste du överväga hur du ska hantera kompromisser mellan tillförlitligheten (skriva varje loggpost till blob-lagringen) och kostnad och skalbarhet (buffrar uppdateringar i programmet och skriver dem till Blob Storage i batchar).  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska lagra loggdata:  

* Om du skapar en tabell design som gör det möjligt att undvika aktiva partitioner, kanske du upptäcker att du inte kan komma åt dina data på ett effektivt sätt.  
* För att bearbeta loggdata behöver en klient ofta läsa in många poster.  
* Även om loggdata ofta är strukturerade kan Blob Storage vara en bättre lösning.  

## <a name="implementation-considerations"></a>Att tänka på vid implementering
I det här avsnittet beskrivs några överväganden som du bör tänka på när du implementerar mönstren som beskrivs i föregående avsnitt. I det här avsnittet används exempel skrivna i C# som använder lagrings klient biblioteket (version 4.3.0 vid tidpunkten för skrivning).  

## <a name="retrieving-entities"></a>Entiteter hämtas
Som det beskrivs i avsnittet design för frågor, är den mest effektiva frågan en punkt fråga. I vissa fall kan du dock behöva hämta flera entiteter. I det här avsnittet beskrivs några vanliga metoder för att hämta entiteter med hjälp av lagrings klient biblioteket.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Köra en punkt fråga med lagrings klient biblioteket
Det enklaste sättet att köra en punkt fråga är att använda åtgärden **Hämta** tabell som visas i följande C#-kodfragment som hämtar en entitet med en **PartitionKey** med värdet "Sales" och en **RowKey** med värdet "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Observera hur det här exemplet förväntar sig att entiteten hämtas till typen **EmployeeEntity**.  

### <a name="retrieving-multiple-entities-using-linq"></a>Hämtar flera entiteter med LINQ
Du kan använda LINQ för att hämta flera entiteter från Table service när du arbetar med Microsoft Azure Cosmos tabell standard bibliotek. 

```azurecli
dotnet add package Microsoft.Azure.Cosmos.Table
```

Om du vill att exemplen nedan ska fungera måste du ta med namn områden:

```csharp
using System.Linq;
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Azure.Cosmos.Table.Queryable;
```

EmployeeTable är ett CloudTable-objekt som implementerar en CreateQuery \<ITableEntity> ()-metod som returnerar en TableQuery \<ITableEntity> . Objekt av den här typen implementerar en IQueryable och tillåter att både LINQ Query-uttryck och punkt notation används.

Hämtar flera entiteter och uppnår genom att ange en fråga med en **WHERE** -sats. För att undvika en tabells ökning bör du alltid inkludera värdet **PartitionKey** i WHERE-satsen och om möjligt **RowKey** -värdet för att undvika tabell-och partitions ökningar. Tabell tjänsten har stöd för en begränsad uppsättning jämförelse operatorer (större än, större än eller lika med, mindre än, mindre än eller lika med, lika med och inte lika med) som ska användas i WHERE-satsen. 

Följande C#-kodfragment hittar alla anställda vars efter namn börjar med "B" (förutsatt att **RowKey** lagrar efter namn) på försäljnings avdelningen (förutsatt att **PartitionKey** lagrar avdelnings namnet):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
            
var employees = query.Execute();  
```

Observera hur frågan anger både en **RowKey** och en **PartitionKey** för att få bättre prestanda.  

Följande kod exempel visar likvärdiga funktioner utan att använda LINQ-syntax:  

```csharp
TableQuery<EmployeeEntity> employeeQuery = 
    new TableQuery<EmployeeEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B")),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")));
            
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Exemplet kapslar flera **CombineFilters** -metoder för att inkludera de tre filter villkoren.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Hämtar ett stort antal entiteter från en fråga
En optimal fråga returnerar en enskild entitet baserat på ett **PartitionKey** -värde och ett **RowKey** -värde. I vissa fall kan du dock ha ett krav för att returnera många entiteter från samma partition eller till och med från flera partitioner.  

Du bör alltid testa programmets prestanda fullständigt i sådana scenarier.  

En fråga mot tabell tjänsten kan returnera högst 1 000 entiteter vid ett tillfälle och kan köras i högst fem sekunder. Om resultatet innehåller fler än 1 000 entiteter, om frågan inte hade slutförts inom fem sekunder, eller om frågan korsar partitionens gränser, returnerar Table service en fortsättnings-token för att tillåta att klient programmet begär nästa uppsättning entiteter. Mer information om hur du använder fortsättnings-token finns i [fråga om tids gräns och sid brytning](/rest/api/storageservices/Query-Timeout-and-Pagination).  

Om du använder lagrings klient biblioteket kan det automatiskt hantera fortsättnings-token åt dig när det returnerar entiteter från Table service. Följande C#-kod exempel som använder lagrings klient biblioteket hanterar automatiskt fortsättnings-token om tabell tjänsten returnerar dem i ett svar:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

Följande C#-kod hanterar fortsättnings-token uttryckligen:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;
do
{
    var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
        // ...
    }
    
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Genom att använda tilläggs-token explicit kan du styra när programmet hämtar nästa data segment. Om ditt klient program till exempel gör det möjligt för användare att gå igenom de entiteter som lagras i en tabell, kan en användare välja att inte gå igenom alla entiteter som hämtats av frågan så att ditt program bara använder en fortsättnings-token för att hämta nästa segment när användaren hade slutat växlingen genom alla entiteter i det aktuella segmentet. Den här metoden har flera fördelar:  

* Du kan begränsa mängden data som hämtas från Table service och som du flyttar över nätverket.  
* Med den kan du utföra asynkron IO i .NET.  
* Det gör att du kan serialisera fortsättnings-token till beständigt lagrings utrymme så att du kan fortsätta i händelse av en program krasch.  

> [!NOTE]
> En fortsättnings-token returnerar vanligt vis ett segment som innehåller 1 000 entiteter, även om det kan vara färre. Detta är också fallet om du begränsar antalet poster som en fråga returnerar genom att använda **ta** för att returnera de första n entiteter som matchar dina Sök villkor: tabell tjänsten kan returnera ett segment som innehåller färre än n entiteter och en fortsättnings-token så att du kan hämta återstående entiteter.  
> 
> 

Följande C#-kod visar hur du ändrar antalet entiteter som returneras i ett segment:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Projektion på Server Sidan
En enskild entitet kan ha upp till 255 egenskaper och vara upp till 1 MB. När du frågar tabellen och hämtar entiteter kanske du inte behöver alla egenskaper och kan undvika att överföra data i onödan (för att minska svars tid och kostnader). Du kan använda projektion på Server sidan för att bara överföra de egenskaper du behöver. I följande exempel hämtas bara egenskapen **e-post** (tillsammans med **PartitionKey**, **RowKey**, **timestamp** och **etag**) från entiteterna som valts av frågan.  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
    new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
    Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Observera hur **RowKey** -värdet är tillgängligt även om det inte finns med i listan över egenskaper som ska hämtas.  

## <a name="modifying-entities"></a>Ändra entiteter
Med lagrings klient biblioteket kan du ändra dina entiteter som lagras i tabell tjänsten genom att infoga, ta bort och uppdatera entiteter. Du kan använda EGTs för att utföra flera infognings-, uppdaterings-och borttagnings åtgärder tillsammans för att minska antalet fördröjningar som krävs och förbättra lösningens prestanda.  

Undantag som har utlösts när lagrings klient biblioteket kör en EGT inkluderar vanligt vis indexet för den entitet som gjorde att batchen kunde köras. Detta är användbart när du felsöker kod som använder EGTs.  

Du bör också fundera över hur din design påverkar hur ditt klient program hanterar samtidighets-och uppdaterings åtgärder.  

### <a name="managing-concurrency"></a>Hantera samtidighet
Som standard implementerar tabell tjänsten optimistisk concurrency-kontroller på nivån för enskilda entiteter för **insert**-, **merge**-och **Delete** -åtgärder, även om det är möjligt för en klient att tvinga tabell tjänsten att kringgå kontrollerna. Mer information om hur tabell tjänsten hanterar samtidighet finns i  [Hantera samtidighet i Microsoft Azure Storage](../blobs/concurrency-manage.md).  

### <a name="merge-or-replace"></a>Sammanfoga eller Ersätt
**Ersättnings** metoden i **TableOperation** -klassen ersätter alltid den fullständiga entiteten i Table service. Om du inte tar med en egenskap i begäran när egenskapen finns i den lagrade entiteten tar begäran bort egenskapen från den lagrade entiteten. Om du inte vill ta bort en egenskap uttryckligen från en lagrad entitet måste du inkludera varje egenskap i begäran.  

Du kan använda **sammanslagnings** metoden för **TableOperation** -klassen för att minska mängden data som du skickar till den Table service när du vill uppdatera en entitet. **Sammanslagnings** metoden ersätter alla egenskaper i den lagrade entiteten med egenskaps värden från entiteten som ingår i begäran, men lämnar kvar egenskaper i den lagrade entiteten som inte ingår i begäran. Detta är användbart om du har stora entiteter och bara behöver uppdatera ett litet antal egenskaper i en begäran.  

> [!NOTE]
> Metoderna **replace** och **merge** Miss fungerar om entiteten inte finns. Alternativt kan du använda metoderna **InsertOrReplace** och **InsertOrMerge** som skapar en ny entitet om den inte finns.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Arbeta med heterogena entitetstyper
Table service är en *schema lös* tabell lagring som innebär att en enskild tabell kan lagra entiteter av flera typer som ger stor flexibilitet i din design. Följande exempel illustrerar en tabell som lagrar både anställda och avdelnings enheter:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Varje entitet måste fortfarande ha **PartitionKey**-, **RowKey**-och **timestamp** -värden, men kan ha en uppsättning egenskaper. Det finns dessutom inget som anger typen av entitet om du inte väljer att lagra informationen någonstans. Det finns två alternativ för att identifiera enhets typen:  

* Lägga av entitetstypen till **RowKey** (eller eventuellt **PartitionKey**). Till exempel **EMPLOYEE_000123** eller **DEPARTMENT_SALES** som **RowKey** -värden.  
* Använd en separat egenskap för att registrera entitetstypen som visas i tabellen nedan.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Medarbetare</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Medarbetare</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Avdelning</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Medarbetare</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Det första alternativet, beroende på enhets typ till **RowKey**, är användbart om det finns en möjlighet att två entiteter av olika typer kan ha samma nyckel värde. Den grupperar också entiteter av samma typ tillsammans i partitionen.  

De metoder som beskrivs i det här avsnittet är särskilt relevanta för diskussions [arvs relationerna](table-storage-design-modeling.md#inheritance-relationships) tidigare i den här hand boken i artikeln [modellerings relationer](table-storage-design-modeling.md).  

> [!NOTE]
> Du bör överväga att inkludera ett versions nummer i enhets typens värde om du vill att klient program ska kunna utveckla POCO-objekt och arbeta med olika versioner.  
> 
> 

I resten av det här avsnittet beskrivs några av funktionerna i lagrings klient biblioteket som underlättar arbetet med flera entitetstyper i samma tabell.  

### <a name="retrieving-heterogeneous-entity-types"></a>Hämtar heterogena entitetstyper
Om du använder lagrings klient biblioteket har du tre alternativ för att arbeta med flera olika entitetstyper.  

Om du vet vilken typ av entitet som lagras med ett särskilt värde för **RowKey** och **PartitionKey** kan du ange entitetstypen när du hämtar entiteten som du ser i föregående två exempel som hämtar entiteter av typen **EmployeeEntity**: [köra en punkt fråga med hjälp av lagrings klient biblioteket](#executing-a-point-query-using-the-storage-client-library) och [hämtar flera entiteter med LINQ](#retrieving-multiple-entities-using-linq).  

Det andra alternativet är att använda **DynamicTableEntity** -typen (en egenskaps uppsättning) i stället för en konkret Poco-entitetstyp (det här alternativet kan också förbättra prestanda eftersom det inte behövs att serialisera och deserialisera entiteten till .net-typer). Följande C#-kod kan användas för att hämta flera entiteter av olika typer från tabellen, men returnerar alla entiteter som **DynamicTableEntity** -instanser. Den använder sedan egenskapen **EntityType** för att fastställa typen av varje entitet:  

```csharp
string filter =
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
        TableOperators.And,
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "F")));
        
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
    
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
    EntityProperty entityTypeProperty;
    if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
    {
        if (entityTypeProperty.StringValue == "Employee")
        {
            // use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Om du vill hämta andra egenskaper måste du använda metoden **TryGetValue** i egenskapen **Properties** för klassen **DynamicTableEntity** .  

Ett tredje alternativ är att kombinera med **DynamicTableEntity** -typen och en **EntityResolver** -instans. På så sätt kan du matcha flera POCO-typer i samma fråga. I det här exemplet använder **EntityResolver** -delegaten egenskapen **EntityType** för att skilja mellan de två typerna av entiteter som frågan returnerar. **Lösnings** metoden använder **lösare** ombudet för att lösa **DynamicTableEntity** -instanser till **TableEntity** -instanser.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{
    TableEntity resolvedEntity = null;
    if (props["EntityType"].StringValue == "Department")
    {
        resolvedEntity = new DepartmentEntity();
    }
    else if (props["EntityType"].StringValue == "Employee")
    {
        resolvedEntity = new EmployeeEntity();
    }
    else 
    {
        throw new ArgumentException("Unrecognized entity", "props");
    }

    resolvedEntity.PartitionKey = pk;
    resolvedEntity.RowKey = rk;
    resolvedEntity.Timestamp = ts;
    resolvedEntity.ETag = etag;
    resolvedEntity.ReadEntity(props, null);
    return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
        
TableQuery<DynamicTableEntity> entityQuery = new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
    if (e is DepartmentEntity)
    {
        // ...
    }
    else if (e is EmployeeEntity)
    {
        // ...
    }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Ändra typer av heterogena entiteter
Du behöver inte känna till typen av en entitet för att ta bort den och du vet alltid vilken typ av enhet som används när du infogar den. Du kan dock använda **DynamicTableEntity** -typ för att uppdatera en entitet utan att känna till dess typ och utan att använda en Poco Entity-klass. I följande kod exempel hämtas en enskild entitet och kontrollerar att egenskapen **EmployeeCount** finns innan den uppdateras.  

```csharp
TableResult result = employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;
if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
    throw new InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}

countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));
```

## <a name="controlling-access-with-shared-access-signatures"></a>Kontrol lera åtkomst med signaturer för delad åtkomst
Du kan använda signaturer för delad åtkomst (SAS) för att göra det möjligt för klient program att ändra (och fråga)-tabell enheter utan att behöva inkludera din lagrings konto nyckel i din kod. Det finns vanligt vis tre viktiga fördelar med att använda SAS i ditt program:  

* Du behöver inte distribuera lagrings konto nyckeln till en osäker plattform (till exempel en mobil enhet) för att tillåta att enheten får åtkomst till och ändrar entiteter i Table service.  
* Du kan avlasta en del av arbetet som Web-och Worker-roller utför vid hantering av dina entiteter på klient enheter som slutanvändares datorer och mobila enheter.  
* Du kan tilldela en begränsad behörighet och begränsad tid till en klient (till exempel tillåta skrivskyddad åtkomst till särskilda resurser).  

Mer information om hur du använder SAS-token med Table service finns i [använda signaturer för delad åtkomst (SAS)](../../storage/common/storage-sas-overview.md).  

Du måste dock fortfarande generera SAS-token som ger ett klient program till entiteterna i tabell tjänsten: du bör göra detta i en miljö som har säker åtkomst till dina lagrings konto nycklar. Normalt använder du en webb-eller arbets roll för att generera SAS-token och leverera dem till de klient program som behöver åtkomst till dina entiteter. Eftersom det fortfarande finns en omkostnader som används för att skapa och leverera SAS-token till klienter, bör du överväga hur du bäst kan minska den här belastningen, särskilt i stora volym scenarier.  

Det går att skapa en SAS-token som beviljar åtkomst till en delmängd av entiteterna i en tabell. Som standard skapar du en SAS-token för en hel tabell, men du kan också ange att SAS-token beviljar åtkomst till antingen ett intervall med **PartitionKey** -värden eller ett intervall med **PartitionKey** -och **RowKey** -värden. Du kan välja att generera SAS-token för enskilda användare av systemet, så att varje användares SAS-token endast tillåter åtkomst till sina egna entiteter i tabell tjänsten.  

## <a name="asynchronous-and-parallel-operations"></a>Asynkrona och parallella åtgärder
Förutsatt att du sprider dina begär anden över flera partitioner kan du förbättra genomflödet och klientens svars tider med hjälp av asynkrona eller parallella frågor.
Du kan till exempel ha två eller flera arbets Rolls instanser som har åtkomst till dina tabeller parallellt. Du kan ha enskilda arbets roller som är ansvariga för specifika uppsättningar av partitioner eller bara ha flera arbets Rolls instanser, och varje användare kan komma åt alla partitioner i en tabell.  

Inom en klient instans kan du förbättra data flödet genom att köra lagrings åtgärder asynkront. Med lagrings klient biblioteket är det enkelt att skriva asynkrona frågor och ändringar. Du kan till exempel börja med den synkrona metoden som hämtar alla entiteter i en partition som visas i följande C#-kod:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

    TableContinuationToken continuationToken = null;
    do
    {
        var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
        
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

Du kan enkelt ändra koden så att frågan körs asynkront på följande sätt:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);
    
    TableContinuationToken continuationToken = null;
    do
    {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
    
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

I det här asynkrona exemplet kan du se följande ändringar från den synkrona versionen:  

* Metodsignaturen innehåller nu den **asynkrona** modifieraren och returnerar en **uppgifts** instans.  
* I stället för att anropa **ExecuteSegmented** -metoden för att hämta resultat anropar metoden **ExecuteSegmentedAsync** -metoden och använder modifieraren **await** för att hämta resultat asynkront.  

Klient programmet kan anropa den här metoden flera gånger (med olika värden för parametern **Department** ) och varje fråga kommer att köras i en separat tråd.  

Det finns ingen asynkron version av **execute** -metoden i klassen **TableQuery** eftersom **IEnumerable** -gränssnittet inte stöder asynkron uppräkning.  

Du kan också infoga, uppdatera och ta bort entiteter asynkront. I följande C#-exempel visas en enkel, synkron metod för att infoga eller ersätta en anställds entitet:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Du kan enkelt ändra koden så att uppdateringen körs asynkront på följande sätt:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

I det här asynkrona exemplet kan du se följande ändringar från den synkrona versionen:  

* Metodsignaturen innehåller nu den **asynkrona** modifieraren och returnerar en **uppgifts** instans.  
* I stället för att anropa metoden **execute** för att uppdatera entiteten anropar metoden **ExecuteAsync** -metoden och använder modifieraren **await** för att hämta resultat asynkront.  

Klient programmet kan anropa flera asynkrona metoder som det här, och varje metod anrop körs i en separat tråd.  

## <a name="next-steps"></a>Nästa steg

- [Modellera relationer](table-storage-design-modeling.md)
- [Utforma för frågor](table-storage-design-for-query.md)
- [Kryptera tabelldata](table-storage-design-encrypt-data.md)
- [Utforma för dataändring](table-storage-design-for-modification.md)