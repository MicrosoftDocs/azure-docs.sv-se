---
title: Ändra inställningar för ReliableDictionaryActorStateProvider
description: Lär dig mer om att konfigurera Azure-Service Fabric tillstånds känsliga aktörer av typen ReliableDictionaryActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: fbd6f7cd3ade753c659464522408aa715cce48f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75609748"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Konfigurera Reliable Actors--ReliableDictionaryActorStateProvider
Du kan ändra standard konfigurationen för ReliableDictionaryActorStateProvider genom att ändra settings.xml-filen som genereras i Visual Studio-paket roten under mappen config för den angivna aktören.

Azure Service Fabric runtime söker efter fördefinierade avsnitts namn i settings.xml-filen och använder konfigurations värden när de underliggande kör komponenterna skapas.

> [!NOTE]
> Ta **inte** bort eller ändra avsnitts namnen för följande konfigurationer i settings.xml-filen som genereras i Visual Studio-lösningen.
> 
> 

Det finns också globala inställningar som påverkar konfigurationen av ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Global konfiguration
Den globala konfigurationen anges i kluster manifestet för klustret i avsnittet KtlLogger. Den tillåter konfiguration av den delade logg platsen och storlek plus de globala minnes gränserna som används av loggaren. Observera att ändringar i kluster manifestet påverkar alla tjänster som använder ReliableDictionaryActorStateProvider och tillförlitliga tillstånds känsliga tjänster.

Kluster manifestet är en enskild XML-fil som innehåller inställningar och konfigurationer som gäller för alla noder och tjänster i klustret. Filen kallas vanligt vis för ClusterManifest.xml. Du kan se kluster manifestet för klustret med hjälp av kommandot Get-ServiceFabricClusterManifest PowerShell.

### <a name="configuration-names"></a>Konfigurations namn
| Name | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobyte |8388608 |Minsta antal KB som ska allokeras i kernel-läge för loggen för Write buffer-anslutningspoolen. Den här mediepoolen används för cachelagring av statusinformation innan den skrivs till disken. |
| WriteBufferMemoryPoolMaximumInKB |Kilobyte |Obegränsat |Maximal storlek som mediepoolen för logg skrivnings buffert kan växa till. |
| SharedLogId |GUID |"" |Anger ett unikt GUID som ska användas för att identifiera den delade standard logg filen som används av alla pålitliga tjänster på alla noder i klustret som inte anger SharedLogId i deras tjänstspecifika konfiguration. Om SharedLogId anges måste även SharedLogPath anges. |
| SharedLogPath |Fullständigt kvalificerat Sök vägs namn |"" |Anger den fullständiga sökvägen till den delade logg filen som används av alla pålitliga tjänster på alla noder i klustret som inte anger SharedLogPath i deras tjänstspecifika konfiguration. Men om SharedLogPath anges måste SharedLogId också anges. |
| SharedLogSizeInMB |Megabyte |8192 |Anger antalet MB disk utrymme som ska allokeras statiskt för den delade loggen. Värdet måste vara 2048 eller större. |

### <a name="sample-cluster-manifest-section"></a>Avsnittet exempel på kluster manifest
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Kommentarer
Loggaren har en global pool av minne som tilldelas från icke-växlat kernel-minne som är tillgängligt för alla pålitliga tjänster på en nod för cachelagring av tillstånds data innan de skrivs till den dedikerade logg som är associerad med tillförlitlig tjänst replik. Poolens storlek styrs av inställningarna WriteBufferMemoryPoolMinimumInKB och WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB anger både den ursprungliga storleken på den här lagringspoolen och den lägsta storlek som mediepoolen kan krympa till. WriteBufferMemoryPoolMaximumInKB är den högsta storleken som mediepoolen kan växa till. Varje tillförlitlig tjänst replik som har öppnats kan öka storleken på minnesbufferten genom ett fastställt system som är upp till WriteBufferMemoryPoolMaximumInKB. Om det finns mer efter frågan på minne från mediepoolen än vad som är tillgängligt, kommer begär Anden för minnet att fördröjas tills minnet är tillgängligt. Om lagringspoolen för skrivcache är för liten för en viss konfiguration kan prestanda försämras.

Inställningarna SharedLogId och SharedLogPath används alltid tillsammans för att definiera GUID och platsen för den delade standard loggen för alla noder i klustret. Den delade standard loggen används för alla pålitliga tjänster som inte anger inställningarna i settings.xml för den aktuella tjänsten. För bästa prestanda bör delade loggfiler placeras på diskar som endast används för den delade logg filen för att minska konkurrens.

SharedLogSizeInMB anger mängden disk utrymme som ska förallokeras för den delade standard loggen på alla noder.  SharedLogId och SharedLogPath behöver inte anges för att SharedLogSizeInMB ska kunna anges.

## <a name="replicator-security-configuration"></a>Säkerhets konfiguration för Replicator
Replicators säkerhetskonfigurationer används för att skydda kommunikations kanalen som används vid replikering. Det innebär att tjänsterna inte kan se var and ras replikeringstrafik, vilket säkerställer att de data som har gjorts med hög tillgänglighet också är säkra.
Som standard förhindrar ett tomt säkerhets konfigurations avsnitt replikeringen av säkerheten.

> [!IMPORTANT]
> På Linux-noder måste certifikaten vara PEM-formaterade. Mer information om hur du hittar och konfigurerar certifikat för Linux finns i [Konfigurera certifikat i Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Avsnitts namn
&lt;ActorName &gt; ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Konfiguration av replikerare
Konfigurationer för duplicering används för att konfigurera den ansvarige som ansvarar för att göra aktörs leverantörens tillstånd hög tillförlitlig genom att replikera och bevara tillstånd lokalt.
Standard konfigurationen genereras av Visual Studio-mallen och bör vara tillräckligt. I det här avsnittet beskrivs ytterligare konfigurationer som är tillgängliga för att finjustera replikeringen.

### <a name="section-name"></a>Avsnitts namn
&lt;ActorName &gt; ServiceReplicatorConfig

### <a name="configuration-names"></a>Konfigurations namn
| Name | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekunder |0,015 |Den tids period som replikeraren på den sekundära väntar efter att ha tagit emot en åtgärd innan en bekräftelse skickas till den primära. Alla andra bekräftelser som ska skickas för åtgärder som bearbetas inom detta intervall skickas som ett svar. |
| ReplicatorEndpoint |Ej tillämpligt |Ingen standard-obligatorisk parameter |IP-adress och port som den primära/sekundära replikeraren ska använda för att kommunicera med andra replikeringar i replik uppsättningen. Detta bör referera till en slut punkt för en TCP-resurs i tjänst manifestet. Läs mer om hur du definierar slut punkts resurser i tjänst manifestet i [tjänst manifest resurser](service-fabric-service-manifest-resources.md) . |
| MaxReplicationMessageSize |Byte |50 MB |Maximal storlek på replikeringsdata som kan överföras i ett enda meddelande. |
| MaxPrimaryReplicationQueueSize |Antal åtgärder |8192 |Maximalt antal åtgärder i den primära kön. En åtgärd frigörs efter att den primära replikeraren får en bekräftelse från alla sekundära replikeringar. Värdet måste vara större än 64 och en potens på 2. |
| MaxSecondaryReplicationQueueSize |Antal åtgärder |16384 |Maximalt antal åtgärder i den sekundära kön. En åtgärd frigörs när den har gjort sitt tillstånd hög tillgängligt genom persistence. Värdet måste vara större än 64 och en potens på 2. |
| CheckpointThresholdInMB |MB |200 |Mängden logg fils utrymme som används för att ange en kontroll punkt. |
| MaxRecordSizeInKB |KB |1024 |Största post storlek som replikeraren kan skriva i loggen. Värdet måste vara en multipel av 4 och större än 16. |
| OptimizeLogForLowerDiskUsage |Boolesk |true |Om värdet är true konfigureras loggen så att replikens dedikerade loggfil skapas med hjälp av en NTFS-sparse-fil. Detta minskar den faktiska disk utrymmes användningen för filen. Om värdet är false skapas filen med fasta allokeringar, vilket ger bästa möjliga skriv prestanda. |
| SharedLogId |guid |"" |Anger ett unikt GUID som ska användas för att identifiera den delade logg filen som används med den här repliken. Normalt bör inte tjänsterna använda den här inställningen. Men om SharedLogId anges måste SharedLogPath också anges. |
| SharedLogPath |Fullständigt kvalificerat Sök vägs namn |"" |Anger den fullständiga sökvägen dit den delade logg filen för repliken kommer att skapas. Normalt bör inte tjänsterna använda den här inställningen. Men om SharedLogPath anges måste SharedLogId också anges. |

## <a name="sample-configuration-file"></a>Exempelkonfigurationsfil
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
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

Parametern CheckpointThresholdInMB styr mängden disk utrymme som replikeraren kan använda för att lagra tillståndsinformation i replikens dedikerade loggfil. Om du ökar värdet till ett högre värde än standardinställningen kan det leda till snabbare omkonfigurations tider när en ny replik läggs till i uppsättningen. Detta beror på den partiella tillstånds överföring som äger rum på grund av tillgängligheten för mer historik för åtgärder i loggen. Detta kan eventuellt öka återställnings tiden för en replik efter en krasch.

Om du ställer in OptimizeForLowerDiskUsage på sant, allokeras logg fils utrymmet för att aktiva repliker ska kunna lagra mer tillståndsinformation i loggfilerna, medan inaktiva repliker använder mindre disk utrymme. Detta gör det möjligt att vara värd för fler repliker på en nod. Om du anger OptimizeForLowerDiskUsage till false skrivs tillståndsinformation snabbare till loggfilerna.

Inställningen MaxRecordSizeInKB definierar den maximala storleken för en post som kan skrivas av replikmappen i logg filen. I de flesta fall är standard post storleken på 1024 KB optimal. Men om tjänsten gör att större data objekt ska ingå i tillståndsinformation, kan det här värdet behöva ökas. Det finns lite förmån för att göra MaxRecordSizeInKB mindre än 1024 eftersom mindre poster bara använder det utrymme som krävs för den mindre posten. Vi förväntar oss att det här värdet bara skulle behöva ändras i sällsynta fall.

Inställningarna SharedLogId och SharedLogPath används alltid tillsammans för att en tjänst ska kunna använda en separat delad logg från den delade standard loggen för noden. För bästa möjliga effektivitet bör så många tjänster som möjligt ange samma delade logg. Delade loggfiler bör placeras på diskar som enbart används för den delade logg filen för att minska konkurrens för huvud rörelse. Vi räknar med att dessa värden bara skulle behöva ändras i sällsynta fall.

