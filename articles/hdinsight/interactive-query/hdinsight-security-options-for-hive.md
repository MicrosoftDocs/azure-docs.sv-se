---
title: Säkerhets alternativ för Hive i Azure HDInsight
description: Säkerhets alternativ för Hive i standard-och ESP-kluster.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: a608c34225641a3c7764d6c7dd3872c5f61fe3c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869726"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Säkerhets alternativ för Hive i Azure HDInsight

Det här dokumentet beskriver rekommenderade säkerhets alternativ för Hive i HDInsight. Dessa alternativ kan konfigureras via Ambari.

:::image type="content" source="./media/hdinsight-security-options-for-hive/security-options-hive.png " alt-text="' Säkerhets alternativ för Hive '" border="true":::

## <a name="hiveserver2-authentication"></a>HiveServer2-autentisering

För standard kluster är den rekommenderade inställningen för HiveServer2-autentisering standardvärdet som är ingen. Om du vill aktivera autentisering rekommenderar vi att du uppgraderar till ett [ESP](../domain-joined/hdinsight-security-overview.md) -kluster (Enterprise Security Package). 

För ESP-kluster är [Kerberos](https://web.mit.edu/Kerberos/) -autentisering aktiverat som standard. PAM (Pluggable Authentication modules) och anpassade autentiseringsscheman stöds inte.

## <a name="hiveserver2-authorization"></a>HiveServer2-auktorisering

Standardvärdet är ingen för standard kluster. [SqlStdAuth (SQL-standardbaserad auktorisering)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) kan aktive ras. Auktorisering via [Apache Ranger](https://ranger.apache.org/) stöds inte för standard kluster. Vi rekommenderar att du uppgraderar till ett ESP-kluster för Ranger-auktorisering. 

För ESP-kluster är auktorisering via Ranger aktiverat som standard. 


## <a name="ssl-encryption-for-hiveserver2"></a>SSL-kryptering för HiveServer2

Aktivering av Hiveserver2-SSL rekommenderas inte för antingen standard-eller ESP-kluster. SSL är aktiverat på gatewayen i stället. [Kryptering i överföring](../domain-joined/encryption-in-transit.md) kan aktive ras för kryptering av kommunikation mellan klusternoderna med [Internet Protocol säkerhet (IPSec)](https://en.wikipedia.org/wiki/IPsec).


## <a name="next-steps"></a>Nästa steg
* [Översikt över HiveServer2-autentisering](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [Översikt över HiveServer2-auktorisering](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization)
* [Aktivera SQL-standarden baserad på Hive-auktorisering](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Apache Ranger med Hive](../domain-joined/apache-domain-joined-run-hive.md)
