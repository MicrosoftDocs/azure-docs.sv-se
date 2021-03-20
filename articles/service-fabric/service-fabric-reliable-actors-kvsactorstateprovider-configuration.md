---
title: Ändra inställningar för KVSActorStateProvider
description: Lär dig mer om att konfigurera Azure-Service Fabric tillstånds känsliga aktörer av typen KVSActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: cdb115bd57cf3d5af4388f4efa03c2522feef9ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75609782"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Konfigurera Reliable Actors--KVSActorStateProvider
Du kan ändra standard konfigurationen för KVSActorStateProvider genom att ändra settings.xml-filen som genereras i Microsoft Visual Studio-paket roten under mappen config för den angivna aktören.

Azure Service Fabric runtime söker efter fördefinierade avsnitts namn i settings.xml-filen och använder konfigurations värden när de underliggande kör komponenterna skapas.

> [!NOTE]
> Ta **inte** bort eller ändra avsnitts namnen för följande konfigurationer i settings.xml-filen som genereras i Visual Studio-lösningen.
> 
> 

## <a name="replicator-security-configuration"></a>Säkerhets konfiguration för Replicator
Replicators säkerhetskonfigurationer används för att skydda kommunikations kanalen som används vid replikering. Det innebär att tjänster inte kan se var and ras replikeringstrafik, vilket säkerställer att de data som har gjorts med hög tillgänglighet också är säkra.
Som standard förhindrar ett tomt säkerhets konfigurations avsnitt replikeringen av säkerheten.

> [!IMPORTANT]
> På Linux-noder måste certifikaten vara PEM-formaterade. Mer information om hur du hittar och konfigurerar certifikat för Linux finns i [Konfigurera certifikat i Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Avsnitts namn
&lt;ActorName &gt; ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Konfiguration av replikerare
Konfigurationer för duplicering konfigurerar den ansvariga för att göra providerns tillstånd för aktörs status mycket tillförlitligt.
Standard konfigurationen genereras av Visual Studio-mallen och bör vara tillräckligt. I det här avsnittet beskrivs ytterligare konfigurationer som är tillgängliga för att finjustera replikeringen.

### <a name="section-name"></a>Avsnitts namn
&lt;ActorName &gt; ServiceReplicatorConfig

### <a name="configuration-names"></a>Konfigurations namn
| Name | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekunder |0,015 |Den tids period som replikeraren på den sekundära väntar efter att ha tagit emot en åtgärd innan en bekräftelse skickas till den primära. Alla andra bekräftelser som ska skickas för åtgärder som bearbetas inom detta intervall skickas som ett svar. |
| ReplicatorEndpoint |Ej tillämpligt |Ingen standard-obligatorisk parameter |IP-adress och port som den primära/sekundära replikeraren ska använda för att kommunicera med andra replikeringar i replik uppsättningen. Detta bör referera till en slut punkt för en TCP-resurs i tjänst manifestet. Se [tjänst manifest resurser](service-fabric-service-manifest-resources.md) för att läsa mer om hur du definierar slut punkts resurser i tjänst manifestet. |
| RetryInterval |Sekunder |5 |Tids period efter vilken rereplikeringen skickar ett meddelande igen om den inte får någon bekräftelse för en åtgärd. |
| MaxReplicationMessageSize |Byte |50 MB |Maximal storlek på replikeringsdata som kan överföras i ett enda meddelande. |
| MaxPrimaryReplicationQueueSize |Antal åtgärder |1024 |Maximalt antal åtgärder i den primära kön. En åtgärd frigörs efter att den primära replikeraren får en bekräftelse från alla sekundära replikeringar. Värdet måste vara större än 64 och en potens på 2. |
| MaxSecondaryReplicationQueueSize |Antal åtgärder |2048 |Maximalt antal åtgärder i den sekundära kön. En åtgärd frigörs när den har gjort sitt tillstånd hög tillgängligt genom persistence. Värdet måste vara större än 64 och en potens på 2. |

## <a name="store-configuration"></a>Lagra konfiguration
Store-konfigurationer används för att konfigurera det lokala arkivet som används för att spara det tillstånd som replikeras.
Standard konfigurationen genereras av Visual Studio-mallen och bör vara tillräckligt. Det här avsnittet innehåller information om ytterligare konfigurationer som är tillgängliga för att justera det lokala arkivet.

### <a name="section-name"></a>Avsnitts namn
&lt;ActorName &gt; ServiceLocalStoreConfig

### <a name="configuration-names"></a>Konfigurations namn
| Name | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Millisekunder |200 |Anger det maximala antalet batching-intervall för beständiga lokala arkiv-skrivningar. |
| MaxVerPages |Antal sidor |16384 |Det maximala antalet versions sidor i den lokala lagrings databasen. Den fastställer det maximala antalet utestående transaktioner. |

## <a name="sample-configuration-file"></a>Exempelkonfigurationsfil
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```
## <a name="remarks"></a>Kommentarer
Parametern BatchAcknowledgementInterval styr svars tiden för replikering. Värdet "0" resulterar i lägsta möjliga svars tid, med kostnaden för data flödet (som fler bekräftelse meddelanden måste skickas och bearbetas, var och en innehåller färre bekräftelser).
Ju större värde för BatchAcknowledgementInterval, desto högre det övergripande antalet data flöde för replikering, till kostnaden för högre åtgärds fördröjning. Detta översätter direkt till svars tiden för transaktions incheckningar.

