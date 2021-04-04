---
title: Översikt över TLS-princip för Azure Application Gateway
description: Lär dig hur du konfigurerar TLS-princip för Azure Application Gateway och minskar belastningen på kryptering och dekryptering från en Server grupp på Server sidan.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: amsriva
ms.openlocfilehash: 77239cd8586b8fb07abf6862be436979541bdb99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97631698"
---
# <a name="application-gateway-tls-policy-overview"></a>Översikt över TSL-policyer i Application Gateway

Du kan använda Azure Application Gateway för att centralisera TLS/SSL-certifikat hantering och minska belastningen på kryptering och dekryptering från en Server grupp på Server sidan. Med den här centraliserade TLS-hanteringen kan du också ange en central TLS-princip som passar organisationens säkerhets krav. Detta hjälper dig att uppfylla kraven för efterlevnad samt säkerhets rikt linjer och rekommenderade metoder.

TLS-principen inkluderar kontrollen av TLS-protokollets version samt chiffersviter och i vilken ordning chiffer används under en TLS-handskakning. Application Gateway erbjuder två mekanismer för att styra TLS-principen. Du kan använda antingen en fördefinierad princip eller en anpassad princip.

## <a name="predefined-tls-policy"></a>Fördefinierad TLS-princip

Application Gateway har tre fördefinierade säkerhets principer. Du kan konfigurera din gateway med någon av dessa principer för att få rätt säkerhets nivå. Princip namnen är kommenterade efter det år och den månad då de konfigurerades. Varje princip erbjuder olika TLS-protokoll och chiffersviter. Vi rekommenderar att du använder de senaste TLS-principerna för att säkerställa den bästa TLS-säkerheten.

## <a name="known-issue"></a>Kända problem
Application Gateway v2 har inte stöd för följande DHE-chiffer och de används inte för TLS-anslutningar med klienter, även om de nämns i de fördefinierade principerna. I stället för DHE-chiffer rekommenderas säkra och snabbare ECDHE-chiffer.

- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Egenskap  |Värde  |
|---|---|
|Namn     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Standardvärde| Sant (om ingen fördefinierad princip har angetts) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Egenskap  |Värde  |
|   ---      |  ---       |
|Namn     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Standardvärde| Falskt |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Egenskap  |Värde  |
|---|---|
|Namn     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Standardvärde| Falskt |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>Anpassad TLS-princip

Om en fördefinierad TLS-princip måste konfigureras för dina krav måste du definiera en egen anpassad TLS-princip. Med en anpassad TLS-princip har du fullständig kontroll över den lägsta TLS-protokollversion som stöds, samt de chiffersviter som stöds och deras prioritetsordning.

> [!IMPORTANT]
> Om du använder en anpassad SSL-princip i Application Gateway v1 SKU (standard eller WAF), måste du se till att lägga till de obligatoriska chiffersviter "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256" i listan. Detta chiffer krävs för att aktivera mått och loggning i Application Gateway v1 SKU.
> Detta är inte obligatoriskt för Application Gateway v2 SKU (Standard_v2 eller WAF_v2).
 
### <a name="tlsssl-protocol-versions"></a>TLS/SSL-protokoll versioner

* SSL 2,0 och 3,0 är inaktiverat som standard för alla programgatewayer. Dessa protokoll versioner kan inte konfigureras.
* En anpassad TLS-princip ger dig möjlighet att välja något av följande tre protokoll som lägsta TLS-protokollversion för din Gateway: TLSv1_0, TLSv1_1 och TLSv1_2.
* Om ingen TLS-princip har definierats är alla tre protokollen (TLSv1_0, TLSv1_1 och TLSv1_2) aktiverade.

### <a name="cipher-suites"></a>Chiffersviter

Application Gateway stöder följande chiffersviter som du kan använda för att välja en anpassad princip. Ordningen för chiffersviter avgör prioritetsordningen under TLS-förhandlingen.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> TLS-chiffersviter som används för anslutningen baseras också på typen av certifikat som används. I klient till Application Gateway-anslutningar baseras de chiffersviter som används på typen av Server certifikat i Application Gateway-lyssnaren. De chiffersviter som används i Application Gateway till backend-anslutningspoolen baseras på typen av Server certifikat på backend-poolens servrar.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du konfigurerar en TLS-princip kan du läsa [Konfigurera versioner av TLS-principer och cipher-paket på Application Gateway](application-gateway-configure-ssl-policy-powershell.md).
