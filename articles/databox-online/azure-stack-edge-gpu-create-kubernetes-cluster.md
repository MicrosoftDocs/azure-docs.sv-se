---
title: Skapa och hantera ett Kubernetes-kluster på Azure Stack Edge Pro GPU-enhet | Microsoft Docs
description: Beskriver hur du skapar och hanterar ett Kubernetes-kluster på Azure Stack Edge Pro GPU-enhet via Windows PowerShell-gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/27/2021
ms.author: alkohli
ms.openlocfilehash: bf73640c1a4014b61809f407e1873686c229273f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954639"
---
# <a name="connect-to-and-manage-a-kubernetes-cluster-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Ansluta till och hantera ett Kubernetes-kluster via kubectl på din Azure Stack Edge Pro GPU-enhet

På din Azure Stack Edge Pro-enhet skapas ett Kubernetes-kluster när du konfigurerar Compute-rollen. När Kubernetes-klustret har skapats kan du ansluta till och hantera klustret lokalt från en klient dator via ett inbyggt verktyg, till exempel *kubectl*.

Den här artikeln beskriver hur du ansluter till ett Kubernetes-kluster på din Azure Stack Edge Pro-enhet och sedan hanterar den med *kubectl*. 


## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har åtkomst till en Azure Stack Edge Pro-enhet.

2. Du har aktiverat din Azure Stack Edge Pro-enhet enligt beskrivningen i [aktivera Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

3. Du har aktiverat Compute-rollen på enheten. Ett Kubernetes-kluster skapades också på enheten när du konfigurerade beräkningen på enheten enligt anvisningarna i [Konfigurera Compute på din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-configure-compute.md).

4. Du har åtkomst till ett Windows-klientsystem som kör PowerShell 5,0 eller senare för att få åtkomst till enheten. Du kan också ha andra klienter med ett [operativ system som stöds](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . 

5. Du har Kubernetes API-slutpunkten från **enhets** sidan i det lokala webb gränssnittet. Mer information finns i instruktionerna i [Get KUBERNETES API-slutpunkt](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)


## <a name="connect-to-powershell-interface"></a>Ansluta till PowerShell-gränssnittet

När Kubernetes-klustret har skapats kan du komma åt det här klustret för att skapa namn områden och användare och tilldela användare till namn områden. Detta kräver att du ansluter till PowerShell-gränssnittet på enheten. Följ de här stegen på Windows-klienten som kör PowerShell.

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]


## <a name="configure-cluster-access-via-kubernetes-rbac"></a>Konfigurera kluster åtkomst via Kubernetes RBAC

När Kubernetes-klustret har skapats kan du använda kommandot *kubectl* via cmdline för att få åtkomst till klustret. 

I den här metoden skapar du ett namn område och en användare. Sedan kopplar du användaren till namn området. Du måste också hämta en *konfigurations* fil som gör att du kan använda en Kubernetes-klient för att kommunicera direkt med Kubernetes-klustret som du skapade utan att behöva ansluta till PowerShell-gränssnittet för din Azure Stack Edge Pro-enhet.

1. Skapa ett namnområde. Ange:

    `New-HcsKubernetesNamespace -Namespace <string>` 

    > [!NOTE]
    > För både namn områdes-och användar namn gäller [konventionerna för namngivning av DNS-underdomäner](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) .

    Här är exempel på utdata:

    `[10.100.10.10]: PS> New-HcsKubernetesNamespace -Namespace "myasetest1"`

2. Skapa en användare och hämta en konfigurations fil. Ange:

    `New-HcsKubernetesUser -UserName <string>`

    > [!NOTE]
    > Du kan inte använda *aseuser* som användar namn eftersom det är reserverat för en standard användare som är associerad med IoT-namnrymden för Azure Stack Edge Pro.

    Här är ett exempel på utdata från konfigurations filen:
   
    ```powershell
    [10.100.10.10]: PS> New-HcsKubernetesUser -UserName "aseuser1"
        apiVersion: v1
        clusters:
        - cluster:
            certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01ERXlPVEUyTlRFeE4xb1hEVE13TURFeU5qRTJOVEV4TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXNpCkdZUHB0U1VWcDhKZEdVcHE1MVBURWhsZm8wWkk3YXFBdUlrOHZWdUFCZHJQK1FBSzFxcEN1di93NjIwbUtpZ0QKak1aT3Q4QkREREppWHF6UDZRZm5Oc0U2VXBHMnh0YnYrcTZHV2R5K0t6WkxMbXlwWGY3VjlzZEJnejVKVDNvYQpIdzFja2NTUklHSlV3UWxTbklNaHJUS3JUNDZFUUp3d282TmlNUzZMZDZieVk3WkUrTGg3OS9aNEhLanhTRmhMClc5ZG8veThZR3FXUDZmZTFmMmVmSkhUeGtwR05HZE1UVjNuOFlCZ0pSRzdrNjh0N2MrZ1NhbUlVWVJpTUNSNFAKYlFxcFpscWYvV2REZEJHOFh6aDJ0M1l4SkVIMm00T0Z1cSsvUitMYm95aHdKbmNMdVJ5OEpNZWlwTEQ3UlN0QwpZTDNNR0EzN2JieTRyYm4zVzg4Q0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNbzFwWlBtQzV1cmRPZUJhSWQ4eEQzRkxCMG8KTlErbXBXMWpDd0ZtY3h6dUtlWmRsNXc2N0tuS2JTcDR0TXo1cXg3bUtSc0UxcnBoWkh2VHlKUXg1ZFk2ZE1Kdgp5d2FQZjBpT05TNlU2cC9INE12U1dJaEtJZ1FuTnE1dDh4TjJCNnZpQW41RmZoRkx6WEQrUlZGSm42cnovWkZnCmV6MHpxTkNKYmcvelFucFROcmQ2cnFFRHpoSVFZOVdYVWQycFh3ZXRqUXJpMkpZamh4NmtEcTVoRkZTM0FLUnIKOWlQTVQxaWNkR1NUMFVvM1hIZ1k2ck45WGp3MHFrY2I0Sy83UlVVWlRvS3dKamROR3lNTnpad000L2puR0p5SwpQTE9ycU5Ddlkvb0lkVEM5eVZVY3VRbXVlR0VqT20xUnN1RDFHYVE0RTZwakppVWJpMVdrajJ1bFhOWT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
            server: https://10.128.47.90:6443
            name: kubernetes
        contexts:
        - context:
            cluster: kubernetes
            user: aseuser1
            name: aseuser1@kubernetes
        current-context: aseuser1@kubernetes
        kind: Config
        preferences: {}
        users:
        - name: aseuser1
            user:
            client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwVENDQWJtZ0F3SUJBZ0lJWlFXcjY2cGFWSm93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBeE1qa3hOalV4TVRkYUZ3MHlNVEF4TWpneU1qVTJNVGRhTUJNeApFVEFQQmdOVkJBTVRDR0Z6WlhWelpYSXhNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDCkFRRUF4R3RDaXJ2cVhGYm5wVmtaYlVPRWQ3cXg2UzNVZ092MlhHRHNKM2VYWXN0bUxQVjMrQnlBcWwyN211L1AKaWdmaWt6MG9QSW1iYmtvcVJkamlYckxFZnk0N3dHcEhzdUhUOHNLY2tHTnJleFE2bXhaZ29xaU1nL2FuMUpMdwpiOFEvVnlQaWdVdUt6eVBseEhUZmlmSVM5MzR1VnZVZUc0dzlMRjAyZ2s2Nitpc0ZtanhsVmhseWRMNlc2UmZTCjl0OGpNMEFkdEpJL0xNbE13RHJJRVdFKzM4WDVNelJhQkJYNnlzNDFWSkZxekcwdW14dHdxN2pGOXp1UTE4ekIKalRZaDl3OWVKcDJwS2Fvak5tNE9SSDh4SzVSaUhocjJ2anFJWXkxRDd2WDh0b0U1K05HNmxHZjh5L1NvQnNRbQpmOG9vL1k3SEZmQXVGdlN6WUc1RUlQTFM4UUlEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEClZSMGxCQXd3Q2dZSUt3WUJCUVVIQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNR1BxY0YzS1BCbHZ0K24KN1NOcGE3anhWYkhZVGxyNTgwVWxzek93WEwwVnVPUUlCYmN2djIzZk9HNkhDZlQ0bWxBU0JRWVNZcmpLMjJTTwpTWld4cjNQUDlhVzNHajkxc0ttSnc1ZUF1WFhQbUJpK1RWQzBvY0ZLaEQvZ0o1aC93YnBaVndpVjVyRWE5Kzc2CnhNcFAzRld6dG5tT1hPaEl6UFNlR3B4YWpwQXd3ZXd4QU0yb0xGRFZFcy9XTFFMODJZM3NFcE93NVNaSVJJNXMKUHhMUTVnV1ZPM2x2SXcwZ3IrdkJlanZSOUZKaWVuTWFRdGdjSVgyRmpDaDBRMHVYRkdsTVNXWEljbjRLRTR0TApQSFFMalRSVUwyVnRXcW1YZ1RBM3RzN01DcGNRTFdPZFJUYkpSejZCbkc1aXVwcDdOSlFvYW9YcWpNVk5DVDZCCllYMEd0Skk9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
            client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBeEd0Q2lydnFYRmJucFZrWmJVT0VkN3F4NlMzVWdPdjJYR0RzSjNlWFlzdG1MUFYzCitCeUFxbDI3bXUvUGlnZmlrejBvUEltYmJrb3FSZGppWHJMRWZ5NDd3R3BIc3VIVDhzS2NrR05yZXhRNm14WmcKb3FpTWcvYW4xSkx3YjhRL1Z5UGlnVXVLenlQbHhIVGZpZklTOTM0dVZ2VWVHNHc5TEYwMmdrNjYraXNGbWp4bApWaGx5ZEw2VzZSZlM5dDhqTTBBZHRKSS9MTWxNd0RySUVXRSszOFg1TXpSYUJCWDZ5czQxVkpGcXpHMHVteHR3CnE3akY5enVRMTh6QmpUWWg5dzllSnAycEthb2pObTRPUkg4eEs1UmlIaHIydmpxSVl5MUQ3dlg4dG9FNStORzYKbEdmOHkvU29Cc1FtZjhvby9ZN0hGZkF1RnZTellHNUVJUExTOFFJREFRQUJBb0lCQUVVSUVXM2kxMTQycU5raQo5RjNEWWZZV1pscTJZYjRoc0FjTmhWSGxwUTN5d0dsQ3FEUktDQ3BZSVF3MkJqSFR6WnpEM0xWU0E0K0NmMUxuCkE4QVdnaHJVcStsWE1QVzhpcG9DTGJaTlNzUUord0x3bld2dFl0MHFQaGZtd0p2M1UrK1RUQkwyOHNVVUw3ZVkKLzh0aWlhbno3ZU5mNklIMENyZmgxcnQ3WWhsemtRd1hBVHNScVJja0dMaTgrdGN5WnVzdGFhbENUSzBGRTdCaQpBUGE5a2w1SG56eCs4TTcvNWladHkwTUIxYWpWMnlGblBkUmlKSFVCb1AxVVV0QUthYjVZU0RvNllkZ2pIUTRHCjNWN1l1YWZobnVFMXA0VVIvUkloVVdjRlVVaTFBOFpZMFdnd1BDTmhnMWpQZU5vb2Y1UHpRbEY1OTRBREVwUUYKOFR2bG92RUNnWUVBOWZZbUxyY0tlQ0JiMTFoQVhoTi91Z1RTbU5xNnpFL1pPSWl6M0xwckdjRDhvWDdCVW9GcgplelkxbktSS2tkczE5OERnVjlQZUhuNzllQTRoMjM5RkIwNFFhMUJBdUVMRzRsdHJ3VlNxaFBENUR6YkcrSEhSCnJtYThVMEpUSmVVS0tJVjRUUGxlTzFtK2tjbkRJVXY1ckpwZDVXU3RvcUhXdk9RZkEvRUF0VlVDZ1lFQXpHOTcKTitCZVVvbFNiREttVUNGdTdPZGhYSXJYR3RnSEorZ2JOMDlnSHRURG5PY0IxZ1NzNkpZa1FPQU9qbWFxK05lRAp5SUF1NytheWlFRmpyT2tzTGhkSTREUXNkWFZveFFGVko1V1JwWlk3UTVRaFZpYUR2enR4NDlzSDlKSkplM2U0Cnl3NWdpNGkxKy90MnY2eWRKcWdNQ0xxOHlEdFRrcE9PSitkbkp5MENnWUJwZ3lpcURaZU9KTU9CUTdpSkl2QSsKQ21lVmJ1K0hTaEd6TU9HSHBPamc2V3IybEh1Mk94S3lqblM5TjdWTmtLNDhGQitwVFpnUm1RUi9CZ0Q4T2tLUQplYXFOZnFYazViQ1AxZ3dKcVpwazRVTFdoZmNoQ1NLY0lESlZ2VFFTSTRrU0RQK29kYWs0Nkt6WnVhWGRtTXdJCmdVZ2FhZkFhdmpaeVhhSDRmT0NDNlFLQmdHVXJCaDh3dVh5KzJEc1RGWnF4OE9McjNoS2Q0clUyRXRSODJIc1cKbk1xbEgraVZxU0x3VFdFTWJBUnUzTVU3cVlCYnBxdWlRNWdVNG1UcmR4Z3FpK0tEUTEwd2RJL3IrbDBEdTlCTApCRGlkajlaeGg4M0tZWWhSTXBzLzJULys1TDVsRU4zcnozczl2RkZtcisxS3pycENqeklDdDBtZmtrd0hHV0pGCjhaWkJBb0dBVXB3aUIrcWlHbkpxU1FtZHNSZFVabGFBaTRpbGhaa01RYTRHem95ZFQ3OTVHTm44ZThBRjd3WHMKTGpyYjdEV1FwakdCMnZpUlkySUZBVmIyKzZsdDlwOVJRMTZnSmxpNU5ZRXVvQWRoWXBsVWdBZGFHWHNGNHdabwo3SHFHTHBGdmUxVU5Gb0dQdkxpWUNrUFVYdGduQ3dNb0R2SEpKNzVYMXl6ckh6cmxUS1k9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==  
        
    [10.100.10.10]: PS>
    ```
    

3. En konfigurations fil visas som oformaterad text. Kopiera den här filen och spara den som en *konfigurations* fil. 

    > [!IMPORTANT]
    > Spara inte config-filen som *. txt* -fil, spara filen utan fil namns tillägg.

4. Konfigurations filen ska finnas i `.kube` mappen i din användar profil på den lokala datorn. Kopiera filen till mappen i din användar profil.

    ![Plats för konfigurations filen på klienten](media/azure-stack-edge-j-series-create-kubernetes-cluster/location-config-file.png)

5. Koppla namn området till den användare som du skapade. Ange:

    `Grant-HcsKubernetesNamespaceAccess -Namespace <string> -UserName <string>`

    Här är exempel på utdata:

    `[10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace "myasetest1" -UserName "aseuser1"`

    När du har konfigurations filen behöver du inte fysisk åtkomst till klustret. Om klienten kan pinga Azure Stack Edge Pro-enhetens IP-adress, bör du kunna dirigera klustret med *kubectl* -kommandon.

6. Starta en ny PowerShell-session på klienten. Du behöver inte vara ansluten till enhets gränssnittet. Nu kan du installera `kubectl` på klienten med följande kommando:

    ```powershell
    PS C:\windows\system32> curl https://storage.googleapis.com/kubernetes-release/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    
    PS C:\windows\system32>
    ```
    Om till exempel Kubernetes-huvudnoden körde v 1.15.2, installerar du v 1.15.2 på klienten.

    > [!IMPORTANT]
    > Ladda ned en klient som inte skiljer sig mer än en mindre version från huvudversionen. Klient versionen men kan leda till att huvud servern får en lägre version. Till exempel bör en v 1.3-huvud fungera med v 1.1-, v 1.2-och v 1.3-noder och bör fungera med v 1.2-, v 1.3-och v 1.4-klienter. Mer information om Kubernetes-klientens version finns i [support policy för Kubernetes version och versions förvrängning](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew). Mer information om Kubernetes Server-versionen på Azure Stack Edge Pro finns i Hämta Kubernetes Server version.<!-- insert link-->
    > Ibland `kubectl` är det förinstallerat i systemet om du kör Docker för Windows eller andra verktyg. Det är viktigt att ladda ned den aktuella versionen av `kubectl` som anges i det här avsnittet för att arbeta med det här Kubernetes-klustret. 

    Det tar flera minuter att installera installationen.

7. Kontrol lera att den installerade versionen är den som du laddade ned. Du bör ange den absoluta sökvägen till platsen där `kubectl.exe` har installerats i systemet.
    
    ```powershell
    PS C:\Users\myuser> C:\windows\system32\kubectl.exe version
    Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
    Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
    PS C:\Users\myuser>
    ``` 
    
    Mer information om `kubectl` kommandon som används för att hantera Kubernetes-klustret finns i [Översikt över kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

8. Lägg till en DNS-post i värd filen på systemet. 

    1. Kör anteckningar som administratör och öppna `hosts` filen som finns på `C:\windows\system32\drivers\etc\hosts` . 
    2. Använd den information som du sparade från sidan **enhet** i det lokala användar gränssnittet i föregående steg för att skapa posten i hosts-filen. 

        Kopiera till exempel den här slut punkten `https://compute.asedevice.microsoftdatabox.com/[10.100.10.10]` för att skapa följande post med IP-adressen för enheten och DNS-domänen: 

        `10.100.10.10 compute.asedevice.microsoftdatabox.com`

9. Kontrol lera att du kan ansluta till Kubernetes-poddar genom att skriva:
    
    ```powershell
    PS C:\Users\myuser> kubectl get pods -n "myasetest1"
    No resources found.
    PS C:\Users\myuser>
    ```
Nu kan du distribuera dina program i namn området och sedan Visa dessa program och deras loggar.

> [!IMPORTANT]   
> Det finns många kommandon som du inte kan köra, till exempel de kommandon som kräver administratörs åtkomst. Du kan bara utföra åtgärder som är tillåtna för namnområdet.


## <a name="remove-kubernetes-cluster"></a>Ta bort Kubernetes-kluster

Om du vill ta bort Kubernetes-klustret måste du ta bort IoT Edge-konfigurationen.

Detaljerade anvisningar finns i [ta bort IoT Edge konfiguration](azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).
   

## <a name="next-steps"></a>Nästa steg

- [Distribuera ett tillstånds lösa program på Azure Stack Edge Pro](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
