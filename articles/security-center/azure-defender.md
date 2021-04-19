---
title: Översikt över Azure Defender och tillgängliga planer
description: Lär dig Azure Defender om planer, skydd och aviseringar. Aktivera sedan Azure Defender på dina prenumerationer för avancerad säkerhet.
author: memildin
ms.author: memildin
ms.date: 9/30/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 0bd0d9c2230b8400aa3197044f944daceb93c715
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718529"
---
# <a name="introduction-to-azure-defender"></a>Introduktion till Azure Defender

Azure Security Center funktionerna omfattar de två breda grundpelarna för molnsäkerhet:

- **Hantering av molnsäkerhetsstatus (CSPM)** – Security Center är tillgängligt **kostnadsfritt för** alla Azure-användare. Den kostnadsfria upplevelsen innehåller CSPM-funktioner som säkerhetspoäng, identifiering av felaktiga säkerhetskonfigurationer i dina Azure-datorer, tillgångsinventering med mera. Använd dessa CSPM-funktioner för att stärka din hybridmolnstatus och spåra efterlevnad med de inbyggda principerna.

- **CWP (Cloud Workload Protection)** – Security Center integrated cloud workload protection platform (CWPP), **Azure Defender**, ger dig avancerade, intelligenta, skydd av dina Azure- och hybridresurser och arbetsbelastningar. Aktivering av Azure Defender innehåller en rad ytterligare säkerhetsfunktioner som beskrivs på den här sidan. Förutom de inbyggda principerna kan du lägga till anpassade principer Azure Defender initiativ när du har aktiverat en plan. Du kan lägga till regler , till exempel NIST och Azure CIS, samt [Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction) för en verkligt anpassad vy av din efterlevnad.

I Azure Defender instrumentpanel i Security Center du synlighet och kontroll över CWP-funktionerna för din miljö:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Ett exempel på Azure Defender instrumentpanel" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Vilka resurstyper kan Azure Defender säkra?

Azure Defender säkerhetsaviseringar och avancerat skydd mot hot för virtuella datorer, SQL-databaser, containrar, webbprogram, ditt nätverk med mera.

När du Azure Defender från  området Priser och inställningar i Azure Security Center aktiveras följande Defender-planer samtidigt och ger omfattande skydd för beräknings-, data- och tjänstlager i din miljö:

- [Azure Defender för servrar](defender-for-servers-introduction.md)
- [Azure Defender för App Service](defender-for-app-service-introduction.md)
- [Azure Defender för Storage](defender-for-storage-introduction.md)
- [Azure Defender för SQL](defender-for-sql-introduction.md)
- [Azure Defender för Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender för containerregister](defender-for-container-registries-introduction.md)
- [Azure Defender för Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender för Resource Manager](defender-for-resource-manager-introduction.md)
- [Azure Defender för DNS](defender-for-dns-introduction.md)

Var och en av dessa planer förklaras separat i Security Center dokumentationen.

> [!TIP]
> Azure Defender for IoT (förhandsversion) är en separat produkt. All information finns i Introduktion [till Azure Defender for IoT (förhandsversion)](../defender-for-iot/overview.md). 

## <a name="hybrid-cloud-protection"></a>Hybridmolnskydd

Du kan även skydda din Azure-miljö och lägga till Azure Defender i din hybridmolnmiljö:

- Skydda dina icke-Azure-servrar
- Skydda dina virtuella datorer i andra moln (till exempel AWS och GCP)

Du får anpassad hotinformation och prioriterade aviseringar enligt din specifika miljö så att du kan fokusera på det som är viktigast.

Om du vill utöka skyddet till virtuella datorer och SQL-databaser som finns i andra moln eller lokalt distribuerar du [Azure Arc](https://azure.microsoft.com/services/azure-arc/) aktivera Azure Defender. Azure Arc för servrar är en kostnadsfri tjänst, men tjänster som används på Arc-aktiverade servrar, till exempel Azure Defender, debiteras enligt prissättningen för den tjänsten. Läs mer i [Lägga till icke-Azure-datorer med Azure Arc](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc).

> [!TIP]
> Den inbyggda anslutningsappen för AWS hanterar Azure Arc distributionen åt dig. Läs mer i [Ansluta dina AWS-konton till Azure Security Center](quickstart-onboard-aws.md).



## <a name="azure-defender-security-alerts"></a>Azure Defender säkerhetsaviseringar 

När Azure Defender identifierar ett hot i någon del av din miljö genereras en säkerhetsavisering. De här aviseringarna beskriver information om de berörda resurserna, föreslagna åtgärdssteg och i vissa fall ett alternativ för att utlösa en logikapp som svar.

Om en avisering genereras av Security Center eller tas emot av Security Center från en integrerad säkerhetsprodukt kan du exportera den. Om du vill exportera dina aviseringar till Azure Sentinel, siem från tredje part eller något annat externt verktyg följer du anvisningarna i Stream-aviseringar till en [SIEM-, SOAR- eller IT Service Management-lösning](export-to-siem.md).

> [!NOTE]
> Det kan ta olika lång tid innan aviseringar från olika källor visas. Aviseringar som kräver analys av nätverkstrafik kan till exempel ta längre tid än aviseringar relaterade till misstänkta processer som körs på virtuella datorer.


## <a name="azure-defender-advanced-protection-capabilities"></a>Azure Defender avancerade skyddsfunktioner

Azure Defender använder avancerad analys för skräddarsydda rekommendationer relaterade till dina resurser. 

Skydd omfattar att skydda hanteringsportarna för dina virtuella datorer med just-in-time-åtkomst och anpassningsbara programkontroller för att skapa listor över tillåtna för vilka appar som ska och inte ska köras på dina datorer. 

Använd panelerna för avancerat skydd i Azure Defender för att övervaka och konfigurera vart och ett av dessa skydd. 

## <a name="vulnerability-assessment-and-management"></a>Sårbarhetsbedömning och hantering

Azure Defender omfattar sårbarhetsgenomsökning för dina virtuella datorer och containerregister utan extra kostnad. Skannrarna drivs av Qualys, men du behöver inte en Qualys-licens eller ens ett Qualys-konto – allt hanteras sömlöst i Security Center. 

Granska resultaten från dessa sårbarhetsskannrar och svara på dem alla inifrån Security Center. Detta gör Security Center närmare att vara den enda fönsterrutan för alla dina molnsäkerhetsarbete.

Läs mer på följande sidor:

- [Security Center integrerad lösning för sårbarhetsbedömning för virtuella Azure-datorer](deploy-vulnerability-assessment-vm.md)
- [Identifiera sårbarheter i avbildningar i Azure-containerregister](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om fördelarna med Azure Defender. 

> [!div class="nextstepaction"]
> [Aktivera Azure Defender](enable-azure-defender.md)