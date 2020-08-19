---
title: Självstudie för att konfigurera Azure Data Box | Microsoft Docs
description: I den här självstudien lär du dig hur du ansluter kablarna på Azure Data Box, hur du ansluter Azure Data Box-enheten och hur du startar den.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 53309023a838f4b256327a37866408f556c81d29
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87924164"
---
::: zone target="docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>Självstudier: Kabelansluta och ansluta till Azure Data Box

::: zone-end

::: zone target="chromeless"

## <a name="cable-and-connect-to-your-device"></a>Koppla in och ansluta till enheten

::: zone-end

::: zone target="docs"

I den här självstudien beskrivs hur du kabelansluter, ansluter och slår på Azure Data Box-enheten.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kabelansluta Data Box
> * Ansluta till Data Box

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har beställt Azure Data Box.
    - Information om importbeställningar finns i [Självstudie: Beställ Azure Data Box](data-box-deploy-ordered.md).
    - Information om exportbeställningar finns i [Självstudie: Beställa Azure Data Box](data-box-deploy-export-ordered.md)
1. Du har fått din Data Box och att orderstatusen i portalen är **Levererad**. 
    - Det finns en adressetikett i den genomskinliga fickan som är fäst under den befintliga etiketten på enheten. Spara etiketten – du behöver den till returleveransen.
    - Vissa regioner i Europa kan få enheten inpackad i en låda. Se till att packa upp enheten och spara lådan för returförsändelse.
1. Du har läst [säkerhetsföreskrifterna för Data Box](data-box-safety.md).
1. Du har fått en jordad strömkabel att använda med lagringsenheten på 100 TB.
1. Du har en värddator som används för att kopiera data (importbeställning) till eller kopiera data från (exportbeställning) din Data Box-enhet. Värddatorn måste
    - Köra ett [operativsystem som stöds](data-box-system-requirements.md).
    - Vara ansluten till höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10 GbE anslutning inte är tillgänglig kan en 1 GbE datalänk användas, men då påverkas kopieringshastigheten. 
1. Du måste ha tillgång till en plan yta där du kan placera Data Box. Om du vill placera enheten på en standardrackhylla måste du ha en 7U-plats i datacenterracket. Du kan placera enheten liggande eller stående i racket.
1. Du har skaffat följande kablar för att ansluta Data Box till värddatorn.
    - En eller flera 10-GbE SFP+ Twinax-kopparkablar eller SFP+-fiberoptikkablar (använd med DATA 1- och DATA 2-nätverksgränssnitt). Data Box har Mellanox ConnectX®-3 Pro EN Dual-Port 10GBASE-T-adaptrar med PCI Express 3.0-nätverksgränssnitt. Därmed bör kablar som är kompatibla med det här gränssnittet fungera. Till exempel användes en CISCO SFP-H10GB-CU3M 10GBASE-CU TWINAX SFP +3M-kabel för intern testning. Mer information finns i [listan över kablar och växlar från Mellanox som stöds](https://www.mellanox.com/pdf/firmware/ConnectX3-FW-2_42_5000-release_notes.pdf).
    - En RJ-45 CAT 6-nätverkskabel (använd med MGMT-nätverksgränssnitt)
    - En RJ-45 CAT 6A- eller en RJ-45 CAT 6-nätverkskabel (använd med DATA 3-nätverksgränssnitt konfigurerat som 10 Gbps respektive 1 Gbps)

## <a name="cable-your-device"></a>Kabelanslut din enhet

Kabelanslut enheten med hjälp av följande steg.

1. Inspektera enheten och leta efter tecken på manipulation eller andra uppenbara skador. Fortsätt inte om enheten har manipulerats eller skadats. Kontakta Microsoft Support omedelbart för att få hjälp med att bedöma om enheten är i gott skick eller om de bör ersättas.
2. Transportera enheten till den plats där du vill starta den. Placera enheten på en plan yta. Du kan även placera enheten på en standardrackhylla.
3. Anslut strömmen och nätverkskablarna. Bakplanet på en ansluten enhet för en vanlig konfiguration visas nedan. Beroende på din miljö kan du välja andra [kabelanslutningsalternativ](data-box-cable-options.md).
    
    ![Bakplan på kabelansluten Data Box-enhet](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. Anslut strömkabeln till den märkta strömingången. Den andra änden av strömkabeln ansluts till en strömfördelare.
    2. Använd RJ-45 CAT 6-kabeln till att ansluta MGMT-porten i ena änden och en bärbar dator i den andra änden.            
    3. Använd RJ-45 CAT 6A-kabeln till att ansluta DATA 3-porten i ena änden. DATA 3 konfigureras som 10 GbE om du ansluter via RJ-45 CAT 6A-kabel och som 1 GbE om du ansluter via RJ-45 CAT 6-kabel.
    4. Beroende på vilket nätverksgränssnitt som du vill ansluta för dataöverföring använder du upp till två 10 GbE-SFP+ Twinax-kopparkablar eller SFP+-fiberoptikkablar för att ansluta DATA 1- respektive DATA 2-portarna. 
    5. De andra ändarna av kablarna från dataportarna ansluts till värddatorn via en 10 GbE-switch.

4. Leta reda på strömknappen på enhetens frontpanel. Slå på enheten.

    ![Strömknappen på Data Box](media/data-box-deploy-set-up/data-box-powered-door-open.png)

::: zone-end

::: zone target="chromeless"

När du har tagit emot enheten måste du koppla in och ansluta till enheten. 

## <a name="cable-your-device"></a>Kabelanslut din enhet

1. Fortsätt inte om det finns tecken på att enheten har manipulerats eller skadats. Kontakta Microsoft Support så att de kan skicka en ersättningsenhet.
2. Se till att du har följande kablar innan du kabelansluter enheten:
    
    - (Medföljer) jordad strömkabel graderad 10 A eller högre med en IEC60320 C-13-kontakt i ena änden av enheten.
    - En RJ-45 CAT 6-nätverkskabel (använd med MGMT-nätverksgränssnitt)
    - Två 10 GbE SFP+ Twinax-kopparkablar (använd med 10 Gbps DATA 1-, DATA 2-nätverksgränssnitt)
    - En RJ-45 CAT 6A- eller en RJ-45 CAT 6-nätverkskabel (använd med DATA 3-nätverksgränssnitt konfigurerat som 10 Gbps respektive 1 Gbps)

3. Ta bort och placera enheten på en plan yta. 
    
4. Kabelanslut enheten enligt nedan.  

    ![Bakplan på kabelansluten Data Box-enhet](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Anslut strömkabeln till enheten.
    2. Använd RJ-45 CAT 6-nätverkskabeln till att ansluta värddatorn till hanteringsporten (MGMT) på enheten. 
    3. Använd SFP+ Twinax-kopparkabeln till att ansluta minst ett 10 Gbps-nätverksgränssnitt (rekommenderas över 1 Gbps), DATA 1 eller DATA 2 för data. 
    4. Slå på enheten. Strömknappen finns på enhetens frontpanel.

::: zone-end

::: zone target="docs"


## <a name="connect-to-your-device"></a>Ansluta till din enhet

Konfigurera din enhet med hjälp av följande steg via det lokala webbgränssnittet och portalens användargränssnitt.

1. Konfigurera Ethernet-nätverkskortet på den bärbara dator du använder för att ansluta till enheten med den statiska IP-adressen 192.168.100.5 och undernätet 255.255.255.0. 
2. Anslut till MGMT-porten på enheten och öppna dess lokala webbgränssnitt på https\://192.168.100.10. Det kan ta upp till 5 minuter efter att du slagit på enheten.
3. Klicka på **Details** (Information) och klicka sedan på **Go on to the webpage** (Fortsätt till webbsidan).

   ![Ansluta till det lokala webbgränssnittet](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png) 

4. En **inloggningssida** visas för det lokala webbgränssnittet. Kontrollera att enhetens serienummer matchar både portalens användargränssnitt och det lokala webbgränssnittet. Enheten är låst i det här läget.
5. Logga in på [Azure-portalen](https://portal.azure.com).
6. Ladda ned enhetens autentiseringsuppgifter från portalen. Gå till **Allmänt > Enhetsinformation**. Kopiera **enhetslösenordet**. Enhetslösenordet är knutet till en specifik order i portalen. 

    ![Hämta enhetsautentiseringsuppgifter](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. Ange det enhetslösenord som du har fått från Azure-portalen i det föregående steget för att logga in på det lokala webbgränssnittet för enheten. Klicka på **Logga in**.
8. På **instrumentpanelen** kontrollerar du att nätverksgränssnitten är konfigurerade. 
   - Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt. 
   - Om DHCP inte har aktiverats går du till **Ställ in nätverksgränssnitt** och tilldelar statiska IP-adresser, om det behövs.

     ![Instrumentpanel för enhet](media/data-box-deploy-set-up/data-box-dashboard-1.png)

När datanätverksgränssnitten har konfigurerats kan du även använda IP-adressen för något av DATA 1–DATA 3-gränssnitten för att komma åt det lokala webbgränssnittet på `https://<IP address of a data network interface>`. 

När enhetskonfigurationen är klar kan du ansluta till enhetsresurserna och kopiera data. 

::: zone-end

::: zone target="chromeless"

## <a name="connect-your-device"></a>Anslut enheten

1. Hämta lösenordet för enheten på **Allmänt > Enhetsinformation** på [Azure-portalen](https://portal.azure.com).
2. Tilldela en statisk IP-adress, 192.168.100.5, och undernät 255.255.255.0 till Ethernet-adaptern på den dator du använder för att ansluta till Data Box. Öppna det lokala webbgränssnittet på enheten på `https://192.168.100.10`. Anslutningen kan upp till 5 minuter att slutföra efter att du slagit på enheten. 
3. Logga in med lösenordet från Azure-portalen. Ett felmeddelande visas som anger ett problem med webbplatsens säkerhetscertifikat. Följ de webbläsarspecifika instruktionerna för att fortsätta till webbsidan.
4. Som standard konfigureras nätverksinställningarna för 10 Gbps-datagränssnitt (eller 1 Gbps) som DHCP. Om det behövs kan du konfigurera det här nätverket som statiskt och ange en IP-adress. 

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Kabelansluta Data Box
> * Ansluta till Data Box

Gå vidare till nästa självstudie som beskriver hur du kopierar data.

> [!div class="nextstepaction"]
> [Kopiera data till Azure Data Box för en importbeställning](./data-box-deploy-copy-data.md)

Eller

> [!div class="nextstepaction"]
> [Kopiera data från Azure Data Box för en exportbeställning](./data-box-deploy-export-copy-data.md)

::: zone-end

