---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ee92e145acd981bb8c84b43986b90306b994c262
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95554013"
---
#### <a name="to-configure-and-register-the-device"></a>Konfigurera och registrera enheten

1. Gå in i Windows PowerShell-gränssnittet på din StorSimple-enhets seriekonsol. Mer instruktioner finns i [Använd PuTTY för att ansluta till enhetens seriekonsol](#use-putty-to-connect-to-the-device-serial-console). **Se till att följa proceduren exakt för att du ska få åtkomst till konsolen.**

2. Öppna en kommandotolk genom att trycka på **Retur** i sessionen som öppnas.

3. Du kommer att uppmanas att välja det språk som du vill ställa in för din enhet. Ange språket och tryck på **Retur**.

4. I seriemenyn för konsolen som visas väljer du alternativ 1, **Logga in med fullständig åtkomst**.
     Slutför steg 5–12 för att konfigurera de minsta nödvändiga nätverksinställningarna för din enhet. **De här konfigurationsstegen behöver genomföras på den aktiva styrenheten för enheten.** Menyn för seriekonsolen indikerar status för styrenheten i banderollmeddelandet. Om du inte är ansluten till den aktiva styrenheten, kopplar du från och ansluter till den aktiva styrenheten.

5. Ange ditt lösenord i kommandotolken. Enheten standardlösenord är **Password1**.

6. Ange följande kommando: `Invoke-HcsSetupWizard`.

7. En installationsguide kommer visas och hjälpa dig att konfigurera nätverksinställningarna för enheten. Ange följande information:
   
   * IP-adress för DATA 0-nätverksgränssnittet
   * Nätmask
   * Gateway
   * IP-adress för primär DNS-server

   Ett exempel på de utdata som returneras visas nedan.

    ```
        ---------------------------------------------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: 8100-SHX0991003G44MT
        Software Version: 6.3.9600.17759
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Active
        ---------------------------------------------------------------

        Your device needs to be registered with the Microsoft Azure StorSimple Manager service. Please run 'Invoke-HcsSetupWizard' to set up your device.

        Controller0>Invoke-HcsSetupWizard

        Which IP address family would you like to configure on interface Data0?
        [4] IPv4 [6] IPv6 [B] Both (Default is "4"): 4

        Data0 IPv4 address:10.111.111.00
        Data0 IPv4 subnet: 255.255.252.0
        Data0 IPv4 gateway: 10.111.111.11

        IPv4 primary DNS server [10.222.118.154]:10.222.222.111
    ```

    <br>
    I föregående exempelutdata ser du att nätverksinställningarna verifieras efter varje steg i processen.

     > [!NOTE]
     > Du kan behöva vänta några minuter för att nätmask- och DNS-inställningarna ska appliceras. Om du får ett felmeddelande om att "kontrollera nätverksanslutningen till Data 0", kontrollera den fysiska nätverksanslutningen på DATA 0-nätverksgränssnittet för din aktiva styrenhet.

8. (Valfritt) konfigurera din webbproxyserver. Även om webbproxykonfigurationen är valfri, **var medveten om att om du använder en webbproxy så kan du bara konfigurera den här**. Mer information finns i [Konfigurera en webbproxy för din enhet](../articles/storsimple/storsimple-8000-configure-web-proxy.md).
9. Konfigurera en primär NTP-server för din enhet. NTP-servrar krävs, eftersom din enhet måste synkronisera tiden så att den kan autentisera med molntjänstleverantören. Kontrollera att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till Internet. Om det inte är möjligt anger du en intern NTP-server.

    Ett exempel på utdata visas nedan.

    ```
        Would you like to configure a web proxy?
        [Y] Yes [N] No (Default is "N"):N

        Primary NTP server [time.windows.com]:time.windows.com

    ```

10. Av säkerhetsskäl upphör enhetens administratörslösenord att gälla efter den första sessionen. Du måste ändra lösenordet nu. Ange ett administratörslösenord för enheten när du ombes göra det. Ett giltigt enhetsadministratörslösenord för enheten måste vara mellan 8 och 15 tecken. Lösenordet måste innehålla tre av följande: gemener, versaler, siffror och specialtecken.

    ```
        The device administrator password must be between 8 and 15 characters. The password must contain a combination of uppercase letters, lowercase letters, numbers and special characters.
        Administrator Password:********
        Confirm Administrator Password:********
    ```
11. I det sista steget i installationsguiden registreras din enhet med StorSimple Device Manager-tjänsten. För det här behöver du tjänstens registreringsnyckel som du fick i steg 2. Efter att du angett registreringsnyckeln kan du behöva vänta 2-3 minuter innan enheten är registrerad.
    
    > [!NOTE]
    > Du kan trycka på Ctrl + C när som helst för att avsluta installationsguiden. Om du har angett alla nätverksinställningar (IP-adress för Data 0, nätmask och Gateway), kommer informationen att sparas.
    
    Ett exempel på utdata visas nedan.

    ```
        The service registration key is available in the StorSimple Manager service.
        Enter service registration key:**************************************
        Device registration is in progress. Please wait.

    ```

12. När enheten är registrerad visas en krypteringsnyckel för tjänstdata. Kopiera den här nyckeln och spara den på säker plats. **Den här nyckeln krävs tillsammans med tjänstregistreringsnyckeln för att registrera ytterligare enheter med StorSimple Device Manager-tjänsten.** Referera till [StorSimple-säkerhet](../articles/storsimple/storsimple-8000-security.md) för ytterligare information om den här nyckeln.
    
    ![StorSimple registrera enhet 7](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup1.png)
    
    > [!NOTE]
    > Om du vill kopiera text från seriekonsolfönstret, markerar du bara texten. Sedan ska du kunna klistra in den i urklipp eller valfri textredigerare. ANVÄND INTE Ctrl + C för att kopiera krypteringsnyckeln för tjänstdata. Ctrl + C avslutar installationsguiden. Som ett resultat, kommer enhetens administratörslösenord inte att ändras och enheten kommer återgå till standardlösenordet.
    
13. Avsluta seriekonsolen.
14. Gå tillbaka till Azure Portal och utför följande steg:
    
    1. Gå till StorSimple Device Manager-tjänsten.
    2. Klicka på **Enheter**.
    3. I listan med enheter kontrollerar du att enheten har anslutit till tjänsten genom att leta upp dess status. Enhetens status bör vara **Redo för installation**.
       
        ![StorSimple-enhetssidan](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup2.png)
       
        Du kan behöva vänta några minuter innan enhetens status ändras till **Redo för installation**.
       
        Om enheten inte visas i listan kontrollerar du att ditt brandväggsnätverk är konfigurerat enligt [nätverkskraven för din StorSimple-enhet](../articles/storsimple/storsimple-8000-system-requirements.md). Kontrollera att port 9354 är öppen för utgående kommunikation eftersom den används av Service Bus för kommunikation mellan StorSimple Device Manager-tjänsten och enheten.