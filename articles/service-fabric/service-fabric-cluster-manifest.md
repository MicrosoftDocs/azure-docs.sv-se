---
title: Konfigurera ditt fristående Azure Service Fabric-kluster
description: Lär dig hur du konfigurerar ett fristående eller lokalt Azure Service Fabric-kluster.
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: fd93263b38340ce080cca1aecb98f3a599ff1861
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91843166"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Konfigurations inställningar för ett fristående Windows-kluster
I den här artikeln beskrivs konfigurations inställningar för ett fristående Azure Service Fabric-kluster som kan anges i *ClusterConfig.jspå* filen. Du kommer att använda den här filen för att ange information om klustrets noder, säkerhetskonfigurationer och nätverks sto pol Ogin vad gäller fel-och uppgraderings domäner.  När du har ändrat eller lagt till konfigurations inställningar kan du antingen [skapa ett fristående kluster](service-fabric-cluster-creation-for-windows-server.md) eller [Uppgradera konfigurationen av ett fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md).

När du [hämtar det fristående Service Fabric-paketet](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)ingår även ClusterConfig.jspå exempel. Exemplen som har "DevCluster" i sina namn skapar ett kluster med alla tre noderna på samma dator med hjälp av logiska noder. Av de här noderna måste minst en vara markerad som en primär nod. Den här typen av kluster är användbar för utvecklings-eller test miljöer. Det stöds inte som ett produktions kluster. Exemplen med "MultiMachine" i sina namn hjälper till att skapa produktions klass kluster, med varje nod på en separat dator. Antalet primära noder för dessa kluster baseras på klustrets [Tillförlitlighets nivå](#reliability). I version 5,7, API-version 05-2017, tog vi bort egenskapen Tillförlitlighets nivå. I stället beräknar vår kod den mest optimerade Tillförlitlighets nivån för klustret. Försök inte att ange ett värde för den här egenskapen i version 5,7 och senare.

* ClusterConfig.Unsecure.DevCluster.jspå och ClusterConfig.Unsecure.MultiMachine.jspå Visa hur du skapar ett oskyddat test-eller produktions kluster.

* ClusterConfig.Windows.DevCluster.jspå och ClusterConfig.Windows.MultiMachine.jspå Visa hur du skapar test-eller produktions kluster som skyddas med hjälp av [Windows-säkerhet](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.jspå och ClusterConfig.X509.MultiMachine.jspå Visa hur du skapar test-eller produktions kluster som skyddas med hjälp av [X509-certifikatbaserad säkerhet](service-fabric-windows-cluster-x509-security.md).

Nu ska vi undersöka de olika avsnitten i en ClusterConfig.jsi filen.

## <a name="general-cluster-configurations"></a>Allmänna klusterkonfigurationer
Allmänna klusterkonfigurationer behandlar de breda klustrade konfigurationerna, som du ser i följande JSON-kodfragment:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Du kan ge ditt Service Fabric-kluster ett eget namn genom att tilldela det till variabeln Name. ClusterConfigurationVersion är versions numret för klustret. Öka det varje gång du uppgraderar Service Fabric klustret. Lämna API version inställt på standardvärdet.

## <a name="nodes-on-the-cluster"></a>Noder i klustret
Du kan konfigurera noderna i Service Fabric-klustret med hjälp av noden noder, som följande kod avsnitt visar:
```json
"nodes": [{
    "nodeName": "vm0",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD0"
}, {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType1",
    "faultDomain": "fd:/dc1/r1",
    "upgradeDomain": "UD1"
}, {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType2",
    "faultDomain": "fd:/dc1/r2",
    "upgradeDomain": "UD2"
}],
```

Ett Service Fabric-kluster måste innehålla minst tre noder. Du kan lägga till fler noder i det här avsnittet enligt din installation. I följande tabell förklaras konfigurations inställningar för varje nod:

| **Nodkonfiguration** | **Beskrivning** |
| --- | --- |
| nodeName |Du kan ge noden ett eget namn. |
| Adresser |Ta reda på IP-adressen för din nod genom att öppna ett kommando fönster och skriva `ipconfig` . Anteckna IPV4-adressen och tilldela den till variabeln iPAddress. |
| nodeTypeRef |Varje nod kan tilldelas en annan nodtyp. [Node-typerna](#node-types) definieras i följande avsnitt. |
| Faulydomain |Fel domäner gör det möjligt för kluster administratörer att definiera de fysiska noder som kan sluta fungera på samma tid på grund av delade fysiska beroenden. |
| upgradeDomain |Uppgraderings domäner beskriver uppsättningar av noder som stängs för Service Fabric uppgraderingar på ungefär samma tid. Du kan välja vilka noder som ska tilldelas till vilka uppgraderings domäner, eftersom de inte begränsas av fysiska krav. |

## <a name="cluster-properties"></a>Kluster egenskaper
Avsnittet Egenskaper i ClusterConfig.jspå används för att konfigurera klustret som det visas:

### <a name="reliability"></a>Tillförlitlighet
Begreppet reliabilityLevel definierar antalet repliker eller instanser av Service Fabric system tjänster som kan köras på de primära noderna i klustret. Den fastställer tillförlitligheten för dessa tjänster och därmed klustret. Värdet beräknas av systemet när klustret skapas och uppgraderings tiden.

### <a name="diagnostics"></a>Diagnostik
I avsnittet diagnosticsStore kan du konfigurera parametrar för att aktivera diagnostik och felsöka nod-eller kluster fel, som du ser i följande kodfragment: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

Metadata är en beskrivning av kluster diagnosen och kan ställas in enligt din installation. Dessa variabler bidrar till att samla in ETW-spårnings loggar och krasch dum par samt prestanda räknare. Mer information om ETW-spårnings loggar finns i [tracelog](/windows-hardware/drivers/devtest/tracelog) och [ETW-spårning](/dotnet/framework/wcf/samples/etw-tracing). Alla loggar, inklusive [krasch dum par](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf) och [prestanda räknare](/windows/win32/perfctrs/performance-counters-portal), kan dirigeras till ConnectionString-mappen på din dator. Du kan också använda AzureStorage för att lagra diagnostik. Se följande exempel-kodfragment:

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>Säkerhet
Säkerhets avsnittet är nödvändigt för ett säkert fristående Service Fabric-kluster. I följande fragment visas en del av det här avsnittet:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

Metadata är en beskrivning av ditt säkra kluster och kan ställas in enligt din installation. ClusterCredentialType och ServerCredentialType bestämmer vilken typ av säkerhet som klustret och noderna implementerar. De kan ställas in på antingen *X509* för en certifikatbaserad säkerhet eller *Windows* för Active Directory-baserad säkerhet. Resten av säkerhets avsnittet baseras på typen av säkerhet. Information om hur du fyller i resten av säkerhets avsnittet finns i [certifikat-baserad säkerhet i ett fristående kluster](service-fabric-windows-cluster-x509-security.md) eller [Windows-säkerhet i ett fristående kluster](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Nodtyper
I avsnittet nodeTypes beskrivs vilken typ av noder som ditt kluster har. Minst en nodtyp måste anges för ett kluster, vilket visas i följande kodfragment: 

```json
"nodeTypes": [{
    "name": "NodeType0",
    "clientConnectionEndpointPort": "19000",
    "clusterConnectionEndpointPort": "19001",
    "leaseDriverEndpointPort": "19002"
    "serviceConnectionEndpointPort": "19003",
    "httpGatewayEndpointPort": "19080",
    "reverseProxyEndpointPort": "19081",
    "applicationPorts": {
        "startPort": "20575",
        "endPort": "20605"
    },
    "ephemeralPorts": {
        "startPort": "20606",
        "endPort": "20861"
    },
    "isPrimary": true
}]
```

Namnet är det egna namnet för den här nodtypen. Om du vill skapa en nod av den här nodtypen tilldelar du det egna namnet till variabeln nodeTypeRef för noden, som [tidigare nämnts](#nodes-on-the-cluster). Definiera de anslutnings slut punkter som används för varje nodtyp. Du kan välja ett port nummer för dessa anslutnings slut punkter, så länge de inte står i konflikt med andra slut punkter i klustret. I ett kluster med flera noder finns det en eller flera primära noder (det vill säga isPrimary är inställt på *Sant*), beroende på [reliabilityLevel](#reliability). Mer information om primära och andra typer av noder finns i [Service Fabric överväganden för kluster kapacitets planering](service-fabric-cluster-capacity.md) för information om NodeTypes och reliabilityLevel. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Slut punkter som används för att konfigurera nodtyper
* clientConnectionEndpointPort är den port som klienten använder för att ansluta till klustret när klient-API: er används. 
* clusterConnectionEndpointPort är porten där noderna kommunicerar med varandra.
* leaseDriverEndpointPort är den port som används av kluster låne driv rutinen för att ta reda på om noderna fortfarande är aktiva. 
* serviceConnectionEndpointPort är den port som används av de program och tjänster som distribueras på en nod för att kommunicera med Service Fabric-klienten på en viss nod.
* httpGatewayEndpointPort är den port som används av Service Fabric Explorer för att ansluta till klustret.
* ephemeralPorts åsidosätter de [dynamiska portar som används av operativ systemet](https://support.microsoft.com/kb/929851). Service Fabric använder en del av dessa portar som program portar och återstående är tillgängliga för operativ systemet. Den mappar även det här intervallet till det befintliga intervallet som finns i operativ systemet, så i alla syfte kan du använda de intervall som anges i JSON-exempelfilerna. Se till att skillnaden mellan start-och slut portarna är minst 255. Du kan stöta på konflikter om skillnaden är för låg, eftersom intervallet delas med operativ systemet. Om du vill se det konfigurerade dynamiska port intervallet kör du `netsh int ipv4 show dynamicport tcp` .
* Applicationports får är de portar som används av Service Fabric-program. Program ports intervallet bör vara tillräckligt stort för att kunna användas för att fastställa slut punkts kravet för dina program. Intervallet ska vara exklusiv från det dynamiska port intervallet på datorn, det vill säga ephemeralPorts-intervallet som angetts i konfigurationen. Service Fabric använder dessa portar när nya portar krävs och tar hand om att öppna brand väggen för dessa portar. 
* reverseProxyEndpointPort är en valfri reverse proxy-slutpunkt. Mer information finns i [Service Fabric omvänd proxy](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Logg inställningar
I avsnittet fabricSettings kan du ange rot kataloger för Service Fabric data och loggar. Du kan bara anpassa dessa kataloger när du skapar det första klustret. Se följande exempel avsnitt:

```json
"fabricSettings": [{
    "name": "Setup",
    "parameters": [{
        "name": "FabricDataRoot",
        "value": "C:\\ProgramData\\SF"
    }, {
        "name": "FabricLogRoot",
        "value": "C:\\ProgramData\\SF\\Log"
}]
```

Vi rekommenderar att du använder en icke-OS-enhet som FabricDataRoot och FabricLogRoot. Den ger mer tillförlitlighet i att undvika situationer när operativ systemet slutar svara. Om du bara anpassar data roten placeras logg roten en nivå under data roten.

### <a name="stateful-reliable-services-settings"></a>Tillstånds känsliga Reliable Services inställningar
I avsnittet KtlLogger kan du ange globala konfigurations inställningar för Reliable Services. Mer information om de här inställningarna finns i [Konfigurera tillstånds känslig Reliable Services](service-fabric-reliable-services-configuration.md). I följande exempel visas hur du ändrar den delade transaktions loggen som skapas för att återställa pålitliga samlingar för tillstånds känsliga tjänster:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Tilläggs funktioner
Konfigurera tilläggs funktioner genom att konfigurera API version som 04-2017 eller högre och konfigurera addonFeatures som visas här:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
Alla tillgängliga tilläggs funktioner kan visas i [Service Fabric REST API-referensen](/rest/api/servicefabric/sfrp-model-addonfeatures).

### <a name="container-support"></a>Stöd för containrar
Om du vill aktivera stöd för behållare för både Windows Server-behållare och Hyper-V-behållare för fristående kluster måste DNS service-tilläggs funktionen vara aktive rad.

## <a name="next-steps"></a>Nästa steg
När du har en fullständig *ClusterConfig.jspå* en fil som har kon figurer ATS enligt den fristående kluster konfigurationen kan du distribuera klustret. Följ stegen i [skapa ett fristående Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md). 

Om du har ett fristående kluster distribuerat kan du också [Uppgradera konfigurationen av ett fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md). 

Lär dig hur du [visualiserar ditt kluster med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
