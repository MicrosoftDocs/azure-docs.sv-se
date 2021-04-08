---
title: Metod tips för kluster säkerhet
titleSuffix: Azure Kubernetes Service
description: Lär dig metod tips för kluster operatörer för att hantera kluster säkerhet och uppgraderingar i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: a56cf35fe3780aa53b12581358bd91fe44e8c8a1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585068"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Metod tips för kluster säkerhet och uppgraderingar i Azure Kubernetes service (AKS)

När du hanterar kluster i Azure Kubernetes service (AKS) är säkerheten för dina arbets belastningar och data en viktig faktor. Särskilt när du kör kluster för flera klienter med hjälp av logisk isolering måste du skydda åtkomsten till resurser och arbets belastningar. För att minimera risken för angrepp måste du också se till att du tillämpar de senaste säkerhets uppdateringarna för Kubernetes och Node OS.

Den här artikeln fokuserar på hur du skyddar ditt AKS-kluster. Lär dig att:

> [!div class="checklist"]
> * Använd Azure Active Directory-och Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC) för att skydda åtkomst till API-servern
> * Skydda container åtkomst till nod resurser
> * Uppgradera ett AKS-kluster till den senaste Kubernetes-versionen
> * Håll noderna aktuella och tillämpa säkerhets korrigeringar automatiskt

Du kan också läsa metod tips för [hantering av behållar avbildningar][best-practices-container-image-management] och [Pod säkerhet][best-practices-pod-security].

Du kan också använda [Azure Kubernetes Services-integrering med Security Center][security-center-aks] för att identifiera hot och se rekommendationer för att skydda dina AKS-kluster.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Säker åtkomst till API-servern och klusternoder

**Rekommendationer för bästa praxis** – att säkra åtkomsten till Kubernetes API-Server är ett av de viktigaste sakerna du kan göra för att skydda klustret. Integrera Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC) med Azure Active Directory för att kontrol lera åtkomsten till API-servern. Med de här kontrollerna kan du skydda AKS på samma sätt som du skyddar åtkomsten till dina Azure-prenumerationer.

Kubernetes-API-servern tillhandahåller en enda anslutnings punkt för förfrågningar om att utföra åtgärder inom ett kluster. Om du vill skydda och granska åtkomst till API-servern begränsar du åtkomsten och ger de minst privilegierade åtkomst behörigheter som krävs. Den här metoden är inte unik för Kubernetes, men är särskilt viktig när AKS-klustret är logiskt isolerat för användning med flera klienter.

Azure Active Directory (AD) tillhandahåller en företags klar identitets hanterings lösning som kan integreras med AKS-kluster. Eftersom Kubernetes inte tillhandahåller en identitets hanterings lösning, kan den på annat sätt vara svår att tillhandahålla ett detaljerat sätt att begränsa åtkomsten till API-servern. Med Azure AD-integrerade kluster i AKS använder du dina befintliga användar-och grupp konton för att autentisera användare till API-servern.

![Azure Active Directory integrering för AKS-kluster](media/operator-best-practices-cluster-security/aad-integration.png)

Använd Kubernetes RBAC och Azure AD-integration för att skydda API-servern och ange det minsta antalet behörigheter som krävs för en begränsad uppsättning resurser, till exempel ett enda namn område. Olika användare eller grupper i Azure AD kan beviljas olika Kubernetes-roller. Med dessa detaljerade behörigheter kan du begränsa åtkomsten till API-servern och tillhandahålla en tydlig Gransknings logg över åtgärder som utförs.

Den rekommenderade rekommenderade metoden är att använda grupper för att ge åtkomst till filer och mappar jämfört med enskilda identiteter, använda *Azure AD-* gruppmedlemskap för att binda användare till Kubernetes-roller i stället för enskilda *användare*. När en användares grupp medlemskap ändras ändras deras åtkomst behörigheter för AKS-klustret enligt detta. Om du binder användaren direkt till en roll kan deras jobb funktion ändras. Azure AD-gruppmedlemskapet skulle uppdateras, men behörigheter för AKS-klustret skulle inte återspegla detta. I det här scenariot kommer användaren att få fler behörigheter än vad användaren behöver.

Mer information om Azure AD-integrering, Kubernetes RBAC och Azure RBAC finns i [metod tips för autentisering och auktorisering i AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Skydda behållar åtkomst till resurser

**Vägledning för bästa praxis** – begränsa åtkomsten till åtgärder som behållare kan utföra. Ange minst antal behörigheter och Undvik att använda rotor/Privilege-eskalering.

På samma sätt som du vill ge användare eller grupper det lägsta antalet behörigheter som krävs bör behållare också begränsas till endast de åtgärder och processer som de behöver. Konfigurera inte program och behållare som kräver eskalerade privilegier eller rot åtkomst för att minimera risken för angrepp. Till exempel anges `allowPrivilegeEscalation: false` i pod-manifestet. Dessa *Pod säkerhets kontexter* är inbyggda i Kubernetes och gör att du kan definiera ytterligare behörigheter som användaren eller gruppen som ska köras som, eller vilka Linux-funktioner som ska exponeras. Mer metod tips finns i [skydda Pod-åtkomst till resurser][pod-security-contexts].

Om du vill ha mer detaljerad kontroll över container åtgärder kan du också använda inbyggda Linux-säkerhetsfunktioner som *apparmor* och *seccomp*. Dessa funktioner definieras på nodnivå och implementeras sedan via ett Pod-manifest. Inbyggda Linux-säkerhetsfunktioner är bara tillgängliga på Linux-noder och poddar.

> [!NOTE]
> Kubernetes-miljöer, i AKS eller någon annan stans, är inte helt säkra för att ta skydd på flera klienter. Ytterligare säkerhetsfunktioner som *apparmor*, *Seccomp*, *Pod Security Policies* eller mer detaljerade KUBERNETES rollbaserad åtkomst kontroll (Kubernetes RBAC) för noder gör det svårare att utnyttja dem. Men för verklig säkerhet när du kör en skydds arbets belastning med flera innehavare, är en hypervisor den enda säkerhets nivå som du bör lita på. Säkerhets domänen för Kubernetes blir hela klustret, inte en enskild nod. För dessa typer av farliga arbets belastningar med flera klienter bör du använda fysiskt isolerade kluster.

### <a name="app-armor"></a>App-skydd

Om du vill begränsa vilka åtgärder som behållare kan utföra kan du använda [apparmor][k8s-apparmor] Linux kernel-säkerhetsmodulen. AppArmor är tillgänglig som en del av den underliggande AKS Node OS och är aktiverat som standard. Du skapar AppArmor-profiler som begränsar åtgärder som Läs-, Skriv-eller körnings-eller systemfunktioner, till exempel monterings fil system. Standard profiler för AppArmor begränsar åtkomsten till olika `/proc` `/sys` platser och platser, och ger ett sätt att isolera behållare logiskt från den underliggande noden. AppArmor fungerar för alla program som körs på Linux, inte bara Kubernetes poddar.

![AppArmor-profiler som används i ett AKS-kluster för att begränsa container åtgärder](media/operator-best-practices-container-security/apparmor.png)

Följande exempel skapar en profil som förhindrar skrivning till filer för att se AppArmor i praktiken. [SSH][aks-ssh] till en AKS-nod och skapa sedan en fil med namnet *Deny-Write. Profile* och klistra in följande innehåll:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

AppArmor-profiler läggs till med hjälp av `apparmor_parser` kommandot. Lägg till profilen i AppArmor och ange namnet på profilen som du skapade i föregående steg:

```console
sudo apparmor_parser deny-write.profile
```

Inga utdata returneras om profilen tolkas korrekt och tillämpas på AppArmor. Du kommer tillbaka till kommando tolken.

På den lokala datorn skapar du ett Pod-manifest med namnet *AKS-AppArmor. yaml* och klistrar in följande innehåll. Detta manifest definierar en anteckning för `container.apparmor.security.beta.kubernetes` Lägg till referenser till *den neka-Skriv-* profil som skapades i föregående steg:

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

Distribuera exempel-Pod med kommandot [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f aks-apparmor.yaml
```

När Pod har distribuerats använder du kontrol lera att *Hello-apparmor-* Pod visas som *blockerade*:

```
$ kubectl get pods

NAME             READY   STATUS    RESTARTS   AGE
aks-ssh          1/1     Running   0          4m2s
hello-apparmor   0/1     Blocked   0          50s
```

Mer information om AppArmor finns [i AppArmor-profiler i Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Säker data behandling

Medan AppArmor fungerar för alla Linux-program fungerar [seccomp (*SEK* urera *comp* uting)][seccomp] på process nivå. Seccomp är också en Linux kernel-säkerhetsmodul och stöds internt av Docker-körningen som används av AKS-noder. Med seccomp kan processen anropa att containrarna kan utföras är begränsade. Du kan skapa filter som definierar vilka åtgärder som ska tillåtas eller nekas och sedan använda anteckningar i ett Pod YAML-manifest för att associera med seccomp-filtret. Detta motsvarar den bästa metoden att bara bevilja behållar den minsta behörighet som behövs för att köra och inte fler.

Om du vill se seccomp i praktiken skapar du ett filter som förhindrar ändring av behörigheter för en fil. [SSH][aks-ssh] till en AKS-nod och skapa sedan ett seccomp-filter med namnet */var/lib/kubelet/seccomp/Prevent-chmod* och klistra in följande innehåll:

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

På den lokala datorn skapar du ett Pod-manifest med namnet *AKS-seccomp. yaml* och klistrar in följande innehåll. Det här manifestet definierar en anteckning för `seccomp.security.alpha.kubernetes.io` och refererar till filtret för *att förhindra chmod* som skapats i föregående steg:

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

Distribuera exempel-Pod med kommandot [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f ./aks-seccomp.yaml
```

Visa status för poddar med kommandot [kubectl get poddar][kubectl-get] . Pod rapporterar ett fel. `chmod`Kommandot förhindras från att köras av seccomp-filtret, som visas i följande exempel på utdata:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Mer information om tillgängliga filter finns i [Seccomp Security profils for Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Uppdatera regelbundet till den senaste versionen av Kubernetes

**Vägledning för bästa praxis** – du kan hålla dig uppdaterad om nya funktioner och fel korrigeringar, och regelbundet uppgradera Kubernetes-versionen i ditt AKS-kluster.

Kubernetes släpper nya funktioner i snabbare takt än mer traditionella infrastruktur plattformar. Kubernetes-uppdateringar omfattar nya funktioner, fel-eller säkerhets korrigeringar. Nya funktioner går vanligt vis igenom en *alfa* och sedan *beta* status innan de blir *stabila* och är allmänt tillgängliga och rekommenderas för produktions användning. Den här versionen av versionen gör att du kan uppdatera Kubernetes utan att regelbundet räkna upp de ändringar som har brutits eller att du behöver justera dina distributioner och mallar.

AKS stöder tre lägre versioner av Kubernetes. Det innebär att när en ny del uppdaterings version introduceras, kommer den äldsta lägre versionen och de nya korrigerings versioner som stöds att dras tillbaka. Mindre uppdateringar av Kubernetes sker regelbundet. Kontrol lera att du har en styrnings process för att kontrol lera och uppgradera efter behov, så att du inte får slut på support. Mer information finns i [Kubernetes-versioner som stöds AKS][aks-supported-versions].

Om du vill kontrol lera vilka versioner som är tillgängliga för klustret använder du kommandot [AZ AKS get-uppgraderingar][az-aks-get-upgrades] som visas i följande exempel:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Sedan kan du uppgradera ditt AKS-kluster med hjälp av kommandot [AZ AKS Upgrade][az-aks-upgrade] . Uppgraderings processen cordons och tömmer en nod i taget, schemalägger poddar på återstående noder och distribuerar sedan en ny nod som kör de senaste OS-och Kubernetes-versionerna.

Vi rekommenderar starkt att du testar nya del versioner i en utvecklings test miljö så att du kan kontrol lera att arbets belastningen fortsätter att fungera med den nya Kubernetes-versionen. Kubernetes kan använda API: er som är inaktuella, till exempel i version 1,16, som kan förlita sig på dina arbets belastningar. När du ansluter nya versioner till produktion bör du överväga att använda [flera noder i olika versioner](use-multiple-node-pools.md) och uppgradera enskilda pooler en i taget för att progressivt återställa uppdateringen över ett kluster. Om du kör flera kluster bör du uppgradera ett kluster i taget för att progressivt övervaka eller ändra effekter.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

Mer information om uppgraderingar i AKS finns i [Kubernetes-versioner som stöds i AKS][aks-supported-versions] och [uppgradera ett AKS-kluster][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Bearbeta uppdateringar och omstarter av Linux-noder med kured

**Vägledning för bästa praxis** – AKS hämtar och installerar automatiskt säkerhets korrigeringar på varje Linux-noder, men startar inte om automatiskt om det behövs. Använd `kured` om du vill titta efter väntande omstarter, och på ett säkert sätt Cordon och tömma noden för att tillåta noden att starta om, tillämpa uppdateringarna och vara så säkra som möjligt med avseende på operativ systemet. För Windows Server-noder utför regelbundet en AKS uppgraderings åtgärd för att på ett säkert sätt Cordon och tömma poddar och distribuera uppdaterade noder.

Varje kväll får Linux-noder i AKS säkerhets korrigeringar som är tillgängliga via sin distribution Update-kanal. Detta beteende konfigureras automatiskt när noderna distribueras i ett AKS-kluster. För att minimera störningar och potentiell påverkan på att köra arbets belastningar, startas inte noderna om automatiskt om en säkerhets korrigering eller kernel-uppdatering kräver det.

Kured-projektet med öppen källkod [(KUbernetes REboot)][kured] av Weaveworks söker efter väntande Node-omstarter. När en Linux-nod använder uppdateringar som kräver en omstart, är noden säkert avspärrade och töms för att flytta och schemalägga poddar på andra noder i klustret. När noden har startats om läggs den tillbaka i klustret och Kubernetes återupptar schemaläggningen poddar på den. För att minimera störningar får endast en nod i taget startas om `kured` .

![Startprocessen för AKS-noden med kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Om du vill ha en bättre kornig het för att kontrol lera om omstarter sker `kured` kan du integrera med Prometheus för att förhindra omstarter om det finns andra underhålls händelser eller pågående kluster problem. Den här integrationen minimerar ytterligare komplikationer genom att starta om noderna medan du vid ett aktivt fel sökning av andra problem.

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
