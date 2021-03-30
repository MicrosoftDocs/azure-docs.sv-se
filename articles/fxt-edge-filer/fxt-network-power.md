---
title: 'Självstudie: ansluta kablar till en Azure FXT Edge-filer'
description: Så här kabelansluter du nätverks portarna och ansluter kraften för Azure FXT Edge-maskin vara
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 80458d99ed6ad19c8a837f0989798d4dcc156b61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219538"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Självstudie: skapa nätverks anslutningar och tillhandahålla kraft till Azure FXT Edge-noden

I den här självstudien lär du dig att ansluta nätverks anslutningarna för en Azure FXT Edge-nod.

I den här kursen lär du dig:

> [!div class="checklist"]
>
> * Så här väljer du typ av nätverks kabel för din miljö
> * Så här ansluter du en Azure FXT Edge-nod till ditt data Center nätverk
> * Så här dirigerar du kablar genom kabel hanterings arm (CMA)
> * Så här ansluter du kraften till den rackmonterade enheten och slår på den

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien ska Azure FXT Edge-filer installeras i ett standard utrustnings rack. CMA bör installeras på noden filer.

## <a name="identify-ports"></a>Identifiera portar

Identifiera de olika portarna på bak sidan av dina Azure FXT Edge-filer.

![Tillbaka till en kabelansluten enhet](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Kabelansluta enheten

* Anslut RJ-45-portarna till data centrets nätverks källa enligt beskrivningen i [nätverks portar](#network-ports).  
* Anslut [Idrac-porten](#idrac-port) säkert till ett separat nätverk med en säker DHCP-server.
* Använd USB-portarna och VGA-porten för att ansluta ett tangent bord och övervaka till noden för den inledande installationen. Du måste starta noden och [Ange ett start lösen ord](fxt-node-password.md) för att aktivera nodens andra portar. Läs [Ange de ursprungliga lösen orden](fxt-node-password.md) för mer information.

Den här artikeln beskriver också hur du [ansluter AC-kraften](#connect-power-cables) för noden.

Den här artikeln beskriver också hur du ansluter till nodens [serie port](#serial-port-only-when-necessary), om det behövs för specialiserad fel sökning.

### <a name="network-ports"></a>Nätverksportar

Varje Azure FXT Edge-nod innehåller följande nätverks portar:

* Sex data portar med hög hastighet 25GbE/10 GbE:

  * Fyra portar som tillhandahålls av två nätverkskort med dual-port-plugin-program
  * Två portar från moder kortet mezzaninfil nätverkskort

* Två 1GbE-portar som tillhandahålls av Moder kortet mezzaninfil nätverkskort

25GbE-dataportarna med hög hastighet har SFP28-kompatibla burar. Om du vill använda optiska kablar måste du installera SFP28 Optical sänded-moduler (ej angivet).

1GbE-portarna har standard RJ-45-anslutningar.

En fullständig lista över kablar, växlar och Sänd tagare som stöds finns i interoperabilitet med [Cavium FastlinQ 41000-serien](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

Vilken typ av anslutningar som ska användas för systemet beror på din data Center miljö.

* Om du ansluter till ett 25GbE-nätverk kan du ansluta var och en av de höghastighets data portarna med någon av följande kabel typer:

  * Optisk kabel och SFP28 optisk Sänd tagare med 25GbE eller dubbla Rate-funktioner för 25GbE/GbE
  * SFP28-typ 25GbE-kompatibel Direct Attach twinaxial-kabel

* Om du ansluter till ett GbE-nätverk kan du ansluta varje höghastighets data port med något av följande:

  * Optisk kabel och SFP28 optisk Sänd tagare med en 25GbE/10-och-kapacitet med dubbla hastigheter.
  * SFP28-typ 25GbE-kompatibel Direct Attach twinaxial-kabel
  * SFP28 typ-kompatibel twinaxial-kabel

* Nätverks portarna för 1GbE används för kluster hanterings trafik. Markera alternativet **Använd 1 GB hanterings nätverk** när du skapar klustret om du vill skapa ett fysiskt separat nätverk för kluster konfiguration (beskrivs i [Konfigurera hanterings nätverket](fxt-cluster-create.md#configure-the-management-network)). Kabelanslut portarna med standard CAT5 eller bättre kabel enligt beskrivningen i listan över kablar som stöds.

  Du kan lämna 1GbE-portarna utan kabel om du planerar att använda höghastighets portarna för all trafik. Som standard används inte 1GbE-nätverks portarna om en data port med högre hastighet är tillgänglig.  

### <a name="idrac-port"></a>iDRAC-port  

Porten med namnet iDRAC är en 1 GB-anslutning som möjliggör kommunikation med en styrenhet för fjärråtkomst som används för maskin varu hantering och övervakning. FXT-programvaran använder Intelligent Platform Management Interface (IPMI) med den här styrenheten för fel sökning och återställning. Du kan använda det inbyggda Idrac- [gränssnittet](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) för att övervaka maskin vara via den här porten. iDRAC-och IPMI-åtkomst är aktiverat som standard.

> [!Note]
> IDRAC-porten kan kringgå operativ systemet och interagera direkt med maskin vara på noden.

Använd de här säkerhets strategierna när du ansluter och konfigurerar iDRAC-porten:

* Anslut endast iDRAC-portar till ett nätverk som är fysiskt avskilt från det data nätverk som används för att komma åt klustret.
* Ange ett säkert iDRAC administratörs lösen ord på varje nod. Du måste ange det här lösen ordet för att aktivera maskin varans Följ anvisningarna i [Ange maskin varu lösen ord](fxt-node-password.md).
* Standard port konfigurationen för iDRAC använder DHCP och IPv4 för tilldelning av IP-adresser. Kontrol lera att din DHCP-miljö är väl skyddad och att anslutningarna är begränsade mellan DHCP-klienter och DHCP-servern. (Kluster kontroll panelen innehåller inställningar för att ändra nodens adress konfiguration när du har skapat klustret.)
* Lämna iDRAC-porten inställt på "dedikerat läge" (standard), vilket begränsar iDRAC/IPMI-nätverkstrafik till den dedikerade RJ-45-porten.

IDRAC-porten kräver ingen nätverks anslutning med hög hastighet.
  
### <a name="serial-port-only-when-necessary"></a>Seriell port (endast vid behov)

I vissa situationer kan Microsoft service och support be dig att ansluta en terminal till en nods seriella port för att diagnosticera ett problem.

Så här kopplar du konsolen:

1. Leta reda på serie porten (COM1) på bak sidan av FXT Edge-noden.
1. Använd en nollmodemkabel för att ansluta den seriella porten till en Terminal som kon figurer ATS för ANSI-115200-8N1.
1. Logga in på-konsolen och vidta andra åtgärder som riktas till support personal.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Dirigera kablar i kabel hanterings arm (CMA)

Varje Azure FXT Edge-nod har en valfri kabel hanterings arm. CMA fören klar kabel Routning och ger enklare åtkomst till chassits bak utan att behöva koppla från kablar.

Följ de här anvisningarna för att dirigera kablarna genom CMA:

1. Med hjälp av anknyterna binder kan du bunta ihop kablarna när de går in och avsluta korgarna, så att de inte stör närliggande system (1).
1. Med CMA i tjänst positionen dirigerar du kabel paketet genom de inre och de yttre korgarna (2).
1. Använd den förinstallerade hooken och looparna i endera änden av korgarna för att skydda kablarna (3).
1. Sväng CMA igen på plats i facket (4).
1. Installera status indikator kabeln på bak sidan av systemet och skydda kabeln genom att dirigera den via CMA. Koppla den andra änden av kabeln till hörnet av den yttre CMA-korgen (5).

   > [!CAUTION]
   > Undvik potentiella skador från att skjuta ut kablar genom att skydda eventuella slack i status indikator kabeln när du har dirigerat den här kabeln genom CMA.

![Bild av CMA med installerade kablar](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
> Om du inte har installerat CMA kan du använda de två Hook-och loop-banden som finns i järnvägs paketet för att dirigera kablarna på bak sidan av systemet.
>
>  1. Leta upp de yttre CMA hakparenteserna på insidan av båda rackets flänsar.
>  2. Paketera kablarna försiktigt och ta bort dem från system kopplingarna till vänster och höger sida.
>  3. Koppla Hook-och loop-bandet genom de verktyg som finns på de yttre CMA hakparenteserna på varje sida av systemet för att skydda kabel paketen.
>
>     ![Kablar som dirigeras utan en CMA](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Om krav för IP-adress

För maskinvarukonfigurationer i en Azure FXT Edge-baserad hybrid Storage cache hanteras IP-adresser av kluster program varan.

Varje nod kräver minst en IP-adress, men nodadresser tilldelas när noder läggs till i eller tas bort från klustret.

Det totala antalet IP-adresser som krävs beror på antalet noder som utgör din cache.

Konfigurera IP-adressintervallet genom att använda program varan på kontroll panelen efter att noderna har installerats. Läs mer i [samla in information om klustret](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Anslut strömförsörjnings kablar

Varje Azure FXT Edge-nod använder två Power Supply-enheter (PSUs).

> [!TIP]
> Om du vill dra nytta av de två redundanta PSUs ansluter du varje ström sladd till en Power distributions enhet (PDU) på en oberoende gren krets.
>
> Du kan använda en UPS för att driva PDU: en för extra skydd.

1. Anslut de inkluderade ström sladdarna till PSUs i chassit. Kontrol lera att kablarna och PSUs är helt isatta.
1. Koppla ström sladdarna till strömförsörjnings enheterna på utrustnings racket. Använd om möjligt två separata ström källor för de två kablarna.

### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Starta en Azure FXT Edge-nod

Om du vill starta noden trycker du på strömbrytaren framför systemet. Knappen är på höger sida i kontroll panelen.

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Stäng av en Azure FXT Edge-nod

Strömbrytaren kan användas för att stänga av systemet vid testning och innan du lägger till det i ett kluster. När en Azure FXT Edge-nod används som en del av ett kluster bör du dock använda program varan i kluster kontroll panelen för att stänga av maskin varan. Läs om [hur du säkert stänger av maskin vara från Azure FXT Edge](fxt-power-off.md) -information.

## <a name="next-steps"></a>Nästa steg

När du har avslutat kablaget för maskin varan kan du slå på varje nod och initiera dem genom att ange sina rot lösen ord.
> [!div class="nextstepaction"]
> [Ange ursprungliga lösen ord](fxt-node-password.md)
