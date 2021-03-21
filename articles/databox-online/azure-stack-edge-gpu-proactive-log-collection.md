---
title: Förstå proaktiv logg insamling på Azure Stack Edge Pro-enhet
description: Beskriver hur proaktiv logg insamling utförs på en Azure Stack Edge Pro-enhet och hur du inaktiverar den.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: alkohli
ms.openlocfilehash: bdd44bf81e34b60ee648b71c6dc3bde5a96d3deb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443020"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Proaktiv logg insamling på din Azure Stack Edge-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Proaktiv logg samling samlar in system hälso indikatorer på din Azure Stack Edge-enhet så att du effektivt kan felsöka eventuella problem med enheten. Proaktiv logg insamling är aktiverat som standard. I den här artikeln beskrivs vad som loggas, hur Microsoft hanterar data och hur du inaktiverar eller aktiverar proaktiv logg insamling. 

Informationen i den här artikeln gäller Azure Stack Edge Pro GPU, Azure Stack Edge Pro R och Azure Stack Edge Mini R-enheter.

## <a name="about-proactive-log-collection"></a>Om proaktiv logg insamling

Microsofts kund support-och teknik team använder system loggar från Azure Stack gräns enheten för att effektivt identifiera och åtgärda problem som kan uppstå under driften. Proaktiv logg insamling är en metod som varnar Microsoft om att ett problem/en händelse har upptäckts av kundens Azure Stack Edge-installation. Se [proaktiva logg insamlings indikatorer](#proactive-log-collection-indicators) för händelser som spåras. Support loggar som rör problemet laddas automatiskt upp till ett Azure Storage-konto som Microsoft hanterar och kontrollerar. Microsoft Support och Microsoft-tekniker granskar dessa support loggar för att fastställa bästa möjliga åtgärd för att lösa problemet med kunden.

> [!NOTE]
> De här loggarna används bara för fel söknings syfte och ger support till kunderna i händelse av problem.


## <a name="enabling-proactive-log-collection"></a>Aktiverar proaktiv logg insamling

Proaktiv logg insamling är aktiverat som standard. Du kan inaktivera proaktiv logg insamling när du försöker aktivera enheten via det lokala användar gränssnittet. 

1. I enhetens lokala webb gränssnitt går du till sidan **Kom igång** .

2. På **aktiverings** panelen väljer du **Aktivera**. 

    ![Lokal webb gränssnitt "moln information" sida 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)

3. I fönstret **Aktivera** :

   1. Ange aktiverings **nyckeln** som du fick i [Hämta aktiverings nyckeln för Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

      När den är aktive rad aktive ras proaktiv logg insamling som standard, vilket gör att Microsoft kan samla in loggar baserat på enhetens hälso status. Dessa loggar överförs till ett Azure Storage-konto. 

      Du kan inaktivera proaktiv logg insamling och hindra Microsoft från att samla in loggar.

   1. Om du vill inaktivera proaktiv logg insamling på enheten väljer du **inaktivera**.

   1. Välj **Aktivera**.

   ![Lokal webb gränssnitt "moln information" sida 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)

## <a name="proactive-log-collection-indicators"></a>Proaktiva logg insamlings indikatorer

När den proaktiv logg samlingen har Aktiver ATS laddas loggarna upp automatiskt när någon av följande händelser identifieras på enheten:  


|Varning/fel/villkor  |Beskrivning  |
|---------|---------|
|AcsUnhealthyCondition     |Azures konsekventa tjänster är inte felfria.         |
|IOTEdgeAgentNotRunningCondition      |IoT Edge Agent körs inte.         |
|UpdateInstallFailedEvent | Det gick inte att installera uppdateringen.        |

 
Microsoft kommer att fortsätta att lägga till nya händelser i föregående lista. Inget ytterligare medgivande krävs för nya händelser. På den här sidan kan du läsa om de senaste indikatorerna för inaktiva proaktiva loggar.    
 

## <a name="other-log-collection-methods"></a>Andra logg insamlings metoder

Förutom proaktiv logg insamling, som samlar in vissa loggar som rör ett särskilt problem, kan andra logg samlingar ge en mycket djupare förståelse för systemets hälsa och beteende. Dessa andra loggar kan vanligt vis samlas in under en support förfrågan eller utlösas av Microsoft baserat på telemetridata från enheten.

## <a name="handling-data"></a>Hantera data

När proaktiv logg insamling är aktive rad, accepterar kunden att Microsoft samlar in loggar från Azure Stack Edge-enheten enligt beskrivningen här. Kunden bekräftar och samverkar också med uppladdningen och lagringen av dessa loggar i ett Azure Storage konto som hanteras och styrs av Microsoft.

Microsoft använder endast data för att felsöka systemets hälsa och problem. Data används inte för marknadsföring, annonsering eller andra kommersiella ändamål utan kund medgivande. 

De data som Microsoft samlar in hanteras enligt våra standard sekretess metoder. Om en kund bestämmer sig för att återkalla sitt medgivande för proaktiv logg insamling kommer alla data som samlats in med medgivande innan återkallning inte att påverkas.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [samlar in ett support paket](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).