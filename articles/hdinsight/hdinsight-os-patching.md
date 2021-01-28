---
title: Konfigurera operativ systemets uppdaterings schema för Azure HDInsight-kluster
description: Lär dig hur du konfigurerar operativ systemets uppdaterings schema för Linux-baserade HDInsight-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: 636caf592baa4df771f7cc50095911d0337456d0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939393"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Konfigurera operativ systemets uppdaterings schema för Linux-baserade HDInsight-kluster

> [!IMPORTANT]
> Ubuntu-avbildningar blir tillgängliga för nya Azure HDInsight-kluster inom tre månader efter att de har publicerats. Att köra kluster installeras inte automatiskt. Kunder måste använda skript åtgärder eller andra mekanismer för att korrigera ett kluster som körs. Vi rekommenderar att du kör dessa skript åtgärder och tillämpar säkerhets uppdateringar direkt efter att klustret har skapats.

HDInsight ger stöd för att utföra vanliga uppgifter i klustret, till exempel att installera OS-korrigeringsfiler, säkerhets uppdateringar och starta om noder. Dessa uppgifter utförs med hjälp av följande två skript som kan köras som [skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md)och som kon figurer ATS med parametrar:

- `schedule-reboots.sh` -Gör en omedelbar omstart eller Schemalägg en omstart på klusternoderna.
- `install-updates-schedule-reboots.sh` -Installera alla uppdateringar, endast kernel + säkerhets uppdateringar eller bara kernel-uppdateringar.

> [!NOTE]  
> Skript åtgärder tillämpar inte automatiskt uppdateringar för alla framtida uppdaterings cykler. Kör skripten varje gången nya uppdateringar måste tillämpas för att installera uppdateringarna och starta sedan om den virtuella datorn.

## <a name="preparation"></a>Förberedelse

Korrigering på en representativ icke-produktions miljö innan du distribuerar till produktion. Utveckla en plan för att testa systemet på ett bra sätt innan du korrigerar den faktiska korrigeringen.

Från tid till gång, från en SSH-session med klustret, kan det hända att du får ett meddelande om att säkerhets uppdateringar är tillgängliga. Meddelandet kan se ut ungefär så här:

```
89 packages can be updated.
82 updates are security updates.

*** System restart required ***

Welcome to Spark on HDInsight.

```

Uppdatering är valfri och du behöver.

## <a name="restart-nodes"></a>Starta om noder
  
Skript [schema – omstarter](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)anger den typ av omstart som ska utföras på datorerna i klustret. När du skickar skript åtgärden ställer du in den så att den gäller för alla tre nodtyper: Head Node, Worker-nod och Zookeeper. Om skriptet inte tillämpas på en nodtyp, kommer de virtuella datorerna för den nodtypen inte att uppdateras eller startas om.

`schedule-reboots script`Accepterar en numerisk parameter:

| Parameter | Godkända värden | Definition |
| --- | --- | --- |
| Typ av omstart att utföra | 1 eller 2 | Värdet 1 möjliggör omstart av schemat (schemalagt i 12-24 timmar). Värdet 2 aktiverar omedelbar omstart (om 5 minuter). Om ingen parameter anges är standardvärdet 1. |  

## <a name="install-updates-and-restart-nodes"></a>Installera uppdateringar och starta om noder

Skriptet [install-updates-Schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) innehåller alternativ för att installera olika typer av uppdateringar och starta om den virtuella datorn.

`install-updates-schedule-reboots`Skriptet accepterar två numeriska parametrar, enligt beskrivningen i följande tabell:

| Parameter | Godkända värden | Definition |
| --- | --- | --- |
| Typ av uppdateringar som ska installeras | 0, 1 eller 2 | Värdet 0 installerar endast kernel-uppdateringar. Värdet 1 installerar kernel + säkerhets uppdateringar och 2 installerar alla uppdateringar. Om ingen parameter anges är standardvärdet 0. |
| Typ av omstart att utföra | 0, 1 eller 2 | Värdet 0 inaktiverar omstart. Värdet 1 möjliggör omstart av schemat och 2 aktiverar omedelbar omstart. Om ingen parameter anges är standardvärdet 0. Användaren måste ändra Indataparametern 1 till indataparameter 2. |

> [!NOTE]
> Du måste markera ett skript som beständigt när du har tillämpat det på ett befintligt kluster. Annars kommer alla nya noder som skapats med skalnings åtgärder att använda standard uppdaterings schemat. Om du använder skriptet som en del av processen för att skapa kluster sparas det automatiskt.

> [!NOTE]
> Alternativet för schemalagd omstart görs en automatisk omstart av de korrigerade klusternoderna under en period på 12 till 24 timmar och tar hänsyn till hög tillgänglighet, uppdaterings domän och fel domän överväganden. Schemalagd omstart avslutar inte pågående arbets belastningar, men kan ta bort kluster kapaciteten under tiden när noderna inte är tillgängliga, vilket leder till längre bearbetnings tider. 

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder skript åtgärder finns i följande avsnitt i [Anpassa Linux-baserade HDInsight-kluster med skript åtgärd](hdinsight-hadoop-customize-cluster-linux.md):

- [Använd en skript åtgärd när klustret skapas](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [Tillämpa en skript åtgärd på ett kluster som körs](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
