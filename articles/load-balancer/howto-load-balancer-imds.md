---
title: Hämta metadata för belastningsutjämnare med hjälp av Azure-Instance Metadata Service (IMDS)
titleSuffix: Azure Load Balancer
description: Kom igång med att lära dig hur du hämtar metadata för belastningsutjämnare med hjälp av Azure Instance Metadata Service.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 95d0e1ceb9e05ce58f388c3f88dc98b2cf6a0cc5
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559595"
---
# <a name="retrieve-load-balancer-metadata-using-the-azure-instance-metadata-service-imds"></a>Hämta metadata för belastningsutjämnare med hjälp av Azure-Instance Metadata Service (IMDS)

## <a name="prerequisites"></a>Förutsättningar

* Använd den [senaste API-versionen](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#supported-api-versions) för din begäran.

## <a name="sample-request-and-response"></a>Exempel förfrågan och svar
> [!IMPORTANT]
> I det här exemplet kringgås proxyservrar. Du **måste** kringgå proxyservrar när du frågar efter IMDS. Mer information finns i [proxyservrar](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#proxies).
### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01" | ConvertTo-Json
```
> [!NOTE]
> Parametern-noproxy introducerades i PowerShell 6,0. Om du använder en äldre version av PowerShell, ta bort-noproxy i begär ande texten och se till att du inte använder en proxy när du hämtar IMDS-information. Läs mer [här](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#proxies).
> 
### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H "Metadata:true" --noproxy "*" "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01"
```

---
### <a name="sample-response"></a>Exempelsvar

```json
{
   "loadbalancer": {
    "publicIpAddresses":[
      {
         "frontendIpAddress":"51.0.0.1",
         "privateIpAddress":"10.1.0.4"
      }
   ],
   "inboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frontendIpAddress":"2603:10e1:100:2::1:1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
   ],
   "outboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frotendIpAddress":"2603:10e1:100:2::1:1",
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
    ]
   }
}

```

## <a name="next-steps"></a>Nästa steg
[Vanliga felkoder och fel söknings steg](troubleshoot-load-balancer-imds.md)

Läs mer om [Azure instance metadata service](../virtual-machines/windows/instance-metadata-service.md)

[Hämta alla metadata för en instans](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#access-azure-instance-metadata-service)

[Distribuera en standard Load Balancer](quickstart-load-balancer-standard-public-portal.md)