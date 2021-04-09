---
title: Det går inte att skapa kluster med DomainNotFound-fel i Azure HDInsight
description: Fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 54096e6d1741a7be9f50d4b4f7f0fcabbe880588
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943347"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Scenario: det går inte att skapa kluster med DomainNotFound-fel i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det går inte att skapa ett HDI-kluster (Enterprise Security Package) med `DomainNotFound` fel meddelande.

## <a name="cause"></a>Orsak

Felaktiga DNS-inställningar.

## <a name="resolution"></a>Lösning

När domänanslutna kluster distribueras, skapar HDI ett internt användar namn och lösen ord i AAD DS (för varje kluster) och ansluter alla klusternoder till den här domänen. Domän anslutningen görs med hjälp av Samba-verktyg. Se till att följande krav är uppfyllda:

* Domän namnet måste matcha DNS.
* IP-adressen för domän kontrol Lanterna ska anges i DNS-inställningarna för det virtuella nätverk där klustret ska distribueras.
* Om det virtuella nätverket är peer-kopplat med det virtuella nätverket AAD DS måste det göras manuellt.
* Om du använder DNS-vidarebefordrare måste domän namnet lösas korrekt i det virtuella nätverket.
* Säkerhets principer (NSG: er) blockerar inte domän anslutningen.

### <a name="additional-debugging-steps"></a>Ytterligare fel söknings steg

* Distribuera en virtuell Windows-dator i samma undernät, domän Anslut datorn med ett användar namn och lösen ord (detta kan göras via kontroll panelens gränssnitt), eller

* Distribuera en virtuell Ubuntu-dator i samma undernät och domän ansluta till datorn
  * SSH till datorn
  * sudo su
  * Kör skriptet med användar namn och lösen ord
  * Skriptet kommer att pinga, skapa nödvändiga konfigurationsfiler och sedan domän. Om det lyckas är dina DNS-inställningar lämpliga.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]