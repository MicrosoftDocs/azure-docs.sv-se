---
title: Flera HDInsight-kluster & ett Azure Data Lake Storage konto
description: Lär dig hur du använder mer än ett HDInsight-kluster med ett enda Data Lake Storage konto
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 6e220592f53103320c3bdb586fcbd0106219bfed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939535"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Använd flera HDInsight-kluster med ett Azure Data Lake Storage konto

Från och med HDInsight version 3,5 kan du skapa HDInsight-kluster med Azure Data Lake Storage-konton som standard fil system.
Data Lake Storage stöder obegränsad lagring som gör det idealiskt inte bara för att vara värd för stora mängder data. men även för att vara värd för flera HDInsight-kluster som delar ett enda Data Lake Storage-konto. Instruktioner för hur du skapar ett HDInsight-kluster med Data Lake Storage som lagrings utrymme finns i [snabb start: Konfigurera kluster i HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

Den här artikeln ger rekommendationer till Data Lake Storage-administratören för att konfigurera ett enda och delat Data Lake Storage-konto som kan användas i flera **aktiva** HDInsight-kluster. De här rekommendationerna gäller för att vara värd för flera skyddade och icke-säkra Apache Hadoop-kluster på ett delat Data Lake Storage konto.

## <a name="data-lake-storage-file-and-folder-level-acls"></a>Data Lake Storage ACL: er för fil-och mappnivå

Resten av den här artikeln förutsätter att du har en korrekt kunskap om ACL: er för fil-och mappnivå i Azure Data Lake Storage, som beskrivs i detalj vid [åtkomst kontroll i Azure Data Lake Storage](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Data Lake Storage-inställningar för flera HDInsight-kluster

Låt oss ta en mapphierarki i två nivåer för att förklara rekommendationerna för att använda flera HDInsight-kluster med ett Data Lake Storage-konto. Se till att du har ett Data Lake Storage-konto med mappstrukturen **/Clusters/Finance**. Med den här strukturen kan alla kluster som krävs av ekonomi organisationen använda/Clusters/Finance som lagrings plats. I framtiden, om en annan organisation, till exempel marknadsföring, vill skapa HDInsight-kluster med samma Data Lake Storage konto kan de skapa/Clusters/Marketing. Nu ska vi bara använda **/Clusters/Finance**.

Om du vill att den här mappstrukturen ska användas effektivt av HDInsight-kluster måste Data Lake Storage administratören tilldela lämpliga behörigheter enligt beskrivningen i tabellen. Behörigheterna som visas i tabellen motsvarar åtkomst-ACL: er och inte standard-ACL: er.

|Mapp  |Behörigheter  |Ägande användare  |Ägande grupp  | Namngiven användare | Namngivna användar behörigheter | Namngiven grupp | Namngivna grupp behörigheter |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |administratör |administratör  |Tjänstens huvudnamn |--x  |FINGRP   |r-x         |
|/clusters | rwxr-x--x |administratör |administratör |Tjänstens huvudnamn |--x  |FINGRP |r-x         |
|/clusters/finance | rwxr-x--t |administratör |FINGRP  |Tjänstens huvudnamn |RWX  |-  |-     |

I tabellen,

- **admin** är skapare och administratör för det data Lake Storage kontot.
- **Tjänstens huvud** namn är det Azure Active Directory (AAD) som är kopplat till kontot.
- **FINGRP** är en användar grupp som skapats i AAD och som innehåller användare från ekonomi organisationen.

Instruktioner för hur du skapar ett AAD-program (som också skapar ett huvud namn för tjänsten) finns i [skapa ett AAD-program](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Instruktioner för hur du skapar en användar grupp i AAD finns i [hantera grupper i Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Några viktiga saker att tänka på.

- Mappstrukturen på den två nivån (**/Clusters/Finance/**) måste skapas och tillhandahållas med lämpliga behörigheter av data Lake Storage admin **innan** du använder lagrings kontot för kluster. Den här strukturen skapas inte automatiskt när du skapar kluster.
- Exemplet ovan rekommenderar att du anger den ägande gruppen **/Clusters/Finance** som **FINGRP** och tillåter **r-x-** åtkomst till FINGRP till hela mapphierarkin med början från roten. Detta säkerställer att medlemmarna i FINGRP kan navigera i mappstrukturen från roten.
- Om olika AAD-tjänstens huvud namn kan skapa kluster under **/Clusters/Finance**, ser den tröga biten (när den har angetts i **ekonomi** -mappen) till att mappar som skapats av ett huvud namn för tjänsten inte kan tas bort av det andra.
- När mappstrukturen och behörigheterna är på plats skapar HDInsight-klustret en kluster bestämd lagrings plats under **/Clusters/Finance/**. Till exempel kan lagringen för ett kluster med namnet fincluster01 vara **/Clusters/Finance/fincluster01**. Ägarskapet och behörigheterna för de mappar som skapats av HDInsight-klustret visas i tabellen här.

    |Mapp  |Behörigheter  |Ägande användare  |Ägande grupp  | Namngiven användare | Namngivna användar behörigheter | Namngiven grupp | Namngivna grupp behörigheter |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr – x---  |Tjänstens huvudnamn |FINGRP  |- |-  |-   |-  |

## <a name="recommendations-for-job-input-and-output-data"></a>Rekommendationer för indata och utdata för jobb

Vi rekommenderar att indata till ett jobb och utdata från ett jobb lagras i en mapp utanför **/Clusters**. Detta säkerställer att även om den klustrade mappen tas bort för att frigöra lagrings utrymme är jobbets indata och utdata fortfarande tillgängliga för framtida bruk. I sådana fall måste du se till att mapphierarkin för lagring av jobbets indata och utdata tillåter lämplig åtkomst nivå för tjänstens huvud namn.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Gräns för kluster som delar ett enda lagrings konto

Gränsen för antalet kluster som kan dela ett enda Data Lake Storage-konto beror på vilken arbets belastning som körs på dessa kluster. Om du har för många kluster eller mycket stora arbets belastningar i klustren som delar ett lagrings konto kan lagrings kontot komma in/ut för att få en begränsad begränsning.

## <a name="support-for-default-acls"></a>Stöd för Default-ACLs

När du skapar ett huvud namn för tjänsten med åtkomst till namngivna användare (som visas i tabellen ovan) rekommenderar vi **inte** att du lägger till den namngivna användaren med en standard-ACL. Genom att tillhandahålla åtkomst med namnet-användare med hjälp av standard-ACL: er blir tilldelningen av 770 behörigheter för ägande användare, ägande grupp och andra. Även om det här standardvärdet 770 inte tar bort behörigheter från ägande användare (7) eller ägande grupp (7), tar det bort alla behörigheter för andra (0). Detta resulterar i ett känt problem med ett visst användnings fall som beskrivs i detalj i avsnittet [kända problem och lösningar](#known-issues-and-workarounds) .

## <a name="known-issues-and-workarounds"></a>Kända problem och lösningar

I det här avsnittet visas kända problem med att använda HDInsight med Data Lake Storage och deras lösningar.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Offentligt synliga lokaliserade Apache Hadoop garn resurser

När ett nytt Azure Data Lake Storage-konto skapas, tilldelas rot katalogen automatiskt med behörighets-ACL-bitarna inställt på 770. Rotmappens ägande användare anges till den användare som skapade kontot (Data Lake Storage administratören) och den ägande gruppen har angetts till den primära gruppen för den användare som skapade kontot. Det finns ingen åtkomst för "andra".

De här inställningarna är kända för att påverka ett visst HDInsight-användnings fall som samlas in i [garn 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Det gick inte att utföra jobb bidrag med ett fel meddelande som liknar detta:

```output
Resource XXXX is not publicly accessible and as such cannot be part of the public cache.
```

Som anges i garn JIRA som är länkad tidigare, medan lokaliserade offentliga resurser, verifierar lokaliserings tjänsten att alla begärda resurser är offentliga genom att kontrol lera deras behörigheter i fjärrfilsystemet. Alla LocalResource som inte uppfyller det villkoret avvisas för lokalisering. Sök efter behörigheter, inklusive Läs åtkomst till filen för "andra". Det här scenariot fungerar inte direkt när du är värd för HDInsight-kluster på Azure Data Lake, eftersom Azure Data Lake nekar all åtkomst till "andra" på Rotmappens nivå.

#### <a name="workaround"></a>Lösning

Ange Läs-och kör behörigheter för **andra** via hierarkin, till exempel at **/** , **/Clusters** och **/Clusters/Finance** , som visas i tabellen ovan.

## <a name="see-also"></a>Se även

- [Snabbstart: Konfigurera kluster i HDInsight](./hdinsight-hadoop-provision-linux-clusters.md)
- [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](hdinsight-hadoop-use-data-lake-storage-gen2.md)