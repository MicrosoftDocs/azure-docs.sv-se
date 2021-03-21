---
title: Tillförlitlig serialisering av samlings objekt
description: Lär dig mer om Azure Service Fabric Reliable Collections-objekt serialisering, inklusive standard strategin och hur du definierar anpassad serialisering.
ms.topic: conceptual
ms.date: 5/8/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 29bb9a2dfb028d223d63559b35735e78d7e6bcf8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784367"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Tillförlitlig serialisering av samlings objekt i Azure Service Fabric
Reliable Collections replikerar och bevarar sina objekt för att se till att de är varaktiga över maskin haverier och strömavbrott.
Både för att replikera och spara objekt, måste pålitliga samlingar serialisera dem.

Reliable Collections hämta lämplig serialisering för en specifik typ från en pålitlig tillstånds hanterare.
Reliable State Manager innehåller inbyggda serialiserare och gör det möjligt att registrera anpassade serialiserare för en specifik typ.

## <a name="built-in-serializers"></a>Inbyggda serialiserare

Reliable State Manager innehåller inbyggd serialisering för några vanliga typer, så att de kan serialiseras effektivt som standard. För andra typer är Reliable State Manager tillbaka för att använda [DataContractSerializer](/dotnet/api/system.runtime.serialization.datacontractserializer).
Inbyggda serialiserare är mer effektiva eftersom de vet att deras typer inte kan ändras och att de inte behöver inkludera information om typen, t. ex. typ namn.

En tillförlitlig tillstånds hanterare har inbyggd serialisering för följande typer: 
- GUID
- boolesk
- stor
- SByte
- byte[]
- char
- sträng
- decimal
- double
- flyt
- int
- uint
- long
- ulong
- short
- ushort

## <a name="custom-serialization"></a>Anpassad serialisering

Anpassade serialiserare används ofta för att öka prestandan eller för att kryptera data via kabeln och på disk. Bland annat är anpassade serialiserare ofta mer effektiva än allmän serialisering eftersom de inte behöver serialisera information om typen. 

[IReliableStateManager. TryAddStateSerializer \<T> ](/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) används för att registrera en anpassad serialiserare för den aktuella typen T. Registreringen bör ske i StatefulServiceBase för att säkerställa att innan återställningen startar, har alla pålitliga samlingar åtkomst till den relevanta serialiseraren för att läsa sina sparade data.

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> Anpassade serialiserare prioriteras framför inbyggda serialiserare. Till exempel, när en anpassad serialisering för int registreras, används den för att serialisera heltal i stället för den inbyggda serialiseraren för int.

### <a name="how-to-implement-a-custom-serializer"></a>Implementera en anpassad serialiserare

En anpassad serialisering måste implementera [IStateSerializer \<T> ](/dotnet/api/microsoft.servicefabric.data.istateserializer-1) -gränssnittet.

> [!NOTE]
> IStateSerializer \<T> innehåller en överlagring för skrivning och läsning som tar i ett ytterligare T-kallat bas värde. Detta API är för differentiell serialisering. Funktionen för för närvarande differentiell serialisering visas inte. Dessa två överlagringar anropas därför inte förrän differentiell serialisering exponeras och aktive ras.

Följande är ett exempel på en anpassad typ som heter OrderKey som innehåller fyra egenskaper

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

Följande är ett exempel på en implementering av IStateSerializer \<OrderKey> .
Observera att Läs-och skriv överföringar som tar i baseValue anropar deras respektive överlagring för vidarebefordring av kompatibilitet.

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>Försämring
I en [uppgradering av rullande program](service-fabric-application-upgrade.md)tillämpas uppgraderingen på en delmängd noder, en uppgraderings domän i taget. Under den här processen kommer vissa uppgraderings domäner att finnas på den nyare versionen av programmet och vissa uppgraderings domäner kommer att finnas på den äldre versionen av programmet. Under distributionen måste den nya versionen av programmet kunna läsa den gamla versionen av dina data och den gamla versionen av programmet måste kunna läsa den nya versionen av dina data. Om data formatet inte vidarebefordras och är bakåtkompatibelt, kan uppgraderingen Miss lyckas eller vara sämre, data kan gå förlorade eller skadas.

Om du använder inbyggd serialisering behöver du inte bekymra dig om kompatibilitet.
Men om du använder en anpassad serialiserare eller DataContractSerializer måste data vara oändligt baklänges och vidarebefordras.
Med andra ord måste varje version av serialiseraren kunna serialisera och deserialisera alla versioner av typen.

Data kontrakts användare bör följa de väldefinierade versions reglerna för att lägga till, ta bort och ändra fält. Data kontrakt har också stöd för att hantera okända fält, ansluta till serialiserings-och avserialiserings processen och hantera klass arvet. Mer information finns i [använda data kontrakt](/dotnet/framework/wcf/feature-details/using-data-contracts).

Anpassade serialiserare måste följa rikt linjerna för den serialiserare som de använder för att kontrol lera att de är baklänges och vidarebefordrar kompatibla.
Vanliga sätt att stödja alla versioner är att lägga till storleks information i början och bara lägga till valfria egenskaper.
På så sätt kan varje version läsa så mycket som möjligt och hoppa över den återstående delen av data strömmen.

## <a name="next-steps"></a>Nästa steg
  * [Serialisering och uppgradering](service-fabric-application-upgrade-data-serialization.md)
  * [Referens för utvecklare för tillförlitliga samlingar](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
  * Genom [att uppgradera programmet med Visual Studio](service-fabric-application-upgrade-tutorial.md) går du igenom en program uppgradering med Visual Studio.
  * Genom [att uppgradera ditt program med hjälp av PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) får du en program uppgradering med PowerShell.
  * Styr hur programmet uppgraderas med hjälp av [uppgraderings parametrar](service-fabric-application-upgrade-parameters.md).
  * Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att titta på [avancerade ämnen](service-fabric-application-upgrade-advanced.md).
  * Åtgärda vanliga problem i program uppgraderingar genom att följa stegen i [Felsöka program uppgraderingar](service-fabric-application-upgrade-troubleshooting.md).
