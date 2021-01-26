---
title: Översikt över Azure Defender och tillgängliga planer
description: Lär dig mer om Azure Defender-planer,-skydd och-aviseringar. Aktivera sedan Azure Defender för dina prenumerationer för avancerad säkerhet.
author: memildin
ms.author: memildin
ms.date: 9/30/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 23d00acc03ee879036a823f0c3d7fe2f67902e31
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797017"
---
# <a name="introduction-to-azure-defender"></a>Introduktion till Azure Defender

Azure Security Centers funktioner beskriver de två breda pelare för moln säkerhet:

- **Cloud Security position Management (CSPM)** – Security Center är tillgängligt **utan kostnad** för alla Azure-användare. Den kostnads fria upplevelsen omfattar CSPM-funktioner som säker poäng, identifiering av felkonfigurationer av säkerhet i dina Azure-datorer, till gångs inventering med mera. Använd dessa CSPM-funktioner för att förstärka ditt hybrid moln position och spåra kompatibiliteten med de inbyggda principerna.

- **Cloud Worker Protection (CWP)** – Security Center s integrerade Cloud Worker Protection Platform (CWPP), **Azure Defender**, ger avancerad, intelligent, skydd av dina Azure-och hybrid resurser och arbets belastningar. Genom att aktivera Azure Defender får du en rad ytterligare säkerhetsfunktioner enligt beskrivningen på den här sidan. Förutom de inbyggda principerna kan du lägga till anpassade principer och initiativ när du har aktiverat någon Azure Defender-plan. Du kan lägga till reglerande standarder – till exempel NIST och Azure CIS – samt Azures säkerhets riktmärke för en riktigt anpassad vy över din efterlevnad.

Azure Defender-instrumentpanelen i Security Center ger insyn och kontroll över CWP-funktionerna i din miljö:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Ett exempel på Azure Defender-instrumentpanelen" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Vilka resurs typer kan Azure Defender skydda?

Azure Defender innehåller säkerhets aviseringar och Avancerat skydd för virtuella datorer, SQL-databaser, behållare, webb program, nätverk och mycket annat.

När du aktiverar Azure Defender från avsnittet **priser och inställningar** i Azure Security Center, aktive ras följande Defender-planer samtidigt och ger omfattande försvars områden i din miljö:

- [Azure Defender för servrar](defender-for-servers-introduction.md)
- [Azure Defender för App Service](defender-for-app-service-introduction.md)
- [Azure Defender för Storage](defender-for-storage-introduction.md)
- [Azure Defender för SQL](defender-for-sql-introduction.md)
- [Azure Defender för Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender för containerregister](defender-for-container-registries-introduction.md)
- [Azure Defender för Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender för Resource Manager](defender-for-resource-manager-introduction.md)
- [Azure Defender för DNS](defender-for-dns-introduction.md)

Var och en av dessa planer beskrivs separat i Security Center-dokumentationen.

> [!TIP]
> Azure Defender för IoT (för hands version) är en separat produkt. Du hittar all information i [Introduktion till Azure Defender for IoT (för hands version)](../defender-for-iot/overview.md). 

## <a name="hybrid-cloud-protection"></a>Hybrid moln skydd

Du kan lägga till Azure Defender-funktioner i din hybrid moln miljö, samt skydda din Azure-miljö:

- Skydda dina icke-Azure-servrar
- Skydda dina virtuella datorer i andra moln (till exempel AWS och GCP)

Du får en anpassad Hot information och prioriterade aviseringar enligt din miljö så att du kan fokusera på det som är mest viktigt.

Om du vill utöka skyddet till virtuella datorer och SQL-databaser som finns i andra moln eller lokalt, distribuerar du [Azure-bågen](https://azure.microsoft.com/services/azure-arc/) och aktiverar Azure Defender. Azure-bågen för servrar är en kostnads fri tjänst, men tjänster som används på Arc-aktiverade servrar, till exempel Azure Defender, debiteras enligt priserna för tjänsten. Läs mer i [Lägg till icke-Azure-datorer med Azure Arc](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc).

> [!TIP]
> Den ursprungliga anslutningen för AWS transparent hanterar Azure Arc-distributionen åt dig. Läs mer i [ansluta dina AWS-konton till Azure Security Center](quickstart-onboard-aws.md).



## <a name="azure-defender-security-alerts"></a>Säkerhets aviseringar för Azure Defender 

När Azure Defender identifierar ett hot i alla områden i miljön genererar den en säkerhets avisering. De här aviseringarna beskriver information om de resurser som påverkas, föreslagna åtgärds steg och i vissa fall ett alternativ för att utlösa en logisk app som svar.

Om en avisering genereras av Security Center eller tas emot av Security Center från en integrerad säkerhets produkt, kan du exportera den. Om du vill exportera aviseringar till Azure Sentinel, SIEM eller andra externa verktyg, följer du anvisningarna i [Stream-aviseringar till en Siem, Soar eller IT-Tjänstehanterings lösning](export-to-siem.md).

> [!NOTE]
> Aviseringar från olika källor kan ta olika tids mängder att visas. Till exempel kan aviseringar som kräver analys av nätverks trafiken ta längre tid än aviseringar som är relaterade till misstänkta processer som körs på virtuella datorer.


## <a name="azure-defender-advanced-protection-capabilities"></a>Azure Defender avancerade skydds funktioner

Azure Defender använder avancerade analyser för skräddarsydda rekommendationer relaterade till dina resurser. 

Skyddet omfattar att skydda de virtuella datorernas hanterings portar med just-in-Time-åtkomst och anpassningsbara program kontroller för att skapa listor för vilka appar som ska och inte ska köras på dina datorer. 

Använd panelerna Avancerat skydd på Azure Defender-instrumentpanelen för att övervaka och konfigurera vart och ett av dessa skydd. 

## <a name="vulnerability-assessment-and-management"></a>Sårbarhets bedömning och hantering

Azure Defender innehåller sårbarhets sökning för dina virtuella datorer och behållar register utan extra kostnad. Skannern drivs av Qualys, men du behöver inte en Qualys-licens eller till och med ett Qualys-konto – allt hanteras sömlöst i Security Center. 

Granska resultaten från dessa sårbarhets skannrar och svara på dem i Security Center. Detta ger Security Center närmare det enda fönstret i glas för alla dina moln säkerhets åtgärder.

Läs mer på följande sidor:

- [Security Center är en integrerad lösning för sårbarhets bedömning för Azure Virtual Machines](deploy-vulnerability-assessment-vm.md)
- [Identifiera sårbarheter i avbildningar i Azure Container register](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om fördelarna med Azure Defender. 

> [!div class="nextstepaction"]
> [Aktivera Azure Defender](security-center-pricing.md#enable-azure-defender)