---
title: Översikt över Microsoft Azure Stack Edge Pro med GPU | Microsoft Docs
description: Beskriver Azure Stack Edge Pro med GPU, en lagrings lösning som använder en fysisk enhet för nätverks baserad överföring till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 01/18/2021
ms.author: alkohli
ms.openlocfilehash: 8e829d149e96cf658c7a5aaf37291e84def2061e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602663"
---
# <a name="what-is-azure-stack-edge-pro-with-gpu"></a>Vad är Azure Stack Edge Pro med GPU?

Azure Stack Edge Pro med GPU är en AI-aktiverad Edge Computing-enhet med funktioner för nätverks data överföring. Den här artikeln ger en översikt över Azure Stack Edge Pro-lösning, fördelar, viktiga funktioner och scenarier där du kan distribuera den här enheten.

Azure Stack Edge Pro med GPU är en maskinvaru-som-tjänst-lösning. Microsoft levererar en molnbaserad enhet som fungerar som nätverks lagrings-gateway och har en inbyggd GPU (Graphic Processing Unit) som aktiverar accelererad AI-inferencing. 

## <a name="use-cases"></a>Användningsfall

Här följer de olika scenarier där Azure Stack Edge Pro kan användas för snabb Machine Learning (ML) inferencing vid kant-och förbearbetning av data innan de skickas till Azure.

- **Härledning med Azure Machine Learning** – med Azure Stack Edge Pro kan du köra ml-modeller för att få snabba resultat som kan åtgärdas innan data skickas till molnet. Den fullständiga data uppsättningen kan alternativt överföras för att fortsätta att träna och förbättra dina ML-modeller. Mer information om hur du använder Azure ML-accelererade maskin varu modeller på den Azure Stack Edge Pro-enheten finns i [Distribuera Azure ml-maskin accelererade modeller på Azure Stack Edge Pro](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- **Förbearbeta data i data** Transformation innan du skickar dem till Azure via beräknings alternativ som arbets belastningar i behållare och Virtual Machines för att skapa en mer åtgärds bara data uppsättning. Förbearbeta kan användas för att: 

    - Aggregera data.
    - Ändra data, till exempel för att ta bort personliga data.
    - Delmängd data för att optimera lagring och bandbredd, eller för ytterligare analys.
    - Analysera och reagera på IoT-händelser. 

- **Överför data över nätverk till Azure** – Använd Azure Stack Edge Pro för att enkelt och snabbt överföra data till Azure för att möjliggöra ytterligare beräkning och analys eller för arkivering. 

## <a name="key-capabilities"></a>De viktigaste funktionerna

Azure Stack Edge Pro har följande funktioner:

|Funktion |Beskrivning  |
|---------|---------|
|Accelererad AI-inferencing| Aktive ras av den inbyggda GPU: n (en eller två beroende på modellen).|
|Edge-bearbetning      |Stöder VM-och container arbets belastningar för att tillåta analys, bearbetning och filtrering av data. |
|Dataåtkomst     | Direktåtkomst till data från Azure Storage-blobar och Azure Files med moln-API:er för ytterligare databehandling i molnet. Lokal cache på enheten används för snabb åtkomst till de senast använda filerna.|
|Molnbaserad hantering     |Enhet och tjänst hanteras via Azure Portal.  |
|Offlineuppladdning     | Frånkopplat läge stöder scenarier med offlineuppladdning.|
|Fil överförings protokoll som stöds      | Stöd för standard-SMB-, NFS-och REST-protokoll för data inmatning. <br> Mer information om versioner som stöds finns i [Azure Stack Edge Pro system-krav](azure-stack-edge-system-requirements.md).|
|Datauppdatering     | Möjlighet att uppdatera lokala filer med det senaste från molnet.|
|Kryptering    | BitLocker-stöd för lokal kryptering av data och skydda dataöverföring till molnet via *https*.|
|Bandbredds begränsning| Begränsa bandbredds användningen under hög belastnings tider.|
<!--|ExpressRoute | Säkerhet har lagts till via ExpressRoute. Använd peering-konfiguration där trafik från lokala enheter till moln lagrings slut punkter överförs över ExpressRoute. Mer information finns i [Översikt över ExpressRoute](../expressroute/expressroute-introduction.md).|-->

## <a name="components"></a>Komponenter

Azure Stack Edge Pro-lösningen består av Azure Stack Edge-resurs, Azure Stack Edge Pro-fysisk enhet och ett lokalt webb gränssnitt.

* **Azure Stack den fysiska enheten för Edge Pro** – en rackoptimerad 1U-server som tillhandahålls av Microsoft och som kan konfigureras för att skicka data till Azure.
    
* **Azure Stack Edge-resurs** – en resurs i Azure Portal som låter dig hantera en Azure Stack Edge Pro-enhet från ett webb gränssnitt som du kan komma åt från olika geografiska platser. Använd Azure Stack Edge-resursen för att skapa och hantera resurser, Visa och hantera enheter och aviseringar och hantera resurser.  

    Mer information finns i [skapa en order för din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).

* **Azure Stack Edge Pro-lokalt webb gränssnitt** – ett webbläsarbaserat lokalt användar gränssnitt på din Azure Stack Edge Pro-enhet är främst avsett för den inledande konfigurationen av enheten. Använd det lokala webb gränssnittet även för att köra diagnostik, stänga av och starta om Azure Stack Edge Pro-enhet, Visa kopierings loggar och kontakta Microsoft Support för att skicka en tjänstbegäran.

    Information om hur du använder det webbaserade användar gränssnittet finns i [använda det webbaserade gränssnittet för att administrera Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Regional tillgänglighet

Azure Stack fysisk enhet i Edge Pro, Azure-resursen och mål lagrings kontot som du överför data till, behöver inte allt vara i samma region.

- **Resurs tillgänglighet** – i den här versionen är resursen tillgänglig i USA, västra EU och Asien, sydöstra regioner.

- **Enhets tillgänglighet** – för en lista över alla länder/regioner där Azure Stack Edge Pro-enheten är tillgänglig går du till avsnittet **tillgänglighet** på fliken **Azure Stack Edge Pro** för [Azure Stack Edge Pro-prissättning](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro).
    
- **Destinationslagringskonton** – de lagringskonton som lagrar data som är tillgängliga i alla Azure-regioner. De regioner där lagrings konton lagrar Azure Stack Edge Pro-data bör ligga nära den plats där enheten befinner sig för optimala prestanda. Ett lagringskonto som är långt från enheten leder till långa svarstider och långsammare prestanda.

Azure Stack Edge-tjänsten är en icke-regional tjänst. Mer information finns i [regioner och Tillgänglighetszoner i Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). Azure Stack Edge-tjänsten är inte beroende av en speciell Azure-region, vilket gör det flexibelt för globala avbrott och drift avbrott i hela regionen.

## <a name="next-steps"></a>Nästa steg

- Granska [system kraven för Azure Stack Edge Pro](azure-stack-edge-gpu-system-requirements.md).

- Förstå [Azure Stack Edge Pro-gränser](azure-stack-edge-limits.md).
- Distribuera [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) i Azure Portal.
