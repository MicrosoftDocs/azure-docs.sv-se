---
title: Så här hämtar du värd namnen för Azure HDInsight-klusternoder
description: Lär dig mer om hur du hämtar värdnamn och FQDN-namn på klusternoder i Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
author: yanancai
ms.author: yanacai
ms.date: 03/23/2021
ms.openlocfilehash: 676b4ccd52e8a6f1f378756a255ad55b74f18ebe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111491"
---
# <a name="find-the-host-names-of-cluster-nodes"></a>Hitta värd namnen för klusternoder

HDInsight-kluster skapas med offentlig DNS-clustername.azurehdinsight.net. När du använder SSH till enskilda noder eller ställer in anslutning till klusternoder med i samma anpassade virtuella nätverk, måste du använda värdnamn eller fullständigt kvalificerade domän namn (FQDN) för klusternoder.

I den här artikeln får du lära dig hur du hämtar värd namnen för klusternoder. Du kan hämta den manuellt via Ambari-webbgränssnittet eller automatiskt via Ambari REST API.

> [!WARNING]
> Använd följande rekommenderade metoder för att hämta värdnamn för klusternoder. Numren i värd namnet är inte garanterade i sekvens och HDInsight kan ändra värd namns formatet så att det överensstämmer med virtuella datorer med versions uppdatering. Ta inte beroende av en viss namngivnings konvention som finns idag. 
>

Du kan hämta värd namnen genom Ambari-gränssnittet eller Ambari-REST API. 

## <a name="get-the-host-names-from-ambari-web-ui"></a>Hämta värd namnen från Ambari-webbgränssnittet
Du kan använda Ambari-webbgränssnitt för att hämta värd namnen när du SSH till noden. Vyn Ambari Web UI hosts finns i ditt HDInsight-kluster på `https://CLUSTERNAME.azurehdinsight.net/#/main/hosts` , där `CLUSTERNAME` är namnet på klustret.

![Get-Host-Names-in-Ambari-UI](.\media\find-host-name\find-host-name-in-ambari-ui.png)

## <a name="get-the-host-names-from-ambari-rest-api"></a>Hämta värd namnen från Ambari REST API
När du skapar Automation-skript kan du använda Ambari-REST API för att hämta värd namnen innan du ansluter till värdarna. Numren i värd namnet är inte garanterade i sekvens och HDInsight kan ändra värd namns formatet så att det överensstämmer med virtuella datorer med versions uppdatering. Ta inte beroende av en viss namngivnings konvention som finns idag. 

Här följer några exempel på hur du hämtar FQDN för noderna i klustret. Mer information om Ambari REST API finns i [Hantera HDInsight-kluster med hjälp av Apache Ambari REST API](.\hdinsight-hadoop-manage-ambari-rest-api.md)

I följande exempel används [JQ](https://stedolan.github.io/jq/) eller [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) för att parsa JSON Response-dokumentet och bara Visa värd namnen.

```bash
export password=''
export clusterName=''
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$clusterName=''
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```