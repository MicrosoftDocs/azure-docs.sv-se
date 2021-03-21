---
title: Azure DDoS Protection referens arkitekturer
description: Lär dig mer om Azure DDoS Protection-referens arkitekturer.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: e5472620fe9b07d152a5325b0654044cb1505fd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94992445"
---
# <a name="ddos-protection-reference-architectures"></a>DDoS Protection referens arkitekturer

DDoS Protection standard är utformad [för tjänster som distribueras i ett virtuellt nätverk](../virtual-network/virtual-network-for-azure-services.md). För andra tjänster gäller standard tjänsten för DDoS Protection Basic. Följande referens arkitekturer ordnas efter scenarier, med arkitektur mönster grupperade tillsammans.

## <a name="virtual-machine-windowslinux-workloads"></a>Arbets belastningar för virtuella datorer (Windows/Linux)

### <a name="application-running-on-load-balanced-vms"></a>Program som körs på belastningsutjämnade virtuella datorer

Den här referens arkitekturen visar en uppsättning beprövade metoder för att köra flera virtuella Windows-datorer i en skalnings uppsättning bakom en belastningsutjämnare för att förbättra tillgängligheten och skalbarheten. Den här arkitekturen kan användas för alla tillstånds lösa arbets belastningar, till exempel en webb server.

![Diagram över referens arkitekturen för ett program som körs på belastningsutjämnade virtuella datorer](./media/ddos-best-practices/image-9.png)

En arbetsbelastning distribueras över flera VM-instanser i den här arkitekturen. Det finns en enda offentlig IP-adress och Internet trafiken distribueras till de virtuella datorerna via en belastningsutjämnare. DDoS Protection standard har Aktiver ATS på det virtuella nätverket för belastningsutjämnaren i Azure (Internet) som har den offentliga IP-adress som är kopplad till den.

Belastningsutjämnaren distribuerar inkommande Internet-begäranden till VM-instanserna. Skalnings uppsättningar för virtuella datorer tillåter att antalet virtuella datorer skalas in eller ut manuellt eller automatiskt baserat på fördefinierade regler. Detta är viktigt om resursen är under DDoS-attack. Mer information om den här referens arkitekturen finns i [den här artikeln](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

### <a name="application-running-on-windows-n-tier"></a>Program som körs på Windows N-nivå

Det finns många sätt att implementera en arkitektur på N-nivå. Följande diagram visar ett typiskt webb program på tre nivåer. Den här arkitekturen bygger på artikeln [Kör belastningsutjämnade virtuella datorer för skalbarhet och tillgänglighet](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Webb- och företagsnivåer använder belastningsutjämnade virtuella datorer.

![Diagram över referens arkitekturen för ett program som körs på Windows N-nivå](./media/ddos-best-practices/image-10.png)

I den här arkitekturen är DDoS Protection standard aktiverat i det virtuella nätverket. Alla offentliga IP-adresser i det virtuella nätverket får DDoS-skydd för Layer 3 och 4. För Layer 7-skydd distribuerar du Application Gateway i SKU: n för WAF. Mer information om den här referens arkitekturen finns i [den här artikeln](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

> [!NOTE]
> Scenarier där en enskild virtuell dator körs bakom en offentlig IP-adress stöds inte.

### <a name="paas-web-application"></a>PaaS-webbprogram

Den här referens arkitekturen visar körning av ett Azure App Service program i en enda region. Den här arkitekturen visar en uppsättning beprövade metoder för ett webb program som använder [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) och [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
En vänte region har kon figurer ATS för failover-scenarier.

![Diagram över referens arkitekturen för ett PaaS-webbprogram](./media/ddos-best-practices/image-11.png)

Azure Traffic Manager dirigerar inkommande begär anden till Application Gateway i en av regionerna. Vid normal drift dirigerar den förfrågningar till Application Gateway i den aktiva regionen. Om regionen blir otillgänglig, Traffic Manager växlar över till Application Gateway i vänte läge.

All trafik från Internet som är avsedd för webb programmet dirigeras till den [Application Gateway offentliga IP-adressen](../application-gateway/application-gateway-web-app-overview.md) via Traffic Manager. I det här scenariot är App Service (webbapp) inte direkt externt och skyddas av Application Gateway. 

Vi rekommenderar att du konfigurerar Application Gateway WAF SKU (förhindra läge) för att skydda mot Layer 7-attacker (HTTP/HTTPS/WebSocket). Dessutom är Web Apps konfigurerade för att [endast acceptera trafik från Application Gateway](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP-adress.

Mer information om den här referens arkitekturen finns i [den här artikeln](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

## <a name="mitigation-for-non-web-paas-services"></a>Minskning för icke-PaaS tjänster

### <a name="hdinsight-on-azure"></a>HDInsight på Azure

Den här referens arkitekturen visar hur du konfigurerar DDoS Protection standard för ett [Azure HDInsight-kluster](../hdinsight/index.yml). Se till att HDInsight-klustret är länkat till ett virtuellt nätverk och att DDoS Protection är aktiverat på det virtuella nätverket.

![Panelerna "HDInsight" och "avancerade inställningar" med inställningar för virtuellt nätverk](./media/ddos-best-practices/image-12.png)

![Val för att aktivera DDoS Protection](./media/ddos-best-practices/image-13.png)

I den här arkitekturen dirigeras trafik till HDInsight-klustret från Internet till den offentliga IP-adress som är associerad med belastningsutjämnaren för HDInsight Gateway. Gateway-belastningsutjämnaren skickar sedan trafiken till Head-noderna eller arbetsnoderna direkt. Eftersom DDoS Protection standard är aktiverat i det virtuella HDInsight-nätverket får alla offentliga IP-adresser i det virtuella nätverket DDoS skydd för Layer 3 och 4. Den här referens arkitekturen kan kombineras med referens arkitekturer för N-nivå och flera regioner.

Mer information om den här referens arkitekturen finns i [utöka Azure HDInsight med hjälp av en Azure Virtual Network](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -dokumentation.


> [!NOTE]
> Azure App Service Environment för PowerApps eller API Management i ett virtuellt nätverk med en offentlig IP-adress stöds inte internt.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en DDoS-skydds plan](manage-ddos-protection.md).