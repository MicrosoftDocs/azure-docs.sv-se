---
title: Självstudie för att konfigurera nätverks inställningar för Azure Stack Edge Pro R-enhet i Azure Portal | Microsoft Docs
description: Självstudie för att distribuera Azure Stack Edge Pro R instruerar dig att konfigurera nätverks-, beräknings-och webbproxyinställningar för den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: 11c1d76b5784587f234455f81595778897569eb1
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594357"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-pro-r"></a>Självstudie: Konfigurera nätverk för Azure Stack Edge Pro R

I den här självstudien beskrivs hur du konfigurerar nätverk för din Azure Stack Edge Pro R-enhet med hjälp av det lokala webb gränssnittet.

Anslutnings processen kan ta ungefär 20 minuter att slutföra.

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Förutsättningar
> * Konfigurera nätverk
> * Aktivera beräknings nätverk
> * Konfigurera webbproxy


## <a name="prerequisites"></a>Förutsättningar

Innan du konfigurerar och konfigurerar din Azure Stack Edge Pro R-enhet ser du till att:

* Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge Pro R](azure-stack-edge-gpu-deploy-install.md).
* Du har anslutit till det lokala webb gränssnittet på enheten som beskrivs i [ansluta till Azure Stack Edge Pro R](azure-stack-edge-gpu-deploy-connect.md)


## <a name="configure-network"></a>Konfigurera nätverk

Sidan **Kom igång** visar de olika inställningar som krävs för att konfigurera och registrera den fysiska enheten med Azure Stack Edge-tjänsten. 

Följ dessa steg om du vill konfigurera nätverket för enheten.

1. Gå till sidan **Kom igång** i enhetens lokala webb gränssnitt. 

2. På panelen **nätverk** väljer du **Konfigurera** för att gå till sidan **nätverk** . 
    
    <!--![Local web UI "Network settings" tile](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)-->

    Det finns fyra nätverks gränssnitt på den fysiska enheten. PORT 1 och PORT 2 är nätverksgränssnitt på 1 Gbit/s. PORT 3 och PORT 4 är alla nätverks gränssnitt för 10/25 Gbit/s. PORT 1 konfigureras automatiskt som en port för endast hantering, och PORT 2 till PORT 4 är alla data portar. På sidan **nätverk** ser du nedan.
    
    ![Sidan nätverks inställningar för lokalt webb gränssnitt](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2.png)

   
3. Om du vill ändra nätverks inställningarna väljer du en port och i den högra rutan som visas ändrar du IP-adress, undernät, Gateway, primär DNS och sekundär DNS. 

    - Om du väljer port 1 kan du se att den är förkonfigurerad som statisk. 

        ![Lokalt webb gränssnitt "port 1 nätverks inställningar"](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-3.png)

    - Om du väljer port 2, Port 3 eller port 4 konfigureras alla dessa portar som DHCP som standard.

        ![Lokalt webb gränssnitt "Port 3 nätverks inställningar"](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-4.png)

    Tänk på följande när du konfigurerar nätverksinställningarna:

   * Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt. IP-adress, undernät, gateway och DNS tilldelas automatiskt.
   * Om DHCP inte är aktive rad kan du tilldela statiska IP-adresser om det behövs.
   * Du kan konfigurera nätverks gränssnittet som IPv4.
   * NIC-team (Network Interface Card) eller länk agg regering stöds inte med Azure Stack Edge.
   * Serie numret för vilken port som helst motsvarar nodens serie nummer.
    <!--* On the 25-Gbps interfaces, you can set the RDMA (Remote Direct Access Memory) mode to iWarp or RoCE (RDMA over Converged Ethernet). Where low latencies are the primary requirement and scalability is not a concern, use RoCE. When latency is a key requirement, but ease-of-use and scalability are also high priorities, iWARP is the best candidate.-->
    När enhets nätverket har kon figurer ATS uppdateras sidan på det sätt som visas nedan.

    ![Lokalt webb gränssnitt "nätverks inställningar" Sidan 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2a.png)<!--change-->


     >[!NOTE]
     >
     > * Vi rekommenderar att du inte byter nätverksgränssnittets lokala IP-adress från statisk till DCHP om du inte har någon annan IP-adress för att ansluta till enheten. Om du använder ett nätverksgränssnitt och växlar till DHCP finns det inget sätt att fastställa DHCP-adressen. Om du vill ändra till en DHCP-adress väntar du tills enheten har Aktiver ATS med tjänsten och ändrar sedan. Du kan sedan Visa IP-adresserna för alla nätverkskort i **enhets egenskaperna** i Azure Portal för din tjänst.


    När du har konfigurerat och tillämpat nätverks inställningarna väljer du **Nästa: Compute** för att konfigurera Compute Network.

## <a name="enable-compute-network"></a>Aktivera beräknings nätverk

Följ dessa steg om du vill aktivera Compute och konfigurera Compute Network. 

1. På sidan **Beräkna** väljer du ett nätverks gränssnitt som du vill aktivera för beräkning. 

    ![Compute-sidan i lokalt användar gränssnitt 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-2.png)

1. I dialog rutan **nätverks inställningar** väljer du **Aktivera**. När du aktiverar beräkning skapas en virtuell växel på enheten i nätverks gränssnittet. Den virtuella växeln används för beräknings infrastrukturen på enheten. 
    
1. Tilldela **Kubernetes Node IP-adresser**. Dessa statiska IP-adresser är för den virtuella Compute-datorn.  

    För en *n*-Node-enhet anges ett sammanhängande intervall med minst *n + 1* IPv4-adresser (eller mer) för den virtuella beräknings datorn med hjälp av start-och slut-IP-adresserna. Den angivna Azure Stack Edge är en 1-nods enhet, minst 2 sammanhängande IPv4-adresser tillhandahålls. De här IP-adresserna måste finnas i samma nätverk där du har aktiverat Compute och den virtuella växeln har skapats.

    > [!IMPORTANT]
    > Kubernetes på Azure Stack Edge använder 172.27.0.0/16 undernät för Pod och 172.28.0.0/16 under nätet för tjänsten. Se till att de inte används i nätverket. Om dessa undernät redan används i nätverket kan du ändra dessa undernät genom att köra `Set-HcsKubeClusterNetworkInfo` cmdleten från PowerShell-gränssnittet på enheten. Mer information finns i [ändra Kubernetes-Pod och tjänst under nät](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. Tilldela **Kubernetes external service IP-adresser**. Dessa är också IP-adresser för belastnings utjämning. Dessa sammanhängande IP-adresser är för tjänster som du vill exponera utanför Kubernetes-klustret och du anger det statiska IP-intervallet beroende på antalet tjänster som exponeras. 
    
    > [!IMPORTANT]
    > Vi rekommenderar starkt att du anger minst 1 IP-adress för Azure Stack Edge Pro R Hub-tjänsten för att få åtkomst till Compute-moduler. Alternativt kan du ange ytterligare IP-adresser för andra tjänster/IoT Edge moduler (1 per tjänst/modul) som behöver nås från utanför klustret. IP-adresserna för tjänsten kan uppdateras senare. 
    
1. Välj **Använd**.

    ![Compute-sidan i lokalt användar gränssnitt 3](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. Konfigurationen tar några minuter att tillämpa och du kan behöva uppdatera webbläsaren. Du kan se att den angivna porten är aktive rad för beräkning. 
 
    ![Compute-sidan i lokalt gränssnitt 4](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Välj **Nästa: webbproxy** för att konfigurera webbproxy.  

  
## <a name="configure-web-proxy"></a>Konfigurera webbproxy

Detta är en valfri konfiguration.

> [!IMPORTANT]
> * Om du aktiverar beräknings-och användnings IoT Edge modul på din Azure Stack Edge Pro R-enhet rekommenderar vi att du anger webbproxy-autentisering som **ingen**. NTLM stöds inte.
>* PAC-filer (Proxy-Auto config) stöds inte. En PAC-fil definierar hur webbläsare och andra användar agenter automatiskt kan välja rätt proxyserver (åtkomst metod) för att hämta en viss URL. Proxyservrar som försöker avlyssna och läsa all trafik (och sedan signera om allting med sin egen certifiering) är inte kompatibelt eftersom proxyns certifikat inte är betrott. Vanligt vis fungerar transparenta proxyservrar bra med Azure Stack Edge Pro R. icke-transparenta webb-proxyservrar stöds inte.


1. Gör följande på sidan **Web Proxy-inställningar** :

    1. I rutan **webbproxy-URL** anger du webb adressen i följande format: `http://host-IP address or FQDN:Port number` . Du kan inte använda HTTPS-adresser.

    2. Under **autentisering** väljer du **ingen** eller **NTLM**. Om du aktiverar beräknings-och användnings IoT Edge modul på din Azure Stack Edge Pro R-enhet rekommenderar vi att du ställer in webbproxy-autentisering på **ingen**. **NTLM** stöds inte.

    3. Om du använder autentisering anger du ett användar namn och lösen ord.

    4. Om du vill validera och tillämpa de konfigurerade webbproxyinställningarna väljer du **tillämpa**.
    
   ![Lokalt webb gränssnitt "webb proxyinställningar" sida 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

2. När inställningarna har tillämpats väljer du **Nästa: enhet**.


## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att:

> [!div class="checklist"]
> * Förutsättningar
> * Konfigurera nätverk
> * Aktivera beräknings nätverk
> * Konfigurera webbproxy


Information om hur du konfigurerar din Azure Stack Edge Pro R-enhet finns i:

> [!div class="nextstepaction"]
> [Konfigurera enhetsinställningar](./azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)
