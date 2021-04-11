---
title: Certifing enhets paket och indirekt anslutna enheter
titleSuffix: Azure Certified
description: Se skicka en indirekt ansluten enhet för certifiering.
author: cbroad
ms.author: cbroad
ms.date: 02/23/2021
ms.topic: how-to
ms.service: certification
ms.openlocfilehash: da3110b562bc5ddbd37657f31cbdd3790a13b897
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969465"
---
# <a name="device-bundles-and-indirectly-connected-devices"></a>Enhets paket och indirekt anslutna enheter

För att stödja enheter som interagerar med Azure via en enhet, SaaS eller PaaS-erbjudanden, vår överförings portal ( https://www.certify.azure.com) , och enhets katalog ( https://devicecatalog.azure.com) möjliggör koncept för sammanställningar och beroenden för att befordra och aktivera dessa enhets kombinationer för åtkomst till vårt Azure Certified Device-program.

Beroende på vilka produkt rader och tjänster som erbjuds kan din situation kräva en kombination av dessa steg:


![Skapa projekt beroenden](./media/indirect-connected-device/picture-1.png )
## <a name="sensors-and-indirect-devices"></a>Sensorer och indirekta enheter
Många sensorer kräver att en enhet ansluter till Azure. Dessutom kan du ha flera kompatibla enheter som fungerar med sensor enheten. **För att kunna hantera dessa scenarier måste du först certifiera enheterna innan du certifierar sensorn som ska skicka information via dem.**

Exempel på matris för ![ överförings kombinationer exempel](./media/indirect-connected-device/picture-2.png )

För att certifiera din sensor, som kräver en separat enhet:
1.  Certifiera först [enheten](https://certify.azure.com) och publicera den till Azure-certifierad enhets katalog
    - Om du har flera, kompatibla direkt lagrings enheter (som i exemplet ovan) ska du skicka dem separat för certifiering och publicera till katalogen även
2.  Med sensorn som är ansluten via enheten skickar du sensorn för certifiering
    * På fliken beroenden i avsnittet enhets information anger du följande värden
        * Beroende typ = "maskinvarubaserad Gateway&quot;
        * URL för beroende URL = &quot;URL-länk till enheten i enhets katalogen&quot;
        * Används under test = &quot;Ja&quot;
        * Lägg till eventuella kundvända kommentarer som ska ges till en användare som ser produkt beskrivningen i enhets katalogen. (exempel: &quot;serien 100-enheter krävs för att sensorer ska kunna ansluta till Azure")

3.  Om du har fler enheter som du vill lägga till som valfria för den här enheten kan du välja "+ Lägg till ytterligare beroende". Följ sedan samma vägledning och Observera att den inte användes under testningen. I de kundvända kommentarerna ser du till att dina kunder är medvetna om att andra enheter är kopplade till den här sensorn är tillgängliga (som ett alternativ till den enhet som användes under testningen).

![Alternativ text](./media/indirect-connected-device/picture-3.png "Typ av maskin varu beroende")

## <a name="paas-and-saas-offerings"></a>PaaS-och SaaS-erbjudanden
Som en del av produkt portföljen kan du ha enheter som du certifierar, men din enhet kräver också andra tjänster från ditt företag eller andra tredje part företag. Följ dessa steg om du vill lägga till det här beroendet:
1. Starta sändnings processen för din enhet
2. Ange följande värden på fliken beroenden
    - Beroende typ = "Software service"
    - Tjänst namn = "[ditt produkt namn]"
    - URL för beroende URL = "URL-länk till en produkt sida som beskriver tjänsten"
    - Lägg till alla kunder mot kommentarer som ska ges till en användare som ser produkt beskrivningen i Azure Certified Device-katalogen
3. Om du har andra program, tjänster eller maskin varu beroenden som du vill lägga till som valfria för den här enheten kan du välja "+ Lägg till ytterligare beroende" och följa samma vägledning.

![Typ av program beroende](./media/indirect-connected-device/picture-4.png )

## <a name="bundled-products"></a>Paketerade produkter
Paketerade produkt listor är helt enkelt en lyckad certifiering av en enhet med andra komponenter som kommer att säljas som en del av paketet i en produkt lista. Du kan skicka en enhet som innehåller extra komponenter, till exempel en temperatur sensor och en kamera sensor (#1) eller så kan du skicka en touch-sensor som innehåller en genom strömnings enhet (#2). Genom funktionen "komponent" kan du lägga till flera komponenter i din lista.

Om du tänker göra detta kan du formatera produkt registrerings bilden för att visa att produkten levereras med andra komponenter.  Om ditt paket kräver ytterligare tjänster för att certifiera måste du dessutom identifiera dem genom tjänst beroendet.
Exempel mat ris för paketerade produkter

![Exempel på paket sändning](./media/indirect-connected-device/picture-5.png )

En mer detaljerad beskrivning av hur du använder komponent funktionen på Azure-portalen för Azure-certifierade enheter finns i vår [Hjälp dokumentation](./how-to-using-the-components-feature.md). 

Om en enhet är en passthrough-enhet med en separat sensor i samma produkt, skapar du en komponent som återspeglar genom strömnings enheten och en annan komponent som reflekterar sensorn. Du kan lägga till komponenter i ditt projekt på fliken produkt information i avsnittet enhets information:

![Lägger till komponenter](./media/indirect-connected-device/picture-6.png )

För genom strömnings enheten anger du komponent typ som en kund färdig produkt och fyller i de andra fälten som är relevanta för din produkt. Exempel:

![Komponent information](./media/indirect-connected-device/picture-7.png )

För sensorn lägger du till en andra komponent, ställer in komponent typen som Peripheral och bilage metod som diskret. Exempel:

![Information om andra komponenter](./media/indirect-connected-device/picture-8.png )

När sensor komponenten har skapats redigerar du informationen, navigerar till fliken sensorer och lägger till sensor informationen. Exempel:

![Sensor information](./media/indirect-connected-device/picture-9.png )

Slutför dina projekt information och skicka in enheten för certifiering som vanligt.

