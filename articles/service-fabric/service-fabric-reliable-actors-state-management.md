---
title: Hantering av Reliable Actors tillstånd
description: Beskriver hur Reliable Actors status hanteras, sparas och replikeras för hög tillgänglighet.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: badfc490f26b71881e7970c2c0be3472abfec25a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575609"
---
# <a name="reliable-actors-state-management"></a>Hantering av Reliable Actors tillstånd
Reliable Actors är entrådade objekt som kan kapsla in både logik och tillstånd. Eftersom aktörerna körs på Reliable Services kan de underhålla tillstånden på ett tillförlitligt sätt genom att använda samma beständiga och replikerade mekanismer. På så sätt förlorar aktörerna sina tillstånd efter fel, vid återaktivering efter skräp insamling, eller när de flyttas runt mellan noder i ett kluster på grund av resurs utjämning eller uppgraderingar.

## <a name="state-persistence-and-replication"></a>Beständighet och replikering av tillstånd
Alla Reliable Actors betraktas som *tillstånds känsliga* eftersom varje aktörs instans mappar till ett unikt ID. Det innebär att upprepade anrop till samma aktörs-ID dirigeras till samma aktörs instans. I ett tillstånds lösa system är det däremot inte säkert att klient samtal dirigeras till samma server varje gång. Av den anledningen är aktörs tjänsterna alltid tillstånds känsliga tjänster.

Även om aktörer betraktas som tillstånds känsliga, så innebär det inte att de måste lagra tillstånd på ett tillförlitligt sätt. Aktörer kan välja tillstånds nivån och replikeringens nivå utifrån deras krav på data lagring:

* **Beständigt tillstånd**: status är bestående av disk och replikeras till tre eller fler repliker. Beständigt tillstånd är det mest varaktiga lagrings alternativet och där tillstånd kan behållas genom fullständigt kluster avbrott.
* **Volatile-tillstånd**: status replikeras till tre eller flera repliker och lagras endast i minnet. Flyktigt tillstånd ger en återhämtning mot nodfel och allokeringsfel, samt under uppgraderingar och resurs utjämning. Tillstånd är dock inte kvar på disken. Så om alla repliker går förlorade samtidigt, försvinner även statusen.
* **Inget beständigt tillstånd**: tillstånd är inte replikerat eller skrivs till disk, används bara för aktörer som inte behöver underhålla tillstånden på ett tillförlitligt sätt.

Varje nivå av persistence är helt enkelt en annan *tillstånds leverantör* och *replikeringskonfiguration* för din tjänst. Huruvida tillstånd skrivs till disk beror på tillstånds leverantören – komponenten i en tillförlitlig tjänst som lagrar tillstånd. Replikeringen beror på hur många repliker en tjänst distribueras med. Precis som med Reliable Services kan både tillstånds leverantören och replik antalet enkelt anges manuellt. Aktörs ramverket innehåller ett attribut som, när det används på en aktör, automatiskt väljer en standardprovider och automatiskt genererar inställningar för replik antal för att uppnå någon av dessa tre persistence-inställningar. Attributet StatePersistence ärvs inte av en härledd klass, varje aktörs typ måste ange sin StatePersistence-nivå.

### <a name="persisted-state"></a>Beständigt tillstånd
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Den här inställningen använder en tillstånds leverantör som lagrar data på disk och ställer automatiskt in antalet tjänst repliker på 3.

### <a name="volatile-state"></a>Temporärt tillstånd
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Med den här inställningen används en InMemory-enda tillstånds leverantör och antalet repliker anges till 3.

### <a name="no-persisted-state"></a>Inget beständigt tillstånd
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Med den här inställningen används en InMemory-enda tillstånds leverantör och antalet repliker anges till 1.

### <a name="defaults-and-generated-settings"></a>Standardinställningar och genererade inställningar
När du använder `StatePersistence` -attributet väljs en tillstånds leverantör automatiskt åt dig vid körning när aktörs tjänsten startar. Replik antalet anges dock i kompileringen av build-verktygen för Visual Studio-skådespelare. Build-verktygen genererar automatiskt en *standard tjänst* för aktörs tjänsten i ApplicationManifest.xml. Parametrar skapas för den **minsta replik uppsättningens storlek** och **storlek för mål replik uppsättningen**.

Du kan ändra parametrarna manuellt. Men varje gången `StatePersistence` attributet ändras, anges parametrarna som standardvärden för replik uppsättnings storlek för det valda `StatePersistence` attributet, vilket åsidosätter eventuella tidigare värden. Med andra ord åsidosätts de värden som du anger i ServiceManifest.xml *bara* vid Bygg tiden när du ändrar `StatePersistence` attributvärdet.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Tillstånds hanterare
Varje aktörs instans har sin egen tillstånds ansvarig: en ord lista som är en data struktur som på ett tillförlitligt sätt lagrar nyckel/värde-par. Tillstånds hanteraren är en omslutning runt en tillstånds leverantör. Du kan använda den för att lagra data oavsett vilken beständighets inställning som används. Det ger inga garantier som en tjänst som kör aktören kan ändra från en temporär tillstånds inställning (endast i minnet) till en beständig tillstånds inställning genom en löpande uppgradering och bevara data. Det är dock möjligt att ändra antalet repliker för en tjänst som körs.

Tillstånds hanterarens nycklar måste vara strängar. Värdena är generiska och kan vara vilken typ som helst, inklusive anpassade typer. Värden som lagras i tillstånds hanteraren måste vara serialiserbar för data kontrakt eftersom de kan överföras via nätverket till andra noder under replikeringen och kan skrivas till disk, beroende på en aktörs inställning för tillstånds persistence.

Tillstånds hanteraren visar gemensamma ordboks metoder för att hantera tillstånd, liknande de som finns i en tillförlitlig ord lista.

Exempel på hur du hanterar aktörs tillstånd, Läs [åtkomst, Spara och ta bort Reliable Actorss tillstånd](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Bästa praxis
Här följer några förslag på metoder och fel söknings tips för att hantera din aktörs tillstånd.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Gör aktörens tillstånd så detaljerad som möjligt
Detta är avgörande för prestanda och resursanvändning för ditt program. När det finns en Skriv-/uppdatering av "namngivet tillstånd" för en aktör serialiseras hela värdet som motsvarar det "namngivna tillstånd" och skickas över nätverket till de sekundära replikerna.  Sekundärerna skriver den till en lokal disk och svarar tillbaka till den primära repliken. När den primära tar emot bekräftelser från ett kvorum med sekundära repliker, skriver den till den lokala disken. Anta till exempel att värdet är en klass som har 20 medlemmar och en storlek på 1 MB. Även om du bara har ändrat en av klass medlemmarna som har en storlek på 1 KB, betalar du kostnaden för serialisering och nätverks-och disk skrivningar för hela 1 MB. Om värdet är en samling (t. ex. en lista, matris eller ord lista) betalar du kostnaden för den fullständiga samlingen även om du ändrar en av dess medlemmar. StateManager-gränssnittet i aktörs klassen är som en ord lista. Du bör alltid modellera data strukturen som representerar aktörs status ovanpå den här ord listan.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Hantera aktörens livs cykel korrekt
Du bör ha rensat principen för att hantera storleken på tillstånd i varje partition av en aktörs tjänst. Aktörs tjänsten bör ha ett fast antal aktörer och återanvända dem så mycket som möjligt. Om du skapar nya aktörer kontinuerligt måste du ta bort dem när de är klara med sitt arbete. Aktörs ramverket lagrar metadata om varje aktör som finns. Om du tar bort alla tillstånd för en aktör tas inte metadata bort från aktören. Du måste ta bort aktören (se [ta bort aktörer och deras status](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)) om du vill ta bort all information som lagras i systemet. Som en extra kontroll bör du fråga aktörs tjänsten (se [räkna upp skådespelare](service-fabric-reliable-actors-enumerate.md)) en gång i taget för att se till att antalet aktörer ligger inom det förväntade intervallet.
 
Om du någonsin ser att databas filens storlek för en aktörs tjänst ökar utöver den förväntade storleken ser du till att du följer de föregående rikt linjerna. Om du följer dessa rikt linjer och fortfarande råkar ut för problem med databas fil storlek bör du [öppna ett support ärende](service-fabric-support.md) med produkt teamet för att få hjälp.

## <a name="next-steps"></a>Nästa steg

Tillstånd som lagras i Reliable Actors måste serialiseras innan den skrivs till disk och replikeras för hög tillgänglighet. Läs mer om [typ serialisering för aktör](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Läs sedan mer om [aktörs diagnostik och prestanda övervakning](service-fabric-reliable-actors-diagnostics.md).
