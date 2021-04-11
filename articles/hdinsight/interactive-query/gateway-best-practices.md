---
title: Gateway-djupgående och bästa praxis för Apache Hive i Azure HDInsight
description: Lär dig hur du navigerar till bästa praxis för att köra Hive-frågor via Azure HDInsight-gatewayen
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 549eab1547b75eb9461b23df2c157290943b4ed9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869794"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Gateway-djupgående och bästa praxis för Apache Hive i Azure HDInsight

Azure HDInsight Gateway (Gateway) är HTTPS-frontend-filen för HDInsight-kluster. Gatewayen ansvarar för: autentisering, värd lösning, tjänst identifiering och andra användbara funktioner som krävs för ett modernt distribuerat system. De funktioner som tillhandahålls av gatewayen resulterar i vissa kostnader för vilka det här dokumentet beskriver de bästa metoderna för att navigera. Gateways fel söknings metoder diskuteras också.

## <a name="the-hdinsight-gateway"></a>HDInsight-gatewayen

HDInsight-gatewayen är den enda delen av ett HDInsight-kluster som är offentligt tillgängligt via Internet. Gateway-tjänsten kan nås utan att gå via det offentliga Internet genom att använda den `clustername-int.azurehdinsight.net` interna Gateway-slutpunkten. Den interna Gateway-slutpunkten gör det möjligt att upprätta anslutningar till Gateway-tjänsten utan att avsluta klustrets virtuella nätverk. Gatewayen hanterar alla begär Anden som skickas till klustret och vidarebefordrar sådana förfrågningar till rätt komponenter och kluster värdar.

Följande diagram ger en tydlig illustration av hur gatewayen ger en abstraktion framför alla olika värd upplösnings möjligheter i HDInsight.

:::image type="content" source="./media/gateway-best-practices/host-resolution-diagram.png " alt-text="Värd matchnings diagram" border="true":::

## <a name="motivation"></a>Motivation

Motivation att placera en gateway framför HDInsight-kluster är att tillhandahålla ett gränssnitt för tjänst identifiering och användarautentisering. De autentiseringsmekanismer som tillhandahålls av gatewayen är särskilt relevanta för ESP-aktiverade kluster.

För tjänst identifiering är fördelen med Gateway att varje komponent i klustret kan nås som en annan slut punkt under Gateway-webbplatsen ( `clustername.azurehdinsight.net/hive2` ), i stället för en mängd `host:port` par.

För autentisering gör det möjligt för användare att autentisera med hjälp av ett `username:password` Credential-par. För ESP-aktiverade kluster skulle den här autentiseringsuppgiften vara användarens domän användar namn och lösen ord. Autentisering till HDInsight-kluster via gatewayen kräver inte att klienten skaffar en Kerberos-biljett. Eftersom gatewayen accepterar `username:password` autentiseringsuppgifter och hämtar användarens Kerberos-biljett för användarens räkning, kan säkra anslutningar göras till gatewayen från valfri klient värd, inklusive klienter som är anslutna till olika AA-DDS-domäner än (ESP)-klustret.

## <a name="best-practices"></a>Bästa praxis

Gatewayen är en enskild tjänst (belastning bal anse rad över två värdar) som ansvarar för vidarebefordran av förfrågningar och autentisering. Gatewayen kan bli en Flask hals för Hive-frågor som överskrider en viss storlek. Prestanda försämring av frågor kan observeras när mycket stora **urvals** frågor körs på gatewayen via ODBC eller JDBC. "Mycket stor" innebär frågor som utgör mer än 5 GB data över rader eller kolumner. Den här frågan kan innehålla en lång lista med rader och eller ett brett kolumn antal.

Gatewayens prestanda försämring kring frågor med stor storlek beror på att data måste överföras från det underliggande data lagret (ADLS Gen2) till: HDInsight Hive-servern, gatewayen och slutligen via JDBC-eller ODBC-drivrutinerna till klient värden.

Följande diagram illustrerar de steg som ingår i en SELECT-fråga.

:::image type="content" source="./media/gateway-best-practices/result-retrieval-diagram.png " alt-text="Resultat diagram" border="true":::

Apache Hive är en relationell abstraktion ovanpå ett HDFS-kompatibelt fil system. Den här abstraktionen innebär att **Select** -instruktioner i Hive motsvarar **Läs** åtgärder i fil systemet. **Läs** åtgärder översätts till lämpligt schema innan de rapporteras till användaren. Svars tiden för den här processen ökar med data storlek och totalt antal hopp som krävs för att komma åt slutanvändaren.

Liknande beteende kan uppstå när du kör **create** eller **insert** -instruktioner för stora data, eftersom dessa kommandon motsvarar **Skriv** åtgärder i det underliggande fil systemet. Överväg att skriva data, till exempel RAW-ORC, till fil systemet/datalake i stället för att läsa in det med hjälp av **insert** eller **load**.

I Enterprise Security Pack-aktiverade kluster kan tillräckligt komplexa Apache Ranger-principer orsaka en långsammare tid för att beräkna frågor, vilket kan leda till en gateway-timeout. Om en gateway-tidsgräns har märkts i ett ESP-kluster bör du överväga att minska eller kombinera antalet Ranger-principer.

## <a name="troubleshooting-techniques"></a>Fel söknings tekniker

Det finns flera platser för att undvika och förstå prestanda problem som en del av ovanstående beteende. Använd följande check lista när du upplever frågor om prestanda försämring över HDInsight-gatewayen:

* Använd **begränsnings** satsen när du kör stora **urvals** frågor. **Begränsnings** satsen minskar antalet rader som rapporteras till klient värden. **Begränsnings** satsen påverkar endast generering av resultat och ändrar inte frågeplan. Använd konfigurationen om du vill tillämpa **begränsnings** satsen i frågeuttrycket `hive.limit.optimize.enable` . **Gränsen** kan kombineras med en förskjutning med hjälp av argument formen **Limit x, y**.

* Namnge dina kolumner med intresse när du kör **Select** -frågor i stället för att använda **Select \***. Om du väljer färre kolumner sänks mängden data som ska läsas.

* Prova att köra frågan på intresse genom Apache Beeline. Om resultat hämtning via Apache Beeline tar en längre tid, förväntar sig fördröjningar vid hämtning av samma resultat via externa verktyg.

* Testa en grundläggande Hive-fråga för att säkerställa att det går att upprätta en anslutning till HDInsight-gatewayen. Prova att köra en grundläggande fråga från två eller flera externa verktyg för att se till att inget enskilt verktyg körs i problem.

* Granska alla Apache Ambari-aviseringar för att se till att HDInsight-tjänsterna är felfria. Ambari-aviseringar kan integreras med Azure Monitor för rapportering och analys.

* Om du använder en extern Hive-Metaarkiv kontrollerar du att Azure SQL DB DTU för Hive-Metaarkiv inte har nått sin gräns. Om DTU närmar sig gränsen måste du öka databas storleken.

* Se till att alla verktyg från tredje part, till exempel PBI eller Tableau, använder sid brytning för att visa tabeller och databaser. Kontakta support partner för de här verktygen för att få hjälp med sid brytning. Huvud verktyget som används för sid brytning är parametern JDBC `fetchSize` . En liten hämtnings storlek kan resultera i försämrade Gateway-prestanda, men en för stor hämtnings storlek kan resultera i en gateway-tidsgräns. Justering av hämtnings storlek måste göras på grund av en arbets belastning.

* Om data pipelinen omfattar läsning av stora mängder data från HDInsight-klustrets underliggande lagring, bör du överväga att använda ett verktyg som gränssnitten är direkt med Azure Storage som Azure Data Factory

* Överväg att använda Apache Hive LLAP när du kör interaktiva arbets belastningar, eftersom LLAP kan ge en smidigare upplevelse för att snabbt returnera frågeresultat

* Överväg att öka antalet tillgängliga trådar för Hive Metaarkiv-tjänsten med hjälp av `hive.server2.thrift.max.worker.threads` . Den här inställningen är särskilt relevant när ett stort antal samtidiga användare skickar frågor till klustret

* Minska antalet återförsök som används för att komma åt gatewayen från alla externa verktyg. Om flera återförsök används bör du överväga att följa en exponentiell säkerhets kopiering av återförsöks princip

* Överväg att aktivera komprimering av Hive med konfigurationerna `hive.exec.compress.output` och `hive.exec.compress.intermediate` .

## <a name="next-steps"></a>Nästa steg

* [Apache Beeline på HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md)
* [Fel söknings steg för HDInsight Gateway](./troubleshoot-gateway-timeout.md)
* [Virtuella nätverk för HDInsight](../hdinsight-plan-virtual-network-deployment.md)
* [HDInsight med Express Route](../connect-on-premises-network.md)