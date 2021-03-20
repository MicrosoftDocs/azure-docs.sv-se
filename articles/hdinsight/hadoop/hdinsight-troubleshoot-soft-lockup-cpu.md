---
title: Övervaknings fel i övervaknings enheten fel vid mjuk låsning från Azure HDInsight-kluster
description: Övervaknings-CPU fel för mjuk låsning visas i kernel-syslogs från Azure HDInsight-kluster
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: 5d9d7b0fc21660dd22ff92bbe2de38c759c440ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944346"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scenario: "övervaknings enhet: fel: mjuk låsning – CPU" fel från ett Azure HDInsight-kluster

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Kernel-syslog innehåller fel meddelandet: `watchdog: BUG: soft lockup - CPU` .

## <a name="cause"></a>Orsak

En [bugg](https://bugzilla.kernel.org/show_bug.cgi?id=199437) i Linux-kernel orsakar mjuka CPU-låsningar.

## <a name="resolution"></a>Lösning

Tillämpa kernel-korrigering. Skriptet nedan uppgraderar Linux-kärnan och startar om datorerna vid olika tidpunkter under 24 timmar. Kör skript åtgärden i två batchar. Den första batchen finns på alla noder utom Head-noden. Den andra batchen finns på Head-noden. Kör inte på Head-noden och andra noder på samma tid.

1. Navigera till ditt HDInsight-kluster från Azure Portal.

1. Gå till skript åtgärder.

1. Välj **Skicka ny** och ange indatamängden enligt följande

    | Egenskap | Värde |
    | --- | --- |
    | Skript typ | – Anpassad |
    | Name |Korrigering för problem med tyst kernel-lås |
    | Bash-skript-URI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Node-typ (er) |Arbetare, Zookeeper |
    | Parametrar |Ej tillämpligt |

    Välj **Spara den här skript åtgärden...** om du vill köra skriptet när nya noder läggs till.

1. Välj **Skapa**.

1. Vänta tills körningen har slutförts.

1. Kör skript åtgärden på Head-noden genom att följa samma steg som steg 3, men den här gången med Node Types: Head.

1. Vänta tills körningen har slutförts.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]