---
title: Käll nätverks adress översättning (SNAT) för utgående anslutningar
titleSuffix: Azure Load Balancer
description: Lär dig hur Azure Load Balancer används för utgående Internet anslutning (SNAT).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 99f15afdab917fe28e22df8cb0e372b6c30c8526
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027337"
---
# <a name="using-source-network-address-translation-snat-for-outbound-connections"></a>Använda käll nätverks adress översättning (SNAT) för utgående anslutningar

Klient delens IP-adresser för en offentlig belastningsutjämnare i Azure kan användas för att tillhandahålla utgående anslutning till Internet för Server dels instanser. Den här konfigurationen använder **käll Network Address Translation (SNAT)**. SNAT skriver om IP-adressen för Server delen till den offentliga IP-adressen för belastningsutjämnaren. 

SNAT aktiverar **IP-maskerande** av Server dels instansen. Den här maskerade tjänsten förhindrar att externa källor har en direkt adress till Server dels instanserna. En IP-adress som delas mellan server dels instanser minskar kostnaden för statiska offentliga IP-adresser. En känd IP-adress stöder scenarier som fören klar IP-tillåten med trafik från kända offentliga IP-adresser. 

>[!Note]
> För program som kräver ett stort antal utgående anslutningar eller företags kunder som kräver att en enda uppsättning IP-adresser används från ett visst virtuellt nätverk, [Virtual Network NAT](../virtual-network/nat-overview.md) är den rekommenderade lösningen. Den dynamiska allokeringen tillåter enkel konfiguration och den mest effektiva användningen av SNAT-portar från varje IP-adress. Det gör att alla resurser i det virtuella nätverket kan dela en uppsättning IP-adresser utan att behöva dela en belastningsutjämnare.

>[!Important]
> Även om inga utgående SNAT har kon figurer ATS kommer Azure Storage-konton inom samma region fortfarande vara tillgängliga och Server dels resurser kommer fortfarande att ha åtkomst till Microsoft-tjänster, till exempel Windows-uppdateringar.

>[!NOTE] 
>Den här artikeln beskriver endast Azure Resource Manager distributioner. Granska [utgående anslutningar (klassiska)](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic) för alla klassiska distributions scenarier i Azure.

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> Dela klient delens IP-adress över Server dels resurser

Om Server dels resurser i en belastningsutjämnare inte har offentliga IP-adresser på instans nivå (ILPIP), upprättar de utgående anslutningar via klient delens IP-adress för den offentliga belastningsutjämnaren. Portar används för att generera unika identifierare som används för att underhålla distinkta flöden. Internet använder en fem tuple för att ge den här skillnaden.

Fem tuple består av:

* Mål-IP-adress
* Målport
* Käll-IP-adress
* Käll port och protokoll för att ge den här skillnaden.

Om en port används för inkommande anslutningar, har den en **lyssnare** för inkommande anslutnings begär Anden på den porten. Den porten kan inte användas för utgående anslutningar. För att upprätta en utgående anslutning, används en **tillfällig port** för att tillhandahålla målet med en port som används för att kommunicera och underhålla ett distinkt trafikflöde. När dessa tillfälliga portar används för SNAT kallas de för **SNAT-portar** 

Per definition har varje IP-adress 65 535 portar. Varje port kan antingen användas för inkommande eller utgående anslutningar för TCP (Transmission Control Protocol) och UDP (User Datagram Protocol). 

När en offentlig IP-adress läggs till som en klient dels-IP till en belastningsutjämnare ger Azure 64 000-portar som är berättigade till SNAT.

>[!NOTE]
> Varje port som används för en belastnings Utjämnings regel eller en inkommande NAT-regel använder en rad åtta portar från de här 64 000 portarna, vilket minskar antalet portar som är kvalificerade för SNAT. Om en belastnings Utjämnings regel eller NAT-regel finns i samma intervall på åtta som en annan kommer den att förbruka inga ytterligare portar. 

Genom regler för [utgående trafik](./outbound-rules.md) och belastnings utjämning kan de här SNAT-portarna distribueras till Server dels instanser för att de ska kunna dela de offentliga IP-adresserna för belastningsutjämnaren för utgående anslutningar.

När [Scenario 2](#scenario2) nedan har kon figurer ATS, kommer värden för varje server dels instans att vara SNAT-paket som ingår i en utgående anslutning. 

När du gör SNAT på en utgående anslutning från en server dels instans, skriver värden om käll-IP: en till en av klient delens IP-adresser. 

För att upprätthålla unika flöden skriver värden om käll porten för varje utgående paket till en SNAT-port på backend-instansen.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>Utgående anslutnings beteende för olika scenarier
  * Virtuell dator med offentlig IP-adress.
  * Virtuell dator utan offentlig IP-adress.
  * Virtuell dator utan offentlig IP-adress och utan standard belastnings utjämning.
        
 ### <a name="scenario-1-virtual-machine-with-public-ip"></a><a name="scenario1"></a> Scenario 1: virtuell dator med offentlig IP

 | Typer | Metod | IP-protokoll |
 | ---------- | ------ | ------------ |
 | Offentlig belastningsutjämnare eller fristående | [SNAT (käll nätverks adress översättning)](#snat) </br> används inte. | TCP (Transmission Control Protocol) </br> UDP (User Datagram Protocol) </br> ICMP (Internet Control Message Protocol) </br> ESP (Encapsulating Security Payload) |

 #### <a name="description"></a>Beskrivning

 Azure använder den offentliga IP-adress som tilldelats IP-konfigurationen av instansens nätverkskort för alla utgående flöden. Instansen har alla tillfälliga portar tillgängliga. Det spelar ingen roll om den virtuella datorn är belastningsutjämnad eller inte. Det här scenariot prioriteras framför de andra. 

 En offentlig IP-adress som tilldelas till en virtuell dator är en 1:1-relation (i stället för 1: många) och implementeras som en tillstånds Lös 1:1 NAT.

 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>Scenario 2: virtuell dator utan offentlig IP-adress och bakom offentliga standard Load Balancer

 | Typer | Metod | IP-protokoll |
 | ------------ | ------ | ------------ |
 | Standard offentlig belastningsutjämnare | Användning av IP-adresser för belastningsutjämnare för [SNAT](#snat).| TCP </br> UDP |

 #### <a name="description"></a>Beskrivning

 Belastnings Utjämnings resursen har kon figurer ATS med en utgående regel eller en regel för belastnings utjämning som aktiverar SNAT. Den här regeln används för att skapa en länk mellan den offentliga IP-klient delen med backend-poolen. 

 Om du inte slutför den här regel konfigurationen är beteendet som beskrivs i scenario 3. 

 En regel med en lyssnare krävs inte för att hälso avsökningen ska lyckas.

 När en virtuell dator skapar ett utgående flöde översätter Azure käll-IP-adressen till den offentliga IP-adressen för den offentliga belastnings Utjämnings klient delen. Den här översättningen görs via [SNAT](#snat). 

 Tillfälliga portar för den offentliga IP-adressen för belastningsutjämnaren i klient delen används för att särskilja enskilda flöden från den virtuella datorn. SNAT använder dynamiskt [förallokerade portar](#preallocatedports) när utgående flöden skapas. 

 I det här sammanhanget kallas de tillfälliga portarna som används för SNAT som SNAT-portar. Vi rekommenderar starkt att en [utgående regel](./outbound-rules.md) konfigureras explicit. Om du använder standard SNAT via en belastnings Utjämnings regel är SNAT-portarna fördelade enligt beskrivningen i [standard tilldelnings tabellen för SNAT-portar](#snatporttable).

> [!NOTE]
> **Azure Virtual Network NAT** kan tillhandahålla utgående anslutningar för virtuella datorer utan att det krävs någon belastningsutjämnare. Se [Vad är Azure Virtual Network NAT?](../virtual-network/nat-overview.md) för mer information.

 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-standard-internal-load-balancer"></a><a name="scenario3"></a>Scenario 3: virtuell dator utan offentlig IP-adress och bakom interna standard Load Balancer

 | Typer | Metod | IP-protokoll |
 | ------------ | ------ | ------------ |
 | Standard intern belastningsutjämnare | Ingen Internet anslutning.| Inget |

 #### <a name="description"></a>Beskrivning
 
När du använder en intern standardbelastningsutjämnare använder du inte tillfälliga IP-adresser för SNAT. Den här funktionen har stöd för säkerhet som standard. Den här funktionen säkerställer att alla IP-adresser som används av resurser kan konfigureras och kan reserveras. 

Om du vill få en utgående anslutning till Internet när du använder en standard intern belastningsutjämnare, konfigurerar du en offentlig IP-adress på instans nivå för att följa beteendet i [Scenario 1](#scenario1). 

Ett annat alternativ är att lägga till Server dels instanserna till en offentlig standard belastningsutjämnare med en utgående regel som kon figurer ATS. Server dels instanserna läggs till i en intern belastningsutjämnare för intern belastnings utjämning. Den här distributionen följer beteendet i [Scenario 2](#scenario2). 

> [!NOTE]
> **Azure Virtual Network NAT** kan tillhandahålla utgående anslutningar för virtuella datorer utan att det krävs någon belastningsutjämnare. Se [Vad är Azure Virtual Network NAT?](../virtual-network/nat-overview.md) för mer information.

 ### <a name="scenario-4-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario4"></a>Scenario 4: virtuell dator utan offentlig IP och bakom Basic Load Balancer

 | Typer | Metod | IP-protokoll |
 | ------------ | ------ | ------------ |
 |Inget </br> Basic Load Balancer | [SNAT](#snat) med dynamisk IP-adress på instans nivå| TCP </br> UDP | 

 #### <a name="description"></a>Beskrivning

 När den virtuella datorn skapar ett utgående flöde översätter Azure käll-IP-adressen till en dynamiskt tilldelad offentlig käll-IP-adress. Den här offentliga IP-adressen kan **inte konfigureras** och kan inte reserveras. Den här adressen räknas inte mot prenumerationens offentliga IP-adressresurs. 

Den offentliga IP-adressen kommer att släppas och en ny offentlig IP-adress krävs om du distribuerar om: 

 * Virtuell dator
 * Tillgänglighetsuppsättning
 * Skaluppsättning för virtuella datorer 

 Använd inte det här scenariot för att lägga till IP-adresser i en tillåten. Använd scenario 1 eller 2 där du explicit deklarerar utgående beteende. [SNAT](#snat) -portar är fördelade enligt beskrivningen i [standard tilldelnings tabellen för SNAT-portar](#snatporttable).

## <a name="exhausting-ports"></a><a name="scenarios"></a> Uttömda portar

Varje anslutning till samma mål-IP-adress och mål Port kommer att använda en SNAT-port. Den här anslutningen upprätthåller ett distinkt **trafikflöde** från Server dels instansen eller **klienten** till en **Server**. Den här processen ger servern en distinkt port som trafiken ska riktas mot. Utan den här processen är klient datorn inte medveten om vilket flöde ett paket ingår i.

Föreställ dig att du har flera webbläsare att gå till https://www.microsoft.com , vilket är:

* Mål-IP = 23.53.254.142
* Målport = 443
* Protokoll = TCP

Utan olika mål portar för retur trafiken (den SNAT-port som används för att upprätta anslutningen) har klienten inget sätt att separera ett frågeresultat från en annan.

Utgående anslutningar kan burst-överföras. En server dels instans kan allokeras för otillräckliga portar. Om du inte aktiverar **åter användning av anslutningar** ökar risken för att antalet SNAT- **portar** är.

Nya utgående anslutningar till en mål-IP-adress Miss fungerar när port överbelastningen sker. Anslutningar kommer att lyckas när en port blir tillgänglig. Den här utbelastningen inträffar när 64 000-portarna från en IP-adress sprids tunn över flera Server dels instanser. Information om hur du minskar antalet SNAT-portar finns i [fel söknings guiden](./troubleshoot-outbound-connection.md).  

För TCP-anslutningar använder belastningsutjämnaren en enskild SNAT-port för varje mål-IP och port. Den här MultiUse aktiverar flera anslutningar till samma mål-IP med samma SNAT-port. Den här MultiUse är begränsad om anslutningen inte är till olika mål portar.

För UDP-anslutningar använder belastningsutjämnaren en **port-begränsad kon NAT** -algoritm, som förbrukar en SNAT-port per mål-IP, oavsett mål porten. 

En port återanvänds för ett obegränsat antal anslutningar. Porten återanvänds endast om mål-IP-adressen eller porten är annorlunda.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Standard port tilldelning

Varje offentlig IP-adress som tilldelas till klient delens IP-adress för belastningsutjämnaren ges 64 000 SNAT-portar för dess medlemmar i Server delens pool. Portar kan inte delas med medlemmar i backend-poolen. Ett antal SNAT-portar kan bara användas av en enda server dels instans för att säkerställa att RETUR paket dirigeras korrekt. 

Vi rekommenderar att du använder en explicit utgående regel för att konfigurera SNAT-port tilldelning. Den här regeln maximerar antalet SNAT-portar som varje server dels instans har tillgänglig för utgående anslutningar. 

Om du använder automatisk allokering av utgående SNAT genom en belastnings Utjämnings regel definierar fördelnings tabellen din port tilldelning.

Följande <a name="snatporttable"></a> tabell visar förallokeringar av SNAT-portar för nivåer av backend-poolens storlekar:

| Poolstorlek (VM-instanser) | Förallokerade SNAT-portar per IP-konfiguration |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801 – 1000 | 32 | 

>[!NOTE]
> Om du har en backend-pool med en Max storlek på 10 kan varje instans ha 64000/10 = 6 400 portar om du definierar en explicit utgående regel. Enligt tabellen ovan kommer bara att ha 1 024 om du väljer automatisk allokering.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Utgående regler och Virtual Network NAT

Azure Load Balancer utgående regler och Virtual Network NAT är alternativ som är tillgängliga för utgående från ett virtuellt nätverk.

Mer information om utgående regler finns i [utgående regler](outbound-rules.md).

Mer information om Azure Virtual Network NAT finns i [Vad är azure Virtual Network NAT](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Villkor

*   När en anslutning är inaktiv utan att några nya paket skickas, frigörs portarna efter 4 – 120 minuter.
  * Det här tröskelvärdet kan konfigureras via utgående regler.
*   Varje IP-adress ger 64 000 portar som kan användas för SNAT.
*   Varje port kan användas för både TCP-och UDP-anslutningar till en mål-IP-adress
  * En UDP SNAT-port krävs om mål porten är unik eller inte. För varje UDP-anslutning till en mål-IP används en UDP SNAT-port.
  * En TCP SNAT-port kan användas för flera anslutningar till samma mål-IP förutsatt att mål portarna är olika.
*   SNAT-belastningen inträffar när en server dels instans tar slut på de fick SNAT-portarna. En belastningsutjämnare kan fortfarande ha oanvända SNAT-portar. Om en server dels instans använder SNAT-portar som överstiger de tilldelade SNAT-portarna kan den inte upprätta nya utgående anslutningar.

## <a name="next-steps"></a>Nästa steg

*   [Felsök utgående anslutnings fel på grund av SNAT-belastning](./troubleshoot-outbound-connection.md)
*   [Granska SNAT-mått](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) och bekanta dig med det korrekta sättet att filtrera, dela och visa dem.
