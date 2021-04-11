---
title: Metod tips för Azure Service Fabric-nätverk
description: Regler och design överväganden för att hantera nätverks anslutningar med Azure Service Fabric.
author: chrpap
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: chrpap
ms.openlocfilehash: de6a80f73a97a5732f00e10107fff2330038210a
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122024"
---
# <a name="networking"></a>Nätverk

När du skapar och hanterar Azure Service Fabric-kluster ger du nätverks anslutning för dina noder och program. Nätverks resurserna omfattar IP-adressintervall, virtuella nätverk, belastnings utjämning och nätverks säkerhets grupper. I den här artikeln får du lära dig metod tips för dessa resurser.

Granska Azure [Service Fabric nätverks mönster](./service-fabric-patterns-networking.md) för att lära dig hur du skapar kluster som använder följande funktioner: befintligt virtuellt nätverk eller undernät, statisk offentlig IP-adress, intern belastningsutjämnare eller intern och extern belastningsutjämnare.

## <a name="infrastructure-networking"></a>Infrastruktur nätverk
Maximera prestandan för den virtuella datorn med accelererat nätverk, genom att deklarera *enableAcceleratedNetworking* -egenskapen i din Resource Manager-mall, är följande kodfragment av en NetworkInterfaceConfigurations för skalnings uppsättning för virtuella datorer som möjliggör accelererat nätverk:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
Service Fabric kluster kan tillhandahållas i [Linux med accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md)och [Windows med accelererat nätverk](../virtual-network/create-vm-accelerated-networking-powershell.md).

Accelererat nätverk stöds för Azure Virtual Machine serien SKU: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 och MS/MMS. Accelererat nätverk har testats med Standard_DS8_v3 SKU på 01/23/2019 för ett Service Fabric Windows-kluster och med Standard_DS12_v2 på 01/29/2019 för ett Service Fabric Linux-kluster. Observera att accelererade nätverk kräver minst 4 virtuella processorer. 

Om du vill aktivera accelererat nätverk på ett befintligt Service Fabric-kluster måste du först [skala ett Service Fabric kluster genom att lägga till en skalnings uppsättning för virtuella datorer](./virtual-machine-scale-set-scale-node-type-scale-out.md)för att utföra följande:
1. Etablera en NodeType med accelererat nätverk aktiverat
2. Migrera dina tjänster och deras tillstånd till den etablerade NodeType med accelererat nätverk aktiverat

Att skala ut infrastruktur krävs för att aktivera accelererat nätverk i ett befintligt kluster, eftersom aktivering av accelererade nätverk på plats skulle orsaka drift stopp, eftersom det krävs att alla virtuella datorer i en tillgänglighets uppsättning [stoppas och frigörs innan du aktiverar accelererat nätverk på ett befintligt nätverkskort](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Klusternätverk

* Service Fabric kluster kan distribueras till ett befintligt virtuellt nätverk genom att följa stegen som beskrivs i [Service Fabric nätverks mönster](./service-fabric-patterns-networking.md).

* Nätverks säkerhets grupper (NSG: er) rekommenderas för nodtyper för att begränsa inkommande och utgående trafik till deras kluster. Se till att de nödvändiga portarna är öppna i NSG. 

* Den primära nodtypen som innehåller Service Fabric system tjänsterna behöver inte exponeras via den externa belastningsutjämnaren och kan exponeras av en [intern belastningsutjämnare](./service-fabric-patterns-networking.md#internal-only-load-balancer)

* Använd en [statisk offentlig IP-adress](./service-fabric-patterns-networking.md#static-public-ip-address-1) för klustret.

## <a name="network-security-rules"></a>Nätverks säkerhets regler

De grundläggande reglerna här är minst för en säkerhets låsning av ett Azure-hanterat Service Fabric-kluster. Om du inte öppnar följande portar eller godkänner IP/URL, förhindras att klustret fungerar korrekt och kanske inte stöds. Med den här regeln är det absolut nödvändigt att använda [automatiska uppgraderingar av operativ Systems avbildningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md), annars måste ytterligare portar öppnas.

### <a name="inbound"></a>Inkommande 
|Prioritet   |Namn               |Port        |Protokoll  |Källa             |Mål       |Action   
|---        |---                |---         |---       |---                |---               |---
|3900       |Azure              |19080       |TCP       |Internet           |VirtualNetwork    |Tillåt
|3910       |Klient             |19000       |TCP       |Internet           |VirtualNetwork    |Tillåt
|3920       |Kluster            |1025-1027   |TCP       |VirtualNetwork     |VirtualNetwork    |Tillåt
|3930       |Tillfälliga          |49152-65534 |TCP       |VirtualNetwork     |VirtualNetwork    |Tillåt
|3940       |Program        |20000-30000 |TCP       |VirtualNetwork     |VirtualNetwork    |Tillåt
|3950       |SMB                |445         |TCP       |VirtualNetwork     |VirtualNetwork    |Tillåt
|3960       |RDP                |3389-3488   |TCP       |Internet           |VirtualNetwork    |Neka
|3970       |SSH                |22          |TCP       |Internet           |VirtualNetwork    |Neka
|3980       |Anpassad slut punkt    |80          |TCP       |Internet           |VirtualNetwork    |Tillåt
|4100       |Blockera inkommande      |443         |Valfri       |Valfri                |Valfri               |Tillåt

Mer information om inkommande säkerhets regler:

* **Azure**. Den här porten används av Service Fabric Explorer för att söka efter och hantera klustret, och det används också av Service Fabric Resource Provider för att fråga om information om klustret, för att kunna visa i Azure-Hanteringsportal. Om den här porten inte är tillgänglig från Service Fabric Resource Provider visas ett meddelande om att "noder som inte hittas" eller "UpgradeServiceNotReachable" i Azure Portal och att din nod och program lista är tomma. Det innebär att om du vill ha insyn i klustret i Azure-Hanteringsportal måste belastningsutjämnaren exponera en offentlig IP-adress och din NSG måste tillåta inkommande 19080-trafik.  

* **Klienten**. Klient anslutnings slut punkten för API: er som REST/PowerShell/CLI. 

* **Kluster**. Används för kommunikation mellan noder; ska aldrig blockeras.

* **Tillfällig**. Service Fabric använder en del av dessa portar som program portar och återstående är tillgängliga för operativ systemet. Den mappar även det här intervallet till det befintliga intervallet som finns i operativ systemet, så i alla syfte kan du använda de intervall som anges i exemplet här. Se till att skillnaden mellan start-och slut portarna är minst 255. Du kan stöta på konflikter om skillnaden är för låg, eftersom intervallet delas med operativ systemet. Om du vill se det konfigurerade dynamiska port intervallet kör du *netsh int IPv4 show Dynamic port TCP*. Dessa portar behövs inte för Linux-kluster.

* **Programmet**. Program ports intervallet bör vara tillräckligt stort för att kunna användas för att fastställa slut punkts kravet för dina program. Intervallet ska vara exklusiv från det dynamiska port intervallet på datorn, det vill säga ephemeralPorts-intervallet som angetts i konfigurationen. Service Fabric använder dessa portar när nya portar krävs och tar hand om att öppna brand väggen för dessa portar på noderna.

* **SMB**. Valfritt, runtime-versionen 7.1 + använder inte SMB som standard. SMB-protokollet används av avbildnings Arkiv-tjänsten för två scenarier. Den här porten krävs för att ladda ned paketen från avbildnings Arkiv av noderna samt för att replikera dessa mellan replikerna. 

* **RDP**. Valfritt, om RDP krävs från Internet eller VirtualNetwork för scenarier med hopp. 

* **SSH**. Valfritt, om SSH krävs från Internet eller VirtualNetwork för scenarier med hopp.

* **Anpassad slut punkt**. Ett exempel på ditt program för att aktivera en tillgänglig slut punkt för Internet.

### <a name="outbound"></a>Utgående

|Prioritet   |Namn               |Port        |Protokoll  |Källa             |Mål       |Action   
|---        |---                |---         |---       |---                |---               |---
|3900       |Nätverk            |Valfri         |TCP       |VirtualNetwork     |VirtualNetwork    |Tillåt
|3910       |Resurs leverantör  |443         |TCP       |VirtualNetwork     |ServiceFabric     |Tillåt
|3920       |Uppgradera            |443         |TCP       |VirtualNetwork     |Internet          |Tillåt
|3950       |Blockera utgående     |Valfri         |Valfri       |Valfri                |Valfri               |Neka

Mer information om utgående säkerhets regler:

* **Nätverk**. Kommunikations kanal för undernät och till andra virtuella nätverk.

* **Resurs leverantör**. Anslutning av UpgradeService för att köra alla ARM-distributioner av Service Fabric Resource Provider.

* **Upgradering**. Uppgraderings tjänsten använder download.microsoft.com för att hämta bitarna, vilket krävs för installation, avbildning och körnings uppgraderingar. Tjänsten fungerar med dynamiska IP-adresser. I scenariot av en intern belastningsutjämnare måste ytterligare en extern belastningsutjämnare läggas till i mallen med en regel som tillåter utgående trafik för port 443. Om du vill kan den här porten blockeras efter en lyckad installation, men i det här fallet måste uppgraderings paketet distribueras till noderna eller så måste porten öppnas under kort tid, efteråt en manuell uppgradering krävs.

Använd Azure-brandväggen med [NSG flödes logg](../network-watcher/network-watcher-nsg-flow-logging-overview.md) och [trafik analys](../network-watcher/traffic-analytics.md) för att spåra problem med låsning av säkerhet. ARM-mallen [Service Fabric med NSG](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) är ett användbart exempel att starta. 


## <a name="application-networking"></a>Program nätverk

* Om du vill köra arbets belastningar i Windows-behållare använder du [Öppna Nätverks läge](./service-fabric-networking-modes.md#set-up-open-networking-mode) för att förenkla kommunikationen mellan tjänster och tjänster.

* Använd en omvänd proxy, till exempel [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) eller [Service Fabric den omvända proxyn](./service-fabric-reverseproxy.md) för att visa vanliga program portar, till exempel 80 eller 443.

* För Windows-behållare som finns på gapped datorer som inte kan hämta bas lager från moln lagring i Azure kan du åsidosätta beteendet för det främmande lagret med hjälp av flaggan [--Allow-undistribuerbara-artefakter](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) i Docker daemon.

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Service Fabric skapa kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [skapa ett Linux-kluster](service-fabric-cluster-creation-via-portal.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
