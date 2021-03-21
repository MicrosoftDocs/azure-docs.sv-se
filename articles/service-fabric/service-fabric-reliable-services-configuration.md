---
title: Konfigurera Azure Service Fabric Reliable Services
description: Lär dig mer om att konfigurera tillstånds känsliga Reliable Services i ett Azure Service Fabric-program globalt och för en enskild tjänst.
author: sumukhs
ms.topic: conceptual
ms.date: 10/02/2017
ms.author: sumukhs
ms.custom: devx-track-csharp
ms.openlocfilehash: cda0a9f988afae58a60bff051885a5eec8afe434
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023592"
---
# <a name="configure-stateful-reliable-services"></a>Konfigurera tillstånds känsliga Reliable Services
Det finns två uppsättningar konfigurations inställningar för Reliable Services. En uppsättning är global för alla pålitliga tjänster i klustret medan den andra uppsättningen är specifik för en viss tillförlitlig tjänst.

## <a name="global-configuration"></a>Global konfiguration
Den globala pålitliga tjänst konfigurationen anges i kluster manifestet för klustret under avsnittet KtlLogger. Den tillåter konfiguration av den delade logg platsen och storlek plus de globala minnes gränserna som används av loggaren. Kluster manifestet är en enskild XML-fil som innehåller inställningar och konfigurationer som gäller för alla noder och tjänster i klustret. Filen kallas vanligt vis för ClusterManifest.xml. Du kan se kluster manifestet för klustret med hjälp av kommandot Get-ServiceFabricClusterManifest PowerShell.

### <a name="configuration-names"></a>Konfigurations namn
| Name | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobyte |8388608 |Minsta antal KB som ska allokeras i kernel-läge för loggen för Write buffer-anslutningspoolen. Den här mediepoolen används för cachelagring av statusinformation innan den skrivs till disken. |
| WriteBufferMemoryPoolMaximumInKB |Kilobyte |Obegränsat |Maximal storlek som mediepoolen för logg skrivnings buffert kan växa till. |
| SharedLogId |GUID |"" |Anger ett unikt GUID som ska användas för att identifiera den delade standard logg filen som används av alla pålitliga tjänster på alla noder i klustret som inte anger SharedLogId i deras tjänstspecifika konfiguration. Om SharedLogId anges måste även SharedLogPath anges. |
| SharedLogPath |Fullständigt kvalificerat Sök vägs namn |"" |Anger den fullständiga sökvägen till den delade logg filen som används av alla pålitliga tjänster på alla noder i klustret som inte anger SharedLogPath i deras tjänstspecifika konfiguration. Men om SharedLogPath anges måste SharedLogId också anges. |
| SharedLogSizeInMB |Megabyte |8192 |Anger antalet MB disk utrymme som ska allokeras statiskt för den delade loggen. Värdet måste vara 2048 eller större. |

I exemplet nedan i Azure ARM eller lokal JSON-mall visas hur du ändrar den delade transaktions loggen som skapas för att återställa pålitliga samlingar för tillstånds känsliga tjänster.

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="sample-local-developer-cluster-manifest-section"></a>Exempel avsnitt för lokalt utvecklare-kluster
Om du vill ändra detta i din lokala utvecklings miljö måste du redigera den lokala clustermanifest.xmls filen.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### <a name="remarks"></a>Kommentarer
Loggaren har en global pool av minne som tilldelas från icke-växlat kernel-minne som är tillgängligt för alla pålitliga tjänster på en nod för cachelagring av tillstånds data innan de skrivs till den dedikerade logg som är associerad med tillförlitlig tjänst replik. Poolens storlek styrs av inställningarna WriteBufferMemoryPoolMinimumInKB och WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB anger både den ursprungliga storleken på den här lagringspoolen och den lägsta storlek som mediepoolen kan krympa till. WriteBufferMemoryPoolMaximumInKB är den högsta storleken som mediepoolen kan växa till. Varje tillförlitlig tjänst replik som har öppnats kan öka storleken på minnesbufferten genom ett fastställt system som är upp till WriteBufferMemoryPoolMaximumInKB. Om det finns mer efter frågan på minne från mediepoolen än vad som är tillgängligt, kommer begär Anden för minnet att fördröjas tills minnet är tillgängligt. Om lagringspoolen för skrivcache är för liten för en viss konfiguration kan prestanda försämras.

Inställningarna SharedLogId och SharedLogPath används alltid tillsammans för att definiera GUID och platsen för den delade standard loggen för alla noder i klustret. Den delade standard loggen används för alla pålitliga tjänster som inte anger inställningarna i settings.xml för den aktuella tjänsten. För bästa prestanda bör delade loggfiler placeras på diskar som endast används för den delade logg filen för att minska konkurrens.

SharedLogSizeInMB anger mängden disk utrymme som ska förallokeras för den delade standard loggen på alla noder.  SharedLogId och SharedLogPath behöver inte anges för att SharedLogSizeInMB ska kunna anges.

## <a name="service-specific-configuration"></a>Tjänstspecifik konfiguration
Du kan ändra tillstånds känsliga Reliable Services standardkonfigurationer med hjälp av konfigurations paketet (config) eller tjänst implementeringen (kod).

* **Konfiguration – konfiguration** via konfigurations paketet görs genom att ändra Settings.xml-filen som genereras i Microsoft Visual Studio-paket roten under mappen config för varje tjänst i programmet.
* **Kod**   – konfiguration via kod görs genom att skapa en ReliableStateManager med hjälp av ett ReliableStateManagerConfiguration-objekt med lämpliga alternativ angivna.

Som standard söker Azure Service Fabric runtime efter fördefinierade avsnitts namn i Settings.xml-filen och använder konfigurations värden när de underliggande kör komponenterna skapas.

> [!NOTE]
> Ta **inte** bort avsnitts namnen för följande konfigurationer i Settings.xml-filen som genereras i Visual Studio-lösningen om du inte planerar att konfigurera tjänsten via kod.
> Att byta namn på konfigurations paketets eller avsnittets namn kräver en kod ändring när du konfigurerar ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Säkerhets konfiguration för Replicator
Replicators säkerhetskonfigurationer används för att skydda kommunikations kanalen som används vid replikering. Det innebär att tjänsterna inte kan se var and ras replikeringstrafik, vilket säkerställer att de data som görs med hög tillgänglighet också är säkra. Som standard förhindrar ett tomt säkerhets konfigurations avsnitt replikeringen av säkerheten.

> [!IMPORTANT]
> På Linux-noder måste certifikaten vara PEM-formaterade. Mer information om hur du hittar och konfigurerar certifikat för Linux finns i [Konfigurera certifikat i Linux](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Standard avsnitts namn
ReplicatorSecurityConfig

> [!NOTE]
> Om du vill ändra det här avsnitts namnet åsidosätter du parametern replicatorSecuritySectionName till ReliableStateManagerConfiguration-konstruktorn när du skapar ReliableStateManager för den här tjänsten.
> 
> 

### <a name="replicator-configuration"></a>Konfiguration av replikerare
Konfigurationer för duplicering konfigurerar den ansvarige som ansvarar för att göra tillstånds känsliga Reliable service-tillstånd hög tillförlitlig genom att replikera och bevara tillstånd lokalt.
Standard konfigurationen genereras av Visual Studio-mallen och bör vara tillräckligt. I det här avsnittet beskrivs ytterligare konfigurationer som är tillgängliga för att finjustera replikeringen.

### <a name="default-section-name"></a>Standard avsnitts namn
ReplicatorConfig

> [!NOTE]
> Om du vill ändra det här avsnitts namnet åsidosätter du parametern replicatorSettingsSectionName till ReliableStateManagerConfiguration-konstruktorn när du skapar ReliableStateManager för den här tjänsten.
> 
> 

### <a name="configuration-names"></a>Konfigurations namn
| Name | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekunder |0,015 |Den tids period som replikeraren på den sekundära väntar efter att ha tagit emot en åtgärd innan en bekräftelse skickas till den primära. Alla andra bekräftelser som ska skickas för åtgärder som bearbetas inom detta intervall skickas som ett svar. |
| ReplicatorEndpoint |Ej tillämpligt |Ingen standard-obligatorisk parameter |IP-adress och port som den primära/sekundära replikeraren ska använda för att kommunicera med andra replikeringar i replik uppsättningen. Detta bör referera till en slut punkt för en TCP-resurs i tjänst manifestet. Se [tjänst manifest resurser](service-fabric-service-manifest-resources.md) för att läsa mer om hur du definierar slut punkts resurser i ett tjänst manifest. |
| MaxPrimaryReplicationQueueSize |Antal åtgärder |8192 |Maximalt antal åtgärder i den primära kön. En åtgärd frigörs efter att den primära replikeraren får en bekräftelse från alla sekundära replikeringar. Värdet måste vara större än 64 och en potens på 2. |
| MaxSecondaryReplicationQueueSize |Antal åtgärder |16384 |Maximalt antal åtgärder i den sekundära kön. En åtgärd frigörs när den har gjort sitt tillstånd hög tillgängligt genom persistence. Värdet måste vara större än 64 och en potens på 2. |
| CheckpointThresholdInMB |MB |50 |Mängden logg fils utrymme som används för att ange en kontroll punkt. |
| MaxRecordSizeInKB |KB |1024 |Största post storlek som replikeraren kan skriva i loggen. Värdet måste vara en multipel av 4 och större än 16. |
| MinLogSizeInMB |MB |0 (systemet bestäms) |Minimi storlek på transaktions loggen. Loggen får inte trunkeras till en storlek under den här inställningen. 0 anger att replikeringen ska bestämma den minsta logg storleken. Om du ökar det här värdet ökar risken för partiella kopior och stegvisa säkerhets kopieringar eftersom det är möjligt att minska antalet relevanta logg poster som trunkeras. |
| TruncationThresholdFactor |Faktor |2 |Anger om loggens storlek ska avtrunken utlösas. Tröskelvärdet för trunkering bestäms av MinLogSizeInMB multiplicerat med TruncationThresholdFactor. TruncationThresholdFactor måste vara större än 1. MinLogSizeInMB * TruncationThresholdFactor måste vara mindre än MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |Faktor |4 |Anger när loggens storlek ska begränsas. Tröskelvärdet för begränsning (i MB) bestäms av Max ((MinLogSizeInMB * ThrottlingThresholdFactor), (CheckpointThresholdInMB * ThrottlingThresholdFactor)). Tröskelvärdet för begränsning (i MB) måste vara högre än tröskelvärdet för trunkering (i MB). Tröskelvärdet för trunkering (i MB) måste vara mindre än MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Maximal ackumulerad storlek (i MB) för säkerhets kopierings loggar i en specifik logg kedja för säkerhets kopiering. Det går inte att utföra en stegvis säkerhets kopiering om den stegvisa säkerhets kopieringen skulle generera en säkerhets kopierings logg som orsakar de ackumulerade säkerhets kopierings loggarna sedan den aktuella fullständiga säkerhets kopian skulle vara större än den här storleken. I sådana fall måste användaren göra en fullständig säkerhets kopia. |
| SharedLogId |GUID |"" |Anger ett unikt GUID som ska användas för att identifiera den delade logg filen som används med den här repliken. Normalt bör inte tjänsterna använda den här inställningen. Men om SharedLogId anges måste SharedLogPath också anges. |
| SharedLogPath |Fullständigt kvalificerat Sök vägs namn |"" |Anger den fullständiga sökvägen dit den delade logg filen för repliken kommer att skapas. Normalt bör inte tjänsterna använda den här inställningen. Men om SharedLogPath anges måste SharedLogId också anges. |
| SlowApiMonitoringDuration |Sekunder |300 |Anger övervaknings intervallet för hanterade API-anrop. Exempel: användare som tillhandahöll funktionen motringning. När intervallet har passerat skickas en varnings hälso rapport till Health Manager. |
| LogTruncationIntervalSeconds |Sekunder |0 |Konfigurerbart intervall då logg trunkering ska initieras på varje replik. Den används för att säkerställa att loggen också trunkeras baserat på tid istället för bara logg storleken. Den här inställningen tvingar även borttagning av borttagna poster i en tillförlitlig ord lista. Därför kan det användas för att se till att borttagna objekt rensas i rätt tid. |
| EnableStableReads |Boolesk |Falskt |Aktivering av stabila läsningar begränsar sekundära repliker för att returnera värden som har varit bekräftat. |

### <a name="sample-configuration-via-code"></a>Exempel på konfiguration via kod
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Exempelkonfigurationsfil
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
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


### <a name="remarks"></a>Kommentarer
BatchAcknowledgementInterval styr svars tiden för replikering. Värdet "0" resulterar i lägsta möjliga svars tid, med kostnaden för data flödet (som fler bekräftelse meddelanden måste skickas och bearbetas, var och en innehåller färre bekräftelser).
Ju större värde för BatchAcknowledgementInterval, desto högre det övergripande antalet data flöde för replikering, till kostnaden för högre åtgärds fördröjning. Detta översätter direkt till svars tiden för transaktions incheckningar.

Värdet för CheckpointThresholdInMB styr mängden disk utrymme som replikeraren kan använda för att lagra tillståndsinformation i replikens dedikerade loggfil. Om du ökar värdet till ett högre värde än standardinställningen kan det leda till snabbare omkonfigurations tider när en ny replik läggs till i uppsättningen. Detta beror på den partiella tillstånds överföring som äger rum på grund av tillgängligheten för mer historik för åtgärder i loggen. Detta kan eventuellt öka återställnings tiden för en replik efter en krasch.

Inställningen MaxRecordSizeInKB definierar den maximala storleken för en post som kan skrivas av replikmappen i logg filen. I de flesta fall är standard post storleken på 1024 KB optimal. Men om tjänsten gör att större data objekt ska ingå i tillståndsinformation, kan det här värdet behöva ökas. Det finns lite förmån för att göra MaxRecordSizeInKB mindre än 1024 eftersom mindre poster bara använder det utrymme som krävs för den mindre posten. Vi tror att det här värdet skulle behöva ändras i sällsynta fall.

Inställningarna SharedLogId och SharedLogPath används alltid tillsammans för att en tjänst ska kunna använda en separat delad logg från den delade standard loggen för noden. För bästa möjliga effektivitet bör så många tjänster som möjligt ange samma delade logg. Delade loggfiler bör placeras på diskar som enbart används för den delade logg filen för att minska konkurrensen för huvud flyttning. Vi tror att det här värdet skulle behöva ändras i sällsynta fall.

## <a name="next-steps"></a>Nästa steg
* [Felsöka ditt Service Fabric program i Visual Studio](service-fabric-debugging-your-application.md)
* [Referens för utvecklare för Reliable Services](/previous-versions/azure/dn706529(v=azure.100))
