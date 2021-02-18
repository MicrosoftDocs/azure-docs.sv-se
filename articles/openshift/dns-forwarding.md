---
title: Konfigurera DNS-vidarebefordran för Azure Red Hat OpenShift 4
description: Konfigurera DNS-vidarebefordran för Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 68cc5dbee5b96472d11bdfb3a8bf35d118638dcd
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633877"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Konfigurera DNS-vidarebefordran i ett Azure Red Hat OpenShift 4-kluster

Om du vill konfigurera DNS-vidarebefordran i ett Azure Red Hat OpenShift-kluster måste du ändra DNS-operatorn. Den här ändringen gör att ditt program poddar körs i klustret för att matcha namn som finns på en privat DNS-server utanför klustret. De här stegen är dokumenterade för OpenShift 4,6 [här](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html).

Om du till exempel vill vidarebefordra alla DNS-begäranden för *. example.com för att lösas av en DNS-Server 192.168.100.10 kan du redigera operatörs konfigurationen genom att köra:
 
```bash
oc edit dns.operator/default
```
 
Då startas en redigerare och du kan ersätta `spec: {}` med:
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

Spara filen och avsluta redigeraren.

## <a name="next-steps"></a>Nästa steg
Läs mer information om DNS-vidarebefordran för OpenShift 4,6 [här](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html).