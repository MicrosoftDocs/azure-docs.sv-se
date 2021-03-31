---
title: StorSimple-administration för virtuella webb gränssnitt | Microsoft Docs
description: Beskriver hur du utför grundläggande enhets administrations uppgifter via StorSimple Virtual Array Web UI.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 6bea9c9b7807ffb32b6071c968d3186965477009
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000937"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Använd webb gränssnittet för att administrera din virtuella StorSimple-matris
![konfigurations process flöde](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Översikt
Självstudierna i den här artikeln gäller för den Microsoft Azure StorSimple virtuella matrisen (även kallat den lokala virtuella enheten StorSimple) som kör mars 2016 allmän tillgänglighet (GA). I den här artikeln beskrivs några komplexa arbets flöden och hanterings uppgifter som kan utföras på den virtuella StorSimple-matrisen. Du kan hantera den virtuella StorSimple-matrisen med hjälp av användar gränssnittet för StorSimple Manager tjänsten (kallas för portalens gränssnitt) och det lokala webb gränssnittet för enheten. Den här artikeln fokuserar på de uppgifter som du kan utföra med hjälp av webb gränssnittet.

Den här artikeln innehåller följande självstudier:

* Hämta krypterings nyckeln för tjänst data
* Felsöka konfigurations fel för webb gränssnitt
* Generera ett logg paket
* Stänga av eller starta om enheten

## <a name="get-the-service-data-encryption-key"></a>Hämta krypterings nyckeln för tjänst data
En krypterings nyckel för tjänst data genereras när du registrerar din första enhet med tjänsten StorSimple Manager. Den här nyckeln krävs sedan med tjänst registrerings nyckeln för att registrera ytterligare enheter med tjänsten StorSimple Manager.

Om du har felplacerat krypterings nyckeln för tjänst data och behöver hämta den utför du följande steg i det lokala webb gränssnittet för den enhet som är registrerad i tjänsten.

#### <a name="to-get-the-service-data-encryption-key"></a>Hämta krypterings nyckeln för tjänst data
1. Anslut till det lokala webb gränssnittet. Gå till inställningar för **konfigurations**  >  **moln**.
2. Klicka på **Hämta krypterings nyckel för tjänst data** längst ned på sidan. En nyckel visas. Kopiera och spara den här nyckeln.
   
    ![Hämta krypterings nyckel för tjänst data 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Felsöka konfigurations fel för webb gränssnitt
I vissa fall när du konfigurerar enheten via det lokala webb gränssnittet kan du stöta på fel. För att diagnostisera och felsöka sådana fel kan du köra diagnostiktest.

#### <a name="to-run-the-diagnostic-tests"></a>Köra de diagnostiska testerna
1. I det lokala webb gränssnittet går du till **Felsöka**  >  **diagnostiska tester**.
   
    ![Kör diagnostik 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. Klicka på **Kör diagnostiska tester** längst ned på sidan. Detta kommer att initiera tester för att diagnostisera eventuella problem med nätverks-, enhets-, webbproxy-, tids-eller moln inställningar. Du får ett meddelande om att enheten kör tester.
3. När testerna har slutförts visas resultaten. I följande exempel visas resultatet av diagnostiska tester. Observera att webbproxy-inställningarna inte har kon figurer ATS på den här enheten, och därför kördes inte webbproxy-testet. Alla andra tester för nätverks inställningar, DNS-server och tids inställningar lyckades.
   
    ![Kör diagnostik 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Generera ett logg paket
Ett logg paket består av alla relevanta loggar som kan hjälpa Microsoft Support med fel sökning av eventuella enhets problem. I den här versionen kan ett logg paket genereras via det lokala webb gränssnittet.

#### <a name="to-generate-the-log-package"></a>Så här genererar du logg paketet
1. I det lokala webb gränssnittet går du till **Felsöka**  >  **system loggar**.
   
    ![Generera logg paket 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. Klicka på **Skapa logg paket** längst ned på sidan. Ett paket av system loggarna kommer att skapas. Detta tar några minuter.
   
    ![Generera logg paket 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Du får ett meddelande när paketet har skapats och sidan uppdateras för att ange den tid och det datum då paketet skapades.
   
    ![Generera logg paket 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Klicka på **Hämta logg paket**. Ett zippat paket kommer att laddas ned i systemet.
   
    ![Generera logg paket 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Du kan zippa upp det hämtade logg paketet och se systemloggfilerna.

## <a name="shut-down-and-restart-your-device"></a>Stäng av och starta om enheten
Du kan stänga av eller starta om den virtuella enheten med hjälp av det lokala webbgränssnittet. Du rekommenderas att innan du startar om, ta volymerna eller resurserna offline på värden och sedan på enheten. Detta minimerar risken för skadade data. 

#### <a name="to-shut-down-your-virtual-device"></a>Stänga av den virtuella enheten
1. I det lokala webb gränssnittet går du till **underhålls**  >  **energi inställningar**.
2. Klicka på **Avsluta** längst ned på sidan.
   
    ![enhets avstängning 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. En varning visas om att en avstängning av enheten kommer att avbryta eventuella i/o som pågår, vilket resulterar i en stillestånds tid. Klicka på kryssikonen ![kryssikon](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Varning vid avstängning av enhet](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Du får ett meddelande om att avstängningen har påbörjats.
   
    ![enheten har stängts av](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Enheten kommer nu att stängas av. Om du vill starta enheten måste du göra det via Hyper-V Manager.

#### <a name="to-restart-your-virtual-device"></a>Starta om den virtuella enheten
1. I det lokala webb gränssnittet går du till **underhålls**  >  **energi inställningar**.
2. Klicka på **starta om** längst ned på sidan.
   
    ![omstart av enhet](./media/storsimple-ova-web-ui-admin/image36.png)
3. En varning visas om att omstart av enheten kommer att avbryta alla IOs-enheter som pågår, vilket resulterar i en stillestånds tid. Klicka på kryssikonen ![kryssikon](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Starta om varning](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Du får ett meddelande om att omstarten har startats.
   
    ![omstart initierad](./media/storsimple-ova-web-ui-admin/image39.png)
   
    När omstarten pågår går anslutningen till användar gränssnittet förlorade. Du kan övervaka omstarten genom att regelbundet uppdatera användar gränssnittet. Du kan också övervaka statusen för omstart av enheten via Hyper-V Manager.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [använder tjänsten StorSimple Manager för att hantera enheten](storsimple-virtual-array-manager-service-administration.md).

