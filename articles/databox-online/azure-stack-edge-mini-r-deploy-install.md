---
title: Självstudie för att installera Azure Stack Edge Mini R fysisk enhet | Microsoft Docs
description: Den andra själv studie kursen om hur du installerar Azure Stack Edge Mini R-enheten omfattar hur du kan kabelansluta den fysiska enheten för strömförsörjning och nätverk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Mini R device in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 4f7656337b12cf477c5c71d861d031919e0d55d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96468922"
---
# <a name="tutorial-install-azure-stack-edge-mini-r"></a>Självstudie: installera Azure Stack Edge Mini R

I den här självstudien beskrivs hur du installerar en fysisk enhet för Azure Stack Edge Mini R. Installations proceduren omfattar kablage av enheten.

Installationen kan ta cirka 30 minuter att slutföra.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Inspektera enheten
> * Kabelansluta enheten

## <a name="prerequisites"></a>Förutsättningar

De nödvändiga komponenterna för att installera en fysisk enhet är följande:

### <a name="for-the-azure-stack-edge-resource"></a>För Azure Stack Edge-resursen

Innan du börjar ska du kontrollera att:

* Du har slutfört alla steg i [förbereda för att distribuera Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md).
    * Du har skapat en Azure Stack Edge-resurs för att distribuera enheten.
    * Du har genererat aktiverings nyckeln för att aktivera din enhet med Azure Stack Edge-resursen.

 
### <a name="for-the-azure-stack-edge-mini-r-physical-device"></a>För den fysiska enheten för Azure Stack Edge Mini R

Innan du distribuerar en enhet:

- Kontrollera att enheten på ett säkert sätt vilar på en plan, stabil och jämn arbetsyta.
- Kontrollera att den plats där du ska konfigurera har:
    - Standard växel ström från en oberoende källa eller
    - En PDU-enhet (Rack Power Distribution Unit). 
    

### <a name="for-the-network-in-the-datacenter"></a>För nätverket i datacentret

Innan du börjar:

- Granska nätverks kraven för att distribuera Azure Stack Edge Mini R och konfigurera Data Center nätverket enligt kraven. Mer information finns i [Azure Stack gräns för nätverks krav](azure-stack-edge-mini-r-system-requirements.md#networking-port-requirements).

- Kontrol lera att den lägsta Internet bandbredden är 20 Mbit/s för att enheten ska fungera optimalt. <!-- engg TBC -->


## <a name="inspect-the-device"></a>Inspektera enheten

Enheten levereras som en enskild enhet. Slutför följande steg för att packa upp enheten.

1. Placera lådan på en plan, jämn yta.
2. Kontrol lera om det finns någon skada i enhetens fall. Öppna ärendet och kontrol lera enheten. Om ärendet eller enheten verkar vara skadad kontaktar du Microsoft Support för att få hjälp att utvärdera om enheten är i fungerande skick.
3. När ärendet har öppnats kontrollerar du att du har:
    - En portabel Azure Stack Edge-Mini R-enhet med sido stötfångare anslutna
    - Ett batteri och det hölje som är kopplat till enheten. 
    - En ström sladd för att ansluta batteriet till ström källa 

Kontakta Azure Stack Edge support om du inte fick alla objekt som anges här. Nästa steg är att kabelansluta enheten.


## <a name="cable-the-device"></a>Kabelansluta enheten

I följande procedurer förklaras hur du kan kabelansluta Azure Stack Edge-enheten för strömförsörjning och nätverk.

Innan du börjar kabelansluta enheten behöver du följande:

- Din Azure Stack Edge Mini R-fysiska enhet på installations platsen.
- En ström kabel.
- Minst en 1-GbE RJ-45-nätverkskabel för att ansluta till hanteringsgränssnittet. Det finns två 1-GbE-nätverksgränssnitt på enheten, ett för hantering och ett för data.
- 1 10 – GbE SFP + koppar kabel för varje data nätverks gränssnitt som ska konfigureras. Minst ett data nätverks gränssnitt från PORT 3 eller PORT 4 måste vara anslutet till Internet (med anslutning till Azure).  
- Åtkomst till en Power distributions enhet (rekommenderas).

> [!NOTE]
> - Om du bara ansluter ett data nätverks gränssnitt rekommenderar vi att du använder ett 10-GbE-nätverkskort, till exempel PORT 3 eller PORT 4 för att skicka data till Azure. 
> - För att få bästa prestanda och hantera stora datavolymer bör du ansluta alla dataportar.
> - Den Azure Stack gräns enheten bör anslutas till data Center nätverket så att den kan mata in data från data käll servrar. <!-- engg TBC -->

På din Azure Stack Edge-enhet:

- Front panelen har en SSD-bärvåg. 

    - Enheten har 1 SSD-disk på plats. 
    - Enheten har också ett CFx-kort som fungerar som lagrings plats för operativ system disken.
    
- Front panelen har nätverks gränssnitt och åtkomst till Wi-Fi.

    - 2 X 1 GbE RJ 45-nätverks gränssnitt. Detta är PORT 1 och PORT 2 i enhetens lokala gränssnitt.
    - 2 X 10 GbE SFP + nätverks gränssnitt. Detta är PORT 3 och PORT 4 i enhetens lokala användar gränssnitt. 
    - En Wi-Fi port där en Wi-Fi Sänd tagare är kopplad till den.

- På Front panelen finns också en ström knapp. 

- I panelen bakåt finns ett batteri och ett skydd som är installerat på enheten. 


Utför följande steg för att kabelansluta enheten för strömförsörjning och nätverk.

1. Identifiera de olika nätverks-och lagrings komponenterna på enhetens front plan.

    ![Nätverks-och lagrings gränssnitt på enheten](./media/azure-stack-edge-mini-r-deploy-install/ports-front-plane.png)

2. Leta upp strömbrytaren i det nedre vänstra hörnet på enhetens fram sida. 

    ![Front plan för en enhet med strömbrytaren på enheten](./media/azure-stack-edge-mini-r-deploy-install/device-power-button.png)

3. Batteriet är anslutet till back planet på enheten. Identifiera den andra ström knappen som finns på batteriet. 

    ![Front plan för en enhet med strömbrytaren på batteriet](./media/azure-stack-edge-mini-r-deploy-install/battery-power-button.png)


    Anslut den ena änden av ström sladden till batteriet och till el uttaget. 

    ![Ström sladds anslutning](./media/azure-stack-edge-mini-r-deploy-install/power-cord-connector-1.png) 

    När du bara kör på batteri (batteriet är inte anslutet till strömförsörjningen) måste både strömbrytaren och skjutreglaget i batteriet växlas till på plats. När batteriet är anslutet till en ström källa ska bara ström knappen på fram sidan av enheten växlas till i rätt läge. 

4. Tryck på ström knappen i det främre planet för att aktivera enheten. 
    
    > [!NOTE]
    > Om du vill aktivera eller inaktivera strömförsörjningen till enheten måste du gå över den svarta knappen ovanpå strömbrytaren och sedan aktivera eller inaktivera ström knappen. 

5. Om du konfigurerar Wi-Fi på enheten använder du följande kablage-diagram:

    ![Kablar för Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)  

    - Anslut PORT 1 på nätverksgränssnittet med 1 GbE till den dator som används för att konfigurera den fysiska enheten. PORT 1 är det dedikerade hanteringsgränssnittet.


    Om du använder en kabelansluten konfiguration för den här enheten använder du följande diagram:
     
    ![Kablar för kabelanslutna](./media/azure-stack-edge-mini-r-deploy-install/wired-cabled.png)     
    - Anslut PORT 1 på nätverksgränssnittet med 1 GbE till den dator som används för att konfigurera den fysiska enheten. PORT 1 är det dedikerade hanteringsgränssnittet.
    - Anslut en eller flera PORT 2, PORT 3 eller PORT 4 till datacenter-nätverket/Internet.
    
        - Om du ansluter PORT 2 använder du RJ-45-nätverkskabeln.
        - För nätverks gränssnitten med 10 GbE använder du de SFP + koppar kablarna.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig mer om Azure Stack Edge-ämnen, till exempel hur du:

> [!div class="checklist"]
> * Packa upp enheten
> * Kabelansluta enheten

Gå vidare till nästa självstudie och lär dig hur du ansluter, konfigurerar och aktiverar enheten.

> [!div class="nextstepaction"]
> [Anslut och konfigurera Azure Stack Edge](./azure-stack-edge-mini-r-deploy-connect.md)
