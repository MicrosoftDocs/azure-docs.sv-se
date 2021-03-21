---
title: Hantera enheter med StorSimple Snapshot Manager | Microsoft Docs
description: Beskriver hur du använder snapin-modulen StorSimple Snapshot Manager MMC för att ansluta och hantera StorSimple-enheter.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: dbb7e835d110bcb6cb81fd5425a2aafbb578e692
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96022980"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Använda StorSimple Snapshot Manager för att ansluta och hantera StorSimple-enheter
## <a name="overview"></a>Översikt
Du kan använda noder i fönstret StorSimple Snapshot Manager **scope** för att kontrol lera importerade StorSimple enhets data och uppdatera anslutna lagrings enheter. När du klickar på noden **enheter** kan du dessutom se en lista över anslutna enheter och motsvarande statusinformation i **resultat** fönstret.

![Anslutna enheter](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Bild 1: StorSimple Snapshot Manager ansluten enhet** 

Beroende på dina **visnings** val visar **resultat** fönstret följande information om varje enhet. (Mer information om hur du konfigurerar en vy finns i [menyn Visa](storsimple-use-snapshot-manager.md#view-menu).

| Resultat kolumn | Beskrivning |
|:--- |:--- |
| Name |Namnet på enheten enligt konfigurationen i den klassiska Azure-portalen |
| Modell |Enhetens modell nummer |
| Version |Versionen av program varan som är installerad på enheten |
| Status |Om enheten är tillgänglig |
| Senast synkroniserad |Datum och tid när enheten senast synkroniserades |
| Serie nummer |Enhetens serie nummer |

Om du högerklickar på noden **enheter** i fönstret **omfång** kan du välja bland följande åtgärder:

* Lägga till eller ersätta en enhet
* Anslut en enhet och verifiera importen
* Uppdatera anslutna enheter

Om du klickar på noden **enheter** och högerklickar på ett enhets namn i **resultat** fönstret, kan du välja bland följande åtgärder:

* Autentisera en enhet
* Visa enhetsinformation
* Uppdatera en enhet
* Ta bort en enhets konfiguration
* Ändra ett enhets lösen ord

> [!NOTE]
> Alla dessa åtgärder är också tillgängliga i **Åtgärds** fönstret.


I den här självstudien beskrivs hur du använder StorSimple Snapshot Manager för att ansluta och hantera enheter och utföra följande uppgifter:

* Lägga till eller ersätta en enhet
* Anslut en enhet och verifiera importen
* Uppdatera anslutna enheter
* Autentisera en enhet
* Visa enhetsinformation
* Uppdatera en enskild enhet
* Ta bort en enhets konfiguration
* Ändra ett förfallet enhets lösen ord
* Ersätta en misslyckad enhet

> [!NOTE]
> Allmän information om hur du använder StorSimple Snapshot Manager-gränssnittet finns i [StorSimple Snapshot Manager User Interface](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Lägga till eller ersätta en enhet
Använd följande procedur för att lägga till eller ersätta en StorSimple-enhet.

#### <a name="to-add-or-replace-a-device"></a>Lägga till eller ersätta en enhet
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** högerklickar du på noden **enheter** och klickar sedan på **Konfigurera en enhet**. Dialog rutan **Konfigurera en enhet** visas.
   
    ![Konfigurera en StorSimple-enhet](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. I list rutan **enhet** väljer du IP-adressen för enheten eller den virtuella enheten. 
4. I text rutan **lösen ord** skriver du StorSimple Snapshot Manager lösen ordet som du skapade för enheten i den klassiska Azure-portalen. Klicka på **OK**. StorSimple Snapshot Manager söker efter den enhet som du har identifierat. 
   
   * Om enheten är tillgänglig StorSimple Snapshot Manager lägga till en anslutning.
   * Om enheten inte är tillgänglig av någon anledning returnerar StorSimple Snapshot Manager ett fel meddelande. Stäng fel meddelandet genom att klicka på **OK** och klicka sedan på **Avbryt** för att stänga dialog rutan **Konfigurera en enhet** .

## <a name="connect-a-device-and-verify-imports"></a>Anslut en enhet och verifiera importen
Använd följande procedur för att ansluta en StorSimple-enhet och kontrol lera att alla befintliga volym grupper som har associerade säkerhets kopior importeras.

#### <a name="to-connect-a-device-and-verify-imports"></a>Så här ansluter du en enhet och verifierar importen
1. Om du vill ansluta en enhet till StorSimple Snapshot Manager följer du anvisningarna i lägga till eller ersätta en enhet. När den ansluter till en enhet svarar StorSimple Snapshot Manager så här:
   
   * Om enheten inte är tillgänglig av någon anledning returnerar StorSimple Snapshot Manager ett fel meddelande. 
   
   * Om enheten är tillgänglig StorSimple Snapshot Manager lägga till en anslutning. När du väljer enheten visas den i **resultat** fönstret och fältet status visar att enheten är **tillgänglig**. StorSimple Snapshot Manager importerar alla volym grupper som har kon figurer ATS för enheten, förutsatt att volym grupperna har associerade säkerhets kopior. Säkerhets kopierings principer importeras inte. Volym grupper som inte har tillhör ande säkerhets kopior importeras inte.
2. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
3. Högerklicka på den översta noden i fönstret **omfång** och klicka sedan på **Växla visning av importer**.
   
    ![Välj växla visning av importer](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. Dialog rutan **Växla import** visas visas med statusen för de importerade volym grupperna och säkerhets kopiorna. Klicka på **OK**.

När volym grupperna och säkerhets kopiorna har importer ATS kan du använda StorSimple-Snapshot Manager för att hantera dem, precis som du hanterar volym grupper och säkerhets kopior som du har skapat och konfigurerat med StorSimple Snapshot Manager. 

## <a name="refresh-connected-devices"></a>Uppdatera anslutna enheter
Använd följande procedur för att synkronisera anslutna StorSimple-enheter med StorSimple Snapshot Manager.

#### <a name="to-refresh-connected-devices"></a>Uppdatera anslutna enheter
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** högerklickar du på **enheter** och klickar sedan på **Uppdatera enheter**. Detta synkroniserar de anslutna enheterna med StorSimple Snapshot Manager så att du kan visa volym grupper och säkerhets kopior, inklusive eventuella nyligen tillagda tillägg. 
   
    ![Uppdatera StorSimple-enheterna](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

Åtgärden **Uppdatera enheter** hämtar alla nya volym grupper och eventuella tillhör ande säkerhets kopior från anslutna enheter. Till skillnad från åtgärden **Genomsök volymer** som är tillgänglig för noden **volymer** , återställs inte säkerhets kopierings registret av **Uppdatera enheter** .

## <a name="authenticate-a-device"></a>Autentisera en enhet
Använd följande procedur för att autentisera en StorSimple-enhet med StorSimple Snapshot Manager.

#### <a name="to-authenticate-a-device"></a>Autentisera en enhet
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** klickar du på **enheter**.
3. I **resultat** fönstret högerklickar du på namnet på enheten och klickar sedan på **autentisera**.
4. Dialog rutan **autentisera** visas. Ange enhetens lösen ord och klicka sedan på **OK**.
   
    ![Dialog rutan autentisera](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Visa enhetsinformation
Använd följande procedur om du vill visa information om en StorSimple-enhet och, om det behövs, synkronisera om enheten med StorSimple Snapshot Manager.

#### <a name="to-view-and-resynchronize-device-details"></a>Visa och synkronisera om enhets information
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** klickar du på **enheter**.
3. I **resultat** fönstret högerklickar du på namnet på enheten och klickar sedan på **information**.

4. dialog rutan **enhets information** visas. I den här rutan visas namn, modell, version, serie nummer, status, kvalificerat iSCSI-kvalificerat namn (IQN) och senaste synkronisering datum och tid.

* Synkronisera enheten genom att klicka på **Synkronisera** igen.
* Klicka på **OK** eller **Avbryt** om du vill stänga dialog rutan.
  
  ![Information om enhet](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Uppdatera en enskild enhet
Använd följande procedur för att synkronisera om en enskild StorSimple-enhet med StorSimple Snapshot Manager.

#### <a name="to-refresh-a-device"></a>Uppdatera en enhet
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager. 
2. I fönstret **omfattning** klickar du på **enheter**. 
3. I **resultat** fönstret högerklickar du på namnet på enheten och klickar sedan på **Uppdatera enhet**. Detta synkroniserar enheten med StorSimple Snapshot Manager.

## <a name="delete-a-device-configuration"></a>Ta bort en enhets konfiguration
Använd följande procedur för att ta bort en enskild StorSimple enhets konfiguration från StorSimple Snapshot Manager.

#### <a name="to-delete-a-device-configuration"></a>Ta bort en enhets konfiguration
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** klickar du på **enheter**. 
3. I **resultat** fönstret högerklickar du på namnet på enheten och klickar sedan på **ta bort**. 
4. Följande meddelande visas. Klicka på **Ja** om du vill ta bort konfigurationen eller på **Nej** om du vill avbryta borttagningen.
   
    ![Ta bort enhetskonfiguration](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Ändra ett förfallet enhets lösen ord
Du måste ange ett lösen ord för att autentisera en StorSimple-enhet med StorSimple Snapshot Manager. Du konfigurerar det här lösen ordet när du använder Windows PowerShell-gränssnittet för att konfigurera enheten. Men lösen ordet kan upphöra att gälla. I så fall kan du använda den klassiska Azure-portalen för att ändra lösen ordet. Eftersom enheten har kon figurer ATS i StorSimple Snapshot Manager innan lösen ordet upphörde måste du autentisera enheten på nytt i StorSimple Snapshot Manager.

#### <a name="to-change-the-expired-password"></a>Ändra lösen ordet som har upphört att gälla
1. Starta tjänsten StorSimple Manager i den klassiska Azure-portalen.
2. Klicka på **enheter**  >  **Konfigurera** för enheten.
3. Rulla ned till avsnittet StorSimple Snapshot Manager. Ange ett lösen ord som är 14-15 tecken. Se till att lösen ordet innehåller en blandning av versaler, gemener, siffror och specialtecken.
4. Bekräfta genom att ange lösen ordet igen.
5. Klicka på **Spara** längst ned på sidan.

#### <a name="to-re-authenticate-the-device"></a>Så här autentiserar du enheten på nytt
1. Starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** klickar du på **enheter**. En lista över konfigurerade enheter visas i **resultat** fönstret.
3. Markera enheten, högerklicka på och klicka sedan på **autentisera**.
4. Ange det nya lösen ordet i fönstret **autentisera** .
5. Markera enheten, högerklicka på och välj **Uppdatera enhet**. Detta synkroniserar enheten med StorSimple Snapshot Manager.

## <a name="replace-a-failed-device"></a>Ersätta en misslyckad enhet
Om en StorSimple-enhet Miss lyckas och har ersatts av en växlings enhet (redundans) kan du använda följande steg för att ansluta till den nya enheten och Visa tillhör ande säkerhets kopior.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Ansluta till en ny enhet efter redundans
1. Konfigurera om iSCSI-anslutningen till den nya enheten. Anvisningar finns i "steg 7: montera, initiera och formatera en volym" i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Om den nya StorSimple-enheten har samma IP-adress som den gamla, kanske du kan ansluta den gamla konfigurationen.


1. Stoppa Microsoft StorSimple Management Service:
   
   1. Starta Serverhanteraren.
   2. På instrument panelen för Serverhanteraren väljer du **tjänster** på **verktyg** -menyn.
   3. I fönstret **tjänster** väljer du **hanterings tjänsten för Microsoft-StorSimple**.
   4. Klicka på **stoppa tjänsten** under **Microsoft StorSimple Management Service** i den högra rutan.
2. Ta bort konfigurations informationen som är relaterad till den gamla enheten:
   
   1. Bläddra till C:\ProgramData\Microsoft\StorSimple\BACatalog. i Utforskaren
   2. Ta bort filerna i mappen BACatalog
3. Starta om Microsoft StorSimple Management-tjänsten:
   
   1. På instrument panelen för Serverhanteraren väljer du **tjänster** på **verktyg** -menyn.
   2. I fönstret **tjänster** väljer du **hanterings tjänsten för Microsoft-StorSimple**.
   3. Klicka på **starta om tjänsten** under **Microsoft StorSimple Management Service** i den högra rutan.
4. Starta StorSimple Snapshot Manager.
5. Konfigurera den nya StorSimple-enheten genom att slutföra stegen i steg 2: ansluta en StorSimple-enhet i [distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
6. Högerklicka på noden på den översta nivån i fönstret **omfång** (StorSimple Snapshot Manager i exemplet) och klicka sedan på **Växla import visning**. 
7. Ett meddelande visas när de importerade volym grupperna och säkerhets kopiorna visas i StorSimple Snapshot Manager. Klicka på **OK**.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använder StorSimple Snapshot Manager för att visa och hantera volymer](storsimple-snapshot-manager-manage-volumes.md).

