---
title: Självstudie för att konfigurera nätverks inställningar för Azure Stack Edge Mini R-enhet i Azure Portal
description: Självstudie för att distribuera Azure Stack Edge Mini R instruerar dig att konfigurera nätverks-, beräknings-och webbproxyinställningar för den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
ms.openlocfilehash: 34a11679626653afd04b0cd17c77188cbc995308
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061731"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-mini-r"></a>Självstudie: Konfigurera nätverk för Azure Stack Edge Mini R

I den här självstudien beskrivs hur du konfigurerar nätverk för din Azure Stack Edge Mini R-enhet med en onboard-GPU med hjälp av det lokala webb gränssnittet.

Anslutnings processen kan ta ungefär 20 minuter att slutföra.

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Förutsättningar
> * Konfigurera nätverk
> * Aktivera beräknings nätverk
> * Konfigurera webbproxy


## <a name="prerequisites"></a>Förutsättningar

Innan du konfigurerar och konfigurerar din Azure Stack Edge Mini R-enhet ser du till att:

* Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge Mini R](azure-stack-edge-gpu-deploy-install.md).
* Du har anslutit till det lokala webb gränssnittet på enheten som beskrivs i [Anslut till Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-connect.md)


## <a name="configure-network"></a>Konfigurera nätverk

Sidan **Kom igång** visar de olika inställningar som krävs för att konfigurera och registrera den fysiska enheten med Azure Stack Edge-tjänsten. 

Följ dessa steg om du vill konfigurera nätverket för enheten.

1. Gå till sidan **Kom igång** i enhetens lokala webb gränssnitt. 

2. Om det krävs en uppdatering på noll dagar kan du göra det här genom att konfigurera en dataport med en tråd bunden anslutning. Mer information om hur du konfigurerar en kabelansluten anslutning för den här enheten finns i [enhetens kabel](azure-stack-edge-mini-r-deploy-install.md#cable-the-device). När uppdateringen är klar kan du ta bort den kabelanslutna anslutningen.

3. Skapa certifikat för Wi-Fi och signerings kedja. Både signerings kedjan och Wi-Fi certifikat måste vara DER-format med fil namns tillägget *. cer* . Instruktioner finns i [Skapa certifikat](azure-stack-edge-gpu-manage-certificates.md).

4. I det lokala webb gränssnittet går du till **Kom igång**. Välj **certifikat** på **säkerhets** panelen och välj sedan **Konfigurera**. 

    [![Sidan certifikat för lokalt webb gränssnitt](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png#lightbox)

    1. Välj **+ Lägg till certifikat**. 
    
        [![Lokalt webb gränssnitt "certifikat" sida 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png#lightbox)

    2. Ladda upp signerings kedjan och välj **Använd**.

        ![Lokalt webb gränssnitt "certifikat" Sidan 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-2.png)

    3. Upprepa proceduren med Wi-Fi certifikatet. 

        ![Lokalt webb gränssnitt "certifikat" sidan 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-4.png)

    4. De nya certifikaten ska visas på sidan **certifikat** . 
    
        [![Lokalt webb gränssnitt "certifikat" sida 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png#lightbox)

    5. Gå tillbaka för att **komma igång**.

3. I panelen **nätverk** väljer du **Konfigurera**.  
    
    Det finns fem nätverks gränssnitt på den fysiska enheten. PORT 1 och PORT 2 är nätverksgränssnitt på 1 Gbit/s. PORT 3 och PORT 4 är alla nätverks gränssnitt med 10 Gbit/s. Den femte porten är Wi-Fi port. 

    [![Lokalt webb gränssnitt "nätverks inställningar" sida 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)    
    
    Välj den Wi-Fi porten och konfigurera port inställningarna. 
    
    > [!IMPORTANT]
    > Vi rekommenderar starkt att du konfigurerar en statisk IP-adress för Wi-Fi porten.  

    ![Lokalt webb gränssnitt "nätverks inställningar" Sidan 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    **Nätverks** sidan uppdateras när du har tillämpat inställningarna för Wi-Fi porten.

    ![Lokalt webb gränssnitt "nätverks inställningar" sidan 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)
   
4. Välj **Lägg till Wi-Fi profil** och ladda upp din Wi-Fis profil. 

    ![Lokalt webb gränssnitt "Port WiFi-nätverks inställningar" 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    En profil för trådlöst nätverk innehåller SSID (nätverks namn), lösen ords nyckel och säkerhets information för att kunna ansluta till ett trådlöst nätverk. Du kan hämta Wi-Fis profilen för din miljö från nätverks administratören.

    ![Lokalt webb gränssnitt "Port WiFi-nätverks inställningar" 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    När profilen har lagts till uppdateras listan med Wi-Fi profiler för att avspegla den nya profilen. Profilen ska visa **anslutnings status** som **frånkopplad**. 

    ![Lokalt webb gränssnitt "Port WiFi Network Settings" 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)


5. När profilen för trådlöst nätverk har lästs in ansluter du till den här profilen. Välj **Anslut till Wi-Fi profilen**. 

    ![Lokalt webb gränssnitt "Port Wi-Fi nätverks inställningar" 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

6. Välj den Wi-Fi profil som du lade till i föregående steg och välj **Använd**. 

    ![Lokalt webb gränssnitt "Port Wi-Fi nätverks inställningar" 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    **Anslutnings status** ska uppdateras till **ansluten**. Signal styrkan uppdateras för att indikera kvaliteten på signalen. 

    ![Lokalt webb gränssnitt "Port Wi-Fi nätverks inställningar" 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > För att överföra stora mängder data rekommenderar vi att du använder en tråd bunden anslutning i stället för det trådlösa nätverket. 

6. Koppla bort PORT 1 på enheten från den bärbara datorn. 

7. Tänk på följande när du konfigurerar nätverksinställningarna:

   - Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt. IP-adress, undernät, gateway och DNS tilldelas automatiskt.
   - Om DHCP inte är aktive rad kan du tilldela statiska IP-adresser om det behövs.
   - Du kan konfigurera nätverks gränssnittet som IPv4.
   - NIC-team (Network Interface Card) eller länk agg regering stöds inte med Azure Stack Edge.
   - Serie numret för vilken port som helst motsvarar nodens serie nummer. För en enhet i K-serien visas bara ett serie nummer.

     >[!NOTE] 
     > Vi rekommenderar att du inte byter nätverksgränssnittets lokala IP-adress från statisk till DCHP om du inte har någon annan IP-adress för att ansluta till enheten. Om du använder ett nätverksgränssnitt och växlar till DHCP finns det inget sätt att fastställa DHCP-adressen. Om du vill byta till en DHCP-adress ska du vänta med ändringen tills enheten har registrerats i tjänsten. Du kan sedan Visa IP-adresserna för alla nätverkskort i **enhets egenskaperna** i Azure Portal för din tjänst.

När du har konfigurerat och tillämpat nätverks inställningarna väljer du **Nästa: Compute** för att konfigurera Compute Network.

## <a name="enable-compute-network"></a>Aktivera beräknings nätverk

Följ dessa steg om du vill aktivera Compute och konfigurera Compute Network. 


1. På sidan **Beräkna** väljer du ett nätverks gränssnitt som du vill aktivera för beräkning. 

    ![Compute-sidan i lokalt användar gränssnitt 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-1.png)

1. I dialog rutan **nätverks inställningar** väljer du **Aktivera**. När du aktiverar beräkning skapas en virtuell växel på enheten i nätverks gränssnittet. Den virtuella växeln används för beräknings infrastrukturen på enheten. 
    
1. Tilldela **Kubernetes Node IP-adresser**. Dessa statiska IP-adresser är för den virtuella Compute-datorn.  

    För en *n*-Node-enhet anges ett sammanhängande intervall med minst *n + 1* IPv4-adresser (eller mer) för den virtuella beräknings datorn med hjälp av start-och slut-IP-adresserna. Den angivna Azure Stack Edge är en 1-nods enhet, minst 2 sammanhängande IPv4-adresser tillhandahålls.

    > [!IMPORTANT]
    > Kubernetes på Azure Stack Edge använder 172.27.0.0/16 undernät för Pod och 172.28.0.0/16 under nätet för tjänsten. Se till att de inte används i nätverket. Om dessa undernät redan används i nätverket kan du ändra dessa undernät genom att köra `Set-HcsKubeClusterNetworkInfo` cmdleten från PowerShell-gränssnittet på enheten. Mer information finns i [ändra Kubernetes-Pod och tjänst under nät](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. Tilldela **Kubernetes external service IP-adresser**. Detta är också IP-adresser för belastnings utjämning. Dessa sammanhängande IP-adresser är för tjänster som du vill exponera utanför Kubernetes-klustret och du anger det statiska IP-intervallet beroende på antalet tjänster som exponeras. 
    
    > [!IMPORTANT]
    > Vi rekommenderar starkt att du anger minst 1 IP-adress för Azure Stack Edge Mini R Hub-tjänsten för att få åtkomst till Compute-moduler. Alternativt kan du ange ytterligare IP-adresser för andra tjänster/IoT Edge moduler (1 per tjänst/modul) som behöver nås från utanför klustret. IP-adresserna för tjänsten kan uppdateras senare. 
    
1. Välj **Använd**.

    ![Compute-sidan i lokalt användar gränssnitt 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. Konfigurationen tar några minuter att tillämpa och du kan behöva uppdatera webbläsaren. Du kan se att den angivna porten är aktive rad för beräkning. 
 
    ![Compute-sidan i lokalt gränssnitt 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Välj **Nästa: webbproxy** för att konfigurera webbproxy.  

  
## <a name="configure-web-proxy"></a>Konfigurera webbproxy

Detta är en valfri konfiguration.

> [!IMPORTANT]
> * Om du aktiverar beräknings-och användnings IoT Edge modul på din Azure Stack Edge-Mini R-enhet rekommenderar vi att du ställer in webbproxy-autentisering som **ingen**. NTLM stöds inte.
>* PAC-filer (Proxy-Auto config) stöds inte. En PAC-fil definierar hur webbläsare och andra användar agenter automatiskt kan välja rätt proxyserver (åtkomst metod) för att hämta en viss URL. Proxyservrar som försöker avlyssna och läsa all trafik (och sedan signera om allting med sin egen certifiering) är inte kompatibelt eftersom proxyns certifikat inte är betrott. Vanligt vis fungerar transparenta proxyservrar med Azure Stack Edge Mini R. det finns inte stöd för icke-transparenta webb proxyservrar.


1. Gör följande på sidan **Web Proxy-inställningar** :

    1. I rutan **webbproxy-URL** anger du webb adressen i följande format: `http://host-IP address or FQDN:Port number` . Du kan inte använda HTTPS-adresser.

    2. Välj **Ingen** eller **NTLM** under **Autentisering**. Om du aktiverar beräknings-och användnings IoT Edge modul på din Azure Stack Edge-Mini R-enhet rekommenderar vi att du ställer in webbproxy-autentisering på **ingen**. **NTLM** stöds inte.

    3. Om du använder autentisering anger du ett användar namn och lösen ord.

    4. Om du vill validera och tillämpa de konfigurerade webbproxyinställningarna väljer du **tillämpa**.
    
   ![Sidan Web Proxy-inställningar för lokalt webb gränssnitt](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

2. När inställningarna har tillämpats väljer du **Nästa: enhet**.


## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att:

> [!div class="checklist"]
> * Förutsättningar
> * Konfigurera nätverk
> * Aktivera beräknings nätverk
> * Konfigurera webbproxy


Information om hur du konfigurerar din Azure Stack Edge-Mini R-enhet finns i:

> [!div class="nextstepaction"]
> [Konfigurera enhetsinställningar](./azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)
