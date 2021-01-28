---
title: Det går inte att logga in på Azure HDInsight-kluster
description: Felsök varför det inte går att logga in på Apache Hadoop kluster i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 03657606f7e24436ff5a851fe7eef1e0c4d5dc24
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944316"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Scenario: det går inte att logga in på Azure HDInsight-kluster

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det går inte att logga in på Azure HDInsight-kluster.

## <a name="cause"></a>Orsak

Anledningen kan variera. Tänk på att när du loggar in på kluster-eller app-instrumentpaneler använder du din "kluster inloggning" eller HTTP-autentiseringsuppgifter. När du fjärransluter använder du dina autentiseringsuppgifter för Secure Shell (SSH) eller fjärrskrivbordet.

## <a name="resolution"></a>Lösning

Prova ett eller flera av följande steg för att lösa vanliga problem.

* Försök öppna instrumentpanelen för klustret i en ny webbläsarflik i sekretessläget.

* Om du inte kan återkalla dina SSH-autentiseringsuppgifter kan du [återställa autentiseringsuppgifterna i Ambari-användargränssnittet](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]