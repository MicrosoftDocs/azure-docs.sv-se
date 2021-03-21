---
title: Vad är lösningar för att köra Oracle WebLogic-Server på Azure Virtual Machines
description: Lär dig hur du kör Oracle WebLogic Server på Microsoft Azure Virtual Machines.
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 02/24/2021
ms.author: rezar
ms.openlocfilehash: a5675b313586615d4bad733aec6eabf0360f8489
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694719"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-azure-virtual-machines"></a>Vilka lösningar finns för att köra Oracle WebLogic Server på Azure Virtual Machines?

Den här sidan beskriver lösningar för att köra Oracle WebLogic Server (WLS) på virtuella Azure-datorer. Dessa lösningar har utvecklats gemensamt och stöds av Oracle och Microsoft.

Du kan också köra WLS på Azure Kubernetes-tjänsten. Lösningarna för detta beskrivs i [den här artikeln i Microsoft](./weblogic-aks.md).

WLS är en ledande Java-Programserver som kör några av de viktigaste viktiga företags Java-programmen över hela världen. WLS utgör mellanvaru Stiftelsen för Oracle-programsviten. Oracle och Microsoft strävar efter att ge kunderna möjlighet att välja och flexibilitet för att köra arbets belastningar på Azure som en ledande moln plattform.

Azure WLS-lösningarna syftar till att göra det så enkelt som möjligt att migrera dina Java-program till Azure Virtual Machines. Lösningarna gör detta genom att skapa distribuerade resurser för de flesta vanliga moln etablerings scenarier. Lösningarna etablerar automatiskt virtuella nätverk, lagrings-, Java-, WLS-och Linux-resurser. WebLogic-servern installeras med minimal ansträngning. Lösningarna kan konfigurera säkerhet med en nätverks säkerhets grupp, belastnings utjämning med Azure App Gateway, autentisering med Azure Active Directory, centraliserad loggning med ELK och distribuerad cachelagring med Oracle-samstämmighet. Du kan också ansluta automatiskt till din befintliga databas, inklusive Azure PostgreSQL, Azure SQL och Oracle DB i Oracle-molnet eller Azure. 

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Du kan använda Azure Portal för att distribuera WebLogic-Server på Azure":::

Det finns fyra erbjudanden som är tillgängliga för att möta olika scenarier: en [nod utan en administratörs Server](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls), en [nod med en administratörs Server, ett](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin) [kluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)och ett [dynamiskt kluster](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster). Erbjudandena är tillgängliga utan kostnad. Dessa erbjudanden beskrivs och länkas nedan.

_De här erbjudandena är en egen licens_. De förutsätter att du redan har rätt licenser med Oracle och att de är korrekt licensierade för att köra erbjudanden i Azure.

Det finns stöd för flera operativ system, Java och WLS-versioner via bas avbildningar (till exempel WebLogic Server 14 och JDK 11 på Oracle Linux 7,6). De här bas avbildningarna är också tillgängliga på Azure på egen hand. Bas avbildningarna är lämpliga för kunder som kräver komplexa, anpassade Azure-distributioner. Den aktuella uppsättningen bas avbildningar finns [här](https://azuremarketplace.microsoft.com/marketplace/apps?search=WebLogic%20Server%20Base%20Image&page=1).

_Om du är intresse rad av att jobba nära dina migrerings scenarier med teknik teamet som utvecklar dessa erbjudanden väljer du knappen [kontakta mig](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)_ på [sidan för Marketplace erbjudande översikt](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Program chefer, arkitekter och tekniker kommer att kontakta dig inom kort och börja nära samarbete. Möjligheten att samar beta med ett migreringsjobb är kostnads fri medan erbjudandena är aktiva under utveckling.

## <a name="oracle-weblogic-server-single-node"></a>Enskild nod för Oracle WebLogic-Server

[Det här erbjudandet](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls) tillhandahåller en enskild virtuell dator och installerar WLS på den. Den skapar inte en domän eller startar administrations servern. Det enda nod-erbjudandet är användbart för scenarier med mycket anpassad domän konfiguration.

## <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic-Server med administratörs Server

[Det här erbjudandet](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin) tillhandahåller en enskild virtuell dator och installerar WLS på den. Den skapar en domän och startar administrations servern. Du kan hantera domänen och komma igång med program distributioner direkt.

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server-kluster

[Det här erbjudandet](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster) skapar ett kluster med hög tillgänglighet för virtuella WLS-datorer. Administrations servern och alla hanterade servrar startas som standard. Du kan hantera klustret och komma igång med program med hög tillgänglighet direkt.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Dynamiskt kluster för Oracle WebLogic Server

[Det här erbjudandet](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster) skapar ett hög tillgängligt och skalbart dynamiskt kluster av WLS Virtual Machines. Administrations servern och alla hanterade servrar startas som standard.

Lösningarna gör det möjligt att använda en rad olika produktions klara distributions arkitekturer med relativt enkelt. Du kan möta de flesta migreringar på det mest produktiva sättet genom att fokusera på utveckling av affärs program.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-vms.png" alt-text="Komplexa WebLogic-Server distributioner är aktiverade på Azure":::

Utöver vad som automatiskt tillhandahålls av lösningarna har kunderna fullständig flexibilitet att anpassa sina distributioner ytterligare. Det är sannolikt ovanpå att distribuera program kunder att integrera ytterligare Azure-resurser med sina distributioner. Kunderna uppmanas att [ansluta till utvecklings gruppen](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) och ge feedback om ytterligare förbättringar av lösningarna.

## <a name="next-steps"></a>Nästa steg

Utforska erbjudandena på Azure.

> [!div class="nextstepaction"]
> [Enskild nod för Oracle WebLogic-Server](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic-Server med administratörs Server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server-kluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Dynamiskt kluster för Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
