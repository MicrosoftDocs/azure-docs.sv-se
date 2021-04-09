---
title: OpenShift i Azure-översikt
description: En översikt över OpenShift i Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: aba01fc2317372438bc0d93a6618d518ab03ed0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101672318"
---
# <a name="openshift-in-azure"></a>OpenShift i Azure

OpenShift är en öppen och utöknings bar plattform för behållar program som ger Docker och Kubernetes till företaget.  

OpenShift innehåller Kubernetes för behållar dirigering och hantering. Det lägger till utvecklare-koncentriska och åtgärds drivna verktyg som möjliggör:

- Snabb program utveckling.
- Enkel distribution och skalning.
- Långsiktigt livs cykel underhåll för team och program.

Det finns flera versioner av OpenShift.  I dessa versioner är bara två tillgängliga idag för kunder som ska distribueras i Azure: OpenShift container Platform och OKD (tidigare OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift är ett fullständigt hanterat utbud av OpenShift som körs i Azure. Den här tjänsten hanteras gemensamt och stöds av Microsoft och Red Hat. Mer information finns i dokumentationen för [Azure Red Hat OpenShift-tjänsten](../../openshift/index.yml) .

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform är en Enterprise-förberedd [kommersiell version](https://www.openshift.com) från och stöds av Red Hat. Med den här versionen köper kunderna de nödvändiga rättigheterna för OpenShift container Platform och ansvarar för installation och hantering av hela infrastrukturen.

Eftersom kunder "äger" hela plattformen kan de installera dem i sitt lokala data Center eller i ett offentligt moln (till exempel Azure).

## <a name="okd"></a>OKD

OKD är ett överordnat projekt med [öppen källkod](https://www.okd.io/) av OpenShift som stöds av gruppen. OKD kan installeras på CentOS eller Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera vanliga krav för OpenShift i Azure](./openshift-container-platform-3x-prerequisites.md)
- [Distribuera OpenShift container Platform i Azure](./openshift-container-platform-3x.md)
- [Distribuera OpenShift container Platform Self-Managed Marketplace-erbjudande](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Distribuera OpenShift i Azure Stack](./openshift-azure-stack.md)
- [Uppgifter efter distribution](./openshift-container-platform-3x-post-deployment.md)
- [Felsöka OpenShift-distribution](./openshift-container-platform-3x-troubleshooting.md)
