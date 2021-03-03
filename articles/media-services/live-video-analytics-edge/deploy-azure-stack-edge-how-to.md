---
title: Distribuera video analys i real tid på Azure Stack Edge
description: Den här artikeln innehåller de steg som hjälper dig att distribuera video analys på din Azure Stack Edge.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: d49167890009d58b21c3678cb89f608bad665abd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730277"
---
# <a name="deploy-live-video-analytics-on-azure-stack-edge"></a>Distribuera video analys i real tid på Azure Stack Edge

Den här artikeln innehåller de steg som hjälper dig att distribuera video analys på din Azure Stack Edge. När enheten har kon figurer ATS och Aktiver ATS är det dags att distribuera video analys. 

För video analys i real tid distribuerar vi via IoT Hub, men Azure Stack kant resurser exponerar ett Kubernetes-API som gör det möjligt för kunden att distribuera ytterligare icke-IoT Hub medvetna lösningar som kan gränssnitt med real tids video analys. 

> [!TIP]
> Att använda API: et för Kubernetes (K8s) för anpassad distribution är ett avancerat ärende. Vi rekommenderar att kunden skapar Edge-moduler och distribuerar via IoT Hub till varje Azure Stack Edge-resurs i stället för att använda Kubernetes-API: et. I den här artikeln visar vi hur du distribuerar Live Video Analytics-modulen med hjälp av IoT Hub.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration som du har [ägar behörighet](../../role-based-access-control/built-in-roles.md#owner)till.
* En [Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) -resurs
   
* [En IoT Hub](../../iot-hub/iot-hub-create-through-portal.md)
* Ett [tjänst huvud namn](./create-custom-azure-resource-manager-role-how-to.md#create-service-principal) för modulen för video analys i real tid.

   Använd någon av dessa regioner där IoT Hub är tillgängligt: USA, östra 2, centrala USA, norra centrala USA, Östra Japan, västra USA 2, västra centrala USA, Östra Kanada, Storbritannien, södra, Frankrike Central, Frankrike, södra, Schweiz, norra, Schweiz, västra och Japan, västra.
* Lagringskonto

    Vi rekommenderar att du använder GPv2-lagrings konton (General-Purpose v2).  
    Läs mer om ett [Allmänt-syfte v2-lagrings konto](../../storage/common/storage-account-upgrade.md?tabs=azure-portal).
* [Visual Studio Code](https://code.visualstudio.com/) på din utvecklings dator. Kontrol lera att du har [tillägget Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Kontrol lera att nätverket som utvecklings datorn är anslutet till tillåter Avancerat meddelandekö-protokoll över Port 5671. Med den här inställningen kan Azure IoT-verktyg kommunicera med Azure IoT Hub.

## <a name="configuring-azure-stack-edge-for-using-live-video-analytics"></a>Konfigurera Azure Stack Edge för att använda live video analys

Azure Stack Edge är en maskinvaru-som-tjänst-lösning och en AI-aktiverad Edge-baserad data behandlings enhet med funktioner för nätverks data överföring. Läs mer om [Azure Stack Edge och detaljerade installations anvisningar](../../databox-online/azure-stack-edge-deploy-prep.md). Kom igång genom att följa anvisningarna i länkarna nedan:

* [Azure Stack Edge/Data Box Gateway-resurs skapas](../../databox-online/azure-stack-edge-deploy-prep.md)
* [Installera och konfigurera](../../databox-online/azure-stack-edge-deploy-install.md)
* [Anslutning och aktivering](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md)
* [Koppla en IoT Hub till Azure Stack kant](../../databox-online/azure-stack-edge-gpu-deploy-configure-compute.md#configure-compute)
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Aktivera beräknings krav i Azure Stack Edge Local UI

Innan du fortsätter bör du kontrol lera att:

* Du har aktiverat din Azure Stack Edge-resurs.
* Du har åtkomst till ett Windows-klientsystem som kör PowerShell 5,0 eller senare för att få åtkomst till Azure Stack Edge-resursen.
* Om du vill distribuera ett Kubernetes-kluster måste du konfigurera din Azure Stack Edge-resurs via det [lokala webb gränssnittet](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup). 
    
    * Om du vill aktivera beräkningen går du till beräknings sidan i det lokala webb gränssnittet på enheten.
    
        * Välj ett nätverks gränssnitt som du vill aktivera för beräkning. Välj Aktivera. Genom att aktivera beräknings resultatet skapas en virtuell växel på enheten i nätverks gränssnittet.
        * Lämna Kubernetes-testnoden IP-adresser och Kubernetes externa tjänster tomt.
        * Välj tillämpa – den här åtgärden tar cirka 2 minuter.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-commercial.png" alt-text=" Compute-krav i Azure Stack Edge-lokalt användar gränssnitt":::

        * Om DNS inte har kon figurer ATS för Kubernetes-API: et och Azure Stack Edge-resurs, kan du uppdatera fönstrets värd fil.
        
            * Öppna en text redigerare som administratör
            * Öppna filen till C:\Windows\System32\drivers\etc\hosts
            * Lägg till Kubernetes-API-enhetens IPv4 och värdnamn i filen. (Den här informationen finns i Azure Stack Edge-portalen under avsnittet enheter.)
            * Spara och stäng

### <a name="deploy-live-video-analytics-edge-module-using-azure-portal"></a>Distribuera Live Video Analytics Edge-modulen med hjälp av Azure Portal

För detta kommer vi bara att vidta vissa åtgärder för att [distribuera video analys via IoT Hub](deploy-iot-edge-device.md).

1. Hoppa över stegen för att skapa användare och grupper och gå till [distribuera Live Video Analytics Edge](deploy-iot-edge-device.md#deploy-live-video-analytics-edge-module) -modulen. Följ anvisningarna som nämns där.
1. I behållare skapar du alternativ som du inte behöver ange miljövariabler. Hoppa därför över det här steget.
1. Öppna fliken behållare skapa alternativ.

   * Kopiera och klistra in följande JSON i rutan för att begränsa storleken på loggfilerna som skapas av modulen.
    
      ```json
        "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                        "max-size": "10m",
                        "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                    ]
                    }
      ```
    
      > [!NOTE]
      > Om du använder gRPC-protokoll med delad minnes överföring använder du värd IPC-läget för delad minnes åtkomst mellan live video analys och lösningar för härledning.
   
      ```json
          "HostConfig": {
                        "LogConfig": {
                            "Type": "",
                            "Config": {
                            "max-size": "10m",
                            "max-file": "10"`
                            }
                        },
                        "Binds": [
                            "/var/media/:/var/media/",
                            "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                        ],
                        "IpcMode": "host",
                        "ShmSize": 1536870912
                    }
      ```

      > [!NOTE]
      > Avsnittet "binds" i JSON har 2 poster. De kataloger som anges i bindnings avsnittet ovan kommer automatiskt att skapas av LVA.  
        Du kan uppdatera gräns enhetens bindningar, men om du gör det kontrollerar du att dessa kataloger finns på enheten.
    
    * "/var/lib/azuremediaservices:/var/lib/azuremediaservices": används för att binda de beständiga program konfigurations data från behållaren och lagra dem på gräns enheten.
    * "/var/media:/var/media": detta binder Media-mapparna mellan gräns enheten och behållaren. Detta används för att lagra videoinspelningarna när du kör en Media Graph-topologi som har stöd för lagring av video klipp på gräns enheten.
        
1. Fortsätt med stegen i dokumentet och fyll i modulens dubbla inställningar.
1. Välj **Nästa**: vägar för att fortsätta till avsnittet vägar. Ange vägar.

    Behåll standard vägarna och välj Nästa: granska + skapa för att fortsätta till gransknings avsnittet.
1. [Granska och verifiera distributionen](deploy-iot-edge-device.md#review-deployment)

#### <a name="optional-setup-docker-volume-mounts"></a>Valfritt Konfigurera Docker-volym monteringar

Om du vill visa data i arbets kataloger följer du de här stegen för att konfigurera Docker-volym monteringar innan du distribuerar. 

Dessa steg beskriver hur du skapar en gateway-användare och konfigurerar fil resurser för att visa innehållet i Live Video Analytics-arbetskatalogen och Media-mappen live video analys.

> [!NOTE]
> Bindnings monteringar stöds, men volym monteringar gör att data kan visas och om du vill kopiera den via en fjärr anslutning. Det går att använda både bind-och volym monteringar, men de kan inte peka på samma container Sök väg.

1. Öppna Azure Portal och gå till Azure Stack Edge-resursen.
1. Skapa en **Gateway-användare** som har åtkomst till resurser.
    
    1. I det vänstra navigerings fönstret klickar du på **Cloud Storage Gateway**.
    1. Klicka på **användare** i det vänstra navigerings fönstret.
    1. Klicka på Jon **+ Lägg till användare** i Ange användar namn och lösen ord. (Rekommenderas: `lvauser` ).
    1. Klicka på **Lägg till**.
    
1. Skapa en **lokal resurs** för Live Video Analytics-persistence.

    1. Klicka på **Cloud Storage Gateway->resurser**.
    1. Klicka på **+ Lägg till resurser**.
    1. Ange ett resurs namn. (Rekommenderas: `lva` ).
    1. Behåll resurs typen som SMB.
    1. Se till att **använda resurs med Edge Compute** är markerat.
    1. Se till att **Konfigurera som lokal Edge-resurs** är markerad.
    1. I användar information ger du åtkomst till resursen till den nyligen skapade användaren.
    1. Klicka på **skapa**.
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/local-share.png" alt-text="Lokal resurs":::  

    > [!TIP]
    > Med hjälp av Windows-klienten som är ansluten till Azure Stack Edge ansluter du till SMB-resurserna enligt de steg [som beskrivs i det här dokumentet](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share).    

1. Skapa en fjär resurs för fil lagrings lagring.

    1. Skapa först ett Blob Storage-konto i samma region genom att klicka på **Cloud Storage Gateway->lagrings konton**.
    1. Klicka på **Cloud Storage Gateway->resurser**.
    1. Klicka på **+ Lägg till resurser**.
    1. Ange ett resurs namn. (Rekommenderas: Media).
    1. Behåll resurs typen som SMB.
    1. Se till att **använda resurs med Edge Compute** är markerat.
    1. Se till att **Konfigurera som lokal Edge-resurs** inte är markerat.
    1. Välj det nyligen skapade lagrings kontot.
    1. Ange ett behållar namn.
    1. Ange lagrings typen för att blockera blob.
    1. I användar information ger du åtkomst till resursen till den nyligen skapade användaren.
    1. Klicka på **skapa**.    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/remote-share.png" alt-text="Fjär resurs":::
    
    
1. Uppdatera behållaren för video analys Edge-modulen för att skapa alternativ (se punkt 4 i [Lägg till moduler-dokument](deploy-iot-edge-device.md#add-modules)) om du vill använda volym monteringar.

   ```json
      "createOptions": 
         {
             "HostConfig": 
             {
                 "Binds": 
                 [
                     "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                 ],
                 "Mounts": 
                 [
                     {
                         "Target": "/var/media",
                         "Source": "media",
                         "Type": "volume"
                     }
                 ]
             }
         }
    ```

### <a name="verify-that-the-module-is-running"></a>Kontrol lera att modulen körs

Det slutliga steget är att se till att modulen är ansluten och körs som förväntat. Körningsstatusen för modulen bör vara aktiv för din IoT Edge-enhet i IoT Hub-resursen.

Verifiera att modulen körs genom att göra följande:

1. Gå tillbaka till Azure Stack Edge-resursen i Azure Portal.
1. Välj panelen moduler. Då går du till bladet moduler. Identifiera den modul som du har distribuerat i listan över moduler. Körnings status för modulen som du har lagt till ska köras.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/iot-edge-custom-module.png" alt-text="Anpassad modul":::

### <a name="configure-the-azure-iot-tools-extension"></a>Konfigurera tillägget Azure IoT-verktyg

Följ dessa anvisningar för att ansluta till din IoT Hub med hjälp av tillägget Azure IoT-verktyg.

1. I Visual Studio Code väljer du Visa > Explorer. Eller Välj CTRL + SKIFT + E.
1. I det nedre vänstra hörnet på fliken Utforskaren väljer du Azure IoT Hub.
1. Välj ikonen fler alternativ om du vill se snabb menyn. Välj sedan ange IoT Hub anslutnings sträng.
1. När en inmatad ruta visas anger du IoT Hub anslutnings strängen. 

   * Om du vill hämta anslutnings strängen går du till IoT Hub i Azure Portal och klickar på principer för delad åtkomst i det vänstra navigerings fönstret.
   * Klicka på iothubowner hämta nycklar för delad åtkomst.
   * Kopiera anslutnings strängen – primär nyckel och klistra in den i text rutan på VSCode.

   Anslutnings strängen ser ut så här:<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   Om anslutningen lyckas visas listan över gräns enheter. Du bör se din Azure Stack Edge. Nu kan du hantera dina IoT Edge enheter och interagera med Azure IoT Hub via snabb menyn. Om du vill visa de moduler som har distribuerats på gräns enheten expanderar du noden moduler under Azure Stack enheten.
    
## <a name="troubleshooting"></a>Felsökning

* **Kubernetes API-åtkomst (kubectl)**

    * Följ dokumentationen för att konfigurera datorn för [åtkomst till Kubernetes-klustret](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-create-kubernetes-cluster).
    * Alla distribuerade IoT Edge-moduler använder `iotedge` namn området. Se till att inkludera det när du använder kubectl.  

* **Modul loggar**

    `iotedge`Verktyget kan inte användas för att hämta loggar. Du måste använda [kubectl-loggar](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs)  för att visa loggarna eller pipe till en fil. Exempel: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`  

* **Pod-och Node-mått**

    Använd [kubectl överst](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top)  för att se Pod-och Node-mått.
    <br/>`kubectl top pods -n iotedge` 

* **Modul nätverk**   
För modul identifiering på Azure Stack Edge krävs det att modulen har värd port bindningen i createOptions. Modulen kommer sedan att adresseras över `moduleName:hostport` .
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* **Volym montering**

    En modul kan inte startas om behållaren försöker montera en volym till en befintlig katalog som inte är tom.

* **Delat minne när du använder gRPC**

    Delat minne på Azure Stack kant resurser stöds över poddar i alla namn områden med hjälp av värd-IPC.
    Konfigurera delat minne i en Edge-modul för distribution via IoT Hub.
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    //(Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
    
* **Erfar Pod Co-location**

    När du använder K8s för att distribuera anpassade härlednings lösningar som kommunicerar med real tids analys via gRPC, måste du se till att poddar distribueras på samma noder som direktsända video analys moduler.

    * **Alternativ 1** – Använd Node Affinity och inbyggda Node-etiketter för samplacering.

    För närvarande går det inte att använda den anpassade konfigurationen för avvaler eftersom användarna inte har behörighet att ange etiketter på noderna. Beroende på kundens topologi och namn konventioner kanske de kan använda [inbyggda Node-etiketter](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels). Ett nodeAffinity-avsnitt som refererar Azure Stack Edge-resurser med Real Video Analytics kan läggas till i pod-manifestet för för att uppnå samplacering.
    * **Alternativ 2** – Använd Pod-tillhörighet för samplacering (rekommenderas).
Kubernetes har stöd för [Pod-tillhörighet](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)  som kan schemalägga poddar på samma nod. Ett podAffinity-avsnitt som refererar till modulen för video analys i real tid kan läggas till i pod-manifestet för att uppnå samplacering.

    ```json   
    // Example Live Video Analytics module deployment match labels
    selector:
      matchLabels:
        net.azure-devices.edge.deviceid: dev-ase-1-edge
        net.azure-devices.edge.module: mediaedge
    
    // Example Inference deployment manifest pod affinity
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: net.azure-devices.edge.module
                operator: In
                values:
                - mediaedge
            topologyKey: "kubernetes.io/hostname"
    ```
* **404-felkod vid användning av `rtspsim` modul**  
Behållaren kommer att läsa videor från exakt en mapp i behållaren. Om du mappar/binder en extern mapp till den som redan finns i behållar avbildningen kommer Docker att dölja de filer som finns i behållar avbildningen.  
 
    Till exempel, utan bindningar kan behållaren ha följande filer:  
    ```
    root@rtspsim# ls /live/mediaServer/media  
    /live/mediaServer/media/camera-300s.mkv  
    /live/mediaServer/media/win10.mkv  
    ```
     
    Och värden kan ha följande filer:
    ```    
    C:\MyTestVideos> dir
    Test1.mkv
    Test2.mkv
    ```
     
    Men när följande bindning läggs till i distributions manifest filen kommer Docker att skriva över innehållet i/live/mediaServer/media för att matcha det som finns på värden.
    `C:\MyTestVideos:/live/mediaServer/media`
    
    ```
    root@rtspsim# ls /live/mediaServer/media
    /live/mediaServer/media/Test1.mkv
    /live/mediaServer/media/Test2.mkv
    ```

## <a name="next-steps"></a>Nästa steg

Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa direkta metoder. [Anropa de direkta metoderna](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls) i modulen.