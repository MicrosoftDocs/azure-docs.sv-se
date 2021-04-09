---
title: Aktivera cookie-baserad tillhörighet med Application Gateway
description: Den här artikeln innehåller information om hur du aktiverar cookie-baserad tillhörighet med en Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a7806cf9518090539ba540a9a522af1aae2691f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397424"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Aktivera cookie-baserad tillhörighet med en Application Gateway
Som beskrivs i [Azure Application Gateway-dokumentationen](./application-gateway-components.md#http-settings)Application Gateway stöder cookie-baserad tillhörighet, vilket innebär att den kan dirigera efterföljande trafik från en användarsession till samma server för bearbetning.

## <a name="example"></a>Exempel
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```