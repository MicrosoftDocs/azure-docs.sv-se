---
title: Självstudie för att konfigurera Azure Data Box Heavy | Microsoft Docs
description: I den här självstudien får du lära dig hur du ansluter kablarna på din Azure Data Box Heavy, hur du ansluter Azure Data Box Heavy-enheten och hur du startar den.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: c38ff7e642017afe5f220ae26d3a04c2c0b706ee
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920950"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>Självstudier: Koppla in och ansluta till Azure Data Box Heavy

::: zone-end

::: zone target = "chromeless"

## <a name="get-started-with-azure-data-box-heavy"></a>Kom igång med Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

Den här självstudien beskriver hur du kopplar in, ansluter och slår på Azure Data Box Heavy-enheten.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Koppla in Data Box Heavy
> * Ansluta till Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Den här guiden innehåller anvisningar som beskriver förhandskraven, hur du kopplar in och ansluter din enhet, samt hur du kopierar data, laddar upp dem till Azure och sedan kontrollerar att alla data har överförts.

::: zone-end

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har slutfört självstudien [: Beställ Azure Data Box Heavy](data-box-heavy-deploy-ordered.md).
2. Du har fått din Data Box Heavy-enhet och orderstatusen på portalen är **Levererad**.
3. Du har gått igenom [säkerhetsriktlinjerna för Data Box Heavy](data-box-safety.md).
4. Du måste ha tillgång till en plan yta i datacentret med närhet till en nätverksanslutning som kan hantera en enhet med det här fotavtrycket. Den här enheten kan inte monteras i ett rack.
5. Du har fått fyra jordade strömkablar som du ska använda med lagringsenheten.
6. Du bör ha en värddator som är ansluten till datacenternätverket. Din Data Box Heavy kommer att kopiera data från den här datorn. Värddatorn måste köra ett [operativsystem](data-box-heavy-system-requirements.md) som stöds.
7. Datacentret måste ha höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. 
8. Du måste ha en bärbar dator med RJ-45-kabel för att kunna ansluta till det lokala användargränssnittet och konfigurera enheten. Använd den bärbara datorn för att konfigurera varje nod av enheten en gång.
9. Du behöver en 40 Gbit/s-kabel eller 10 Gbit/s-kabel per enhetsnod.
    - Välj kablar som är kompatibla med [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)-nätverksgränssnittet.
    - För 40 Gbit/s-kabeln måste enhetens kabelände vara QSFP+.
    - För 10 Gbit/s-kabeln behöver du en SFP+-kabel som ansluts till en 10 Gbit/s-växel i ena änden, med en QSFP+ till SFP+-adapter (eller QSA-adaptern) för änden som ansluts till enheten.

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>Koppla in enheten för strömförsörjning

Koppla in enheten med hjälp av följande steg.

1. Inspektera enheten och leta efter tecken på manipulation eller andra uppenbara skador. Fortsätt inte om enheten har manipulerats eller skadats. [Kontakta Microsoft Support](data-box-disk-contact-microsoft-support.md) direkt för att få hjälp med att bedöma om enheten är i gott skick eller om den måste ersättas.
2. Flytta enheten till installationsplatsen.

    ![Installationsplats för Data Box Heavy-enhet](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Lås de bakre hjulen på enheten enligt nedan.

    ![Låsta hjul på Data Box Heavy-enhet](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Leta upp reglagen som låser upp enhetens fram- och bakdörrar. Lås upp och öppna den främre dörren så långt det går. Gör samma sak med den bakre dörren.
    Båda dörrarna måste vara öppna när enheten körs för optimalt luftflöde genom enheten.

    ![Öppna Data Box Heavy-dörrar](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. Facket på baksidan av enheten bör ha fyra strömkablar. Ta bort alla kablar från facket och lägg dem åt sidan.

    ![Data Box Heavy-strömkablar i facket](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. Nästa steg är att identifiera de olika portarna på baksidan av enheten. Det finns två enhetsnoder, **NODE1** och **NODE2**. Varje nod har fyra nätverksgränssnitt, **MGMT**, **DATA1**, **DATA2** och **DATA3**. **MGMT** används för att konfigurera hanteringen under den inledande konfigurationen av enheten. **DATA1**-**DATA3** är dataportar. **MGMT**- och **DATA3**-portar är 1 Gbit/s-portar, medan **DATA1** och **DATA2** kan fungera som 40 Gbit/s- portar eller 10 Gbit/s-portar. Längst ned på de två enhetsnoderna finns fyra strömförsörjningsenheter (PSU:er) som delas mellan de två enhetsnoderna. Om du står framför enheten är **PSU:erna** **PSU1**, **PSU2**, **PSU3** och **PSU4** från vänster till höger.

    ![Data Box Heavy-portar](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Anslut alla fyra strömkablar till enhetens strömförsörjning. De gröna LED-indikatorerna tänds och blinkar.
8. Aktivera enhetens noder med hjälp av strömknapparna på den främre panelen. Låt strömknappen vara nedtryckt under några sekunder tills de blå lamporna tänds. Nu bör alla gröna LED-strömindikatorer på baksidan av enheten lysa. Den främre driftpanelen innehåller också felindikatorer. När en felindikator lyser tyder det på en felaktig PSU eller fläkt eller ett problem med diskenheterna.  

    ![Data Box Heavy-enhetens främre driftpanel](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Koppla in den första noden för nätverket

Koppla in den ena av enhetens noder genom att följa stegen nedan.

1. Använd en CAT 6 RJ-45-nätverkskabel (kabeln överst till höger i bilden som är ansluten till uttaget som är märkt MGMT) för att ansluta värddatorn till 1 Gbit/s-hanteringsporten.
2. Använd en QSFP+-kabel (fiber eller koppar) för att ansluta minst ett 40 Gbit/s-nätverksgränssnitt (helst över 1 Gbit/s) för data. Om du använder en 10 Gbit/s-växel använder du en SFP+-kabel med en QSFP+ till SFP+-adapter (QSA-adaptern) för att ansluta 40 Gbit/s-nätverksgränssnittet för data.

    ![Inkopplade Data Box Heavy-portar](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DATA 1 och DATA2 är omkastade och matchar inte vad som visas i det lokala webbgränssnittet.
    > 40 Gbit/s-kabeladaptern ansluts när den sätts i på det sätt som visas nedan.

    ![40-Gbit/s-kabeladapter för Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Konfigurera den första noden

Utför följande steg för att konfigurera enheten med hjälp av den lokala konfigurationen och Azure-portalen.

1. Ladda ned enhetens autentiseringsuppgifter från portalen. Gå till **Allmänt > Enhetsinformation**. Kopiera **enhetslösenordet**. Dessa lösenord är knutna till en specifik order på portalen. Du ser de två serienumren som motsvarar de två noderna i Data Box Heavy. Enhetsadministratörens lösenord för båda noderna är samma.

    ![Autentiseringsuppgifter för Data Box Heavy-enheten](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Anslut klientarbetsstationen till enheten via en CAT6 RJ-45-nätverkskabel.
3. Konfigurera Ethernet-kortet på den dator som du använder för att ansluta till enheten med den statiska IP-adressen `192.168.100.5` och undernätet `255.255.255.0`.

    ![Data Box Heavy ansluter till lokalt webbgränssnitt](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Anslut till det lokala webbgränssnittet på enheten på följande URL-adress: `http://192.168.100.10`. Klicka på **Avancerat** och sedan på **Fortsätt till 192.168.100.10 (osäker)** .
5. En **inloggningssida** visas för det lokala webbgränssnittet.
    
    - Ett av nodens serienummer på den här sidan matchar både portalens användargränssnitt och det lokala webbgränssnittet. Observera mappningen mellan nodnumret och serienumret. Det finns två noder och två enhetsserienummer på portalen. Den här mappningen gör det enklare att se vilken nod som matchar vilket serienummer.
    - Enheten är låst i det här läget.
    - Ange administratörslösenordet för enheten som du fick i föregående steg för att logga in på enheten. Klicka på **Logga in**.

    ![Logga in i det lokala webbgränssnittet för Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. På instrumentpanelen kontrollerar du att nätverksgränssnitten är konfigurerade. Det finns fyra nätverksgränssnitt på enhetsnoden, två 1 Gbit/s- och två 40 Gbit/s-gränssnitt. Det ena 1 Gbit/s-gränssnittet är ett hanteringsgränssnitt och kan därför inte konfigureras av användaren. De återstående tre nätverksgränssnitten är dedikerade till data och kan konfigureras av användaren.

- Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt.
- Om DHCP inte har aktiverats går du till Ställ in nätverksgränssnitt och tilldelar statiska IP-adresser om det behövs.

    ![Data Box Heavy-instrumentpanel – nod 1](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Konfigurera den andra noden

Följ stegen som beskrivs i [Konfigurera den första noden](#configure-first-node) för den andra noden på enheten.

![Data Box Heavy-instrumentpanel – nod 2](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

När enhetskonfigurationen är klar kan du ansluta till enhetsresurserna och kopiera data från datorn till enheten.

## <a name="next-steps"></a>Nästa steg

I den här självstudien om Azure Data Box Heavy har du bland annat lärt dig att:

> [!div class="checklist"]
> * Koppla in Data Box Heavy
> * Ansluta till Data Box Heavy

Gå vidare till nästa självstudiekurs och lär dig hur du kopierar data på en Data Box Heavy.

> [!div class="nextstepaction"]
> [Kopiera data till Azure Data Box](./data-box-heavy-deploy-copy-data.md)

::: zone-end
