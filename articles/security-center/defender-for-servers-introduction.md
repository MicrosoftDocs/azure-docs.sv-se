---
title: Azure Defender för servrar – fördelar och funktioner
description: Lär dig mer om fördelarna och funktionerna i Azure Defender för servrar.
author: memildin
ms.author: memildin
ms.date: 9/23/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 36c32c4eefdaa1c7604f2b0f19e98cf6a6d4310d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102096538"
---
# <a name="introduction-to-azure-defender-for-servers"></a>Introduktion till Azure Defender för servrar

Azure Defender för-servrar lägger till hot identifiering och avancerade försvar för dina Windows-och Linux-datorer.

För Windows integrerar Azure Defender med Azure-tjänster för att övervaka och skydda dina Windows-baserade datorer. Security Center visar aviseringar och reparations förslag från alla dessa tjänster i ett lättanvänt format.

För Linux samlar Azure Defender in gransknings poster från Linux-datorer med hjälp av **granskning**, ett av de vanligaste ramverken för Linux-granskning. granskade liv i Mainline-kärnan. 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>Vilka är fördelarna med Azure Defender för servrar?

De hot identifierings-och skydds funktioner som ingår i Azure Defender för servrar är:

- **Integrerad licens för Microsoft Defender för slut punkt (endast Windows)** – Azure Defender för servrar innehåller  [Microsoft Defender för slut punkt](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Tillsammans tillhandahåller de omfattande funktioner för slut punkts identifiering och-svar (EDR). Mer information finns i [skydda dina slut punkter](security-center-wdatp.md).

    När Defender för slut punkten identifierar ett hot utlöses en avisering. Aviseringen visas i Security Center. Från Security Center kan du också pivotera till Defender för slut punkts konsolen och utföra en detaljerad undersökning för att få fram omfattningen av angreppet. Läs mer om Microsoft Defender för slut punkt.

    > [!IMPORTANT]
    > **Microsoft Defender för slut punkts** sensor aktive ras automatiskt på Windows-servrar som använder Security Center.

- **Genomsökning av sårbarhets bedömning för virtuella datorer** – den sårbarhets-skanner som ingår i Azure Security Center drivs av Qualys. 

    Qualys ' Scanner är ett av de ledande verktygen för real tids identifiering av sårbarheter i dina Azure-och hybridbaserade virtuella datorer. Du behöver inte en Qualys-licens eller ens ett Qualys-konto – allt hanteras sömlöst i Security Center. Mer information finns i [Azure Defender: s integrerade lösning för sårbarhets bedömning för Azure och hybrid datorer](deploy-vulnerability-assessment-vm.md).

- **JIT (just-in-Time)-skydd för virtuella datorer (VM)** är aktivt för att nå datorer med öppna hanterings portar, som RDP eller ssh. Alla dina virtuella datorer är potentiella mål för ett angrepp. När en virtuell dator har komprometterats används den som start punkt för att angripa ytterligare resurser i din miljö.

    När du aktiverar Azure Defender för-servrar kan du använda just-in-Time VM-åtkomst för att låsa inkommande trafik till dina virtuella datorer, vilket minskar exponeringen för attacker och ger enkel åtkomst till att ansluta till virtuella datorer när det behövs. Mer information finns i [förstå JIT VM-åtkomst](just-in-time-explained.md).

- **File Integrity Monitoring (FIM)** – File Integrity Monitoring (FIM), som även kallas för ändrings övervakning, undersöker filer och register över operativ system, program vara och andra för ändringar som kan tyda på ett angrepp. En jämförelse metod används för att avgöra om filens aktuella tillstånd skiljer sig från den senaste genomsökningen av filen. Du kan utnyttja den här jämförelsen för att avgöra om giltiga eller misstänkta ändringar har gjorts i dina filer.

    När du aktiverar Azure Defender för-servrar kan du använda FIM för att verifiera integriteten för Windows-filer, dina Windows-register och Linux-filer. Mer information finns i avsnittet [om övervakning av fil integritet i Azure Security Center](security-center-file-integrity-monitoring.md).

- **Adaptiva program kontroller (AAC)** – anpassningsbara program kontroller är en intelligent och automatiserad lösning för att definiera listor över kända säkra program för dina datorer.

    När du har aktiverat och konfigurerat anpassade program kontroller får du säkerhets aviseringar om andra program körs än de som du har definierat som säkra. Mer information finns i [använda anpassningsbara program kontroller för att minska dina datorers angrepps ytor](security-center-adaptive-application.md).

- **Anpassningsbar nätverks härdning (Anh)** – genom att använda nätverks säkerhets grupper (NSG) för att filtrera trafik till och från resurser, ökar nätverks säkerheten position. Det kan dock fortfarande finnas fall där den faktiska trafiken som flödar genom NSG är en del av de NSG-regler som definierats. I dessa fall kan ytterligare förbättra säkerheten position genom att skärpa NSG-reglerna, baserat på de faktiska trafik mönstren.

    Anpassad nätverks härdning ger rekommendationer för ytterligare härdning av NSG-reglerna. Den använder en Machine Learning-algoritm som faktorer i faktisk trafik, känd betrodd konfiguration, Hot information och andra risk indikatorer och ger rekommendationer för att endast tillåta trafik från specifika IP/port-tupler. Mer information finns i [förbättra din nätverks säkerhets position med anpassningsbar nätverks härdning](security-center-adaptive-network-hardening.md).

- **Docker-värd härdning** – Azure Security Center identifierar ohanterade behållare som finns på virtuella Linux-datorer med IaaS eller andra Linux-datorer som kör Docker-behållare. Security Center kontinuerligt bedömer konfigurationen av dessa behållare. Den jämför sedan dem med Center for Internet Security (CIS) Docker-benchmark. Security Center innehåller hela ruleset av CIS Docker-benchmark och varnar dig om dina behållare inte uppfyller någon av kontrollerna. Mer information finns i [torka Docker-värdarna](harden-docker-hosts.md).

- Identifiering av fillösa **attacker (endast Windows)** – fil lösa attacker injicera skadliga nytto laster i minnet för att undvika identifiering genom diskbaserade skannings tekniker. Angriparens nytto Last behålls sedan i minnet för komprometterade processer och utför en mängd olika skadliga aktiviteter.

  Med fil lösa angrepp kan automatiserade minnes kriminal tekniska-tekniker identifiera fillösa verktyg för attacker, tekniker och beteenden. Den här lösningen genomsöker regelbundet datorn vid körning och extraherar insikter direkt från processens minne. I vissa insikter ingår identifieringen av: 

  - Välkända verktyg och program vara för kryptografi utvinning 

  - Shellcode, som är en liten del av kod som vanligt vis används som nytto last vid användning av program varu sårbarheter.

  - Inmatad skadlig körbar fil i process minne

  Vid filbaserad attack identifiering skapas detaljerade säkerhets aviseringar som innehåller beskrivningar med ytterligare process-metadata, till exempel nätverks aktivitet. Detta påskyndar aviserings prioritering, korrelation och underordnade svars tider. Den här metoden kompletterar event-baserade EDR-lösningar och ger ökad identifierings täckning.

  Mer information om aviseringar om filbaserad attack identifiering finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-windows).

- **Granskade Linux-aviseringar och Log Analytics agent integration (endast Linux)** – det granskade systemet består av ett under system i kernel-nivå, som är ansvarigt för att övervaka system anrop. Den filtrerar dem efter en angiven regel uppsättning och skriver meddelanden för dem till en socket. Security Center integrerar funktioner från det granskade paketet i Log Analytics agenten. Den här integrationen aktiverar insamling av granskade händelser i alla Linux-distributioner som stöds, utan krav.

    granskade poster samlas in, berikas och aggregeras i händelser med hjälp av Log Analytics agent för Linux-agenten. Security Center lägger kontinuerligt till nya analyser som använder Linux-signaler för att identifiera skadliga beteenden i molnet och lokala Linux-datorer. På samma sätt som Windows-funktioner, är dessa analys omfång över misstänkta processer, misstänkta inloggnings försök, inläsning av kernel-modul och andra aktiviteter. Dessa aktiviteter kan tyda på att en dator är utsatt för en attack eller har brutits.  

    En lista över Linux-aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-linux).


## <a name="simulating-alerts"></a>Simulera aviseringar

Du kan simulera aviseringar genom att hämta något av följande spel böcker:

- För Windows: [Azure Security Center Spelbok: säkerhets aviseringar](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- För Linux: [Azure Security Center Spelbok: Linux-identifieringar](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf).




## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Azure Defender för servrar. 

Information om relaterade material finns i följande artiklar: 

- Om en avisering genereras av Security Center eller tas emot av Security Center från en annan säkerhets produkt, kan du exportera den. Om du vill exportera aviseringar till Azure Sentinel, SIEM eller andra externa verktyg, följer du anvisningarna i [Exportera aviseringar till en Siem](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Aktivera Azure Defender](enable-azure-defender.md)