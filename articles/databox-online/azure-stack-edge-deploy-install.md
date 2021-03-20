---
title: Självstudie för att installera – packa upp, rack, kabel Azure Stack Edge Pro fysisk enhet | Microsoft Docs
description: Den andra själv studie kursen om hur du installerar Azure Stack Edge Pro omfattar att packa upp, racka och kabelansluta den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Pro in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 9aa02521d91d41380b1bdac3efe50ab3d196a856
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90894197"
---
# <a name="tutorial-install-azure-stack-edge-pro"></a>Självstudie: installera Azure Stack Edge Pro

I den här självstudien beskrivs hur du installerar en fysisk enhet för Azure Stack Edge Pro. Installationsproceduren omfattar uppackning, rackmontering och kabelanslutning av enheten. 

Det kan ta cirka två timmar att slutföra installationen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Packa upp enheten
> * Rackmontera enheten
> * Kabelansluta enheten

## <a name="prerequisites"></a>Förutsättningar

De nödvändiga komponenterna för att installera en fysisk enhet är följande:

### <a name="for-the-azure-stack-edge-resource"></a>För Azure Stack Edge-resursen

Innan du börjar ska du kontrollera att:

* Du har slutfört alla steg i [förbereda för att distribuera Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md).
    * Du har skapat en Azure Stack Edge-resurs för att distribuera enheten.
    * Du har genererat aktiverings nyckeln för att aktivera din enhet med Azure Stack Edge-resursen.

 
### <a name="for-the-azure-stack-edge-pro-physical-device"></a>För den fysiska enheten för Azure Stack Edge Pro

Innan du distribuerar en enhet:

- Kontrollera att enheten på ett säkert sätt vilar på en plan, stabil och jämn arbetsyta.
- Kontrollera att den plats där du ska konfigurera har:
    - Vanlig nätström från en oberoende källa

        ELLER
    - En strömfördelare (PDU) för rack med en avbrottsfri strömkälla (UPS)
    - En tillgänglig 1U-plats på det rack där du ska montera enheten

### <a name="for-the-network-in-the-datacenter"></a>För nätverket i datacentret

Innan du börjar:

- Granska nätverks kraven för att distribuera Azure Stack Edge Pro och konfigurera Data Center nätverket enligt kraven. Mer information finns i [Azure Stack gräns för nätverks krav för Edge Pro](azure-stack-edge-system-requirements.md#networking-port-requirements).

- Se till att den minsta Internetbandbredden är 20 Mbps för att enheten ska fungera optimalt.


## <a name="unpack-the-device"></a>Packa upp enheten

Den här enheten levereras i en enda låda. Slutför följande steg för att packa upp enheten. 

1. Placera lådan på en plan, jämn yta.
2. Kontrollera lådan och förpackningsskummet för att se om det förekommer krosskador, skärningar, vattenskador eller andra uppenbara skador. Om lådan eller förpackningen är allvarligt skadad ska du inte öppna den. Kontakta Microsoft Support för att få hjälp att bedöma om enheten är i gott skick.
3. Packa upp lådan. När du har packat upp lådan kontrollerar du att det finns:
    - En enda kabinett Azure Stack Edge Pro-enhet
    - Två strömkablar
    - En järn vägs pakets sammansättning
    - En broschyr om säkerhet, miljö och reglerande information

Kontakta Azure Stack Edge pro-support om du inte fick alla objekt som anges här. Nästa steg är att rackmontera enheten.


## <a name="rack-the-device"></a>Rackmontera enheten

Enheten måste installeras i ett 19-tums standardrack. Använd följande procedur för att racka montera enheten på en standardiserad 19-tums rack lösning.

> [!IMPORTANT]
> Azure Stack Edge Pro-enheter måste vara rack montering för att fungera korrekt.


### <a name="prerequisites"></a>Förutsättningar

- Innan du börjar läser du säkerhets anvisningarna i broschyren om säkerhet, miljö och information. Broschyren levererades med enheten.
- Börja installera räler på det tilldelade utrymmet som är närmast botten av rack inne slutningen.
- För konfigurationen av konfiguration av järn vägs montering:
    -  Du måste ange åtta skruvar: #10-32, #12-24, #M5 eller #M6. Skruvens huvud diameter måste vara mindre än 10 mm (0,4 ").
    -  Du behöver en platt Screwdriver.

### <a name="identify-the-rail-kit-contents"></a>Identifiera innehållet i järnvägs paketet

Leta upp komponenterna för installation av järnvägs kit-sammansättningen:
1. Två A7 Dell ReadyRails II glidande järnvägs-sammansättningar
2. Två Hook-och loop-remmar

    ![Identifiera innehåll i järnvägs kit](./media/azure-stack-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Installera och ta bort verktygs lösa spår (håls-håls-hål eller Round håls-rack)

> [!TIP]
> Det här alternativet är verktyg-mindre eftersom det inte kräver verktyg för att installera och ta bort räler i den ej trådade kvadraten eller runda hålen i racken.

1. Placera de vänstra och högra järnvägarna på **bak** sidan och placera varje slut punkt i hålen på fram sidan av de lodräta rackets fläns.
2. Justera varje slut punkt i de nedre och övre hålen av önskade U-blank steg.
3. Engagera bak sidan av järnvägen tills den är helt placerade på den lodräta rack flänsen och att Lås klickar på plats. Upprepa stegen för att placera och placera ut frontend-delen på den lodräta rackets fläns.
4. Om du vill ta bort räler drar du tillbaka spärr knappen på slut punktens mitt punkt och tar bort varje järnväg.

    ![Installera och ta bort verktygs lösa spår](./media/azure-stack-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Installera och ta bort verktygsbaserade räler (trådade håls ställningar)

> [!TIP]
> Det här alternativet visas eftersom det kräver ett verktyg (_en flat-lutad Screwdriver_) för att installera och ta bort räler i de trådade runda hålen i racken.

1. Ta bort stiften från front-och bakre monterings hakparenteser med hjälp av en flat-lutad Screwdriver.
2. Ta bort och rotera del sammansättningarna för järnvägs lås för att ta bort dem från monterings hakparenteserna.
3. Koppla de vänstra och högra monterings skenorna till de främre lodräta racken med två par skruvar.
4. Dra tillbaka vänster och höger hak paren tes framåt mot de bakre lodräta rack flänsarna och fäst dem med två par skruvar.

    ![Installera och ta bort verktyg med verktyg](./media/azure-stack-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Installera systemet i en rack

1. Hämta de inre bildskärmarna från racket tills de låser sig.
2. Hitta den bakre järnvägs Standoff på varje sida av systemet och sänk dem till bak sidan av bild sammansättningarna. Rotera systemet nedåt tills alla räl-standoffs har placerats på J-facken.
3. Push-inaktive ring av systemet tills lås spaken klickar på plats.
4. Tryck på knapparna för att låsa fri lock på båda spåren och Skjut upp systemet i racket.

    ![Installera system i ett rack](./media/azure-stack-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Ta bort systemet från racket

1. Leta upp Lås spaken på sidorna av de inre spåren.
2. Lås upp varje spaken genom att rotera den upp till dess versions position.
3. Grepp sidorna i systemet ordentligt och hämta det framåt tills järnvägs standoffs är längst fram i J-facken. Lyft upp och bort systemet från racket och placera det på en nivå yta.

    ![Ta bort systemet från racket](./media/azure-stack-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Engagera och släppa slam-spärren

> [!NOTE]
> För system som inte är utrustade med slam lås skyddar du systemet med hjälp av skruvar, enligt beskrivningen i steg 3 i den här proceduren.

1. Placera fram-och slam-spärren på båda sidor av systemet.
2. Låsningarna sker automatiskt när systemet har lanserats i racket och släpps genom att du tar upp spärren.
3. För att skydda systemet för leverans i racket eller för andra instabila miljöer, letar du upp hård Mount-skruven under varje lås och drar varje skruv med en #2 Phillips-Screwdriver.

    ![Engagera och Frisläpp slam-spärr](./media/azure-stack-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Kabelansluta enheten

Dirigera kablarna och kablar sedan enheten. Följande procedurer beskriver hur du kan kabelansluta din Azure Stack Edge Pro-enhet för strömförsörjning och nätverk.

Innan du börjar kabelansluta enheten behöver du följande:

- Din fysiska Azure Stack Edge Pro-enhet, packas upp och rack montering.
- Två strömkablar.
- Minst en 1-GbE RJ-45-nätverkskabel för att ansluta till hanteringsgränssnittet. Det finns två 1-GbE-nätverksgränssnitt på enheten, ett för hantering och ett för data.
- En 25-GbE SFP+-kopparkabel för varje datanätverksgränssnitt som ska konfigureras. Minst ett datanätverksgränssnitten PORT 2, PORT 3, PORT 4, PORT 5 eller PORT 6 måste vara anslutet till Internet (med anslutning till Azure).  
- Åtkomst till två strömfördelare (rekommenderas).

> [!NOTE]
> - Om du bara ansluter ett data nätverks gränssnitt rekommenderar vi att du använder ett 25/10-GbE-nätverkskort, till exempel PORT 3, PORT 4, PORT 5 eller PORT 6 för att skicka data till Azure. 
> - För att få bästa prestanda och hantera stora datavolymer bör du ansluta alla dataportar.
> - Azure Stack Edge Pro-enheten bör anslutas till data Center nätverket så att den kan mata in data från data käll servrar.

På din Azure Stack Edge Pro-enhet:

- På Front panelen finns det disk enheter och en ström knapp.

    - Det finns 10 disk fack överst på enheten.
    - Fack 0 har en SATA-enhet på 240 GB som används som en operativ system disk. Fack 1 är tomt och fack 2 till 9 är NVMe-SSD som används som data diskar.
- Back planet innehåller redundanta strömförsörjnings enheter (PSUs).
- Det bakre planet har sex nätverks gränssnitt:

    - 2 1 Gbit/s-gränssnitt.
    - 4 25 Gbit/s-gränssnitt som också kan fungera som 10 Gbit/s-gränssnitt.
    - En BMC (Baseboard Management Controller).

- Det bakre planet har två nätverkskort som motsvarar 6 portar:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

En fullständig lista över kablar, växlar och Sänd tagare som stöds för dessa nätverkskort finns i [Cavium FastlinQ 41000-serien](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
 
Utför följande steg för att kabelansluta enheten för strömförsörjning och nätverk.

1. Identifiera de olika portarna på enhetens bak plan.

    ![Back plan för en kabelansluten enhet](./media/azure-stack-edge-deploy-install/backplane-cabled.png)

2. Leta upp disk facken och strömbrytaren på enhetens fram sida.

    ![Front plan för en enhet](./media/azure-stack-edge-deploy-install/device-front-plane-labeled-1.png)

3. Anslut strömkablarna till vart och ett av nätaggregaten i höljet. För att säkerställa hög tillgänglighet installerar och ansluter du båda nätaggregaten till olika strömkällor.
4. Anslut strömkablarna till rackets strömfördelare (PDU). Se till att de två strömfördelarna använder separata strömkällor.
5. Tryck på ström knappen för att aktivera enheten.
6. Anslut PORT 1 på nätverksgränssnittet med 1 GbE till den dator som används för att konfigurera den fysiska enheten. PORT 1 är det dedikerade hanteringsgränssnittet.
7. Anslut en eller flera av PORT 2, PORT 3, PORT 4, PORT 5 eller 6 PORT till datacenternätverket/Internet.

    - Om du ansluter PORT 2 använder du RJ-45-nätverkskabeln.
    - För nätverks gränssnitten på 10/25-GbE använder du de SFP + koppar kablarna.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig mer om Azure Stack Edge Pro-ämnen, till exempel hur du:

> [!div class="checklist"]
> * Packa upp enheten
> * Rackmontera enheten
> * Kabelansluta enheten

Gå vidare till nästa självstudie och lär dig hur du ansluter, konfigurerar och aktiverar enheten.

> [!div class="nextstepaction"]
> [Anslut och konfigurera Azure Stack Edge Pro](./azure-stack-edge-deploy-connect-setup-activate.md)
