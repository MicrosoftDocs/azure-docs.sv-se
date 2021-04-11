---
title: Azure DDoS Protection grundläggande metod tips
description: Lär dig bästa säkerhets praxis med DDoS-skydd.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: ea5e9e9883c8c1da17cf99dabc72f2339c8dbe1a
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107102985"
---
# <a name="fundamental-best-practices"></a>Grundläggande regelverk

I följande avsnitt får du vägledning för att skapa DDoS-elastiska tjänster på Azure.

## <a name="design-for-security"></a>Design för säkerhet

Se till att säkerheten är en prioritet under hela livs cykeln för ett program, från design och implementering till distribution och drift. Program kan ha buggar som tillåter en relativt låg mängd begär Anden att använda en inordinat-mängd resurser, vilket resulterar i avbrott i tjänsten. 

För att skydda en tjänst som körs på Microsoft Azure bör du ha en god förståelse för din program arkitektur och fokusera på de [fem bild stenarna med program kvalitet](/azure/architecture/guide/pillars).
Du bör känna till vanliga trafik volymer, anslutnings modellen mellan programmet och andra program och de tjänst slut punkter som exponeras för det offentliga Internet.

Att se till att ett program är tillräckligt flexibelt för att hantera en denial of service som är riktad mot själva programmet är viktigast. Säkerhet och sekretess är inbyggda i Azure-plattformen, från och med [säkerhets utvecklings livs cykeln (SDL)](https://www.microsoft.com/sdl/default.aspx). SDL adresserar säkerheten i varje utvecklings fas och säkerställer att Azure uppdateras kontinuerligt för att göra det ännu säkrare.

## <a name="design-for-scalability"></a>Design för skalbarhet

Skalbarhet är hur väl ett system kan hantera ökad belastning. Utforma dina program så att de kan [skalas horisontellt](/azure/architecture/guide/design-principles/scale-out) för att möta efter frågan på en förstärkt belastning, särskilt i händelse av en DDoS-attack. Om ditt program är beroende av en enda instans av en tjänst skapas en enskild felpunkt. Genom att tillhandahålla flera instanser blir systemet mer flexibelt och mer skalbart.

För [Azure App Service](../app-service/overview.md)väljer du en [App Service plan](../app-service/overview-hosting-plans.md) som erbjuder flera instanser. För Azure Cloud Services konfigurerar du var och en av dina roller så att de använder [flera instanser](../cloud-services/cloud-services-choose-me.md). För [Azure Virtual Machines](../virtual-machines/index.yml)kontrollerar du att arkitekturen för virtuella datorer (VM) innehåller fler än en virtuell dator och att varje virtuell dator ingår i en [tillgänglighets uppsättning](../virtual-machines/windows/tutorial-availability-sets.md). Vi rekommenderar att du använder [skalnings uppsättningar för virtuella datorer](../virtual-machine-scale-sets/overview.md) för funktioner för automatisk skalning.

## <a name="defense-in-depth"></a>Skydd på djupet

Idén bakom försvaret är att hantera risker genom att använda olika metoder för försvar. Skiktning av säkerhets försvar i ett program minskar risken för en lyckad attack. Vi rekommenderar att du implementerar säkra utformningar för dina program genom att använda de inbyggda funktionerna i Azure-plattformen.

Risken för angrepp ökar t. ex. den storlek (arean *) i* programmet. Du kan minska arean genom att använda en godkännande lista för att stänga det exponerade IP-adressutrymmet och lyssnande portar som inte behövs på belastningsutjämnaren ([Azure Load Balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md) och [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)). [Nätverks säkerhets grupper (NSG: er)](../virtual-network/network-security-groups-overview.md) är ett annat sätt att minska angrepps ytan.
Du kan använda [tjänst Taggar](../virtual-network/network-security-groups-overview.md#service-tags) och [program säkerhets grupper](../virtual-network/network-security-groups-overview.md#application-security-groups) för att minimera komplexiteten för att skapa säkerhets regler och konfigurera nätverks säkerhet som ett naturligt tillägg till ett programs struktur.

Du bör Distribuera Azure-tjänster i ett [virtuellt nätverk](../virtual-network/virtual-networks-overview.md) närhelst det är möjligt. Den här metoden gör det möjligt för tjänst resurser att kommunicera via privata IP-adresser. Azure Service-trafik från ett virtuellt nätverk använder offentliga IP-adresser som standard käll-IP-adresser. Genom att använda [tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) växlar tjänst trafiken till att använda privata adresser i det virtuella nätverket som käll-IP-adresser när de ansluter till Azure-tjänsten från ett virtuellt nätverk.

Vi ser ofta kunders lokala resurser som tar emot angrepp tillsammans med sina resurser i Azure. Om du ansluter en lokal miljö till Azure rekommenderar vi att du minimerar exponeringen för lokala resurser till det offentliga Internet. Du kan använda funktionerna för skalning och avancerad DDoS i Azure genom att distribuera välkända offentliga entiteter i Azure. Eftersom dessa offentligt tillgängliga entiteter ofta är ett mål för DDoS-attacker, minskar risken för lokala resurser genom att placera dem i Azure.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en DDoS-skydds plan](manage-ddos-protection.md).