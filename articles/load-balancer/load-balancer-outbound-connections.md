---
title: Källnätverksadressöversättning (SNAT) för utgående anslutningar
titleSuffix: Azure Load Balancer
description: Lär dig Azure Load Balancer används för utgående Internetanslutning (SNAT).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 3b92ef3ce195a2eee9bce53e08d977593a9f1fc2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477714"
---
# <a name="using-source-network-address-translation-snat-for-outbound-connections"></a>Använda källnätverksadressöversättning (SNAT) för utgående anslutningar

Frontend-IP-adresser för en offentlig Azure-lastbalanserare kan användas för att tillhandahålla utgående anslutning till Internet för backend-instanser. Den här konfigurationen **använder källnätverksadressöversättning (SNAT).** SNAT skriver om IP-adressen för backend till lastbalanseras offentliga IP-adress. 

SNAT möjliggör **IP-maskering av** backend-instansen. Den här maskering förhindrar att externa källor har en direkt adress till backend-instanserna. En IP-adress som delas mellan backend-instanser minskar kostnaden för statiska offentliga IP-adresser. En känd IP-adress stöder scenarier som att förenkla IP-listan över tillåtna ip-adresser med trafik från kända offentliga IP-adresser. 

>[!Note]
> För program som kräver ett stort antal utgående anslutningar eller företagskunder som kräver att en enda uppsättning IP-adresser används från ett visst [virtuellt nätverk Virtual Network NAT](../virtual-network/nat-overview.md) den rekommenderade lösningen. Den dynamiska allokeringen möjliggör enkel konfiguration och den mest effektiva användningen av SNAT-portar från varje IP-adress. Det gör att alla resurser i det virtuella nätverket kan dela en uppsättning IP-adresser utan att de behöver dela en lastbalanserare.

>[!Important]
> Även om utgående SNAT har konfigurerats kommer Azure-lagringskonton inom samma region fortfarande att vara tillgängliga och backend-resurser kommer fortfarande att ha åtkomst till Microsoft-tjänster till exempel Windows Updates.

>[!NOTE] 
>Den här artikeln Azure Resource Manager endast distributioner. Granska [Utgående anslutningar (klassisk) för alla](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic) klassiska distributionsscenarier i Azure.

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> Dela IP-adressen för frontend mellan backend-resurser

Om en lastbalanserares backend-resurser inte har offentliga IP-adresser på instansnivå (ILPIP) upprättar de utgående anslutningar via ip-adressen på frontend-adressen för den offentliga lastbalanseraren. Portar används för att generera unika identifierare som används för att underhålla distinkta flöden. Internet använder en femtuppeln för att ge den här skillnaden.

Femtuppeln består av:

* Mål-IP-adress
* Målport
* Käll-IP-adress
* Källport och protokoll för att tillhandahålla den här skillnaden.

Om en port används för inkommande anslutningar har den en **lyssnare för** inkommande anslutningsbegäranden på den porten. Den porten kan inte användas för utgående anslutningar. För att upprätta en utgående anslutning används **en ohemlig port** för att förse målet med en port för att kommunicera och underhålla ett distinkt trafikflöde. När dessa tillfälliga portar används för SNAT kallas de **SNAT-portar** 

Per definition har varje IP-adress 65 535 portar. Varje port kan antingen användas för inkommande eller utgående anslutningar för TCP(Transmission Control Protocol) och UDP(User Datagram Protocol). 

När en offentlig IP-adress läggs till som en IP-adress i en lastbalanserare ger Azure 64 000 portar som är berättigade till SNAT.

>[!NOTE]
> Varje port som används för en belastningsutjämning eller inkommande NAT-regel förbrukar ett intervall på åtta portar från dessa 64 000 portar, vilket minskar antalet portar som är berättigade till SNAT. Om en belastningsutjämning eller nat-regel är i samma intervall på åtta som en annan kommer den inte att använda några ytterligare portar. 

Via [regler för utgående](./outbound-rules.md) trafik och belastningsutjämning kan dessa SNAT-portar distribueras till backend-instanser så att de kan dela lastbalanseras offentliga IP-adresser för utgående anslutningar.

När [scenario 2](#scenario2) nedan har konfigurerats kommer värden för varje backend-instans att SNAT-paket som ingår i en utgående anslutning. 

När du utför SNAT på en utgående anslutning från en backend-instans, skriver värden om käll-IP-adressen till någon av ip-adresserna för frontend. 

För att upprätthålla unika flöden skriver värden om källporten för varje utgående paket till en SNAT-port på backend-instansen.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>Utgående anslutningsbeteende för olika scenarier
  * Virtuell dator med offentlig IP-adress.
  * Virtuell dator utan offentlig IP-adress.
  * Virtuell dator utan offentlig IP-adress och utan standardlastbalanserare.
        
 ### <a name="scenario-1-virtual-machine-with-public-ip-either-with-or-without-a-load-balancer"></a><a name="scenario1"></a> Scenario 1: Virtuell dator med offentlig IP-adress med eller utan lastbalanserare.

 | Sammanslutningar | Metod | IP-protokoll |
 | ---------- | ------ | ------------ |
 | Offentlig lastbalanserare eller fristående | [SNAT (källnätverksadressöversättning)](#snat) </br> används inte. | TCP (Transmission Control Protocol) </br> UDP (User Datagram Protocol) </br> ICMP (Internet Control Message Protocol) </br> ESP (inkapsling av säkerhetsnyttolast) |

 #### <a name="description"></a>Beskrivning

 All trafik återgår till den begärande klienten från den virtuella datorns offentliga IP-adress (IP på instansnivå).
 
 Azure använder den offentliga IP-adress som tilldelats IP-konfigurationen för instansens nätverkskort för alla utgående flöden. Instansen har alla tillfälliga portar tillgängliga. Det spelar ingen roll om den virtuella datorn är belastningsutjämnad eller inte. Det här scenariot har företräde framför de andra. 

 En offentlig IP-adress som tilldelats en virtuell dator är en 1:1-relation (i stället för 1: många) och implementeras som en tillståndslös NAT 1:1 NAT.

 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>Scenario 2: Virtuell dator utan offentlig IP-adress och bakom offentliga standard-Load Balancer

 | Sammanslutningar | Metod | IP-protokoll |
 | ------------ | ------ | ------------ |
 | Offentlig standardlastbalanserare | Användning av lastbalanserings-IP-adresser på frontend-sidan [för SNAT](#snat).| TCP </br> UDP |

 #### <a name="description"></a>Beskrivning

 Lastbalanseringsresursen konfigureras med en utgående regel eller en belastningsutjämningsregel som aktiverar SNAT. Den här regeln används för att skapa en länk mellan den offentliga IP-adressens frontend med backend-poolen. 

 Om du inte slutför den här regelkonfigurationen beskrivs beteendet i scenario 3. 

 En regel med en lyssnare krävs inte för att hälsoavsökningen ska lyckas.

 När en virtuell dator skapar ett utgående flöde översätter Azure käll-IP-adressen till den offentliga IP-adressen för den offentliga lastbalanseringsdatorns frontend. Den här översättningen görs via [SNAT](#snat). 

 Tillfälliga portar i lastbalanseringsdatorns offentliga IP-adress används för att särskilja enskilda flöden som kommer från den virtuella datorn. SNAT använder dynamiskt [förallokerade tillfälliga portar när](#preallocatedports) utgående flöden skapas. 

 I det här sammanhanget kallas de tillfälliga portar som används för SNAT SNAT-portar. Vi rekommenderar starkt att en regel [för utgående](./outbound-rules.md) trafik uttryckligen konfigureras. Om du använder standard-SNAT via en belastningsutjämningsregel allokeras SNAT-portar i förväg enligt beskrivningen i [standardtabellen för SNAT-portallokering.](#snatporttable)

> [!NOTE]
> **Azure Virtual Network NAT** kan ge utgående anslutning för virtuella datorer utan behov av en lastbalanserare. Se [Vad är Azure Virtual Network NAT?](../virtual-network/nat-overview.md) för mer information.

 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-standard-internal-load-balancer"></a><a name="scenario3"></a>Scenario 3: Virtuell dator utan offentlig IP och bakom intern standard-Load Balancer

 | Sammanslutningar | Metod | IP-protokoll |
 | ------------ | ------ | ------------ |
 | Intern standardlastbalanserare | Ingen Internetanslutning.| Inget |

 #### <a name="description"></a>Beskrivning
 
När du använder en intern standardlastbalanserare används inte tillfälliga IP-adresser för SNAT. Den här funktionen stöder säkerhet som standard. Den här funktionen säkerställer att alla IP-adresser som används av resurser kan konfigureras och kan reserveras. 

För att uppnå utgående anslutning till Internet när du använder en intern standardlastbalanserare konfigurerar du en offentlig IP-adress på instansnivå så att den följer beteendet [i scenario 1.](#scenario1) 

Ett annat alternativ är att lägga till instanserna i en offentlig standardlastbalanserare med en konfigurerad regel för utgående trafik. Backend-instanserna läggs till i en intern lastbalanserare för intern lastbalansering. Den här distributionen följer beteendet i [scenario 2](#scenario2). 

> [!NOTE]
> **Azure Virtual Network NAT** kan ge utgående anslutning för virtuella datorer utan behov av en lastbalanserare. Se [Vad Azure Virtual Network NAT?](../virtual-network/nat-overview.md) för mer information.

 ### <a name="scenario-4-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario4"></a>Scenario 4: Virtuell dator utan offentlig IP-adress och bakom Grundläggande Load Balancer

 | Sammanslutningar | Metod | IP-protokoll |
 | ------------ | ------ | ------------ |
 |Inget </br> Grundläggande lastbalanserare | [SNAT](#snat) med dynamisk IP-adress på instansnivå| TCP </br> UDP | 

 #### <a name="description"></a>Beskrivning

 När den virtuella datorn skapar ett utgående flöde översätter Azure källans IP-adress till en dynamiskt tilldelad offentlig käll-IP-adress. Den här offentliga **IP-adressen kan inte konfigureras** och kan inte reserveras. Den här adressen räknas inte mot prenumerationens gräns för offentliga IP-resurser. 

Den offentliga IP-adressen släpps och en ny offentlig IP-adress begärs om du distribuerar om: 

 * Virtuell dator
 * Tillgänglighetsuppsättning
 * Skaluppsättning för virtuella datorer 

 Använd inte det här scenariot för att lägga till IP-adresser i en lista över tillåtna ip-adresser. Använd scenario 1 eller 2 där du uttryckligen deklarerar utgående beteende. [SNAT-portar](#snat) förallokeras enligt beskrivningen i allokeringstabellen [för standard-SNAT-portar.](#snatporttable)

## <a name="exhausting-ports"></a><a name="scenarios"></a> Portar som tar slut

Varje anslutning till samma mål-IP och målport använder en SNAT-port. Den här anslutningen upprätthåller ett **distinkt trafikflöde** från serverinstansen eller **klienten** till en **server**. Den här processen ger servern en distinkt port för att hantera trafik. Utan den här processen är klientdatorn inte medveten om vilket flöde ett paket ingår i.

Tänk dig att flera webbläsare går till https://www.microsoft.com , vilket är:

* Mål-IP = 23.53.254.142
* Målport = 443
* Protokoll = TCP

Utan olika målportar för returtrafiken (SNAT-porten som används för att upprätta anslutningen) kan klienten inte separera ett frågeresultat från ett annat.

Utgående anslutningar kan burst-användas. En backend-instans kan tilldelas otillräckliga portar. Om **återanvändning av anslutningar** inte är aktiverat ökar risken för SNAT-portutmattning. 

Nya utgående anslutningar till en mål-IP-adress misslyckas när porten tar slut. Anslutningar lyckas när en port blir tillgänglig. Den här uttömningen inträffar när de 64 000 portarna från en IP-adress sprids tunt över många backend-instanser. Vägledning om hur du åtgärdar SNAT-portutmattning finns i [felsökningsguiden](./troubleshoot-outbound-connection.md).  

För TCP-anslutningar använder lastbalanseraren en enda SNAT-port för varje mål-IP och port. Den här multianvändningen möjliggör flera anslutningar till samma mål-IP med samma SNAT-port. Den här multianvändningen är begränsad om anslutningen inte är till olika målportar.

För UDP-anslutningar använder lastbalanseraren en portbegränsad NAT-algoritm med en SNAT-port per **mål-IP** oavsett målport. 

En port återanvänds för ett obegränsat antal anslutningar. Porten återanvänds endast om mål-IP-adressen eller porten skiljer sig åt.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Standardportallokering

Varje offentlig IP-adress som tilldelas som en IP-adress i din lastbalanserare får 64 000 SNAT-portar för dess medlemmar i backend-poolen. Portar kan inte delas med medlemmar i en backend-pool. Ett intervall med SNAT-portar kan bara användas av en enda backend-instans för att säkerställa att returpaket dirigeras korrekt. 

Vi rekommenderar att du använder en explicit utgående regel för att konfigurera SNAT-portallokering. Den här regeln maximerar antalet SNAT-portar som varje backend-instans har tillgängliga för utgående anslutningar. 

Om du använder automatisk allokering av utgående SNAT via en belastningsutjämningsregel definierar allokeringstabellen din portallokering.

I följande <a name="snatporttable"></a> tabell visas SNAT-portförplatser för nivåer med storlekar på backend-pooler:

| Poolstorlek (VM-instanser) | Förallokerade SNAT-portar per IP-konfiguration |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 | 

>[!NOTE]
> Om du har en backend-pool med en maxstorlek på 10 kan varje instans ha 64 000/10 = 6 400 portar om du definierar en explicit regel för utgående trafik. Enligt tabellen ovan har var och en endast 1 024 om du väljer automatisk allokering.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Regler för utgående och Virtual Network NAT

Azure Load Balancer utgående regler och Virtual Network NAT alternativ är tillgängliga för utgående trafik från ett virtuellt nätverk.

Mer information om regler för utgående trafik finns i [Regler för utgående trafik.](outbound-rules.md)

Mer information om Azure Virtual Network NAT finns i [Vad är Azure Virtual Network NAT](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Villkor

*   När en anslutning är inaktiv utan att några nya paket skickas släpps portarna efter 4–120 minuter.
  * Det här tröskelvärdet kan konfigureras via regler för utgående trafik.
*   Varje IP-adress har 64 000 portar som kan användas för SNAT.
*   Varje port kan användas för både TCP- och UDP-anslutningar till en ip-måladress
  * En UDP SNAT-port krävs oavsett om målporten är unik eller inte. För varje UDP-anslutning till en mål-IP-adress används en UDP SNAT-port.
  * En TCP SNAT-port kan användas för flera anslutningar till samma mål-IP förutsatt att målportarna skiljer sig åt.
*   SNAT-utmattning inträffar när en backend-instans tar slut på angivna SNAT-portar. En lastbalanserare kan fortfarande ha oanvända SNAT-portar. Om en backend-instanss använda SNAT-portar överskrider de angivna SNAT-portarna kan den inte upprätta nya utgående anslutningar.

## <a name="next-steps"></a>Nästa steg

*   [Felsöka utgående anslutningsfel på grund av SNAT-utmattning](./troubleshoot-outbound-connection.md)
*   [Granska SNAT-mått och](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) bekanta dig med rätt sätt att filtrera, dela och visa dem.
