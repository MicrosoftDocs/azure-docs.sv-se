---
title: Aktivera ett Edge container Registry på Azure Stack Edge Pro GPU-enhet
description: Beskriver hur du aktiverar ett lokalt gräns behållar register på Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: f19625f1d558071ccb29487efe56146756c7692c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437544"
---
# <a name="enable-edge-container-registry-on-your-azure-stack-edge-pro-gpu-device"></a>Aktivera Edge container Registry på Azure Stack Edge Pro GPU-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

I den här artikeln beskrivs hur du aktiverar Edge container Registry och använder det inifrån Kubernetes-klustret på din Azure Stack Edge Pro-enhet. Exemplet som används i artikeln beskriver hur du skickar en avbildning från ett käll register, i det här fallet Microsoft container Registry, till registret på den Azure Stack gräns enheten, gräns behållar registret.

### <a name="about-edge-container-registry"></a>Om Edge container Registry

Program för beräkning av behållare körs på behållar avbildningar och de här avbildningarna lagras i registren. Register kan vara offentliga, till exempel Docker hubb, privat eller molnbaserad provider som Azure Container Registry. Mer information finns i [om register, databaser och avbildningar](../container-registry/container-registry-concepts.md).

Ett kant behållar register tillhandahåller en lagrings plats på gränsen, på din Azure Stack Edge Pro-enhet. Du kan använda det här registret för att lagra och hantera dina privata behållar avbildningar.

I en miljö med flera noder kan behållar avbildningar hämtas och skickas vidare till gräns container registret en gång. Alla Edge-program kan använda Edge container Registry för efterföljande distributioner.


## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har åtkomst till en Azure Stack Edge Pro-enhet.

1. Du har aktiverat din Azure Stack Edge Pro-enhet enligt beskrivningen i [aktivera Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

1. Du har aktiverat Compute-rollen på enheten. Ett Kubernetes-kluster skapades också på enheten när du konfigurerade beräkningen på enheten enligt anvisningarna i [Konfigurera Compute på din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Du har Kubernetes API-slutpunkten från **enhets** sidan i det lokala webb gränssnittet. Mer information finns i anvisningarna i [Get KUBERNETES API-slutpunkt](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).

1. Du har åtkomst till ett klient system med ett [operativ system som stöds](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). Om du använder en Windows-klient ska systemet köra PowerShell 5,0 eller senare för att få åtkomst till enheten.

    1. Om du vill hämta och skicka egna behållar avbildningar kontrollerar du att datorn har Docker-klienten installerad. Om du använder en Windows-klient [installerar du Docker Desktop i Windows](https://docs.docker.com/docker-for-windows/install/).  


## <a name="enable-container-registry-as-add-on"></a>Aktivera container Registry som tillägg

Det första steget är att aktivera Edge container Registry som ett tillägg.

1. [Anslut till enhetens PowerShell-gränssnitt](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

1. Om du vill aktivera behållar registret som ett tillägg skriver du: 

    `Set-HcsKubernetesContainerRegistry`
    
    Den här åtgärden kan ta flera minuter att slutföra.

    Här är exempel resultatet av det här kommandot:  
            
    ```powershell
    [10.128.44.40]: PS>Set-HcsKubernetesContainerRegistry
    Operation completed successfully. Use Get-HcsKubernetesContainerRegistryInfo for credentials    
    ```
            
1. Hämta information om behållar registret genom att skriva:

    `Get-HcsKubernetesContainerRegistryInfo`

    Här är exempel på det här kommandot:  
    
    ```powershell
    [10.128.44.40]: PS> Get-HcsKubernetesContainerRegistryInfo
                
    Endpoint                                   IPAddress    Username     Password
    --------                                   ---------    --------     --------
    ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 10.128.44.41 ase-ecr-user i3eTsU4zGYyIgxV
    ``` 

1. Anteckna användar namnet och lösen ordet från utdata från `Get-HcsKubernetesContainerRegistryInfo` . Dessa autentiseringsuppgifter används för att logga in på Edge container Registry när avbildningar skickas.         


## <a name="manage-container-registry-images"></a>Hantera behållare register avbildningar

Du kanske vill komma åt behållar registret utanför Azure Stack Edge-enheten. Du kanske också vill skicka eller ta emot avbildningar i registret.

Följ de här stegen för att få åtkomst till Edge container Registry:

1. Hämta slut punkts informationen för Edge container Registry.
    1. I enhetens lokala användar gränssnitt går du till **enhet**.
    1. Leta upp **slut punkts registret för Edge container**.
        ![Slut container Registry-slutpunkt på enhets sida](media/azure-stack-edge-gpu-edge-container-registry/get-edge-container-registry-endpoint-1.png) 
    1. Kopiera den här slut punkten och skapa en motsvarande DNS-post i `C:\Windows\System32\Drivers\etc\hosts` filen för klienten för att ansluta till slut punktens register slut punkt. 

        <IP address of the Kubernetes main node>    <Edge container registry endpoint> 
        
        ![Lägg till DNS-post för Edge container Registry-slutpunkt](media/azure-stack-edge-gpu-edge-container-registry/add-domain-name-service-entry-hosts-1.png)    

1. Ladda ned gräns container register certifikatet från det lokala användar gränssnittet. 
    1. I enhetens lokala användar gränssnitt går du till **certifikat**.
    1. Leta upp posten för **gräns containerns register certifikat**. Till höger om den här posten väljer du **Hämta** för att ladda ned gräns containerns register certifikat på klient systemet som du använder för att få åtkomst till din enhet. 

        ![Hämta slut punkts certifikat för gräns containerns register](media/azure-stack-edge-gpu-edge-container-registry/download-edge-container-registry-endpoint-certificate-1.png)  

1. Installera det hämtade certifikatet på klienten. Följ dessa steg om du använder en Windows-klient: 
    1. Välj certifikatet och i **guiden Importera certifikat** väljer du lagra plats som **lokal dator**. 

        ![Installera certifikat 1](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. Installera certifikatet på den lokala datorn i det betrodda rot arkivet. 

        ![Installera certifikat 2](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 

1. När certifikatet har installerats startar du om Docker-klienten i systemet.

1. Logga in på Edge container Registry. Ange:

    `docker login <Edge container registry endpoint> -u <username> -p <password>`

    Ange slut punkts registrets slut punkt från sidan **enheter** och det användar namn och lösen ord som du fick från utdata från `Get-HcsKubernetesContainerRegistryInfo` . 

1. Använd Docker push-eller pull-kommandon för att skicka eller ta emot behållar avbildningar från behållar registret.
 
    1. Hämta en bild från Microsoft Container Registry-avbildningen. Ange:
        
        `docker pull <Full path to the container image in the Microsoft Container Registry>`
       
    1. Skapa ett alias för den avbildning som du hämtade med den fullständigt kvalificerade sökvägen till registret.

        `docker tag <Path to the image in the Microsoft container registry> <Path to the image in the Edge container registry/Image name with tag>`

    1. Push-överför avbildningen till registret.
    
        `docker push <Path to the image in the Edge container registry/Image name with tag>`

    1. Kör avbildningen som du har överfört till registret.
    
        `docker run -it --rm -p 8080:80 <Path to the image in the Edge container registry/Image name with tag>`

        Här är ett exempel på utdata från Pull-och push-kommandona:

        ```powershell
        PS C:\WINDOWS\system32> docker login ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 -u ase-ecr-user -p 3bbo2sOtDe8FouD
        WARNING! Using --password via the CLI is insecure. Use --password-stdin.
        Login Succeeded
        PS C:\WINDOWS\system32> docker pull mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        1.17.5-alpine: Pulling from oss/nginx/nginx
        Digest: sha256:5466bbc0a989bd1cd283c0ba86d9c2fc133491ccfaea63160089f47b32ae973b
        Status: Image is up to date for mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        PS C:\WINDOWS\system32> docker tag mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        PS C:\WINDOWS\system32> docker push ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        The push refers to repository [ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx]
        bba7d2385bc1: Pushed
        77cae8ab23bf: Pushed
        2.0: digest: sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff size: 739
        PS C:\WINDOWS\system32> docker run -it --rm -p 8080:80 ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        2020/11/10 00:00:49 [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8080", referrer: "http://localhost:8080/"
        172.17.0.1 - - [10/Nov/2020:00:00:49 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8080/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36" "-"
        ^C
        PS C:\WINDOWS\system32>    
        ```
    
1. Bläddra till `http://localhost:8080` för att Visa behållaren som körs. I det här fallet visas nginx-webbservern som kör.

    ![Visa behållaren som körs](media/azure-stack-edge-gpu-edge-container-registry/view-running-container-1.png)

    Om du vill stoppa och ta bort behållaren trycker du på `Control+C` .

 

## <a name="use-edge-container-registry-images-via-kubernetes-pods"></a>Använd Edge container Registry-avbildningar via Kubernetes poddar

Nu kan du distribuera avbildningen som du har överfört i din Edge container Registry inifrån Kubernetes-poddar.

1. Om du vill distribuera avbildningen måste du konfigurera kluster åtkomst via *kubectl*. Skapa ett namn område, en användare, bevilja användare åtkomst till namn området och hämta en *konfigurations* fil. Kontrol lera att du kan ansluta till Kubernetes-poddar. 
    
    Följ alla steg i [ansluta till och hantera ett Kubernetes-kluster via kubectl på din Azure Stack Edge Pro GPU-enhet](azure-stack-edge-gpu-create-kubernetes-cluster.md). 

    Här är ett exempel på utdata för ett namn område på enheten från vilken användaren kan komma åt Kubernetes-klustret.

    ```powershell
    [10.128.44.40]: PS>New-HcsKubernetesNamespace -Namespace myecr
    [10.128.44.40]: PS>New-HcsKubernetesUser -UserName ecruser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01URXdOVEF6TkRJek1Gb1hEVE13TVRFd016QXpOREl6TUZvd0ZURVRNQkVnNjOVRLWndCQ042cm1XQms2eXFwcXI1MUx6bApTaXMyTy91UEJ2YXNSSUUzdzgrbmEwdG1aTERZZ2F6MkQwMm42Q29mUmtyUTR2d1lLTnR1MlpzR3pUdz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.dbe-hw6h1t2.microsoftdatabox.com:6443
        name: kubernetes
        ===================CUT=========================================CUT==============
        client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJYmVWRGJSTzZ3ell3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURFeE1EVXdNelF5TXpCYUZ3MHlNVEV4TURreU16UTRNal
        ===================CUT=========================================CUT==============
        DMVUvN3lFOG5UU3k3b2VPWitUeHdzCjF1UDByMjhDZ1lCdHdRY0ZpcFh1blN5ak16dTNIYjhveFI2V3VWWmZldFFKNElKWEFXOStVWGhKTFhyQ2x4bUcKWHRtbCt4UU5UTzFjQVNKRVZWVDd6Tjg2ay9kSU43S3JIVkdUdUxlUDd4eGVjV2VRcWJrZEVScUsxN0liTXpiVApmbnNxc0dobEdmLzdmM21kTGtyOENrcWs5TU5aM3MvUVIwRlFCdk94ZVpuUlpTeDVGbUR5S1E9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

    [10.128.44.40]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myecr -UserName ecruser
    [10.128.44.40]: PS>kubectl get pods -n "myecr"
    No resources found.
    PS C:\WINDOWS\system32>
    ```  

2. Bildens pull-hemligheter har redan angetts i alla Kubernetes-namnområden på enheten. Du kan få hemligheter genom att använda `get secrets` kommandot. Här är exempel på utdata:

    ```powershell
    PS C:\WINDOWS\system32> .\kubectl.exe get secrets -n myecr
    NAME                  TYPE                                  DATA   AGE
    ase-ecr-credentials   kubernetes.io/dockerconfigjson        1      99m
    default-token-c7kww   kubernetes.io/service-account-token   3      107m
    sec-smbcredentials    microsoft.com/smb                     2      99m
    PS C:\WINDOWS\system32>   
    ```    

3. Distribuera en POD till ditt namn område med hjälp av kubectl. Använd följande `yaml` . 

    Ersätt avbildningen: `<image-name>` när avbildningen flyttas till behållar registret. Referera till hemligheterna i dina namn områden med imagePullSecrets med ett namn: `ase-ecr-credentials` .
    
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        imagePullPolicy: Always
      imagePullSecrets:
      - name: ase-ecr-credentials
    ```

4. Tillämpa distributionen i namn området som du skapade med kommandot Apply. Kontrol lera att behållaren körs. Här är exempel på utdata:
   
    ```yml
    PS C:\Windows\System32> .\kubectl.exe apply -f .\deployment.yml -n myecr
    pod/nginx configured
    PS C:\Windows\System32> .\kubectl.exe get pods -n myecr
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          27m
    PS C:\Windows\System32>
    ```

## <a name="delete-container-registry-images"></a>Ta bort behållare register avbildningar

Edge Container Registry Storage finns på en lokal resurs i Azure Stack Edge Pro-enhet som är begränsad av tillgängligt lagrings utrymme på enheten. Det är ditt ansvar att ta bort oanvända Docker-avbildningar från behållar registret med Docker HTTP v2 API ( https://docs.docker.com/registry/spec/api/) .  

Följ dessa steg om du vill ta bort en eller flera behållar avbildningar:

1. Ange avbildnings namnet till den avbildning som du vill ta bort.

    ```powershell
    PS C:\WINDOWS\system32> $imageName="nginx"    
    ```

1. Ange användar namn och lösen ord för behållar registret som PS-autentiseringsuppgift

    ```powershell
    PS C:\WINDOWS\system32> $username="ase-ecr-user"
    PS C:\WINDOWS\system32> $password="3bbo2sOtDe8FouD"
    PS C:\WINDOWS\system32> $securePassword = ConvertTo-SecureString $password -AsPlainText -Force
    PS C:\WINDOWS\system32> $credential = New-Object System.Management.Automation.PSCredential ($username, $securePassword)    
    ```

1. Lista de taggar som är associerade med avbildningen

    ```powershell
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/nginx/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32>    
    ```
  
1. Ange den sammanfattning som är associerad med den tagg som du vill ta bort. Detta använder $tags från kommandot utdata från kommando. Om du har flera taggar väljer du en av dem och använder den i nästa kommando.

    ```powershell
    PS C:\WINDOWS\system32> $response = Invoke-WebRequest -Method Head -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$tags" -Headers @{ 'Accept' = 'application/vnd.docker.distribution.manifest.v2+json' }
    PS C:\WINDOWS\system32> $digest = $response.Headers['Docker-Content-Digest']
    PS C:\WINDOWS\system32> $digest
    sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff
    PS C:\WINDOWS\system32>    
    ```
1. Ta bort bilden med sammanfattningen av avbildningen: tagg

    ```powershell
    PS C:\WINDOWS\system32> Invoke-WebRequest -Method Delete -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$digest" | Select-Object -ExpandProperty StatusDescription    
    ```

När du har tagit bort de oanvända bilderna frigörs utrymmet som är kopplat till de oanvända bilderna automatiskt av en process som körs natt. 

## <a name="next-steps"></a>Nästa steg

- [Distribuera ett tillstånds lösa program på Azure Stack Edge Pro](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
