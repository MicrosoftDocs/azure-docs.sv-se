---
title: Självstudie för att installera Azure Stack Edge Pro R fysisk enhet | Microsoft Docs
description: Den andra själv studie kursen om hur du installerar Azure Stack Edge Pro R omfattar hur du kan kabelansluta den fysiska enheten för strömförsörjning och nätverk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Pro R in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: b67da2607d206424f69f53645eda148495ea58ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96468721"
---
# <a name="tutorial-install-azure-stack-edge-pro-r"></a>Självstudie: installera Azure Stack Edge Pro R

I den här självstudien beskrivs hur du installerar en fysisk enhet för Azure Stack Edge Pro R. Installations proceduren omfattar kablage av enheten.

Installationen kan ta cirka 30 minuter att slutföra.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Inspektera enheten
> * Kabelansluta enheten

## <a name="prerequisites"></a>Förutsättningar

De nödvändiga komponenterna för att installera en fysisk enhet är följande:

### <a name="for-the-azure-stack-edge-resource"></a>För Azure Stack Edge-resursen

Innan du börjar ska du kontrollera att:

* Du har slutfört alla steg i [förbereda för att distribuera Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md).
    * Du har skapat en Azure Stack Edge-resurs för att distribuera enheten.
    * Du har genererat aktiverings nyckeln för att aktivera din enhet med Azure Stack Edge-resursen.

 
### <a name="for-the-azure-stack-edge-pro-r-physical-device"></a>För den fysiska enheten för Azure Stack Edge Pro R

Innan du distribuerar en enhet:

- Kontrollera att enheten på ett säkert sätt vilar på en plan, stabil och jämn arbetsyta.
- Kontrollera att den plats där du ska konfigurera har:
    - Vanlig nätström från en oberoende källa

        ELLER
    - En PDU-enhet (Rack Power Distribution Unit). Enheten levereras med en avbrotts fri elkälla (UPS)
    

### <a name="for-the-network-in-the-datacenter"></a>För nätverket i datacentret

Innan du börjar:

- Granska nätverks kraven för att distribuera Azure Stack Edge Pro R och konfigurera Data Center nätverket enligt kraven. Mer information finns i [Azure Stack Edge Pro R nätverks krav](azure-stack-edge-pro-r-system-requirements.md#networking-port-requirements).

- Se till att den minsta Internetbandbredden är 20 Mbps för att enheten ska fungera optimalt.


## <a name="inspect-the-device"></a>Inspektera enheten

Enheten levereras som en enskild enhet. Slutför följande steg för att packa upp enheten.

1. Placera lådan på en plan, jämn yta.
2. Kontrol lera om det finns någon skada i enhetens fall. Öppna ärendet och kontrol lera enheten. Om ärendet eller enheten verkar vara skadad kontaktar du Microsoft Support för att få hjälp att utvärdera om enheten är i fungerande skick.
3. När ärendet har öppnats kontrollerar du att du har:
    - En enda hölje Azure Stack Edge Pro R-enhet
    - En avbrotts fri elkälla (UPS)
    - 2 kort ström kablar för att ansluta enheten till UPS-enheten
    - 1 ström kabel för att ansluta UPS till ström källa

Kontakta Azure Stack Edge Pro R-support om du inte fick alla objekt som anges här. Nästa steg är att kabelansluta enheten.


## <a name="cable-the-device"></a>Kabelansluta enheten

Följande procedurer beskriver hur du kan kabelansluta din Azure Stack Edge Pro R-enhet för strömförsörjning och nätverk.

Innan du börjar kabelansluta enheten behöver du följande:

- Din fysiska enhet för Azure Stack Edge Pro R på installations platsen.
- En ström kabel.
- Minst en 1-GbE RJ-45-nätverkskabel för att ansluta till hanteringsgränssnittet. Det finns två 1-GbE-nätverksgränssnitt på enheten, ett för hantering och ett för data.
- En 10/25-GbE SFP + koppar-kabel för varje data nätverks gränssnitt som ska konfigureras. Minst ett data nätverks gränssnitt från PORT 3 eller PORT 4 måste vara anslutet till Internet (med anslutning till Azure).  
- Åtkomst till en Power distributions enhet (rekommenderas).

> [!NOTE]
> - Om du bara ansluter ett data nätverks gränssnitt rekommenderar vi att du använder ett 25/10-GbE-nätverkskort, till exempel PORT 3 eller PORT 4 för att skicka data till Azure. 
> - För att få bästa prestanda och hantera stora datavolymer bör du ansluta alla dataportar.
> - Azure Stack Edge Pro R-enheten bör anslutas till data Center nätverket så att den kan mata in data från data käll servrar.

På din Azure Stack Edge Pro R-enhet:

- På Front panelen finns det disk enheter och en ström knapp.

    - Det finns 8 disk fack överst på enheten.
    - Enheten har också 2 X M. 2 SATA-diskar inuti som fungerar som operativ system diskar. 

- Back planet innehåller redundanta strömförsörjnings enheter (PSUs).
- Det bakre planet har fyra nätverks gränssnitt:

    - 2 1 Gbit/s-gränssnitt.
    - 2 25 Gbit/s-gränssnitt som också kan fungera som 10 Gbit/s-gränssnitt.
    - En BMC (Baseboard Management Controller).

<!--- The back plane has two network cards corresponding to the 4 ports:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

For a full list of supported cables, switches, and transceivers for these network cards, go to [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).-->
 
Utför följande steg för att kabelansluta enheten för strömförsörjning och nätverk.

1. Identifiera de olika portarna på enhetens bak plan.

    ![Back plan för en kabelansluten enhet](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)

2. Leta upp disk facken och strömbrytaren på enhetens fram sida.

    ![Front plan för en enhet](./media/azure-stack-edge-pro-r-deploy-install/device-front-plane-labeled-1.png)

3. Anslut en ände av ström sladden till UPS-enheten. Koppla den andra änden av ström sladden till rackets Power distributions enhet (PDU). 
5. Tryck på ström knappen för att aktivera enheten.
6. Anslut PORT 1 på nätverksgränssnittet med 1 GbE till den dator som används för att konfigurera den fysiska enheten. PORT 1 är det dedikerade hanteringsgränssnittet.
7. Anslut en eller flera PORT 2, PORT 3 eller PORT 4 till datacenter-nätverket/Internet.

    - Om du ansluter PORT 2 använder du RJ-45-nätverkskabeln.
    - För nätverks gränssnitten på 10/25-GbE använder du de SFP + koppar kablarna.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om Azure Stack Edge Pro R-ämnen som How to:

> [!div class="checklist"]
> * Packa upp enheten
> * Kabelansluta enheten

Gå vidare till nästa självstudie och lär dig hur du ansluter till din enhet.

> [!div class="nextstepaction"]
> [Anslut till Azure Stack Edge Pro R](./azure-stack-edge-pro-r-deploy-connect.md)
