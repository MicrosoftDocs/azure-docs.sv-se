---
title: Metod tips för kluster säkerhet
titleSuffix: Azure Kubernetes Service
description: Lär dig metod tips för kluster operatörer för att hantera kluster säkerhet och uppgraderingar i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: ea63db2d8868a1333ae264c9cfdf31d0b7397a83
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105161"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Metod tips för kluster säkerhet och uppgraderingar i Azure Kubernetes service (AKS)

När du hanterar kluster i Azure Kubernetes service (AKS) är arbets belastningen och data säkerheten en viktig faktor. När du kör kluster med flera klienter med hjälp av logisk isolering måste du särskilt skydda resurs-och arbets belastning. Minimera risken för angrepp genom att använda de senaste säkerhets uppdateringarna för Kubernetes och Node OS.

Den här artikeln fokuserar på hur du skyddar ditt AKS-kluster. Lär dig att:

> [!div class="checklist"]
> * Använd Azure Active Directory-och Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC) för att skydda åtkomst till API-servern.
> * Skydda behållar åtkomsten till Node-resurser.
> * Uppgradera ett AKS-kluster till den senaste versionen av Kubernetes.
> * Se till att noderna är aktuella och tillämpa säkerhets korrigeringar automatiskt.

Du kan också läsa metod tips för [hantering av behållar avbildningar][best-practices-container-image-management] och [Pod säkerhet][best-practices-pod-security].

Du kan också använda [Azure Kubernetes Services-integrering med Security Center][security-center-aks] för att identifiera hot och se rekommendationer för att skydda dina AKS-kluster.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Säker åtkomst till API-servern och klusternoder

> **Vägledning och metodtips** 
>
> Ett av de viktigaste sätten att skydda klustret är att skydda åtkomsten till Kubernetes-API-servern. Om du vill kontrol lera åtkomsten till API-servern integrerar du Kubernetes RBAC med Azure Active Directory (Azure AD). Med de här kontrollerna skyddar du AKS på samma sätt som du skyddar åtkomsten till dina Azure-prenumerationer.

Kubernetes-API-servern tillhandahåller en enda anslutnings punkt för förfrågningar om att utföra åtgärder inom ett kluster. Begränsa åtkomsten och ge lägsta möjliga behörighets nivå för att skydda och granska åtkomst till API-servern. den här metoden är inte unik för Kubernetes, men det är särskilt viktigt när du har delat ditt AKS-kluster för användning med flera innehavare.

Azure AD tillhandahåller en företags klar identitets hanterings lösning som kan integreras med AKS-kluster. Eftersom Kubernetes inte tillhandahåller någon identitets hanterings lösning kan du vara hårt intryckt för att begränsa åtkomsten till API-servern i detalj. Med Azure AD-integrerade kluster i AKS använder du dina befintliga användar-och grupp konton för att autentisera användare till API-servern.

![Azure Active Directory integrering för AKS-kluster](media/operator-best-practices-cluster-security/aad-integration.png)

Med Kubernetes RBAC och Azure AD-integration kan du skydda API-servern och ange de lägsta behörigheter som krävs för en begränsad resurs uppsättning, t. ex. ett enda namn område. Du kan bevilja olika Azure AD-användare eller grupper olika Kubernetes-roller. Med detaljerade behörigheter kan du begränsa åtkomsten till API-servern och tillhandahålla en tydlig gransknings spårning av åtgärder som utförs.

Den rekommenderade bästa metoden är att använda *grupper* för att ge åtkomst till filer och mappar i stället för enskilda identiteter. Du kan till exempel använda ett Azure *AD-* gruppmedlemskap för att binda användare till Kubernetes-roller i stället för enskilda *användare*. När en användares grupp medlemskap ändras ändras deras åtkomst behörigheter för AKS-klustret enligt detta. 

Samtidigt kan vi säga att du binder den enskilda användaren direkt till en roll och att deras jobb funktion ändras. Medan Azure AD Group-medlemskapet uppdateras skulle inte deras behörigheter i AKS-klustret. I det här scenariot slutförs användaren med fler behörigheter än vad som krävs.

Mer information om Azure AD-integrering, Kubernetes RBAC och Azure RBAC finns i [metod tips för autentisering och auktorisering i AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Skydda behållar åtkomst till resurser

> **Vägledning och metodtips** 
> 
> Begränsa åtkomsten till åtgärder som behållare kan utföra. Ange minst antal behörigheter och Undvik att använda rot åtkomst eller privilegie rad eskalering.

På samma sätt som du ska bevilja användare eller grupper minimi kraven krävs bör du också begränsa behållare till endast nödvändiga åtgärder och processer. Undvik att konfigurera program och behållare som kräver eskalerade privilegier eller rot åtkomst för att minimera risken för angrepp. 

Till exempel anges `allowPrivilegeEscalation: false` i pod-manifestet. Med dessa inbyggda Kubernetes *Pod säkerhets kontexter* kan du definiera ytterligare behörigheter, till exempel vilken användare eller grupp som ska köras som eller vilka Linux-funktioner som ska exponeras. Mer metod tips finns i [skydda Pod-åtkomst till resurser][pod-security-contexts].

Du kan även använda inbyggda Linux-säkerhetsfunktioner som *apparmor* och *seccomp* för ännu mer detaljerad kontroll över behållar åtgärder. 
1. Definiera Linux-säkerhetsfunktioner på Node-nivå.
1. Implementera funktioner via ett Pod-manifest. 

Inbyggda Linux-säkerhetsfunktioner är bara tillgängliga på Linux-noder och poddar.

> [!NOTE]
> För närvarande är Kubernetes-miljöer inte helt säkra för att ta skydd på flera klienter. Ytterligare säkerhetsfunktioner, t. ex. *apparmor*, *Seccomp*,*Pod Security Policies* eller Kubernetes RBAC för noder, kan effektivt blockera sårbarheter. 
>
>För verklig säkerhet vid körning av skydds arbets belastningar med flera klienter, är det bara att lita på en hypervisor. Säkerhets domänen för Kubernetes blir hela klustret, inte en enskild nod. 
>
> För dessa typer av farliga arbets belastningar med flera klienter bör du använda fysiskt isolerade kluster.

### <a name="app-armor"></a>App-skydd

Om du vill begränsa container åtgärder kan du använda [apparmor][k8s-apparmor] Linux kernel-säkerhetsmodulen. AppArmor är tillgänglig som en del av den underliggande AKS Node OS och är aktiverat som standard. Du skapar AppArmor-profiler som begränsar Läs-, Skriv-eller körnings åtgärder eller system funktioner som monterings fil system. Standard profiler för AppArmor begränsar åtkomsten till olika `/proc` `/sys` platser och platser, och ger ett sätt att isolera behållare logiskt från den underliggande noden. AppArmor fungerar för alla program som körs på Linux, inte bara Kubernetes poddar.

![AppArmor-profiler som används i ett AKS-kluster för att begränsa container åtgärder](media/operator-best-practices-container-security/apparmor.png)

Följande exempel skapar en profil som förhindrar skrivning till filer för att se AppArmor i praktiken. 
1. [SSH][aks-ssh] till en AKS-nod.
1. Skapa en fil med namnet *Deny-Write. Profile*.
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

AppArmor-profiler läggs till med hjälp av `apparmor_parser` kommandot. 
1. Lägg till profilen i AppArmor.
1. Ange namnet på den profil som skapades i föregående steg:

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    Om profilen är korrekt tolkad och tillämpas på AppArmor visas inga utdata och du kommer tillbaka till kommando tolken.

1. Skapa ett Pod-manifest med namnet *AKS-AppArmor. yaml* från den lokala datorn. Detta manifest:
    * Definierar en anteckning för `container.apparmor.security.beta.kubernetes` .
    * Refererar till den *nekade Skriv-* profil som skapades i föregående steg.

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

1. När Pod har distribuerats använder du kontrol lera att *Hello-apparmor-* Pod visas som *blockerade*:

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

Mer information om AppArmor finns [i AppArmor-profiler i Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Säker data behandling

Medan AppArmor fungerar för alla Linux-program fungerar [seccomp (*SEK* urera *comp* uting)][seccomp] på process nivå. Seccomp är också en Linux kernel-säkerhetsmodul och stöds internt av Docker-körningen som används av AKS-noder. Med seccomp kan du begränsa bearbetnings anropen för behållaren. Anpassa till bästa praxis för att ge containern minimal behörighet endast att köras av:
* Definiera med filter vilka åtgärder som ska tillåtas eller nekas.
* Kommentera i ett Pod YAML-manifest för att associera med seccomp-filtret. 

Om du vill se seccomp i praktiken skapar du ett filter som förhindrar ändring av behörigheter för en fil. 
1. [SSH][aks-ssh] till en AKS-nod.
1. Skapa ett seccomp-filter med namnet */var/lib/kubelet/seccomp/Prevent-chmod*.
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

    I version 1,19 och senare måste du konfigurera följande:

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

1. Skapa ett Pod-manifest med namnet *AKS-seccomp. yaml* och klistra in följande innehåll från den lokala datorn. Detta manifest:
    * Definierar en anteckning för `seccomp.security.alpha.kubernetes.io` .
    * Refererar till filtret för *förebyggande-chmod* som skapades i föregående steg.

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

    I version 1,19 och senare måste du konfigurera följande:

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

1. Distribuera exempel-Pod med kommandot [kubectl Apply][kubectl-apply] :

    ```console
    kubectl apply -f ./aks-seccomp.yaml
    ```

1. Visa Pod-status med kommandot [kubectl get poddar][kubectl-get] . 
    * Pod rapporterar ett fel. 
    * `chmod`Kommandot förhindras från att köras av seccomp-filtret, som visas i följande exempel på utdata:    

    ```
    $ kubectl get pods

    NAME                      READY     STATUS    RESTARTS   AGE
    chmod-prevented           0/1       Error     0          7s
    ```

Mer information om tillgängliga filter finns i [Seccomp Security profils for Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Uppdatera regelbundet till den senaste versionen av Kubernetes

> **Vägledning och metodtips** 
> 
> För att hålla dig uppdaterad om nya funktioner och fel korrigeringar kan du regelbundet uppgradera Kubernetes-versionen i ditt AKS-kluster.

Kubernetes släpper nya funktioner i snabbare takt än mer traditionella infrastruktur plattformar. Kubernetes-uppdateringar omfattar:
* Nya funktioner
* Fel-eller säkerhets korrigeringar 

Nya funktioner går vanligt vis igenom *alfa* -och *beta* status innan de blir *stabila*. När de är stabila är de allmänt tillgängliga och rekommenderas för produktions användning. Med Kubernetes nya funktions fri uppdaterings cykel kan du uppdatera Kubernetes utan att regelbundet kunna räkna upp ändringar eller ändra dina distributioner och mallar.

AKS stöder tre lägre versioner av Kubernetes. När en ny del version av korrigeringen har introducerats kommer den äldsta lägre versionen och de uppdateringar som stöds att dras tillbaka. Mindre Kubernetes-uppdateringar sker regelbundet. Se till att du har en styrnings process för att kontrol lera om det finns nödvändiga uppgraderingar i supporten. Mer information finns i [Kubernetes-versioner som stöds AKS][aks-supported-versions].

Om du vill kontrol lera vilka versioner som är tillgängliga för klustret använder du kommandot [AZ AKS get-uppgraderingar][az-aks-get-upgrades] som visas i följande exempel:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Sedan kan du uppgradera ditt AKS-kluster med hjälp av kommandot [AZ AKS Upgrade][az-aks-upgrade] . Uppgraderings processen på ett säkert sätt:
* Cordons och tömmer en nod i taget.
* Schemalägger poddar på återstående noder.
* Distribuerar en ny nod med de senaste OS-och Kubernetes-versionerna.

>[!IMPORTANT]
> Testa nya del versioner i en utvecklings test miljö och kontrol lera att arbets belastningen fortfarande är felfri med den nya Kubernetes-versionen. 
>
> Kubernetes kan använda API: er (som i version 1,16) som dina arbets belastningar förlitar sig på. När du ansluter nya versioner till produktion bör du överväga att använda [flera noder i olika versioner](use-multiple-node-pools.md) och uppgradera enskilda pooler en i taget för att progressivt återställa uppdateringen över ett kluster. Om du kör flera kluster bör du uppgradera ett kluster i taget för att progressivt övervaka eller ändra effekter.
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

Mer information om uppgraderingar i AKS finns i [Kubernetes-versioner som stöds i AKS][aks-supported-versions] och [uppgradera ett AKS-kluster][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Bearbeta uppdateringar och omstarter av Linux-noder med kured

> **Vägledning och metodtips** 
> 
> Medan AKS automatiskt laddar ned och installerar säkerhets korrigeringar på varje Linux-nod, startas den inte om automatiskt. 
> 1. Används `kured` för att titta efter väntande omstarter.
> 1. På ett säkert sätt Cordon och tömma noden så att noden kan startas om.
> 1. Tillämpa uppdateringarna.
> 1. Var så säker som möjligt med avseende på operativ systemet. 

För Windows Server-noder utför regelbundet en AKS uppgraderings åtgärd för att på ett säkert sätt Cordon och tömma poddar och distribuera uppdaterade noder.

Varje kväll får Linux-noder i AKS säkerhets korrigeringar via sin distribution Update-kanal. Det här beteendet konfigureras automatiskt när noderna distribueras i ett AKS-kluster. För att minimera störningar och potentiell påverkan på att köra arbets belastningar, startas inte noderna om automatiskt om en säkerhets korrigering eller kernel-uppdatering kräver det.

Kured-projektet med öppen källkod [(KUbernetes REboot)][kured] av Weaveworks söker efter väntande Node-omstarter. När en Linux-nod använder uppdateringar som kräver en omstart, är noden säkert avspärrade och töms för att flytta och schemalägga poddar på andra noder i klustret. När noden har startats om läggs den tillbaka i klustret och Kubernetes återupptar Pod-schemaläggning. För att minimera störningar får endast en nod i taget startas om `kured` .

![Startprocessen för AKS-noden med kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Om du vill ha ännu närmare kontroll över omstarter `kured` kan integreras med Prometheus för att förhindra omstarter om det finns andra underhålls händelser eller pågående kluster problem. Den här integrationen minskar risken för att minska risken genom att starta om noderna när du har fel sökning av andra problem.

Mer information om hur du hanterar omstarter av noder finns i [tillämpa säkerhets-och kernel-uppdateringar på noder i AKS][aks-kured].

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på hur du skyddar ditt AKS-kluster. Information om hur du implementerar vissa av dessa områden finns i följande artiklar:

* [Integrera Azure Active Directory med AKS][aks-aad]
* [Uppgradera ett AKS-kluster till den senaste versionen av Kubernetes][aks-upgrade]
* [Bearbeta säkerhets uppdateringar och omstarter av noder med kured][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
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
