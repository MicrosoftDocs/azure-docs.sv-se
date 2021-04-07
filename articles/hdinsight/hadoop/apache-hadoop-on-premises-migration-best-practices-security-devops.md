---
title: 'Säkerhet: migrera lokala Apache Hadoop till Azure HDInsight'
description: Lär dig mer om säkerhets-och DevOps för att migrera lokala Hadoop-kluster till Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: fa6a4a8686fe5a33a6f240a8e972a687e872732a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939747"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight – säkerhets-och DevOps metod tips

Den här artikeln innehåller rekommendationer för säkerhets-och DevOps i Azure HDInsight-system. Den ingår i en serie som ger bästa praxis för att hjälpa till att migrera lokala Apache Hadoop system till Azure HDInsight.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Skydda och styra kluster med Enterprise Security Package

Enterprise Security Package (ESP) stöder Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomst kontroll. Med det ESP-alternativ som valts är HDInsight-klustret anslutet till Active Directory-domänen och företags administratören kan konfigurera rollbaserad åtkomst kontroll (RBAC) för Apache Hive säkerhet med hjälp av Apache Ranger. Administratören kan också granska data åtkomsten för anställda och eventuella ändringar som gjorts i åtkomst kontroll principer.

ESP är tillgängligt på följande kluster typer: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka och interaktiv fråga (Hive LLAP).

Använd följande steg för att distribuera det domänanslutna HDInsight-klustret:

- Distribuera Azure Active Directory (AAD) genom att skicka domän namnet.
- Distribuera Azure Active Directory Domain Services (AAD DS).
- Skapa nödvändiga Virtual Network och undernät.
- Distribuera en virtuell dator i Virtual Network för att hantera AAD DS.
- Anslut den virtuella datorn till domänen.
- Installera AD-och DNS-verktyg.
- Be AAD DS-administratören att skapa en organisationsenhet (OU).
- Aktivera LDAPs för AAD DS.
- Skapa ett tjänst konto i Azure Active Directory med delegerad Läs-& Skriv administratörs behörighet till ORGANISATIONSENHETen, så att det kan. Det här tjänst kontot kan sedan ansluta datorer till domänen och placera datorns huvud namn i ORGANISATIONSENHETen. Det kan också skapa tjänstens huvud namn i den ORGANISATIONSENHET som du anger när du skapar klustret.

    > [!Note]
    > Tjänst kontot behöver inte vara ett AD-domän administratörs konto.

- Distribuera HDInsight ESP-kluster genom att ange följande parametrar:

    |Parameter |Beskrivning |
    |---|---|
    |Domännamn|Det domän namn som är associerat med Azure AD DS.|
    |Domän användar namn|Tjänst kontot i den domänkontrollant-hanterade Azure AD DS-domän som du skapade i föregående avsnitt, till exempel: `hdiadmin@contoso.onmicrosoft.com` . Den här domän användaren kommer att vara administratör för det här HDInsight-klustret.|
    |Domän lösen ord|Lösen ordet för tjänst kontot.|
    |Organisationsenhet|Det unika namnet på den ORGANISATIONSENHET som du vill använda med HDInsight-klustret, till exempel: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com` . Om ORGANISATIONSENHETen inte finns försöker HDInsight-klustret skapa ORGANISATIONSENHETen med hjälp av tjänst kontots behörigheter.|
    |LDAPS-URL|till exempel `ldaps://contoso.onmicrosoft.com:636` .|
    |Åtkomst till användar grupp|De säkerhets grupper vars användare du vill synkronisera till klustret, till exempel: `HiveUsers` . Om du vill ange flera användar grupper avgränsar du dem med semikolone;. Grupp (er) måste finnas i katalogen innan du skapar ESP-klustret.|

Mer information finns i följande artiklar:

- [En introduktion till Apache Hadoop säkerhet med domänanslutna HDInsight-kluster](../domain-joined/hdinsight-security-overview.md)
- [Planera Azure-domänanslutna Apache Hadoop kluster i HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Konfigurera ett domänanslutet HDInsight-kluster med hjälp av Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Synkronisera Azure Active Directory-användare med ett HDInsight-kluster](../hdinsight-sync-aad-users-to-cluster.md)
- [Konfigurera Apache Hive principer i domänanslutna HDInsight](../domain-joined/apache-domain-joined-run-hive.md)
- [Köra apache Oozie i domänanslutna HDInsight Hadoop-kluster](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>Implementera företags säkerhet från slut punkt till slut punkt

Säkerhet från slut punkt till slut punkt kan uppnås med hjälp av följande kontroller:

**Privat och skyddad datapipeline (gräns nivå säkerhet)**
    - Säkerhet på perimeternätverket kan uppnås via virtuella Azure-nätverk, nätverks säkerhets grupper och gateway-tjänster.

**Autentisering och auktorisering för data åtkomst**
    - Skapa ett domänanslutet HDInsight-kluster med hjälp av Azure Active Directory Domain Services. (Enterprise Security Package).
    - Använd Ambari för att ge rollbaserad åtkomst till kluster resurser för AD-användare.
    - Använd Apache Ranger för att ange principer för åtkomst kontroll för Hive på tabell-/kolumn-/rad nivå.
    - SSH-åtkomst till klustret kan bara begränsas till administratören.

**Granskning**
    - Visa och rapportera all åtkomst till kluster resurser och data för HDInsight.
    - Visa och rapportera alla ändringar av principer för åtkomst kontroll.

**Kryptering**
    - Transparent Server-Side kryptering med hjälp av Microsoft-hanterade nycklar eller kund hanterade nycklar.
    - I överförings kryptering med hjälp av Client-Side kryptering, https och TLS.

Mer information finns i följande artiklar:

- [Översikt över virtuella Azure-nätverk](../../virtual-network/virtual-networks-overview.md)
- [Översikt över Azure nätverks säkerhets grupper](../../virtual-network/network-security-groups-overview.md)
- [Peering för virtuella nätverk i Azure](../../virtual-network/virtual-network-peering-overview.md)
- [Säkerhetsguiden för Azure Storage](../../storage/blobs/security-recommendations.md)
- [Azure Storage tjänst kryptering i vila](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Använda övervaknings & avisering

Mer information finns i artikeln:

[Översikt över Azure Monitor](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Uppgradera kluster

Uppgradera regelbundet till den senaste HDInsight-versionen för att dra nytta av de senaste funktionerna. Följande steg kan användas för att uppgradera klustret till den senaste versionen:

1. Skapa ett nytt TEST av HDInsight-kluster med den senaste tillgängliga HDInsight-versionen.
1. Testa på det nya klustret för att se till att jobben och arbets belastningarna fungerar som förväntat.
1. Ändra jobb eller program eller arbets belastningar efter behov.
1. Säkerhetskopiera alla tillfälliga data som lagras lokalt på klusternoderna.
1. Ta bort det befintliga klustret.
1. Skapa ett kluster av den senaste HDInsight-versionen i samma undernät för virtuellt nätverk, med samma standard data och meta-lagring som i det tidigare klustret.
1. Importera alla tillfälliga data som har säkerhetskopierats.
1. Starta jobb/Fortsätt bearbeta med det nya klustret.

Mer information finns i artikeln: [Uppgradera HDInsight-kluster till en ny version](../hdinsight-upgrade-cluster.md).

## <a name="patch-cluster-operating-systems"></a>Korrigera kluster operativ system

Mer information finns i artikeln: [uppdatering av operativ system för HDInsight](../hdinsight-os-patching.md).

## <a name="post-migration"></a>Efter migrering

1. **Åtgärda program** – gör upprepade ändringar i jobben, processerna och skripten.
2. **Utföra tester** – köra funktions-och prestandatester upprepade gånger.
3. **Optimera** -åtgärda eventuella prestanda problem baserat på ovanstående test resultat och testa sedan om för att bekräfta prestanda förbättringarna.

## <a name="next-steps"></a>Nästa steg

Läs mer om [HDInsight 4,0](./apache-hadoop-introduction.md).