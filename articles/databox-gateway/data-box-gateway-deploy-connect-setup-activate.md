---
title: Ansluta till, konfigurera och aktivera Azure Data Box Gateway i Azure Portal
description: Den tredje själv studie kursen för att distribuera Data Box Gateway instruerar dig att ansluta, konfigurera och aktivera den virtuella enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: cb6ef6ac25c4afa72160ba437e0ea3b5492cfd93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96583691"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Självstudie: Ansluta, konfigurera och aktivera Azure Data Box Gateway

## <a name="introduction"></a>Introduktion

I den här självstudien beskrivs hur du ansluter till, konfigurerar och aktiverar din Data Box Gateway-enhet med hjälp av det lokala webb gränssnittet. 

Installations-och aktiverings processen kan ta cirka 10 minuter att slutföra. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ansluta till en virtuell enhet
> * Konfigurera och aktivera den virtuella enheten

## <a name="prerequisites"></a>Förutsättningar

Innan du konfigurerar och konfigurerar Data Box Gateway bör du kontrol lera att:

* Du har etablerat en virtuell enhet och erhållit en ansluten URL till den enligt beskrivningen i [tillhandahålla en data Box gateway i Hyper-V](data-box-gateway-deploy-provision-hyperv.md) eller [etablera en data Box gateway i VMware](data-box-gateway-deploy-provision-vmware.md).
* Du har aktiverings nyckeln från den Data Box Gateway tjänst som du skapade för att hantera Data Box Gateway enheter. Mer information finns i [förbereda för distribution av Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Anslut till konfiguration av lokalt webb gränssnitt 

1. Öppna ett webbläsarfönster och öppna det lokala webb gränssnittet för enheten på:
   
   https: \/ /IP-Address-of-Network-Interface
   
   Använd anslutnings-URL: en som anges i föregående självstudie. Du ser ett fel eller en varning om ett problem med webbplatsens säkerhetscertifikat.

2. Välj **Fortsätt till den här webb sidan**. De här stegen kan variera beroende på vilken webbläsare du använder.
   
    ![Webbplats säkerhets certifikat fel meddelande](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Logga in i den virtuella enhetens webbgränssnitt. Standard lösen ordet är *Password1*. 
   
    ![Logga in på lokalt webb gränssnitt](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. Ändra enhetens lösen ord vid prompten. Det nya lösenordet måste innehålla mellan 8 och 16 tecken. Det måste innehålla 3 av följande: versaler, gemener, siffror och specialtecken.

    ![Ändra enhetens lösenord](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Du är nu på **instrument panelen** för din enhet.

## <a name="set-up-and-activate-the-virtual-device"></a>Konfigurera och aktivera den virtuella enheten
 
På instrument panelen visas de olika inställningar som krävs för att konfigurera och registrera den virtuella enheten med tjänsten Data Box Gateway. **Enhets namnet**, **nätverks inställningarna**, **webbproxyinställningarna** och **tids inställningarna** är valfria. De enda nödvändiga inställningarna är **moln inställningar**.
   
![Sidan instrument panel för lokalt webb gränssnitt](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. I den vänstra rutan väljer du **enhets namn** och anger sedan ett eget namn för din enhet. Det egna namnet måste innehålla mellan 1 och 15 tecken och innehålla bokstäver, siffror och bindestreck. 

    ![Sidan enhets namn för lokalt webb gränssnitt](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. Valfritt Välj **nätverks inställningar** i den vänstra rutan och konfigurera sedan inställningarna. På den virtuella enheten visas minst ett nätverks gränssnitt, beroende på hur många som du har konfigurerat i den underliggande virtuella datorn. Sidan **nätverks inställningar** för en virtuell enhet med ett nätverks gränssnitt aktiverat visas nedan.
    
    ![Sidan nätverks inställningar för lokalt webb gränssnitt](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Tänk på följande när du konfigurerar nätverks inställningar:

    - Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt. IP-adress, undernät, gateway och DNS tilldelas automatiskt.
    - Om DHCP inte är aktive rad kan du tilldela statiska IP-adresser om det behövs.
    - Du kan konfigurera nätverks gränssnittet som IPv4.

     >[!NOTE] 
     > Vi rekommenderar att du inte byter lokal IP-adress för nätverks gränssnittet från statisk till DHCP, om du inte har en annan IP-adress för att ansluta till enheten. Om du använder ett nätverksgränssnitt och växlar till DHCP finns det inget sätt att fastställa DHCP-adressen. Om du vill byta till en DHCP-adress ska du vänta med ändringen tills enheten har registrerats i tjänsten. Du kan sedan Visa IP-adresserna för alla nätverkskort i **enhets egenskaperna** i Azure Portal för din tjänst.

3. (Valfritt) konfigurera din webbproxyserver. Även om webbproxy-konfigurationen är valfri, kan du bara konfigurera den på den här sidan om du använder en webbproxy.
   
   ![Sidan Web Proxy-inställningar för lokalt webb gränssnitt](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   På sidan **webbproxy** gör du följande:
   
   1. I rutan **webbproxy-URL** anger du webb adressen i följande format: `http://&lt;host-IP address or FQDN&gt;:Port number` . Du kan inte använda HTTPS-adresser.
   2. Välj **Ingen** eller **NTLM** under **Autentisering**.
   3. Om du använder autentisering anger du ett **användar namn** och **lösen ord**.
   4. Om du vill validera och tillämpa de konfigurerade webbproxyinställningarna väljer du **tillämpa**.

   > [!NOTE]
   > PAC-filer (Proxy-Auto config) stöds inte. En PAC-fil definierar hur webbläsare och andra användar agenter automatiskt kan välja rätt proxyserver (åtkomst metod) för att hämta en viss URL.
   > Proxyservrar som försöker avlyssna och läsa all trafik (och sedan signera om allting med sin egen certifiering) är inte kompatibla eftersom proxyns certifikat inte är betrott.
   > Vanligt vis fungerar transparenta proxyservrar bra med Azure Data Box Gateway.

4. Valfritt I det vänstra fönstret väljer du **tids inställningar** och konfigurerar sedan tids zonen och de primära och sekundära NTP-servrarna för enheten. 

    NTP-servrar krävs eftersom din enhet måste synkronisera tid så att den kan autentiseras med dina moln tjänst leverantörer.
    
    ![Sidan tids inställningar för lokalt webb gränssnitt](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    På sidan **tids inställningar** gör du följande:
    
    1. I list rutan **tids zon** väljer du den tidszon som motsvarar den geografiska plats där enheten distribueras.
        Standard tids zonen för enheten är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.

    2. Ange en **primär NTP-server** för din enhet eller acceptera standardvärdet `time.windows.com` .   
        Kontrollera att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till Internet.

    3. Alternativt kan du ange en sekundär server för enheten i rutan **sekundär NTP-server** .

    4. Om du vill validera och tillämpa de konfigurerade tids inställningarna väljer du **tillämpa**.

6. I det vänstra fönstret väljer du **moln inställningar** och aktiverar sedan enheten med data Box Gateway tjänsten i Azure Portal.
    
    1. I rutan **aktiverings nyckel** anger du **aktiverings nyckeln** som du fick i [hämta aktiverings nyckeln](data-box-gateway-deploy-prep.md#get-the-activation-key) för data Box Gateway.

    2. Välj **Aktivera**.
       
         ![Sidan Cloud Settings för lokalt webb gränssnitt](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Enheten är aktive rad och viktiga uppdateringar, om det är tillgängligt, tillämpas automatiskt. Du ser ett meddelande om detta. Övervaka uppdaterings förloppet via Azure Portal.

        ![Lokalt webb gränssnitt "moln inställningar" sida 2](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **Dialog rutan innehåller också en återställnings nyckel som du bör kopiera och Spara på en säker plats. Den här nyckeln används för att återställa dina data i händelse av att enheten inte kan starta.**


    4. Du kan behöva vänta några minuter medan uppdateringen slutförs. När uppdateringen är klar loggar du in på enheten. Sidan med **moln inställningar** uppdateras för att indikera att enheten har Aktiver ATS.

        ![Sidan för lokalt webb gränssnitt "moln inställningar" har uppdaterats](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

Enhets installationen är klar. Nu kan du lägga till resurser på enheten.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Ansluta till en virtuell enhet
> * Konfigurera och aktivera den virtuella enheten

Information om hur du överför data med Data Box Gateway finns i:

> [!div class="nextstepaction"]
> [Överför data med data Box Gateway](./data-box-gateway-deploy-add-shares.md).
