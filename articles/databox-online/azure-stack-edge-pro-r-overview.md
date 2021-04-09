---
title: Översikt över Microsoft Azure Stack Edge Pro R | Microsoft Docs
description: Beskriver Azure Stack Edge Pro R-enheter, en lagrings lösning för militära program som använder en fysisk enhet för nätverks baserad överföring till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 40fa1bc776c5cd457e57cf170f629b6cf92800fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586003"
---
# <a name="what-is-the-azure-stack-edge-pro-r"></a>Vad är Azure Stack Edge Pro R?

Azure Stack Edge Pro R är robust, Edge Computing-enhet utformad för användning i tuffa miljöer. Azure Stack Edge Pro R levereras som en lösning för maskin vara som en tjänst. Microsoft levererar en molnbaserad enhet som fungerar som nätverks lagrings-gateway och har en inbyggd GPU (Graphic Processing Unit) som aktiverar accelererad AI-inferencing.

Den här artikeln innehåller en översikt över Azure Stack Edge Pro R-lösningen, viktiga funktioner och scenarier där du kan distribuera den här enheten.


## <a name="key-capabilities"></a>De viktigaste funktionerna

Azure Stack Edge Pro R har följande funktioner:

|Funktion |Beskrivning  |
|---------|---------|
|Robust maskin vara| Robust Server klass maskin vara utformad för svåra miljöer. Enheten ingår i ett flyttbart överförings fall. |
|Molnbaserad hantering     |Enhet och tjänst hanteras via Azure Portal.|
|Edge Compute-arbetsbelastningar   |Möjliggör analys, bearbetning och filtrering av data. Stöder virtuella datorer och arbets belastningar i behållare.|
|Accelererad AI-inferencing| Aktiverat av en NVIDIA T4-GPU.|
|Dataåtkomst     | Direktåtkomst till data från Azure Storage-blobar och Azure Files med moln-API:er för ytterligare databehandling i molnet. Lokal cache på enheten används för snabb åtkomst till de senast använda filerna.|
|Frånkopplat läge| Enhet och tjänst kan hanteras alternativt via Azure Stack hubb. Distribuera, kör, hantera program i offlineläge. <br> Frånkopplat läge stöder scenarier med offlineuppladdning.|
|Fil överförings protokoll som stöds     |Stöd för standard-SMB-, NFS-och REST-protokoll för data inmatning. <br> Mer information om versioner som stöds finns i [system krav för Azure Stack Edge Pro R](azure-stack-edge-gpu-system-requirements.md).|
|Datauppdatering     | Möjlighet att uppdatera lokala filer med det senaste från molnet.|
|Dubbel kryptering    | Med hjälp av Självkrypterande enheter får du det första krypterings lagret. VPN tillhandahåller det andra krypterings lagret. BitLocker-stöd för lokal kryptering av data och säker data överföring till molnet via *https* .|
|Bandbredds begränsning| Begränsa bandbredds användningen under hög belastnings tider.|

<!--|Scale out file server| Available as 1-node and 4-node cluster configurations|-->

## <a name="use-cases"></a>Användningsfall

Här följer de olika scenarier där Azure Stack Edge Pro R kan användas för snabb Machine Learning (ML) inferencing vid kant-och förbehandling av data innan de skickas till Azure.

- **Härledning med Azure Machine Learning** – med Azure Stack Edge Pro R kan du köra ml-modeller för att få snabba resultat som kan åtgärdas innan data skickas till molnet. Den fullständiga data uppsättningen kan alternativt överföras för att fortsätta att träna och förbättra dina ML-modeller. Mer information om hur du använder Azure ML-accelererade maskin varu modeller på den Azure Stack Edge Pro R-enheten finns i [Distribuera Azure ml-maskin accelererade modeller på Azure Stack Edge Pro r](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- **Förbearbeta data** för datatransformering innan du skickar dem till Azure för att skapa en mer åtgärds bara data uppsättning. Förbearbeta kan användas för att:

    - Aggregera data.
    - Ändra data, till exempel för att ta bort personliga data.
    - Delmängd data för att optimera lagring och bandbredd, eller för ytterligare analys.
    - Analysera och reagera på IoT-händelser.

- **Överföra data över nätverk till Azure** – Använd Azure Stack Edge Pro R för att enkelt och snabbt överföra data till Azure för att möjliggöra ytterligare beräkning och analys eller för arkivering.

## <a name="components"></a>Komponenter

Azure Stack Edge Pro R-lösningen består av en Azure Stack gräns resurs, Azure Stack Edge Pro R robust, fysisk enhet och ett lokalt webb gränssnitt.

- **Azure Stack Edge Pro R fysisk enhet** – en beräknings-och lagrings enhet med 1 nod som finns i ett robust överförings fall. En valfri avbrotts fri elkälla (UPS) är också tillgänglig.

    ![Azure Stack Edge Pro R 1 – nod enhet](media/azure-stack-edge-pro-r-overview/device-image-1.png)

- **Azure Stack Edge-resurs** – en resurs i Azure Portal som låter dig hantera en robust, Azure Stack Edge Pro R-enhet från ett webb gränssnitt som du kan komma åt från olika geografiska platser. Använd Azure Stack Edge-resursen för att skapa och hantera resurser, Visa och hantera enheter och aviseringar och hantera resurser.  

- **Azure Stack Edge Pro R Local Web UI** – ett webbläsarbaserat lokalt användar gränssnitt på din Azure Stack Edge Pro r-enhet som främst är avsedd för den inledande konfigurationen av enheten. Använd det lokala webb gränssnittet även för att köra diagnostik, stänga av och starta om Azure Stack Edge Pro-enhet, Visa kopierings loggar och kontakta Microsoft Support för att skicka en tjänstbegäran.


## <a name="region-availability"></a>Regional tillgänglighet

Azure Stack Edge Pro R fysisk enhet, Azure-resurs och mål lagrings konto som du överför data till, behöver inte alla finnas i samma region.

- **Resurs tillgänglighet** – för en lista över alla regioner där Azure Stack Edge-resursen är tillgänglig går du till [Azure-produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). 

- **Enhets tillgänglighet** – för en lista över alla länder där Azure Stack Edge Pro r-enheten är tillgänglig går du till avsnittet **tillgänglighet** på fliken **Azure Stack Edge Pro R** för [Azure Stack Edge Pro r-prissättning](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeProR).

- **Destinationslagringskonton** – de lagringskonton som lagrar data som är tillgängliga i alla Azure-regioner. De regioner där lagrings kontona lagrar Azure Stack Edge Pro R-data bör ligga nära den plats där enheten befinner sig för optimala prestanda. Ett lagringskonto som är långt från enheten leder till långa svarstider och långsammare prestanda.

Azure Stack Edge-tjänsten är en icke-regional tjänst. Mer information finns i [regioner och Tillgänglighetszoner i Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). Azure Stack Edge-tjänsten är inte beroende av en speciell Azure-region, vilket gör det flexibelt för globala avbrott och drift avbrott i hela regionen.

## <a name="next-steps"></a>Nästa steg

- Granska [system kraven för Azure Stack Edge Pro R](azure-stack-edge-gpu-system-requirements.md).
<!--- Understand the [Azure Stack Edge Pro R limits](azure-stack-edge-limits.md).-->
