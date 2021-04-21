---
title: Metodtips för klustersäkerhet
titleSuffix: Azure Kubernetes Service
description: Lär dig metodtips för klusteroperatören för att hantera klustersäkerhet och uppgraderingar i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 5cb103d843aafbb7f72c03d65b45fe3a84f8d1cd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782989"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Metodtips för klustersäkerhet och uppgraderingar i Azure Kubernetes Service (AKS)

När du hanterar kluster i Azure Kubernetes Service (AKS) är arbetsbelastning och datasäkerhet en viktig faktor. När du kör kluster med flera innehavare med logisk isolering behöver du särskilt skydda åtkomsten till resurser och arbetsbelastningar. Minimera risken för angrepp genom att använda de senaste säkerhetsuppdateringarna för Kubernetes och node-operativsystemet.

Den här artikeln fokuserar på hur du skyddar ditt AKS-kluster. Lär dig att:

> [!div class="checklist"]
> * Använd Azure Active Directory och Kubernetes rollbaserad åtkomstkontroll (Kubernetes RBAC) för att skydda API-serveråtkomst.
> * Skydda containeråtkomst till nodresurser.
> * Uppgradera ett AKS-kluster till den senaste Kubernetes-versionen.
> * Håll noderna uppdaterade och tillämpa säkerhetskorrigeringar automatiskt.

Du kan också läsa metodtipsen för [hantering av containeravbildningar][best-practices-container-image-management] och [poddsäkerhet.][best-practices-pod-security]

Du kan också använda [Azure Kubernetes Services-integrering med Security Center][security-center-aks] för att identifiera hot och visa rekommendationer för att skydda dina AKS-kluster.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Säker åtkomst till API-servern och klusternoder

> **Vägledning och metodtips** 
>
> Ett av de viktigaste sätten att skydda klustret är att skydda åtkomsten till Kubernetes API-servern. Om du vill styra åtkomsten till API-servern integrerar du Kubernetes RBAC med Azure Active Directory (Azure AD). Med de här kontrollerna skyddar du AKS på samma sätt som du skyddar åtkomsten till dina Azure-prenumerationer.

Kubernetes API-servern tillhandahåller en enda anslutningspunkt för begäranden om att utföra åtgärder i ett kluster. Om du vill skydda och granska åtkomsten till API-servern begränsar du åtkomsten och ger lägsta möjliga behörighetsnivå. Även om den här metoden inte är unik för Kubernetes, är den särskilt viktig när du logiskt har isolerat ditt AKS-kluster för användning med flera innehavare.

Azure AD tillhandahåller en företagsklar identitetshanteringslösning som integreras med AKS-kluster. Eftersom Kubernetes inte tillhandahåller en identitetshanteringslösning kan det vara svårt att begränsa åtkomsten till API-servern på ett detaljerad sätt. Med Azure AD-integrerade kluster i AKS använder du dina befintliga användar- och gruppkonton för att autentisera användare till API-servern.

![Azure Active Directory för AKS-kluster](media/operator-best-practices-cluster-security/aad-integration.png)

Med Kubernetes RBAC och Azure AD-integrering kan du skydda API-servern och ange de minsta behörigheter som krävs för en begränsad resursuppsättning, till exempel ett enda namnområde. Du kan bevilja olika Azure AD-användare eller grupper olika Kubernetes-roller. Med detaljerade behörigheter kan du begränsa åtkomsten till API-servern och ge en tydlig granskningslogg över åtgärder som utförts.

Rekommenderad metod är att använda grupper *för att* ge åtkomst till filer och mappar i stället för enskilda identiteter. Använd till exempel ett Azure *AD-gruppmedlemskap* för att binda användare till Kubernetes-roller i stället för enskilda *användare*. När en användares gruppmedlemskap ändras ändras deras åtkomstbehörigheter i AKS-klustret i enlighet med detta. 

Under tiden kan vi anta att du binder den enskilda användaren direkt till en roll och att deras jobbfunktion ändras. Medan Azure AD-gruppmedlemskap uppdateras skulle deras behörigheter på AKS-klustret inte göra det. I det här scenariot får användaren fler behörigheter än de behöver.

Mer information om Azure AD-integrering, Kubernetes RBAC och Azure RBAC finns i Metodtips för autentisering och [auktorisering i AKS.][aks-best-practices-identity]

## <a name="secure-container-access-to-resources"></a>Skydda containeråtkomst till resurser

> **Vägledning och metodtips** 
> 
> Begränsa åtkomsten till åtgärder som containrar kan utföra. Ange det minsta antalet behörigheter och undvik att använda rotåtkomst eller privilegierad eskalering.

På samma sätt som du bör ge användare eller grupper de lägsta behörigheter som krävs bör du även begränsa containrar till endast nödvändiga åtgärder och processer. Undvik att konfigurera program och containrar som kräver eskalerade privilegier eller rotåtkomst för att minimera risken för angrepp. 

Ange till exempel i `allowPrivilegeEscalation: false` poddmanifestet. Med de här inbyggda säkerhetskontexterna för Kubernetes-poddar kan du definiera ytterligare behörigheter, till exempel användaren eller gruppen som ska köras som eller de Linux-funktioner som ska *exponeras.* Mer metodtips finns i Skydda [poddåtkomst till resurser.][pod-security-contexts]

Om du vill ha ännu mer detaljerad kontroll över containeråtgärder kan du också använda inbyggda Linux-säkerhetsfunktioner som *AppArmor* och *seccomp.* 
1. Definiera Linux-säkerhetsfunktioner på nodnivå.
1. Implementera funktioner via ett poddmanifest. 

Inbyggda Linux-säkerhetsfunktioner är bara tillgängliga på Linux-noder och poddar.

> [!NOTE]
> Kubernetes-miljöer är för närvarande inte helt säkra för användning med flera innehavare. Ytterligare säkerhetsfunktioner som *AppArmor,* *seccomp,**Pod Security Policies* eller Kubernetes RBAC för noder blockerar effektivt sårbarheter. 
>
>För verklig säkerhet när du kör en arbetsbelastning med flera innehavare är det bara att lita på ett hypervisor-program. Säkerhetsdomänen för Kubernetes blir hela klustret, inte en enskild nod. 
>
> Du bör använda fysiskt isolerade kluster för dessa typer av hotande arbetsbelastningar för flera innehavare.

### <a name="app-armor"></a>AppSkydd

Om du vill begränsa containeråtgärder kan du använda [kernelsäkerhetsmodulen AppArmor][k8s-apparmor] Linux. AppArmor är tillgängligt som en del av det underliggande AKS-nodoperativsystemet och är aktiverat som standard. Du skapar AppArmor-profiler som begränsar läs-, skriv- eller körningsåtgärder eller systemfunktioner som montering av filsystem. AppArmor-standardprofiler begränsar åtkomsten till olika platser och ger ett sätt att `/proc` `/sys` logiskt isolera containrar från den underliggande noden. AppArmor fungerar för alla program som körs på Linux, inte bara Kubernetes-poddar.

![AppArmor-profiler som används i ett AKS-kluster för att begränsa containeråtgärder](media/operator-best-practices-container-security/apparmor.png)

Om du vill se Hur AppArmor fungerar i praktiken skapar följande exempel en profil som förhindrar skrivning till filer. 
1. [SSH till][aks-ssh] en AKS-nod.
1. Skapa en fil med *namnet deny-write.profile*.
1. Klistra in följande innehåll:

    ```
    #include <tunables/global>
    profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
      #include <abstractions/base>
  
      file,
      # Deny all file writes.
      deny /** w,
    }
    ```

AppArmor-profiler läggs till med `apparmor_parser` kommandot . 
1. Lägg till profilen i AppArmor.
1. Ange namnet på den profil som skapades i föregående steg:

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    Om profilen parsas korrekt och tillämpas på AppArmor visas inga utdata och du kommer tillbaka till kommandotolken.

1. Skapa ett poddmanifest med namnet *aks-apparmor.yaml* från din lokala dator. Det här manifestet:
    * Definierar en anteckning för `container.apparmor.security.beta.kubernetes` .
    * Refererar *till den neka-skriv-profil* som skapades i föregående steg.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: hello-apparmor
      annotations:
        container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
    spec:
      containers:
      - name: hello
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
    ```

1. När podden har distribuerats använder du kontrollera *att hello-apparmor-podden* visas *som blockerad:*

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

Mer information om AppArmor finns i [AppArmor-profiler i Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Säker databehandling

Även om AppArmor fungerar för alla Linux-program fungerar seccomp ( sec [ure *comp* uting)][seccomp] på processnivå. Seccomp är också en Linux-kernelsäkerhetsmodul och stöds inbyggt av Den Docker-körning som används av AKS-noder. Med seccomp kan du begränsa containerprocess-anrop. Anpassa dig till bästa praxis för att bevilja containern minimal behörighet att endast köras av:
* När du definierar med filtreras vilka åtgärder som ska tillåtas eller nekas.
* Kommentera i ett YAML-poddmanifest som ska associeras med seccomp-filtret. 

Om du vill se hur seccomp i praktiken används skapar du ett filter som förhindrar att behörigheter ändras för en fil. 
1. [SSH till][aks-ssh] en AKS-nod.
1. Skapa ett seccomp-filter *med namnet /var/lib/kubelet/seccomp/prevent-chmod*.
1. Klistra in följande innehåll:

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "name": "chmod",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "fchmodat",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "chmodat",
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

    I version 1.19 och senare måste du konfigurera följande:

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "names": ["chmod","fchmodat","chmodat"],
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

1. Skapa ett poddmanifest med namnet *aks-seccomp.yaml* från den lokala datorn och klistra in följande innehåll. Det här manifestet:
    * Definierar en anteckning för `seccomp.security.alpha.kubernetes.io` .
    * Refererar *till filtret prevent-chmod* som skapades i föregående steg.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
      annotations:
        seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
    spec:
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

    I version 1.19 och senare måste du konfigurera följande:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
    spec:
      securityContext:
        seccompProfile:
          type: Localhost
          localhostProfile: prevent-chmod
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

1. Distribuera exempelpodden med kommandot [kubectl apply:][kubectl-apply]

    ```console
    kubectl apply -f ./aks-seccomp.yaml
    ```

1. Visa poddstatus med [kommandot kubectl get pods.][kubectl-get] 
    * Podden rapporterar ett fel. 
    * Kommandot `chmod` hindras från att köras av seccomp-filtret, som du ser i följande exempelutdata:    

    ```
    $ kubectl get pods

    NAME                      READY     STATUS    RESTARTS   AGE
    chmod-prevented           0/1       Error     0          7s
    ```

Mer information om tillgängliga filter finns i [Seccomp-säkerhetsprofiler för Docker.][seccomp]

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Uppdatera regelbundet till den senaste versionen av Kubernetes

> **Vägledning och metodtips** 
> 
> Om du vill hålla dig uppdaterad om nya funktioner och felkorrigeringar uppgraderar du regelbundet Kubernetes-versionen i ditt AKS-kluster.

Kubernetes släpper nya funktioner snabbare än mer traditionella infrastrukturplattformar. Kubernetes-uppdateringar omfattar:
* Nya funktioner
* Fel- eller säkerhetskorrigeringar 

Nya funktioner går vanligtvis igenom *alfa- och* *betastatus* innan de blir *stabila.* När den är stabil är den allmänt tillgänglig och rekommenderas för produktionsanvändning. Med Kubernetes nya funktionsuppdateringscykel kan du uppdatera Kubernetes utan att regelbundet stöta på större ändringar eller justera dina distributioner och mallar.

AKS stöder tre mindre versioner av Kubernetes. När en ny mindre korrigeringsversion har introducerats dras de äldsta delversionerna och korrigeringsversionerna som stöds tillbaka. Mindre Kubernetes-uppdateringar sker regelbundet. Se till att du har en styrningsprocess för att söka efter nödvändiga uppgraderingar för att hålla dig inom supporten. Mer information finns i [Kubernetes-versioner av AKS som stöds.][aks-supported-versions]

Om du vill kontrollera vilka versioner som är tillgängliga för klustret använder du [kommandot az aks get-upgrades][az-aks-get-upgrades] enligt följande exempel:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Du kan sedan uppgradera ditt AKS-kluster med hjälp av [kommandot az aks upgrade.][az-aks-upgrade] Uppgraderingsprocessen på ett säkert sätt:
* Avspärrningar och tömmer en nod i taget.
* Schemalägger poddar på återstående noder.
* Distribuerar en ny nod som kör de senaste versionerna av operativsystemet och Kubernetes.

>[!IMPORTANT]
> Testa nya mindre versioner i en utvecklingstestmiljö och kontrollera att din arbetsbelastning förblir felfri med den nya Kubernetes-versionen. 
>
> Kubernetes kan göra API:er inaktuella (som i version 1.16) som dina arbetsbelastningar förlitar sig på. När du för in nya [](use-multiple-node-pools.md) versioner i produktion bör du överväga att använda flera nodpooler på separata versioner och uppgradera enskilda pooler en i taget för att progressivt distribuera uppdateringen i ett kluster. Om du kör flera kluster uppgraderar du ett kluster i taget för att progressivt övervaka påverkan eller ändringar.
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

Mer information om uppgraderingar i AKS finns i [Kubernetes-versioner som stöds i AKS][aks-supported-versions] och [Uppgradera ett AKS-kluster.][aks-upgrade]

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Bearbeta uppdateringar och omstarter av Linux-noder med hjälp av indelade

> **Vägledning och metodtips** 
> 
> AKS laddar automatiskt ned och installerar säkerhetskorrigeringar på varje Linux-nod, men startar inte om automatiskt. 
> 1. Använd `kured` för att hålla utkik efter väntande omstarter.
> 1. Avspärra och töm noden på ett säkert sätt så att noden kan startas om.
> 1. Tillämpa uppdateringarna.
> 1. Var så säker som möjligt med avseende på operativsystemet. 

För Windows Server-noder utför du regelbundet en AKS-uppgraderingsåtgärd för att på ett säkert sätt avspärra och tömma poddar och distribuera uppdaterade noder.

Varje kväll får Linux-noder i AKS säkerhetskorrigeringar via sin distributionsuppdateringskanal. Det här beteendet konfigureras automatiskt när noderna distribueras i ett AKS-kluster. Noder startas inte om automatiskt om en säkerhetskorrigering eller kerneluppdatering kräver det för att minimera avbrott och potentiell påverkan på arbetsbelastningar som körs.

Det felande projektet [(KBootnetes REboot Daemon)][kured] från Weaveworks söker efter väntande omstarter av noden. När en Linux-nod tillämpar uppdateringar som kräver en omstart, blir noden avspärrad och tömd för att flytta och schemalägga poddarna på andra noder i klustret. När noden har startats om läggs den till i klustret igen och Kubernetes återupptar poddschemaläggningen. För att minimera störningar tillåts endast en nod i taget att startas om av `kured` .

![Omstarten av AKS-noden med hjälp av en "kurerad"](media/operator-best-practices-cluster-security/node-reboot-process.png)

Om du vill ha ännu närmare kontroll över omstarter kan du integrera med Prometheus för att förhindra omstarter om det finns andra underhållshändelser eller `kured` klusterproblem som pågår. Den här integreringen minskar störningar genom att starta om noder medan du aktivt felsöker andra problem.

Mer information om hur du hanterar omstarter av noder finns i [Tillämpa säkerhets- och kerneluppdateringar på noder i AKS.][aks-kured]

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på hur du skyddar ditt AKS-kluster. Om du vill implementera några av dessa områden kan du läsa följande artiklar:

* [Integrera Azure Active Directory med AKS][aks-aad]
* [Uppgradera ett AKS-kluster till den senaste versionen av Kubernetes][aks-upgrade]
* [Bearbeta säkerhetsuppdateringar och nod-omstarter med hjälp av indelade][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: ./azure-ad-integration-cli.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: ../security-center/defender-for-kubernetes-introduction.md
