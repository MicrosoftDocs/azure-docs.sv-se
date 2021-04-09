---
title: Snabb start för att konfigurera och Distribuera Azure Stack Edge-GPU | Microsoft Docs
description: Kom igång med att distribuera Azure Stack Edge-GPU när enheten har tagits emot.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to quickly deploy Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 4f3ecb6c9aa4ec200cd1a53b82d7c81a2c8d4dcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100546796"
---
# <a name="quickstart-get-started-with-azure-stack-edge-pro-with-gpu"></a>Snabb start: kom igång med Azure Stack Edge Pro med GPU 

Den här snabb starten beskriver kraven och stegen som krävs för att distribuera din Azure Stack Edge Pro GPU-enhet. Stegen i snabbstarten utförs på Azure-portalen och i enhetens lokala webbgränssnitt. 

Den totala proceduren bör ungefär ta 1,5 timmar att slutföra. Detaljerade steg-för-steg-anvisningar finns i [Självstudier: förbereda för distribution Azure Stack Edge Pro GPU](azure-stack-edge-gpu-deploy-prep.md#deployment-configuration-checklist). 


## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att följande krav är uppfyllda innan du distribuerar:

1. Azure Stack Edge Pro GPU-enhet levereras till din plats, [packas upp](azure-stack-edge-gpu-deploy-install.md#unpack-the-device) och [rack monteras](azure-stack-edge-gpu-deploy-install.md#rack-the-device). 
1. Konfigurera nätverket så att enheten kan komma åt URL- [mönstren och portarna i listan](azure-stack-edge-gpu-system-requirements.md#networking-port-requirements). 
1. Du har ägar-eller deltagar åtkomst till Azure-prenumerationen.
1. I Azure Portal går du till **start > prenumerationer > dina prenumerations > resurs leverantörer**. Sök efter `Microsoft.DataBoxEdge` och registrera resurs leverantören. Upprepa för att registrera `Microsoft.Devices` om du ska skapa en IoT Hub resurs för att distribuera beräknings arbets belastningar.
1. Se till att du har minst två kostnads fria, statiska, sammanhängande IP-adresser för Kubernetes-noder och minst 1 statisk IP-adress för IoT Edge-tjänsten. För varje modul eller extern tjänst distribuerar du ytterligare 1 IP-adress.
1. Se [Check lista för distribution](azure-stack-edge-gpu-deploy-checklist.md) för att få allt du behöver för enhets konfigurationen. 


## <a name="deployment-steps"></a>Distributionssteg

1. **Installera**: Anslut port 1 till en klient dator via en korskopplad kabel eller USB-Ethernet-kort. Anslut minst en annan enhets port för data, helst 25 GbE (från PORT 3 till PORT 6) till Internet via minst 1 GbE-växel och SFP + koppar kablar. Anslut de tillhandahållna ström sladdarna till strömförsörjnings enheterna och separera distributions möjligheter. Tryck på ström knappen på Front panelen för att aktivera enheten.  

    Se [Interoperabilitets mat ris för Cavium FastlinQ 41000-serien](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) och [Mellanox dual port 25G ConnectX-4 Channel-kompatibla produkter](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products) för att få kompatibla nätverks kablar och växlar.

    Här är den minsta kablage-konfiguration som krävs för att distribuera enheten:  ![ back plan för en kabelansluten enhet](./media/azure-stack-edge-gpu-quickstart/backplane-min-cabling-1.png)

2. **Anslut**: Konfigurera IPv4-inställningarna på Ethernet-kortet på datorn med en statisk IP-adress för **192.168.100.5** och undernät **255.255.255.0**. Öppna din webbläsare och Anslut till det lokala webb gränssnittet för enheten på https://192.168.100.10 . Det kan ta några minuter. Fortsätt till webb sidan när du ser säkerhets certifikats varningen.

3. **Logga** in: Logga in på enheten med standard lösen ordet *Password1*. Ändra enhetens administratörs lösen ord. Lösen ordet måste innehålla mellan 8 och 16 tecken och 3 av tecknen versaler, gemener, siffror och specialtecken.

4. **Konfigurera nätverk**: Godkänn standard-DHCP-konfigurationen för den anslutna data porten om du har en DHCP-server i nätverket. Om inte, anger du en IP-, DNS-server och standard-gateway. Se mer information om [nätverks inställningar](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-network).

5. **Konfigurera Compute Network**: skapa en virtuell växel genom att aktivera en port på enheten. Ange 2 kostnads fria, sammanhängande statiska IP-adresser för Kubernetes-noder i samma nätverk som du skapade växeln. Ange minst 1 statisk IP-adress för IoT Edge Hub-tjänsten för att få åtkomst till Compute-moduler och en statisk IP-adress för varje extra tjänst eller behållare som du vill komma åt från utanför Kubernetes-klustret. 

    Kubernetes krävs för att distribuera alla arbets belastningar i behållare. Se mer information om [beräkning av nätverks inställningar](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network).

6. **Konfigurera webbproxy**: om du använder webbproxy i din miljö anger du webb serverns IP-adress i `http://<web-proxy-server-FQDN>:<port-id>` . Ange autentisering till **ingen**. Se mer information om [webbproxy-inställningar](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-web-proxy).

7. **Konfigurera enhet**: Ange ett enhets namn och en DNS-domän eller acceptera standardvärden. 

8. **Konfigurera uppdaterings Server**: godkänn standard Microsoft Update servern eller ange en Windows Server Update Services-server (WSUS) och sökvägen till servern. 

9. **Konfigurera tids inställningar**: acceptera standard tids inställningarna eller ange tidszon, primär NTP-server och sekundär NTP-server i lokalt nätverk eller som offentliga servrar.

10. **Konfigurera certifikat**: om du har ändrat enhets namn och/eller DNS-domän måste du generera certifikat eller lägga till certifikat för att aktivera enheten. 

    - Använd alternativet för att [Skapa certifikat](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)om du vill testa arbets belastningar som inte är produktion. 
    - Om du tar med dina egna certifikat, inklusive signerings kedjan, [lägger du till certifikat](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) i lämpligt format. Se till att ladda upp signerings kedjan först. Se [Skapa certifikat](azure-stack-edge-gpu-create-certificates-tool.md) och [Ladda upp certifikat via det lokala användar gränssnittet](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates).

11. **Aktivera**: för att hämta aktiverings nyckeln 

    1. I Azure Portal går du till **Azure Stack Edge-resurs > översikt > enhets installation > aktivera > generera nyckel**. Kopiera nyckeln. 
    1. I det lokala webb gränssnittet går du till **Kom igång > aktiverar** och anger aktiverings nyckeln. När nyckeln appliceras tar det några minuter innan enheten aktive ras. Ladda ned `<device-serial-number>` . JSON-filen när du uppmanas att lagra enhets nycklar som behövs för en framtida återställning på ett säkert sätt. 

12. **Konfigurera beräkning**: gå till **Översikt > enheten** i Azure Portal. Kontrol lera att enheten är **online**. I den vänstra rutan går du till **Edge compute > kom igång > konfigurera Edge compute > Compute**. Ange en befintlig eller ny IoT Hub-tjänst och vänta i ungefär 20 minuter innan beräkningen har kon figurer ATS. Se mer information om [självstudie: Konfigurera Compute på Azure Stack Edge Pro GPU-enhet](azure-stack-edge-gpu-deploy-configure-compute.md)

Du är redo att distribuera beräknings arbets belastningar på enheten [via IoT Edge](azure-stack-edge-gpu-deploy-sample-module-marketplace.md), [via `kubectl` ](azure-stack-edge-gpu-create-kubernetes-cluster.md) eller [via Azure Arc-aktiverad Kubernetes](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)! Om du får problem under installationen går du till [Felsöka enhets problem](), [beställnings problem](azure-stack-edge-gpu-troubleshoot.md), [certifikat problem](azure-stack-edge-gpu-certificate-troubleshooting.md)eller Kubernetes problem. 

## <a name="next-steps"></a>Nästa steg

[Installera Azure Stack Edge Pro GPU](./azure-stack-edge-gpu-deploy-install.md)



