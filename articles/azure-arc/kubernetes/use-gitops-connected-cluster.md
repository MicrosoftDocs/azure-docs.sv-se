---
title: Distribuera konfigurationer med hjälp av GitOps på Arc-aktiverade Kubernetes-kluster (förhandsversion)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Använd GitOps för att konfigurera ett Azure Arc-aktiverat Kubernetes-kluster (för hands version)
keywords: GitOps, Kubernetes, K8s, Azure, Arc, Azure Kubernetes service, AKS, containers
ms.openlocfilehash: 072bfc8c243eb9b69e06366961019b88b67e0941
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392246"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Distribuera konfigurationer med hjälp av GitOps på Arc-aktiverade Kubernetes-kluster (förhandsversion)

I förhållande till Kubernetes är GitOps en metod för att deklarera det önskade läget för Kubernetes (distributioner, namnrymder osv.) på en git-lagringsplats. Denna deklaration följs av en avsöknings-och pull-baserad distribution av dessa klusterkonfigurationer med hjälp av en operatör. 

Den här artikeln beskriver installationen av GitOps-arbetsflöden i Azure Arc-aktiverade Kubernetes-kluster.

Anslutningen mellan klustret och git-lagringsplatsen skapas som en `Microsoft.KubernetesConfiguration/sourceControlConfigurations` tilläggs resurs i Azure Resource Manager. `sourceControlConfiguration`Resurs egenskaperna representerar var och hur Kubernetes resurser ska flöda från git till klustret. `sourceControlConfiguration`Data lagras krypterade, i vila i en Azure Cosmos DB databas för att säkerställa data sekretessen.

Den som `config-agent` körs i klustret ansvarar för:
* Spåra nya eller uppdaterade `sourceControlConfiguration` tilläggs resurser på den Azure Arc-aktiverade Kubernetes-resursen.
* Distribuera en flödes operatör för att se git-lagringsplatsen för var och en `sourceControlConfiguration` .
* Tillämpar alla uppdateringar som görs i någon `sourceControlConfiguration` . 

Du kan skapa flera `sourceControlConfiguration` resurser på samma Azure Arc-Kubernetes-kluster för att uppnå flera innehavare. Begränsa distributioner inom respektive namnrum genom att skapa var `sourceControlConfiguration` och en med olika `namespace` omfång.

Git-lagringsplatsen kan innehålla:
* YAML-format manifest som beskriver eventuella giltiga Kubernetes-resurser, inklusive namnrymder, ConfigMaps, distributioner, DaemonSets osv. 
* Helm-diagram för att distribuera program. 

En vanlig uppsättning scenarier är att definiera en bas linje konfiguration för din organisation, t. ex. vanliga Azure-roller och bindningar, övervakning eller loggnings agenter eller kluster för många tjänster.

Samma mönster kan användas för att hantera en större samling kluster, som kan distribueras i heterogena miljöer. Du kan till exempel ha en lagrings plats som definierar bas linje konfigurationen för din organisation, som gäller för flera Kubernetes-kluster samtidigt. [Azure policy kan automatisera](use-azure-policy.md) skapandet av en `sourceControlConfiguration` med en speciell uppsättning parametrar på alla Azure Arc-aktiverade Kubernetes-resurser inom ett omfång (prenumeration eller resurs grupp).

Gå igenom följande steg för att lära dig hur du använder en uppsättning konfigurationer med `cluster-admin` omfång.

## <a name="before-you-begin"></a>Innan du börjar

Kontrol lera att du har ett befintligt Azure-Arc aktiverat Kubernetes-kopplat kluster. Om du behöver ett anslutet kluster kan du läsa snabb starten för att [ansluta en Azure-båge med aktiverat Kubernetes-kluster](./connect-cluster.md).

## <a name="create-a-configuration"></a>Skapa en konfiguration

[Exempel lagrings platsen](https://github.com/Azure/arc-k8s-demo) som används i den här artikeln är strukturerad runt personen som är medlem av en kluster operatör som vill etablera några få namn rymder, distribuera en gemensam arbets belastning och ange en team-speciell konfiguration. När du använder den här databasen skapas följande resurser i klustret:


* **Namn områden:** `cluster-config` , `team-a` , `team-b`
* **Distribution:**`cluster-config/azure-vote`
* **ConfigMap:**`team-a/endpoints`

`config-agent`Söker i Azure efter ny eller uppdaterad `sourceControlConfiguration` . Den här aktiviteten tar upp till 30 sekunder.

Om du associerar ett privat lager med slutför du `sourceControlConfiguration` stegen i [tillämpa konfiguration från en privat git-lagringsplats](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Använda Azure CLI

Använd Azure CLI-tillägget för `k8sconfiguration` att länka ett anslutet kluster till [exempel git-lagringsplatsen](https://github.com/Azure/arc-k8s-demo). 
1. Namnge den här konfigurationen `cluster-config` .
1. Instruera agenten att distribuera operatorn i `cluster-config` namn området.
1. Ge operatörs `cluster-admin` behörighet.

```azurecli
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Utdataparametrar**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Pending",
    "lastConfigApplied": "0001-01-01T00:00:00",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": null,
  "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
  "resourceGroup": "MyRG",
  "sshKnownHostsContents": "",
  "systemData": {
    "createdAt": "2020-11-24T21:22:01.542801+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
  },
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
  ```

#### <a name="use-a-public-git-repo"></a>Använda en offentlig git-lagrings platsen

| Parameter | Format |
| ------------- | ------------- |
| `--repository-url` | http [s]://Server/repo [. git] eller git://Server/repo [. git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>Använd en privat git-lagrings platsen med SSH-och flöden skapade nycklar

| Parameter | Format | Kommentarer
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[. git] eller user@server:repo [. git] | `git@` kan ersätta `user@`

> [!NOTE]
> Den offentliga nyckeln som genereras av flödes gruppen måste läggas till i användar kontot i git-tjänstprovidern. Om nyckeln läggs till i lagrings platsen i stället för användar kontot använder du `git@` i stället för `user@` i URL: en. Gå till avsnittet [tillämpa konfiguration från en privat git-lagringsplats](#apply-configuration-from-a-private-git-repository) för mer information.

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>Använd en privat git-lagrings platsen med SSH-och användarspecifika nycklar

| Parameter | Format | Kommentarer |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] eller user@server:repo [. git] | `git@` kan ersätta `user@` |
| `--ssh-private-key` | Base64-kodad nyckel i [PEM-format](https://aka.ms/PEMformat) | Ange nyckel direkt |
| `--ssh-private-key-file` | fullständig sökväg till lokal fil | Ange en fullständig sökväg till en lokal fil som innehåller PEM-format-nyckeln

> [!NOTE]
> Ange din privata nyckel direkt eller i en fil. Nyckeln måste vara i [PEM-format](https://aka.ms/PEMformat) och sluta med rad matningen (\n).  Den tillhör ande offentliga nyckeln måste läggas till i användar kontot i git-tjänstprovidern. Om nyckeln läggs till i lagrings platsen i stället för användar kontot använder du `git@` i stället för `user@` . Gå till avsnittet [tillämpa konfiguration från en privat git-lagringsplats](#apply-configuration-from-a-private-git-repository) för mer information.

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Använd en privat git-värd med SSH-och användarspecifika kända värdar

| Parameter | Format | Kommentarer |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] eller user@server:repo [. git] | `git@` kan ersätta `user@` |
| `--ssh-known-hosts` | Base64-kodad | Tillhandahålla kända värdar innehåll direkt |
| `--ssh-known-hosts-file` | fullständig sökväg till lokal fil | Tillhandahålla kända värdar innehåll i en lokal fil |

> [!NOTE]
> För att kunna autentisera git-lagrings platsen innan du upprättar SSH-anslutningen, har flödes operatorn en lista över vanliga git-värdar i den kända värd filen. Om du använder en ovanlig git-lagrings platsen eller en egen git-värd kan du behöva ange värd nyckeln för att se till att flödet kan identifiera din lagrings platsen. Du kan ange known_hosts innehåll direkt eller i en fil. Använd [specifikationerna för known_hosts innehålls format](https://aka.ms/KnownHostsFormat) tillsammans med ett av de scenarier för SSH-nyckeln som beskrivs ovan när du tillhandahåller ditt eget innehåll.

#### <a name="use-a-private-git-repo-with-https"></a>Använda en privat git-lagrings platsen med HTTPS

| Parameter | Format | Kommentarer |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[. git] | HTTPS med Basic auth |
| `--https-user` | RAW eller Base64-kodad | HTTPS-användarnamn |
| `--https-key` | RAW eller Base64-kodad | HTTPS personlig åtkomsttoken eller lösen ord

> [!NOTE]
> HTTPS Helm release Private auth stöds bara med Helm-operatörs diagram version 1.2.0 + (standard).
> HTTPS Helm release Private auth stöds inte för närvarande för Azure Kubernetes Services-hanterade kluster.
> Om du behöver flöden för att komma åt git-lagrings platsen via din proxy måste du uppdatera Azure Arc-agenterna med proxyinställningarna. Mer information finns i [ansluta med en utgående proxyserver](./connect-cluster.md#connect-using-an-outbound-proxy-server).

#### <a name="additional-parameters"></a>Ytterligare parametrar

Anpassa konfigurationen med följande valfria parametrar:

| Parameter | Beskrivning |
| ------------- | ------------- |
| `--enable-helm-operator`| Växlar för att aktivera stöd för Helm-diagram distributioner. |
| `--helm-operator-params` | Diagram värden för Helm-operator (om aktive rad). Till exempel `--set helm.versions=v3`. |
| `--helm-operator-version` | Diagram version för Helm-operator (om aktive rad). Använd version 1.2.0 +. Standard: ' 1.2.0 '. |
| `--operator-namespace` | Namn på namn området för operatorn. Standard: standard. Max: 23 tecken. |
| `--operator-params` | Parametrar för operatorn. Måste anges inom enkla citat tecken. Till exempel ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main' ``` 

##### <a name="options-supported-in----operator-params"></a>Alternativ som stöds i  `--operator-params` :

| Alternativ | Beskrivning |
| ------------- | ------------- |
| `--git-branch`  | Gren av Git-lagrings platsen som ska användas för Kubernetes-manifest. Standardvärdet är Master. Nyare databaser har rot grenen med namnet `main` , i vilket fall måste du ange `--git-branch=main` . |
| `--git-path`  | Relativ sökväg i git-lagrings platsen för flöde för att hitta Kubernetes-manifest. |
| `--git-readonly` | Git-lagrings platsen anses vara skrivskyddad; Flödet försöker inte skriva till den. |
| `--manifest-generation`  | Om aktive rad kommer flödet att leta efter. flöde. yaml och köra Kustomize eller andra manifest generatorer. |
| `--git-poll-interval`  | Den period då git-lagrings platsen ska avsökas för nya incheckningar. Standardvärdet är `5m` (5 minuter). |
| `--sync-garbage-collection`  | Om det här alternativet är aktiverat tas resurser som den skapade bort, men de finns inte längre i git. |
| `--git-label`  | Etikett för att hålla koll på synkroniseringens förlopp. Används för att tagga git-grenen.  Standardvärdet är `flux-sync`. |
| `--git-user`  | Användar namn för git-incheckning. |
| `--git-email`  | E-postmeddelande som ska användas för git-incheckning. 

Om du inte vill att flödet ska skriva till lagrings platsen och `--git-user` `--git-email` inte har angetts, `--git-readonly` ställs det in automatiskt.

Mer information finns i [flödes dokumentationen](https://aka.ms/FluxcdReadme).

> [!TIP]
> Du kan skapa en `sourceControlConfiguration` i Azure Portal på fliken **GitOps** i den Azure Arc-aktiverade Kubernetes-resursen.

## <a name="validate-the-sourcecontrolconfiguration"></a>Verifiera sourceControlConfiguration

Använd Azure CLI för att kontrol lera att `sourceControlConfiguration` har skapats.

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

`sourceControlConfiguration`Resursen kommer att uppdateras med kompatibilitetsstatus, meddelanden och fel söknings information.

**Utdataparametrar**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

När en `sourceControlConfiguration` har skapats eller uppdaterats händer några saker under huven:

1. Azure-bågen `config-agent` övervakar Azure Resource Manager för nya eller uppdaterade konfigurationer ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ) och ser den nya `Pending` konfigurationen.
1. `config-agent`Läser konfigurations egenskaperna och skapar mål namn rymden.
1. Azure-bågen `controller-manager` förbereder ett Kubernetes tjänst konto med rätt behörighet ( `cluster` eller `namespace` omfång) och distribuerar sedan en instans av `flux` .
1. Om du använder alternativet för SSH med flödes genererade nycklar `flux` genererar en SSH-nyckel och loggar den offentliga nyckeln.
1. `config-agent`Rapporterar status tillbaka till `sourceControlConfiguration` resursen i Azure.

När etablerings processen utförs går det `sourceControlConfiguration` igenom några tillstånds ändringar. Övervaka förloppet med `az k8sconfiguration show ...` kommandot ovan:

| Fas ändring | Beskrivning |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Representerar inledande och pågående tillstånd. |
| `complianceStatus` -> `Installed`  | `config-agent` har kunnat konfigurera klustret och distribuera `flux` utan fel. |
| `complianceStatus` -> `Failed` | `config-agent` ett fel uppstod vid distribution `flux` . informationen bör vara tillgänglig i `complianceStatus.message` svars texten. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Tillämpa konfigurationen från en privat git-lagringsplats

Om du använder en privat git-lagrings platsen måste du konfigurera den offentliga SSH-nyckeln i din lagrings platsen. Den offentliga SSH-nyckeln är antingen den som det flödet genererar eller den som du anger. Du kan konfigurera den offentliga nyckeln antingen på den angivna git-lagrings platsen eller på git-användaren som har åtkomst till lagrings platsen. 

### <a name="get-your-own-public-key"></a>Hämta din egen offentliga nyckel

Om du skapade dina egna SSH-nycklar har du redan privata och offentliga nycklar.

#### <a name="get-the-public-key-using-azure-cli"></a>Hämta den offentliga nyckeln med Azure CLI 

Följande är användbart om flödet genererar nycklarna.

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Hämta den offentliga nyckeln från Azure Portal

Följande är användbart om flödet genererar nycklarna.

1. I Azure Portal navigerar du till den anslutna kluster resursen.
2. På sidan resurs väljer du "GitOps" och visar listan över konfigurationer för klustret.
3. Välj den konfiguration som använder den privata git-lagringsplatsen.
4. I kontext fönstret som öppnas längst ned i fönstret kopierar du **lagrings platsens offentliga nyckel**.

#### <a name="add-public-key-using-github"></a>Lägg till offentlig nyckel med GitHub

Välj ett av följande alternativ:

* Alternativ 1: Lägg till den offentliga nyckeln till ditt användar konto (gäller för alla databaser i ditt konto):  
    1. Öppna GitHub och klicka på din profil ikon i det övre högra hörnet på sidan.
    2. Klicka på **Inställningar**.
    3. Klicka på **SSH-och GPG-nycklar**.
    4. Klicka på **ny SSH-nyckel**.
    5. Ange en rubrik.
    6. Klistra in den offentliga nyckeln utan omgivande citat tecken.
    7. Klicka på **Lägg till SSH-nyckel**.

* Alternativ 2: Lägg till den offentliga nyckeln som en distributions nyckel till git-lagrings platsen (gäller endast denna lagrings platsen):  
    1. Öppna GitHub och navigera till din lagrings platsen.
    1. Klicka på **Inställningar**.
    1. Klicka på **distribuera nycklar**.
    1. Klicka på **Lägg till distributions nyckel**.
    1. Ange en rubrik.
    1. Kontrol lera **Tillåt skriv åtkomst**.
    1. Klistra in den offentliga nyckeln utan omgivande citat tecken.
    1. Klicka på **Lägg till nyckel**.

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Lägg till offentlig nyckel med hjälp av en Azure DevOps-lagringsplats

Använd följande steg för att lägga till nyckeln till dina SSH-nycklar:

1. Under **användar inställningar** längst upp till höger (bredvid profil avbildningen) klickar du på **offentliga SSH-nycklar**.
1. Välj  **+ ny nyckel**.
1. Ange ett namn.
1. Klistra in den offentliga nyckeln utan omgivande citat tecken.
1. Klicka på **Lägg till**.

## <a name="validate-the-kubernetes-configuration"></a>Verifiera Kubernetes-konfigurationen

När `config-agent` har installerat `flux` instansen ska resurser som lagras i git-lagringsplatsen börja flöda till klustret. Kontrol lera att namn områdena, distributionerna och resurserna har skapats med följande kommando:

```console
kubectl get ns --show-labels
```

**Utdataparametrar**

```console
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

Vi kan se att `team-a` `team-b` `itops` namn områdena,, och `cluster-config` har skapats.

`flux`Operatören har distribuerats till `cluster-config` namn området enligt följande `sourceControlConfig` :

```console
kubectl -n cluster-config get deploy  -o wide
```

**Utdataparametrar**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Ytterligare utforskning

Du kan utforska de andra resurserna som distribueras som en del av konfigurations lagrings platsen med hjälp av:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Ta bort en konfiguration

Ta bort en `sourceControlConfiguration` med hjälp av Azure CLI eller Azure Portal.  När du har initierat kommandot Ta bort tas `sourceControlConfiguration` resursen bort omedelbart i Azure. Fullständig borttagning av associerade objekt från klustret ska ske inom 10 minuter. Om `sourceControlConfiguration` är i ett felaktigt tillstånd när det tas bort kan den fullständiga borttagningen av associerade objekt ta upp till en timme.

> [!NOTE]
> När en `sourceControlConfiguration` with `namespace` -omfattning har skapats kan användare med `edit` roll bindning i namn området distribuera arbets belastningar i namn området. När detta `sourceControlConfiguration` med namn områdes omfånget tas bort lämnas namn området kvar och tas inte bort för att undvika att de andra arbets belastningarna bryts. Om det behövs kan du ta bort det här namn området manuellt med `kubectl` .  
> Eventuella ändringar i klustret som resulterade i distributioner från det spårade git-lagrings platsen tas inte bort när tas `sourceControlConfiguration` bort.

```azurecli
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Utdataparametrar**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Nästa steg

- [Använd Helm med käll kontroll konfiguration](./use-gitops-with-helm.md)
- [Använd Azure Policy för att styra kluster konfigurationen](./use-azure-policy.md)