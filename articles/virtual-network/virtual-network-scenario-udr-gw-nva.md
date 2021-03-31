---
title: Hybrid anslutning med program med två nivåer | Microsoft Docs
description: Lär dig hur du distribuerar virtuella apparater och UDR för att skapa en program miljö med flera nivåer i Azure
services: virtual-network
documentationcenter: na
author: KumudD
manager: carmonm
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: kumud
ms.openlocfilehash: 1d2dde4e77a39b114f721cd6d2be250141984e7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86231717"
---
# <a name="virtual-appliance-scenario"></a>Scenario för Virtual-apparat
Ett vanligt scenario mellan större Azure-kunder är att du måste tillhandahålla ett program med två nivåer som exponeras för Internet, samtidigt som du får åtkomst till bak nivån från ett lokalt Data Center. Det här dokumentet vägleder dig genom ett scenario med hjälp av användardefinierade vägar (UDR), en VPN Gateway och virtuella nätverks enheter för att distribuera en miljö med två nivåer som uppfyller följande krav:

* Webb programmet måste vara tillgängligt enbart från det offentliga Internet.
* Webb server som är värd för programmet måste kunna komma åt en server för backend-program.
* All trafik från Internet till webb programmet måste gå igenom en virtuell brand Väggs installation. Den här virtuella installationen används endast för Internet trafik.
* All trafik som kommer till program servern måste gå igenom en virtuell brand Väggs installation. Den här virtuella installationen kommer att användas för åtkomst till backend-servern och åtkomst som kommer in från det lokala nätverket via en VPN Gateway.
* Administratörerna måste kunna hantera de virtuella brand väggarna från sina lokala datorer, genom att använda en tredje brand Väggs installation som uteslutande används i hanterings syfte.

Det här är ett standard nätverk för perimeter (kallas även DMZ) med ett DMZ och ett skyddat nätverk. Ett sådant scenario kan skapas i Azure med hjälp av NSG: er, virtuella brand väggar eller en kombination av båda. I tabellen nedan visas några av fördelarna och nack delar mellan NSG: er-och brand Väggs enheter.

|  | Fördelar | Nackdelar |
| --- | --- | --- |
| **NSG** |Ingen kostnad. <br/>Integrerad i Azure RBAC. <br/>Regler kan skapas i Azure Resource Manager mallar. |Komplexiteten kan variera i större miljöer. |
| **Brandvägg** |Fullständig kontroll över data planet. <br/>Central hantering via brand Väggs konsolen. |Kostnad för brand Väggs utrustning. <br/>Inte integrerat med Azure RBAC. |

I lösningen nedan används virtuella brand väggar för att implementera ett perimeternätverk (DMZ)/Protected nätverks scenario.

## <a name="considerations"></a>Överväganden
Du kan distribuera miljön som beskrivs ovan i Azure med hjälp av olika funktioner som är tillgängliga idag, enligt följande.

* **Virtuellt nätverk (VNet)** . Ett Azure VNet fungerar på samma sätt som ett lokalt nätverk och kan delas upp i ett eller flera undernät för att ge trafik isolering och separering av problem.
* **Virtuell** installation. Flera partner tillhandahåller virtuella enheter i Azure Marketplace som kan användas för de tre brand väggarna som beskrivs ovan. 
* **Användardefinierade vägar (UDR)**. Routningstabeller kan innehålla UDR som används av Azure-nätverk för att styra flödet av paket i ett VNet. Dessa routningstabeller kan tillämpas på undernät. En av de nyaste funktionerna i Azure är möjligheten att tillämpa en routningstabell på GatewaySubnet, vilket ger möjlighet att vidarebefordra all trafik som kommer till Azure VNet från en hybrid anslutning till en virtuell installation.
* **IP-vidarebefordring**. Som standard vidarebefordrar Azure Networking-paket till virtuella nätverkskort endast om paketets mål-IP-adress matchar NÄTVERKSKORTets IP-adress. Om en UDR definierar att ett paket måste skickas till en specifik virtuell installation, skulle Azure Networking-motorn därför släppa paketet. För att se till att paketet levereras till en virtuell dator (i det här fallet en virtuell installation) som inte är målet för paketet måste du aktivera IP-vidarebefordran för den virtuella installationen.
* **Nätverks säkerhets grupper (NSG: er)**. Exemplet nedan använder inte NSG: er, men du kan använda NSG: er som tillämpas på undernät och/eller nätverkskort i den här lösningen för att ytterligare filtrera trafiken i och ut ur dessa undernät och nätverkskort.

![IPv6-anslutning](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

I det här exemplet finns en prenumeration som innehåller följande:

* 2 resurs grupper, visas inte i diagrammet. 
  * **ONPREMRG**. Innehåller alla resurser som behövs för att simulera ett lokalt nätverk.
  * **AZURERG**. Innehåller alla resurser som krävs för Azures virtuella nätverks miljö. 
* Ett virtuellt nätverk med namnet **onpremvnet** används för att efterlikna ett lokalt Data Center segment enligt listan nedan.
  * **onpremsn1**. Undernät som innehåller en virtuell dator (VM) som kör Ubuntu för att efterlikna en lokal server.
  * **onpremsn2**. Undernät som innehåller en virtuell dator som kör Ubuntu för att efterlikna en lokal dator som används av en administratör.
* Det finns en virtuell brand Väggs installation med namnet **OPFW** på **onpremvnet** som används för att underhålla en tunnel till **azurevnet**.
* Ett virtuellt nätverk med namnet **azurevnet** segmenteras enligt listan nedan.
  * **azsn1**. Externt brand Väggs undernät används enbart för den externa brand väggen. All Internet trafik kommer in via det här under nätet. Det här under nätet innehåller bara ett nätverkskort som är länkat till den externa brand väggen.
  * **azsn2**. Klient dels under nät som är värd för en virtuell dator som körs som en webb server som kommer att nås från Internet.
  * **azsn3**. Backend-undernät som är värd för en virtuell dator som kör en backend-Programserver som ska nås av klient webb servern.
  * **azsn4**. Hanterings under nätet används enbart för att ge hanterings åtkomst till alla virtuella brand väggar. Det här under nätet innehåller bara ett nätverkskort för varje brand vägg för virtuella brand väggar som används i lösningen.
  * **GatewaySubnet**. Azure Hybrid Connection-undernät krävs för ExpressRoute och VPN Gateway för att tillhandahålla anslutning mellan Azure virtuella nätverk och andra nätverk. 
* Det finns 3 virtuella brand Väggs enheter i **azurevnet** -nätverket. 
  * **AZF1**. Extern brand vägg som exponeras för det offentliga Internet med hjälp av en offentlig IP-adressresurs i Azure. Du måste se till att du har en mall från Marketplace, eller direkt från din enhets leverantör, som etablerar en virtuell dator med 3 nätverkskort.
  * **AZF2**. Intern brand vägg som används för att styra trafiken mellan **azsn2** och **azsn3**. Detta är även en virtuell 3-NIC-enhet.
  * **AZF3**. Hanterings brand vägg som är tillgänglig för administratörer från det lokala data centret och är ansluten till ett hanterings undernät som används för att hantera alla brand Väggs enheter. Du hittar mallar för virtuella enheter med 2-NIC på Marketplace eller begär en direkt från din enhets leverantör.

## <a name="user-defined-routing-udr"></a>Användardefinierad routning (UDR)
Varje undernät i Azure kan länkas till en UDR-tabell som används för att definiera hur trafik som initieras i det under nätet dirigeras. Om ingen UDR har definierats använder Azure standard vägar för att tillåta trafik att flöda från ett undernät till ett annat. Mer information om UDR finns i [Vad är användardefinierade vägar och IP-vidarebefordring](virtual-networks-udr-overview.md).

För att säkerställa att kommunikationen sker via rätt brand Väggs program, baserat på det senaste kravet ovan, måste du skapa följande routningstabell som innehåller UDR i **azurevnet**.

### <a name="azgwudr"></a>azgwudr
I det här scenariot används den enda trafiken som flödar från lokalt till Azure för att hantera brand väggarna genom att ansluta till **AZF3** och trafiken måste gå igenom den interna brand väggen, **AZF2**. Därför är det bara en väg som krävs i **GatewaySubnet** som visas nedan.

| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Tillåter lokal trafik att uppnå hanterings brand vägg **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Tillåter trafik till backend-undernätet som är värd för program servern via **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Tillåter att all annan trafik dirigeras via **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Tillåter trafik till **azsn2** att flöda från App Server till webserver via **AZF2** |

Du måste också skapa routningstabeller för undernät i **onpremvnet** för att efterlikna det lokala data centret.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Tillåter trafik till **onpremsn2** via **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Tillåter trafik till det säkerhetskopierade under nätet i Azure via **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Tillåter trafik till **onpremsn1** via **OPFW** |

## <a name="ip-forwarding"></a>IP-vidarebefordran
UDR och IP-vidarebefordring är funktioner som du kan använda i en kombination för att tillåta att virtuella apparater används för att styra trafikflödet i ett Azure VNet.  En virtuell installation är helt enkelt en VM som kör ett program som används för att hantera nätverkstrafik på något sätt, som en brandvägg eller en NAT-enhet.

Den här virtuella installations-VM:en måste kunna ta emot inkommande trafik som inte är adresserad till den. För att låta en VM ta emot trafik som är adresserad till andra mål, behöver du aktivera IP-vidarebefordran för VM:en. Det är en Azure-inställning, inte en inställning i gästoperativsystemet. Din virtuella installation behöver fortfarande köra någon typ av program för att hantera inkommande trafik och dirigera den på lämpligt sätt.

Mer information om IP-vidarebefordring finns i [Vad är användardefinierade vägar och IP-vidarebefordring](virtual-networks-udr-overview.md).

Anta till exempel att du har följande konfiguration i ett Azure VNet:

* Under nätet **onpremsn1** innehåller en virtuell dator med namnet **onpremvm1**.
* Under nätet **onpremsn2** innehåller en virtuell dator med namnet **onpremvm2**.
* En virtuell installation med namnet **OPFW** är ansluten till **onpremsn1** och **onpremsn2**.
* En användardefinierad väg som är länkad till **onpremsn1** anger att all trafik till **onpremsn2** måste skickas till **OPFW**.

Om **onpremvm1** försöker upprätta en anslutning med **ONPREMVM2**, kommer UDR att användas och trafik skickas till **OPFW** som nästa hopp. Tänk på att det faktiska paket målet inte ändras, men det säger att **onpremvm2** är målet. 

Utan IP-vidarebefordring aktive rad för **OPFW**, tar Azure Virtual Networking-logiken bort paketen eftersom det bara tillåter att paket skickas till en virtuell dator om den virtuella DATORns IP-adress är målet för paketet.

Med IP-vidarebefordring vidarebefordrar den virtuella Azure-nätverks logiken paketen till OPFW utan att ändra den ursprungliga mål adressen. **OPFW** måste hantera paketen och bestämma vad som ska göras med dem.

För scenariot ovan att fungera måste du aktivera IP-vidarebefordring på nätverkskorten för **OPFW**, **AZF1**, **AZF2** och **AZF3** som används för routning (alla nätverkskort förutom de som är länkade till hanterings under nätet). 

## <a name="firewall-rules"></a>Brandväggsregler
Som det beskrivs ovan garanterar IP-vidarebefordring endast paket som skickas till de virtuella enheterna. Din apparat måste fortfarande bestämma vad som ska göras med dessa paket. I scenariot ovan måste du skapa följande regler i dina enheter:

### <a name="opfw"></a>OPFW
OPFW representerar en lokal enhet som innehåller följande regler:

* **Väg**: all trafik till 10.0.0.0/16 (**azurevnet**) måste skickas via tunnel- **ONPREMAZURE**.
* **Princip**: Tillåt all dubbelriktad trafik mellan **PORT2** och **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 representerar en virtuell Azure-installation som innehåller följande regler:

* **Princip**: Tillåt all dubbelriktad trafik mellan **PORT1** och **PORT2**.

### <a name="azf2"></a>AZF2
AZF2 representerar en virtuell Azure-installation som innehåller följande regler:

* **Väg**: all trafik till 10.0.0.0/16 (**onpremvnet**) måste skickas till Azure-gatewayens IP-adress (dvs. 10.0.0.1) via **PORT1**.
* **Princip**: Tillåt all dubbelriktad trafik mellan **PORT1** och **PORT2**.

## <a name="network-security-groups-nsgs"></a>Nätverks säkerhets grupper (NSG: er)
I det här scenariot används inte NSG: er. Du kan dock tillämpa NSG: er på varje undernät för att begränsa inkommande och utgående trafik. Du kan till exempel använda följande NSG-regler för det externa VB-undernätet.

**Inkommande**

* Tillåt all TCP-trafik från Internet till port 80 på alla virtuella datorer i under nätet.
* Neka all annan trafik från Internet.

**Autentiseringsnivå**

* Neka all trafik till Internet.

## <a name="high-level-steps"></a>Steg på hög nivå
Följ stegen nedan om du vill distribuera det här scenariot.

1. Logga in på din Azure-prenumeration.
2. Om du vill distribuera ett VNet för att efterlikna det lokala nätverket, etablerar du de resurser som är en del av **ONPREMRG**.
3. Etablera de resurser som ingår i **AZURERG**.
4. Etablera tunneln från **onpremvnet** till **azurevnet**.
5. När alla resurser har tillhandahållits loggar du in på **onpremvm2** och pingar 10.0.3.101 för att testa anslutningen mellan **onpremsn2** och **azsn3**.

