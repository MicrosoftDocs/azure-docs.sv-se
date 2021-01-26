---
title: Arbeta med Reliable Collections
description: Lär dig metod tips för att arbeta med pålitliga samlingar i ett Azure Service Fabric-program.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 908f24cc22c969a34a513ff3fd3ceaa788420620
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787883"
---
# <a name="working-with-reliable-collections"></a>Arbeta med Reliable Collections
Service Fabric erbjuder en tillstånds känslig programmerings modell som är tillgänglig för .NET-utvecklare via pålitliga samlingar. Mer specifikt Service Fabric tillhandahåller tillförlitliga ord listor och Reliable Queue-klasser. När du använder dessa klasser partitioneras ditt tillstånd (för skalbarhet), replikeras (för tillgänglighet) och överförs inom en partition (för sur semantik). Nu ska vi titta på en typisk användning av ett tillförlitligt Dictionary-objekt och se vad det faktiskt gör.

```csharp
try
{
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction())
   {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync isn't called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException)
{
   // choose how to handle the situation where you couldn't get a lock on the file because it was 
   // already in use. You might delay and retry the operation
   await Task.Delay(100);
}
```

Alla åtgärder på Reliable Dictionary-objekt (förutom för ClearAsync, som inte kan ångras), kräver ett ITransaction-objekt. Objektet har associerat med alla ändringar som du försöker göra till en tillförlitlig ord lista och/eller Reliable Queue-objekt inom en enda partition. Du kan hämta ett ITransaction-objekt genom att anropa partitionens StateManager's CreateTransaction-metod.

I koden ovan skickas ITransaction-objektet till en tillförlitlig ord listas AddAsync-metod. Internt kan ord listor som accepterar en nyckel ta ett lås-/skrivar lås som är kopplat till nyckeln. Om metoden ändrar nyckelns värde, tar metoden ett Skriv lås i nyckeln och om metoden bara läser från nyckelns värde, tas ett läslås på nyckeln. Eftersom AddAsync ändrar nyckelns värde till det nya, överförda värdet, tas nyckelns Skriv lås. Om två (eller fler) trådar försöker att lägga till värden med samma nyckel samtidigt, kommer en tråd att hämta Skriv låset och de andra trådarna kommer att blockeras. Som standard blockeras metoder i upp till 4 sekunder för att låsa sig. efter 4 sekunder genererar metoderna en TimeoutException. Det finns metod överlagringar som gör att du kan skicka ett explicit timeout-värde om du vill.

Vanligt vis skriver du koden för att reagera på en TimeoutException genom att fånga den och försöka utföra hela åtgärden igen (se koden ovan). I den här enkla koden anropar vi bara uppgiften. fördröjning som skickar 100 millisekunder varje gång. Men i verkligheten kan du vara bättre på att använda en viss typ av exponentiell säkerhets fördröjning i stället.

När låset har hämtats lägger AddAsync till nyckel-och värde objekt referenser till en intern tillfällig ord lista som är associerad med ITransaction-objektet. Detta görs för att ge dig en semantik med Läs-och skriv åtgärder. Det innebär att när du anropar AddAsync, kommer ett senare anrop till TryGetValueAsync med samma ITransaction-objekt att returnera värdet även om du inte har allokerat transaktionen ännu.

> [!NOTE]
> Om du anropar TryGetValueAsync med en ny transaktion returneras en referens till det senast allokerade värdet. Ändra inte referensen direkt, eftersom den kringgår mekanismen för att bevara och replikera ändringarna. Vi rekommenderar att du gör värdena skrivskyddade så att det enda sättet att ändra värdet för en nyckel är via Reliable Dictionary-API: er.

Därefter serialiserar AddAsync dina nyckel-och värde objekt till byte-matriser och lägger till dessa byte-matriser i en loggfil på den lokala noden. Slutligen skickar AddAsync byte-matriser till alla sekundära repliker så att de har samma nyckel/värde-information. Även om nyckel-/värde informationen har skrivits till en loggfil, betraktas inte informationen som en del av ord listan förrän transaktionen som de är kopplad till har genomförts.

I koden ovan genomför anropet till CommitAsync alla transaktioner. Mer specifikt lägger den till inchecknings information i logg filen på den lokala noden och skickar sedan inchecknings posten till alla sekundära repliker. När ett kvorum (majoritet) av replikerna har svarat betraktas alla data ändringar permanenta och eventuella lås som är kopplade till nycklar som ändrades via ITransaction-objektet släpps så att andra trådar/transaktioner kan ändra samma nycklar och deras värden.

Om CommitAsync inte anropas (vanligt vis på grund av ett undantag som genereras) tas ITransaction-objektet bort. Vid avslut av ett icke dedikerat ITransaction-objekt lägger Service Fabric till Avbryt information till den lokala nodens loggfil och behöver inte skickas till någon av de sekundära replikerna. Sedan släpps alla lås som är kopplade till nycklar som manipulerades via transaktionen.

## <a name="volatile-reliable-collections"></a>Flyktigt pålitliga samlingar 
I vissa arbets belastningar, till exempel en replikerad cache, kan en tillfällig data förlust tolereras. Att undvika persistens data till disk kan möjliggöra bättre svars tid och data flöden när du skriver till pålitliga ord listor. Kompromissen med utebliven beständighet är att om kvorumet bryts, kommer all data förlust att ske. Eftersom kvorumet förloras är en sällsynt förekomst, kan den ökade prestandan vara värd för den sällsynta risken för data förlust för dessa arbets belastningar.

För närvarande är flyktig support endast tillgängligt för Reliable-ordlistor och pålitliga köer, och inte ReliableConcurrentQueues. Se listan över [varningar](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections) för att informera ditt beslut om att använda volatile-samlingar.

Om du vill aktivera flyktigt stöd i tjänsten ställer du in ```HasPersistedState``` flaggan i tjänste typs deklarationen till ```false``` , som så här:
```xml
<StatefulServiceType ServiceTypeName="MyServiceType" HasPersistedState="false" />
```

>[!NOTE]
>Befintliga beständiga tjänster kan inte göras volatile och vice versa. Om du vill göra det måste du ta bort den befintliga tjänsten och sedan distribuera tjänsten med den uppdaterade flaggan. Det innebär att du måste använda fullständig data förlust om du vill ändra ```HasPersistedState``` flaggan. 

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Vanliga fall GRO par och hur du undviker dem
Nu när du förstår hur de pålitliga samlingarna fungerar internt kan vi ta en titt på några vanliga fel användnings områden. Se koden nedan:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property's value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

När du arbetar med en vanlig .NET-ord lista kan du lägga till en nyckel/värde i ord listan och sedan ändra värdet för en egenskap (till exempel LastLogin). Den här koden kommer dock inte att fungera korrekt med en tillförlitlig ord lista. Kom ihåg från den tidigare diskussionen, anropet till AddAsync serialiserar nyckel/värde-objekten till byte-matriser och sparar sedan matriserna till en lokal fil och skickar dem till de sekundära replikerna. Om du senare ändrar en egenskap ändras egenskapens värde endast i minnet. den lokala filen eller de data som skickas till replikerna påverkas inte. Om processen kraschar utlöses det i minnet. När en ny process startar eller om en annan replik blir primär, är det gamla egenskap svärdet det som är tillgängligt.

Jag kan inte stressa tillräckligt med hur enkelt det är att göra den typ av misstag som visas ovan. Och du får bara lära dig om misstaget om/när processen slutar fungera. Rätt sätt att skriva koden är att helt enkelt återställa de två raderna:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Här är ett annat exempel som visar vanliga fel:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property's value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Med vanliga .NET-ord listor är koden ovan fin och är ett vanligt mönster: utvecklaren använder en nyckel för att leta upp ett värde. Om värdet finns ändrar utvecklaren egenskapens värde. Men med pålitliga samlingar uppvisar den här koden samma problem som redan diskuterat: **du får inte ändra ett objekt när du har gett det ett tillförlitligt samlings objekt.**

Det korrekta sättet att uppdatera ett värde i en tillförlitlig samling är att hämta en referens till det befintliga värdet och betrakta det objekt som refereras till av denna referens oföränderlig. Skapa sedan ett nytt objekt som är en exakt kopia av det ursprungliga objektet. Nu kan du ändra statusen för det nya objektet och skriva det nya objektet i samlingen så att det blir serialiserat till byte-matriser, läggs till i den lokala filen och skickas till replikerna. När du har gjort ändringarna, är InMemory-objekten, den lokala filen och alla repliker samma exakta tillstånd. Allt är klart!

Koden nedan visar det korrekta sättet att uppdatera ett värde i en tillförlitlig samling:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> currentUser = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue)
   {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key's value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definiera oföränderliga data typer för att förhindra fel i programmerare
Vi vill att kompilatorn ska rapportera fel när du av misstag producerar kod som ligger i ett annat tillstånd för ett objekt som du ska överväga att överväga. Men C#-kompilatorn har inte möjlighet att göra detta. För att undvika potentiella programprograms buggar rekommenderar vi starkt att du definierar de typer som du använder med tillförlitliga samlingar för att vara oföränderliga typer. Det innebär särskilt att du går till kärn värdes typer (t. ex. siffror [Int32, UInt64, osv.], DateTime, GUID, TimeSpan och like). Du kan också använda sträng. Det är bäst att undvika samlings egenskaper som serialisering och deserialisering av dem kan ofta försämra prestanda. Men om du vill använda samlings egenskaper rekommenderar vi starkt att du använder. NETs bibliotek med oföränderlig samling ([system. Collections. oföränderligt](https://www.nuget.org/packages/System.Collections.Immutable/)). Det här biblioteket är tillgängligt för nedladdning från https://nuget.org . Vi rekommenderar också att du förseglar dina klasser och gör fälten skrivskyddade när det är möjligt.

I UserInfo-typen nedan visas hur du definierar en oföränderlig typ som utnyttjar rekommendationerna ovan.

```csharp
[DataContract]
// If you don't seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo
{
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) 
   {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context)
   {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId)
   {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

Typen ItemId är också en oåterkallelig typ som visas här:

```csharp
[DataContract]
public struct ItemId
{
   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName)
   {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Schema version (uppgraderingar)
Internt serialiserar pålitliga samlingar dina objekt med hjälp av. NETs DataContractSerializer. De Serialiserade objekten sparas på den primära replikens lokala disk och överförs också till de sekundära replikerna. När din tjänst är vuxen är det troligt att du vill ändra vilken typ av data (schema) som krävs för din tjänst. Sätt versions hantering av dina data med stor noggrannhet. Först och främst måste du alltid kunna deserialisera gamla data. Det innebär att din avserialiserings kod måste vara oändligt bakåtkompatibel: version 333 av Service koden måste kunna hantera data som placeras i en tillförlitlig samling av version 1 av Service koden 5 år sedan.

Dessutom uppgraderas Service koden till en uppgraderings domän i taget. Under en uppgradering har du därför två olika versioner av Service koden som körs samtidigt. Du behöver inte använda den nya versionen av Service koden för att kunna hantera det nya schemat eftersom gamla versioner av Service koden inte kan hanteras. När det är möjligt bör du utforma varje version av tjänsten så att den är kompatibel med en version. Det innebär särskilt att v1 av din service kod ska kunna ignorera eventuella schema element som den inte uttryckligen hanterar. Det måste dock kunna spara data som inte uttryckligen vet om och skriva ut den när du uppdaterar en ord lista nyckel eller ett värde.

> [!WARNING]
> Även om du kan ändra schemat för en nyckel måste du se till att nyckelns hash-kod och är lika med algoritmerna är stabila. Om du ändrar hur någon av dessa algoritmer fungerar kommer du inte att kunna leta upp nyckeln i den tillförlitliga ord listan någonsin igen.
> .NET-strängar kan användas som en nyckel men Använd själva strängen som nyckel – Använd inte resultatet av String. GetHashCode som nyckel.

Alternativt kan du utföra vad som vanligt vis kallas för en uppgradering. Med en uppgradering i två faser uppgraderar du tjänsten från v1 till v2: v2 innehåller koden som känner till hur den nya schema ändringen fungerar, men den här koden körs inte. När v2-koden läser v1-data, körs den på den och skriver v1-data. Efter att uppgraderingen har slutförts i alla uppgraderings domäner kan du på andra sätt signalera till de v2-instanser som uppgraderingen har slutförts. (Ett sätt att signalera detta är att distribuera en konfigurations uppgradering. det här gör du till en uppgradering i två steg.) V2-instanserna kan nu läsa v1-data, konvertera den till v2-data, använda den och skriva ut den som v2-data. När andra instanser läser v2-data behöver de inte konvertera den, de fungerar bara på den och skriva ut v2-data.

## <a name="next-steps"></a>Nästa steg
Information om hur du skapar Forward-kompatibla data kontrakt finns i [vidarebefordra-kompatibla data kontrakt](/dotnet/framework/wcf/feature-details/forward-compatible-data-contracts)

För att lära dig metod tips om versions data kontrakt, se [data kontrakt version](/dotnet/framework/wcf/feature-details/data-contract-versioning)

Information om hur du implementerar versions tolerans data kontrakt finns i [versions tolerans återanrop för serialisering](/dotnet/framework/wcf/feature-details/version-tolerant-serialization-callbacks)

Information om hur du tillhandahåller en data struktur som kan samverka över flera versioner finns i [IExtensibleDataObject](/dotnet/api/system.runtime.serialization.iextensibledataobject)
