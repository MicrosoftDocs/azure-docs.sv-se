---
title: 'Självstudie: Distribuera konfigurationer med GitOps på ett Azure Arc kubernetes-kluster'
description: Den här självstudien visar hur du tillämpar konfigurationer Azure Arc ett kubernetes-kluster med Azure Arc aktiverat. En konceptuell beskrivning av den här processen finns i artikeln Konfigurationer och GitOps – Azure Arc aktiverat Kubernetes.
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial , devx-track-azurecli
ms.openlocfilehash: 0f1172ffa0d29734e7ec005bf2812eeca215aa0d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484174"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Självstudie: Distribuera konfigurationer med GitOps på ett Azure Arc kubernetes-kluster 

I den här självstudien tillämpar du konfigurationer med GitOps på Azure Arc kubernetes-kluster. Du lär dig följande:

> [!div class="checklist"]
> * Skapa en konfiguration på ett Azure Arc Kubernetes-kluster med hjälp av en Git-lagringsplats som exempel.
> * Kontrollera att konfigurationen har skapats.
> * Tillämpa konfiguration från en privat Git-lagringsplats.
> * Verifiera Kubernetes-konfigurationen.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Ett befintligt Azure Arc aktiverat Kubernetes-anslutet kluster.
    - Om du inte har anslutit ett kluster ännu går du igenom snabbstarten Connect an Azure Arc enabled Kubernetes cluster (Ansluta ett [Azure Arc Aktiverat Kubernetes-kluster).](quickstart-connect-cluster.md)
- En förståelse för fördelarna och arkitekturen med den här funktionen. Läs mer i [artikeln Configurations and GitOps - Azure Arc enabled Kubernetes (Konfigurationer och GitOps – Azure Arc aktiverat Kubernetes).](conceptual-configurations.md)
- Installera `k8s-configuration` Azure CLI-tillägget för version >= 1.0.0:
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

    >[!TIP]
    > Om tillägget `k8s-configuration` redan är installerat kan du uppdatera det till den senaste versionen med hjälp av följande kommando : `az extension update --name k8s-configuration`

## <a name="create-a-configuration"></a>Skapa en konfiguration

[Exempeldatabasen](https://github.com/Azure/arc-k8s-demo) som används i den här artikeln är strukturerad kring en klusteroperators persona. Manifesten på den här lagringsplatsen etablerar några namnområden, distribuerar arbetsbelastningar och tillhandahåller viss teamspecifik konfiguration. När du använder den här lagringsplatsen med GitOps skapas följande resurser i klustret:

* Namnrymder: `cluster-config` , `team-a` , `team-b`
* Distribution: `cluster-config/azure-vote`
* ConfigMap: `team-a/endpoints`

`config-agent`Avsöker Azure efter nya eller uppdaterade konfigurationer. Den här uppgiften tar upp till 30 sekunder.

Om du associerar en privat lagringsplats med konfigurationen utför du stegen nedan i Tillämpa konfiguration från [en privat Git-lagringsplats.](#apply-configuration-from-a-private-git-repository)

## <a name="use-azure-cli"></a>Använda Azure CLI
Använd Azure CLI-tillägget för för `k8s-configuration` för att länka ett anslutet kluster till [Git-exempellagringsplatsen](https://github.com/Azure/arc-k8s-demo). 
1. Ge den här konfigurationen namnet `cluster-config` .
1. Instruera agenten att distribuera operatorn i `cluster-config` namnområdet.
1. Ge operatorn `cluster-admin` behörighet.

    ```azurecli
    az k8s-configuration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
    ```

    ```output
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

### <a name="use-a-public-git-repository"></a>Använda en offentlig Git-lagringsplats

| Parameter | Format |
| ------------- | ------------- |
| `--repository-url` | http[s]://server/repo[.git] eller git://server/repo[.git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>Använda en privat Git-lagringsplats med SSH- och Flux-skapade nycklar

Lägg till den offentliga nyckeln som genereras av Flux till användarkontot i din Git-tjänstleverantör. Om nyckeln läggs till i lagringsplatsen i stället för användarkontot använder `git@` du i stället för i `user@` URL:en. 

Gå till avsnittet [Tillämpa konfiguration från en privat Git-lagringsplats](#apply-configuration-from-a-private-git-repository) för mer information.


| Parameter | Format | Kommentarer
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[.git] eller user@server:repo [.git] | `git@` kan ersätta `user@`

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>Använda en privat Git-lagringsplats med SSH och nycklar som tillhandahålls av användaren

Ange din egen privata nyckel direkt eller i en fil. Nyckeln måste vara i [PEM-format och](https://aka.ms/PEMformat) sluta med nyrad (\n). 

Lägg till den associerade offentliga nyckeln till användarkontot i din Git-tjänstleverantör. Om nyckeln läggs till i lagringsplatsen i stället för användarkontot använder `git@` du i stället för `user@` . 

Hoppa till avsnittet [Tillämpa konfiguration från en privat Git-lagringsplats](#apply-configuration-from-a-private-git-repository) för mer information.  

| Parameter | Format | Kommentarer |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] eller user@server:repo [.git] | `git@` kan ersätta `user@` |
| `--ssh-private-key` | base64-kodad nyckel i [PEM-format](https://aka.ms/PEMformat) | Ange nyckel direkt |
| `--ssh-private-key-file` | fullständig sökväg till lokal fil | Ange en fullständig sökväg till den lokala fil som innehåller nyckeln i PEM-format

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Använda en privat Git-värd med SSH och kända värdar som tillhandahålls av användaren

Flux-operatorn har en lista över vanliga Git-värdar i sin kända värdfil för att autentisera Git-lagringsplatsen innan SSH-anslutningen upprättas. Om du använder en ovanlig *Git-lagringsplats* eller din egen Git-värd kan du ange värdnyckeln så att Flux kan identifiera din lagringsplats. 

Precis som privata nycklar kan du ange ditt known_hosts innehåll direkt eller i en fil. När du tillhandahåller ditt eget innehåll använder [du known_hosts för](https://aka.ms/KnownHostsFormat)innehållsformatet , tillsammans med något av SSH-nyckelscenarierna ovan.

| Parameter | Format | Kommentarer |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] eller user@server:repo [.git] | `git@` kan ersätta `user@` |
| `--ssh-known-hosts` | base64-kodad | Ange känt värdinnehåll direkt |
| `--ssh-known-hosts-file` | fullständig sökväg till lokal fil | Ange känt värdinnehåll i en lokal fil |

### <a name="use-a-private-git-repository-with-https"></a>Använda en privat Git-lagringsplats med HTTPS

| Parameter | Format | Kommentarer |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[.git] | HTTPS med grundläggande autentisering |
| `--https-user` | raw eller base64-kodad | HTTPS-användarnamn |
| `--https-key` | raw eller base64-kodad | Personlig HTTPS-åtkomsttoken eller lösenord

>[!NOTE]
>* Helm-operatörsdiagram version 1.2.0+ stöder privat autentisering med HTTPS Helm-versionen.
>* HTTPS Helm-versionen stöds inte för AKS-hanterade kluster.
>* Om du behöver Flux för att få åtkomst till Git-lagringsplatsen via proxyservern måste du uppdatera Azure Arc-agenterna med proxyinställningarna. Mer information finns i Ansluta [med en utgående proxyserver.](./quickstart-connect-cluster.md#connect-using-an-outbound-proxy-server)


## <a name="additional-parameters"></a>Ytterligare parametrar

Anpassa konfigurationen med följande valfria parametrar:

| Parameter | Beskrivning |
| ------------- | ------------- |
| `--enable-helm-operator`| Växla för att aktivera stöd för Helm-diagramdistributioner. |
| `--helm-operator-params` | Diagramvärden för Helm-operatorn (om aktiverad). Till exempel `--set helm.versions=v3`. |
| `--helm-operator-chart-version` | Diagramversion för Helm-operatorn (om aktiverad). Använd version 1.2.0+. Standard: "1.2.0". |
| `--operator-namespace` | Namn på operatornamnrymden. Standard: "default". Max: 23 tecken. |
| `--operator-params` | Parametrar för operatorn . Måste anges inom enkla citattecken. Till exempel ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` 

### <a name="options-supported-in----operator-params"></a>Alternativ som stöds i  `--operator-params` :

| Alternativ | Beskrivning |
| ------------- | ------------- |
| `--git-branch`  | Gren av Git-lagringsplatsen som ska användas för Kubernetes-manifest. Standardvärdet är "master". Nyare lagringsplatsen har rotgrenen med `main` namnet , i vilket fall du måste ange `--git-branch=main` . |
| `--git-path`  | Relativ sökväg inom Git-lagringsplatsen för Flux för att hitta Kubernetes-manifest. |
| `--git-readonly` | Git-lagringsplatsen betraktas som skrivskyddad. Flux försöker inte skriva till den. |
| `--manifest-generation`  | Om det här alternativet är aktiverat söker Flux efter .flux.yaml och kör Kustomize eller andra manifestgeneratorer. |
| `--git-poll-interval`  | Period då Git-lagringsplatsen avsöks efter nya genomföranden. Standardvärdet `5m` är (5 minuter). |
| `--sync-garbage-collection`  | Om den är aktiverad tar Flux bort resurser som skapats, men som inte längre finns i Git. |
| `--git-label`  | Etikett för att hålla reda på synkroniseringsförloppet. Används för att tagga Git-grenen.  Standardvärdet är `flux-sync`. |
| `--git-user`  | Användarnamn för Git-genomförande. |
| `--git-email`  | E-post som ska användas för Git-genomförande. 

Om du inte vill att Flux ska skriva till lagringsplatsen och eller inte har ställts in `--git-user` så ställs de in `--git-email` `--git-readonly` automatiskt.

Mer information finns i [Flux-dokumentationen.](https://aka.ms/FluxcdReadme)

> [!TIP]
> Du kan skapa en konfiguration i Azure Portal på **fliken GitOps** i den Azure Arc Kubernetes-resursen.

## <a name="validate-the-configuration"></a>Verifiera konfigurationen

Använd Azure CLI för att verifiera att konfigurationen har skapats.

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Konfigurationsresursen uppdateras med kompatibilitetsstatus, meddelanden och felsökningsinformation.

```output
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

När en konfiguration skapas eller uppdateras händer några saker:

1. Den Azure Arc `config-agent` övervakar Azure Resource Manager nya eller uppdaterade konfigurationer ( ) och ser den nya `Microsoft.KubernetesConfiguration/sourceControlConfigurations` `Pending` konfigurationen.
1. `config-agent`läser konfigurationsegenskaperna och skapar målnamnområdet.
1. Den Azure Arc skapar ett Kubernetes-tjänstkonto och mappar det till `controller-manager` [ClusterRoleBinding eller RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) för lämpliga behörigheter ( `cluster` eller `namespace` omfång). Den distribuerar sedan en instans av `flux` .
1. Om du använder alternativet SSH med Flux-genererade nycklar `flux` genererar en SSH-nyckel och loggar den offentliga nyckeln.
1. Rapporterar `config-agent` statusen tillbaka till konfigurationsresursen i Azure.

Medan etableringsprocessen sker går konfigurationsresursen igenom några tillståndsändringar. Övervaka förloppet med `az k8s-configuration show ...` kommandot ovan:

| Fasändring | Beskrivning |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Representerar de inledande och pågående tillstånden. |
| `complianceStatus` -> `Installed`  | `config-agent` har konfigurerat klustret och distribuerats `flux` utan fel. |
| `complianceStatus` -> `Failed` | `config-agent` stötte på ett fel vid distribution av `flux` . Information finns i `complianceStatus.message` svarstexten. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Tillämpa konfiguration från en privat Git-lagringsplats

Om du använder en privat Git-lagringsplats måste du konfigurera den offentliga SSH-nyckeln på lagringsplatsen. Antingen anger du eller så genererar Flux den offentliga SSH-nyckeln. Du kan konfigurera den offentliga nyckeln antingen på den specifika Git-lagringsplatsen eller på den Git-användare som har åtkomst till lagringsplatsen. 

### <a name="get-your-own-public-key"></a>Hämta din egen offentliga nyckel

Om du har genererat dina egna SSH-nycklar har du redan de privata och offentliga nycklarna.

#### <a name="get-the-public-key-using-azure-cli"></a>Hämta den offentliga nyckeln med Hjälp av Azure CLI 

Använd följande i Azure CLI om Flux genererar nycklarna.

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Hämta den offentliga nyckeln från Azure Portal

Gå igenom följande i Azure Portal om Flux genererar nycklarna.

1. I Azure Portal navigerar du till den anslutna klusterresursen.
2. På resurssidan väljer du "GitOps" och ser listan över konfigurationer för det här klustret.
3. Välj den konfiguration som använder den privata Git-lagringsplatsen.
4. I kontextfönstret som öppnas, längst ned i fönstret, kopierar du den offentliga nyckeln **för lagringsplatsen.**

#### <a name="add-public-key-using-github"></a>Lägga till offentlig nyckel med GitHub

Välj ett av följande alternativ:

* Alternativ 1: Lägg till den offentliga nyckeln till ditt användarkonto (gäller för alla lagringsplatsen i ditt konto):  
    1. Öppna GitHub och klicka på din profilikon i det övre högra hörnet på sidan.
    2. Klicka på **Inställningar**.
    3. Klicka på **SSH- och GPG-nycklar.**
    4. Klicka på **Ny SSH-nyckel.**
    5. Ange en rubrik.
    6. Klistra in den offentliga nyckeln utan omgivande citattecken.
    7. Klicka på **Lägg till SSH-nyckel.**

* Alternativ 2: Lägg till den offentliga nyckeln som en distributionsnyckel på Git-lagringsplatsen (gäller endast för den här lagringsplatsen):  
    1. Öppna GitHub och navigera till din lagringsplats.
    1. Klicka på **Inställningar**.
    1. Klicka på **Distribuera nycklar.**
    1. Klicka på **Lägg till distributionsnyckel.**
    1. Ange en rubrik.
    1. Markera **Tillåt skrivåtkomst.**
    1. Klistra in den offentliga nyckeln utan omgivande citattecken.
    1. Klicka på **Lägg till nyckel.**

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Lägga till offentlig nyckel med hjälp av en Azure DevOps-lagringsplats

Använd följande steg för att lägga till nyckeln till dina SSH-nycklar:

1. Under **Användarinställningar** längst upp till höger (bredvid profilbilden) klickar du på **Offentliga SSH-nycklar.**
1. Välj **+ Ny nyckel.**
1. Ange ett namn.
1. Klistra in den offentliga nyckeln utan omgivande citattecken.
1. Klicka på **Lägg till**.

## <a name="validate-the-kubernetes-configuration"></a>Verifiera Kubernetes-konfigurationen

När `config-agent` har installerat `flux` instansen bör resurser som finns på Git-lagringsplatsen börja flöda till klustret. Kontrollera att namnområden, distributioner och resurser har skapats med följande kommando:

```console
kubectl get ns --show-labels
```

```output
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

Vi kan se att `team-a` `team-b` `itops` namnrymderna , , `cluster-config` och har skapats.

Operatorn `flux` har distribuerats till `cluster-config` namnområdet enligt konfigurationsresursens anvisningar:

```console
kubectl -n cluster-config get deploy  -o wide
```

```output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Ytterligare utforskning

Du kan utforska de andra resurserna som distribueras som en del av konfigurationsdatabasen med hjälp av:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```
## <a name="clean-up-resources"></a>Rensa resurser

Ta bort en konfiguration med hjälp av Azure CLI eller Azure Portal. När du har kört borttagningskommandot tas konfigurationsresursen bort omedelbart i Azure. Fullständig borttagning av de associerade objekten från klustret bör ske inom 10 minuter. Om konfigurationen är i ett misslyckat tillstånd när den tas bort, kan den fullständiga borttagningen av associerade objekt ta upp till en timme.

När en konfiguration med omfång tas bort tas inte namnområdet bort av Azure Arc för att `namespace` undvika att befintliga arbetsbelastningar bryts. Om det behövs kan du ta bort det här namnområdet manuellt med hjälp av `kubectl` .

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> Eventuella ändringar i klustret som var resultatet av distributioner från den spårade Git-lagringsplatsen tas inte bort när konfigurationen tas bort.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa självstudie och lär dig hur du implementerar CI/CD med GitOps.
> [!div class="nextstepaction"]
> [Implementera CI/CD med GitOps](./tutorial-gitops-ci-cd.md)
