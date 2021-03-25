---
title: Azure Stack Edge Mini R Wi-Fi-hantering
description: Beskriver hur du använder Azure Portal för att hantera Wi-Fi på Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: a2cc0707c344c3ca537795666a3f60f648026596
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043775"
---
# <a name="use-the-local-web-ui-to-manage-wireless-connectivity-on-your-azure-stack-edge-mini-r"></a>Använd det lokala webb gränssnittet för att hantera trådlös anslutning på Azure Stack Edge Mini R

Den här artikeln beskriver hur du hanterar trådlös nätverks anslutning på din Azure Stack Edge Mini R-enhet. Du kan använda det lokala webb gränssnittet på din Azure Stack Edge Mini R-enhet via för att lägga till, ansluta till och ta bort Wi-Fi profiler.

## <a name="about-wi-fi"></a>Om Wi-Fi

Din Azure Stack Edge Mini R-enhet kan använda både när den är kabelansluten till nätverket eller via ett trådlöst nätverk. Enheten har en Wi-Fi port som måste vara aktive rad för att enheten ska kunna ansluta till ett trådlöst nätverk. 

Enheten har fem portar, PORT 1 till PORT 4 och en femte Wi-Fi port. Här är ett diagram över back planet i en Mini R-enhet när du är ansluten till ett trådlöst nätverk.

![Kablar för Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)


## <a name="add-connect-to-wi-fi-profile"></a>Lägg till, Anslut till Wi-Fi profil

Utför följande steg i enhetens lokala användar gränssnitt för att lägga till och ansluta till en Wi-Fi-profil.

1. Gå till sidan **Kom igång** i enhetens lokala webb gränssnitt. I panelen **nätverk** väljer du **Konfigurera**.  
    
    Det finns fem nätverks gränssnitt på den fysiska enheten. PORT 1 och PORT 2 är nätverksgränssnitt på 1 Gbit/s. PORT 3 och PORT 4 är alla nätverks gränssnitt med 10 Gbit/s. Den femte porten är Wi-Fi port. 

    [![Lokalt webb gränssnitt "nätverks inställningar" sida 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)  
    
    Välj den Wi-Fi porten och konfigurera port inställningarna. 
    
    > [!IMPORTANT]
    > Vi rekommenderar starkt att du konfigurerar en statisk IP-adress för Wi-Fi porten.  

    ![Lokalt webb gränssnitt "nätverks inställningar" Sidan 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    **Nätverks** sidan uppdateras när du har tillämpat inställningarna för Wi-Fi porten.

    ![Lokalt webb gränssnitt "nätverks inställningar" sidan 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)

   
2. Välj **Lägg till Wi-Fi profil** och ladda upp din Wi-Fis profil. 

    ![Lokalt webb gränssnitt "Port WiFi-nätverks inställningar" 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    En profil för trådlöst nätverk innehåller SSID (nätverks namn), lösen ords nyckel och säkerhets information för att kunna ansluta till ett trådlöst nätverk. Du kan hämta Wi-Fis profilen för din miljö från nätverks administratören.

    Information om hur du förbereder dina Wi-Fi profiler finns i [använda Wi-Fi profiler med Azure Stack Edge Mini R-enheter](azure-stack-edge-mini-r-use-wifi-profiles.md).

    ![Lokalt webb gränssnitt "Port WiFi-nätverks inställningar" 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    När profilen har lagts till uppdateras listan med Wi-Fi profiler för att avspegla den nya profilen. Profilen ska visa **anslutnings status** som **frånkopplad**. 

    ![Lokalt webb gränssnitt "Port WiFi Network Settings" 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)
    
3. När profilen för trådlöst nätverk har lästs in ansluter du till den här profilen. Välj **Anslut till Wi-Fi profilen**. 

    ![Lokalt webb gränssnitt "Port Wi-Fi nätverks inställningar" 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

4. Välj den Wi-Fi profil som du lade till i föregående steg och välj **Använd**. 

    ![Lokalt webb gränssnitt "Port Wi-Fi nätverks inställningar" 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    **Anslutnings status** ska uppdateras till **ansluten**. Signal styrkan uppdateras för att indikera kvaliteten på signalen. 

    ![Lokalt webb gränssnitt "Port Wi-Fi nätverks inställningar" 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > För att överföra stora mängder data rekommenderar vi att du använder en tråd bunden anslutning i stället för det trådlösa nätverket. 


## <a name="download-wi-fi-profile"></a>Ladda ned Wi-Fi profil

Du kan hämta en Wi-Fi-profil som du använder för den trådlösa nätverks anslutningen.

1. I enhetens lokala webb gränssnitt går du till **konfiguration > nätverk**. 

2. Under Wi-Fi profil inställningar väljer du **Hämta profil**. Detta bör ladda ned den Wi-Fi-profil som du använder just nu.


## <a name="delete-wi-fi-profile"></a>Ta bort Wi-Fi profil

Du kan ta bort en Wi-Fi profil som du använder för trådlös nätverks anslutning.


1. I enhetens lokala webb gränssnitt går du till **konfiguration > nätverk**. 

2. Under Wi-Fi profil inställningar väljer du **ta bort Wi-Fi profil**.

3. I bladet **ta bort Wi-Fi profil** väljer du den profil som du vill ta bort. Välj **Använd**.


## <a name="configure-cisco-wi-fi-profile"></a>Konfigurera Cisco Wi-Fi-profil

Här följer några rikt linjer för hur du hanterar och konfigurerar en trådlös Cisco-styrenhet och åtkomst punkt på din enhet. 

### <a name="dhcp-bridging-mode"></a>Läge för DHCP-bryggning

Om du vill använda en Ciscos trådlösa styrenhet för enheten måste du aktivera bryggning av DHCP (Dynamic Host Configuration Protocol) på den trådlösa LAN-styrenheten (WLC).

Mer information finns i [läget för DHCP-bryggning](https://www.cisco.com/c/en/us/support/docs/wireless/4400-series-wireless-lan-controllers/110865-dhcp-wlc.html#anc9).

#### <a name="bridging-configuration-example"></a>Exempel på bryggning av konfiguration

Om du vill aktivera funktionen för DHCP-bryggning på kontroll enheten måste du inaktivera funktionen DHCP-proxy på kontroll enheten. Så här aktiverar du DHCP-bryggning med hjälp av kommando raden:

```powershell
(Cisco Controller) > config dhcp proxy disable
(Cisco Controller) > show dhcp proxy
DHCP Proxy Behaviour: disabled
```

Om DHCP-servern inte finns i samma skikt 2 (L2)-nätverk som-klienten, ska sändningen vidarebefordras till DHCP-servern på klient-gatewayen med hjälp av en IP-hjälp. Detta är ett exempel på den här konfigurationen:

```powershell
Switch#conf t
Switch(config)#interface vlan <client vlan #>
Switch(config-if)#ip helper-address <dhcp server IP>
```

Funktionen DHCP-bryggning är en global inställning, vilket påverkar alla DHCP-transaktioner inom kontrollanten. Du måste lägga till IP Helper-instruktioner i den kabelanslutna infrastrukturen för alla nödvändiga virtuella lokala nätverk (VLAN) på styrenheten.

### <a name="enable-the-passive-client-for-wlan"></a>Aktivera passiv klient för WLAN

Så här aktiverar du funktionen passiv klient för WLAN (Wireless Local Area Network) på en trådlös Cisco-styrenhet:

* Det gränssnitt som är kopplat till WLAN måste ha en aktive rad VLAN-taggning.
* Multicast-VLAN måste vara aktiverat för WLAN.
* GARP-vidarebefordran måste vara aktiverat på WLC.

Mer information finns i [multicast-VLAN information om multicast-optimering](https://www.cisco.com/c/en/us/td/docs/wireless/controller/8-5/config-guide/b_cg85/wlan_interfaces.html).

### <a name="troubleshoot"></a>Felsöka

Om det uppstår problem med tilldelningar av IP-adresser på virtuella datorer som körs på en Azure Stack Edge-Mini R-enhet, bör ovanstående konfigurations inställningar i din nätverks miljö verifieras.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar Azure Stack Edge Mini R-enheten](azure-stack-edge-mini-r-deploy-prep.md).
