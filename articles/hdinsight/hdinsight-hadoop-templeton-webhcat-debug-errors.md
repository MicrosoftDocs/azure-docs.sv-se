---
title: Förstå och lösa WebHCat-fel i HDInsight – Azure
description: Lär dig mer om vanliga fel som returneras av WebHCat i HDInsight och hur du löser dem.
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: ce7a21a2b5401c68c6b58bc934fb20b0225ba8ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945455"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Förstå och lösa fel som tas emot från WebHCat på HDInsight

Lär dig mer om fel som har tagits emot när du använder WebHCat med HDInsight och hur du löser dem. WebHCat används internt av verktyg på klient sidan, till exempel Azure PowerShell och Data Lake verktyg för Visual Studio.

## <a name="what-is-webhcat"></a>Vad är WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) är en REST API för [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), ett tabell-och lagrings hanterings lager för Apache Hadoop. WebHCat är aktiverat som standard i HDInsight-kluster och används av olika verktyg för att skicka jobb, Hämta jobb status och så vidare, utan att logga in på klustret.

## <a name="modifying-configuration"></a>Ändrar konfiguration

Flera av de fel som anges i det här dokumentet inträffar eftersom ett konfigurerat maximum har överskridits. När lösnings steget nämner att du kan ändra ett värde, använder du Apache Ambari (webb eller REST API) för att ändra värdet. Mer information finns i [Hantera HDInsight med Apache Ambari](hdinsight-hadoop-manage-ambari.md)

### <a name="default-configuration"></a>Standardkonfiguration

Om följande standardvärden överskrids kan det försämra WebHCat prestanda eller orsaka fel:

| Inställning | Vad läget gör | Standardvärde |
| --- | --- | --- |
| [garn. Scheduler. Capacity. maximum-Applications][maximum-applications] |Maximalt antal jobb som kan vara aktiva samtidigt (väntar eller körs) |10 000 |
| [templeton.exec. Max-procs][max-procs] |Maximalt antal begär Anden som kan betjänas samtidigt |20 |
| [MapReduce. jobhistory. Max-Age-MS][max-age-ms] |Antalet dagar som jobb historiken bevaras |7 dagar |

## <a name="too-many-requests"></a>För många begär Anden

**Http-status kod**: 429

| Orsak | Lösning |
| --- | --- |
| Du har överskridit maximalt antal samtidiga begär Anden som hanteras av WebHCat per minut (standard 20) |Minska din arbets belastning för att säkerställa att du inte skickar fler än det högsta antalet samtidiga förfrågningar eller öka gränsen för samtidiga förfrågningar genom att ändra `templeton.exec.max-procs` . Mer information finns i [ändra konfiguration](#modifying-configuration) |

## <a name="server-unavailable"></a>Servern är inte tillgänglig

**Http-status kod**: 503

| Orsak | Lösning |
| --- | --- |
| Denna status kod inträffar vanligt vis vid redundansväxling mellan den primära och sekundära huvudnoden för klustret |Vänta två minuter och försök sedan igen |

## <a name="bad-request-content-could-not-find-job"></a>Felaktigt innehåll i begäran: det gick inte att hitta jobbet

**Http-status kod**: 400

| Orsak | Lösning |
| --- | --- |
| Jobb information har rensats av jobb historiken |Standard kvarhållningsperioden för jobb historik är 7 dagar. Standard kvarhållningsperioden kan ändras genom att ändra `mapreduce.jobhistory.max-age-ms` . Mer information finns i [ändra konfiguration](#modifying-configuration) |
| Jobbet har stoppats på grund av en redundansväxling |Försök att skicka jobb på upp till två minuter |
| Ett ogiltigt jobb-ID användes |Kontrol lera om jobb-ID: t är korrekt |

## <a name="bad-gateway"></a>Felaktig gateway

**Http-status kod**: 502

| Orsak | Lösning |
| --- | --- |
| Intern skräp insamling sker inom WebHCat-processen |Vänta tills skräp insamling har slutförts eller starta om WebHCat-tjänsten |
| Tids gränsen nåddes i väntan på ett svar från ResourceManager-tjänsten. Det här felet kan inträffa när antalet aktiva program är konfigurerat maximum (standard 10 000) |Vänta tills pågående jobb har slutförts eller öka gränsen för antalet samtidiga jobb genom att ändra `yarn.scheduler.capacity.maximum-applications` . Mer information finns i avsnittet [ändra konfiguration](#modifying-configuration) . |
| Försök att hämta alla jobb via [Get/Jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) -anropet medan `Fields` har angetts till `*` |Hämta inte *all* jobb information. Använd i stället `jobid` för att hämta information för jobb som är större än ett visst jobb-ID. Eller Använd inte `Fields` |
| WebHCat-tjänsten är avstängd vid huvudnoden-redundans |Vänta i två minuter och försök igen |
| Det finns fler än 500 väntande jobb som skickas via WebHCat |Vänta tills pågående jobb har slutförts innan du skickar fler jobb |

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml