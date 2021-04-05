---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 338332eb90fda9afe7655f9f2ad19ed726419b6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95554074"
---
### <a name="to-configure-and-register-the-device"></a>Konfigurera och registrera enheten
1. Gå in i Windows PowerShell-gränssnittet på din StorSimple-enhets seriekonsol. Mer instruktioner finns i [Använd PuTTY för att ansluta till enhetens seriekonsol](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console). **Se till att följa proceduren exakt för att du ska få åtkomst till konsolen.**
2. Öppna en kommandotolk genom att trycka på **Retur** i sessionen som öppnas.
3. Du kommer att uppmanas att välja det språk som du vill ställa in för din enhet. Ange språket och tryck på **Retur**.
   
    ![StorSimple konfigurera och registrera enhet 1](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. I seriemenyn för konsolen som visas väljer du alternativ 1, **Logga in med fullständig åtkomst**.
   
    ![StorSimple registrera enhet 2](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. Utför följande steg för att konfigurera minimi kraven för nätverks inställningar för enheten.
   
   > [!IMPORTANT]
   > De här konfigurationsstegen behöver genomföras på den aktiva styrenheten för enheten. Menyn för seriekonsolen indikerar status för styrenheten i banderollmeddelandet. Om du inte är ansluten till den aktiva kontroll enheten kopplar du från och ansluter sedan till den aktiva kontroll enheten.
   
   1. Ange ditt lösenord i kommandotolken. Enheten standardlösenord är **Password1**.
   2. Ange följande kommando:
      
        `Invoke-HcsSetupWizard`
   3. En installationsguide kommer visas och hjälpa dig att konfigurera nätverksinställningarna för enheten. Ange följande information:
      
      * IP-adress för nätverks gränssnittet för DATA 0
      * Nätmask
      * Gateway
      * IP-adress för primär DNS-server
      * IP-adress för primär NTP-server
      
      > [!NOTE]
      > Du kan behöva vänta några minuter på att nät masken och de DNS-inställningar som ska tillämpas.
    
   4. Du kan också konfigurera webbproxyservern.
      
      > [!IMPORTANT]
      > Även om webbproxykonfigurationen är valfri, var medveten om att om du använder en webbproxy så kan du bara konfigurera den här. Mer information finns i [Konfigurera en webbproxy för din enhet](../articles/storsimple/storsimple-8000-configure-web-proxy.md).
     
6. Tryck på CTRL + C om du vill avsluta installations guiden.
8. Kör följande cmdlet för att peka enheten till Microsoft Azure Government portal (eftersom den pekar på den klassiska Azure-portalen som standard). Detta kommer att starta om båda styrenheterna. Vi rekommenderar att du använder två SparaTillFil-sessioner för att samtidigt ansluta till båda styrenheterna så att du kan se när varje kontroll enhet startas om.
   
    `Set-CloudPlatform -AzureGovt_US`
   
   Ett bekräftelse meddelande visas. Acceptera standardvärdet (**Y**).
9. Kör följande cmdlet för att återuppta installationen:
   
    `Invoke-HcsSetupWizard`
   
    ![Återuppta installations guiden](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
10. Godkänn nätverks inställningarna. Ett verifierings meddelande visas när du har godkänt varje inställning.
11. Av säkerhetsskäl upphör enhetens administratörslösenord att gälla efter den första sessionen. Du måste ändra lösenordet nu. Ange ett administratörslösenord för enheten när du ombes göra det. Ett giltigt enhetsadministratörslösenord för enheten måste vara mellan 8 och 15 tecken. Lösenordet måste innehålla tre av följande: gemener, versaler, siffror och specialtecken.
    
    <br/>![StorSimple registrera enhet 5](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. I det sista steget i installationsguiden registreras din enhet med StorSimple Device Manager-tjänsten. För detta behöver du tjänst registrerings nyckeln som du hämtade i [steg 2: Hämta tjänst registrerings nyckeln](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key). Efter att du angett registreringsnyckeln kan du behöva vänta 2-3 minuter innan enheten är registrerad.
    
    > [!NOTE]
    > Du kan trycka på Ctrl + C när som helst för att avsluta installationsguiden. Om du har angett alla nätverksinställningar (IP-adress för Data 0, nätmask och Gateway), kommer informationen att sparas.
    
    ![StorSimple registrerings förlopp](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. När enheten är registrerad visas en krypteringsnyckel för tjänstdata. Kopiera den här nyckeln och spara den på säker plats. **Den här nyckeln krävs med tjänst registrerings nyckeln för att registrera ytterligare enheter med StorSimple Enhetshanteraren-tjänsten.** Referera till [StorSimple-säkerhet](../articles/storsimple/storsimple-8000-security.md) för ytterligare information om den här nyckeln.
    
    ![StorSimple registrera enhet 7](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)
    > [!IMPORTANT]
    > Om du vill kopiera text från seriekonsolfönstret, markerar du bara texten. Sedan ska du kunna klistra in den i urklipp eller valfri textredigerare.
    > 
    > Använd inte **CTRL + C** för att kopiera krypterings nyckeln för tjänst data. Genom att använda **CTRL + C** avslutar du installations guiden. Som ett resultat, kommer enhetens administratörslösenord inte att ändras och enheten kommer återgå till standardlösenordet.
    
14. Avsluta seriekonsolen.
15. Gå tillbaka till Azure Government Portal och utför följande steg:
    
    1. Gå till StorSimple Device Manager-tjänsten.
    2. Klicka på **Enheter**. Identifiera den enhet som du distribuerar i listan över enheter. Kontrol lera att enheten har lyckats ansluta till tjänsten genom att leta upp statusen. Enhetens status ska vara **Online**.
            
        Om enhetens status är **Offline**, väntar du några minuter på att enheten ska ansluta.
       
        Om enheten fortfarande är offline efter några minuter, måste du kontrollera att ditt brandväggsnätverk har konfigurerats enligt beskrivningen i [nätverkskraven för din StorSimple-enhet](../articles/storsimple/storsimple-8000-system-requirements.md).
       
        Kontrollera att port 9354 är öppen för utgående kommunikation eftersom den används av Service Bus för kommunikation mellan StorSimple Device Manager-tjänsten och enheten.