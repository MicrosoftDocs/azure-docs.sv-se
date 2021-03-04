---
title: 'Självstudie: distribuera konfigurationer med GitOps på ett Azure Arc-aktiverat Kubernetes-kluster'
description: I den här självstudien beskrivs hur du använder konfigurationer i ett Azure Arc-Kubernetes kluster. Ett begrepp som tar hänsyn till den här processen finns i artikeln konfigurationer och GitOps-Azure Arc-aktiverade Kubernetes.
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 64299bd05e82cf6f5452cde3f3da5622eff25e56
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121481"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Självstudie: distribuera konfigurationer med GitOps på ett Azure Arc-aktiverat Kubernetes-kluster 

I den här självstudien kommer du att använda konfigurationer med GitOps på ett Azure Arc-aktiverat Kubernetes-kluster. Du lär dig följande:

> [!div class="checklist"]
> * Skapa en konfiguration i ett Azure-båg aktiverat Kubernetes-kluster med hjälp av ett exempel på git-lagringsplats.
> * Verifiera att konfigurationen har skapats.
> * Använd konfigurations formulär för en privat git-lagringsplats.
> * Verifiera Kubernetes-konfigurationen.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ett befintligt Azure Arc-aktiverat Kubernetes-kopplat kluster.
    - Om du inte har anslutit ett kluster ännu kan du gå igenom vår [Anslut en Azure-båge som är aktive rad Kubernetes kluster snabb start](quickstart-connect-cluster.md).
- En förståelse för den här funktionens fördelar och arkitektur. Läs mer i [konfigurationer och GitOps-Azure Arc-aktiverad Kubernetes-artikel](conceptual-configurations.md).

## <a name="create-a-configuration"></a>Skapa en konfiguration

[Exempel lagrings platsen](https://github.com/Azure/arc-k8s-demo) som används i den här artikeln är strukturerad runt personen i en kluster operatör. Manifesten i den här lagrings platsen etablerar några namn rymder, distribuerar arbets belastningar och tillhandahåller en team-speciell konfiguration. Om du använder den här lagrings platsen med GitOps skapas följande resurser i klustret:

* Namn områden: `cluster-config` , `team-a` , `team-b`
* Spridningen `cluster-config/azure-vote`
* ConfigMap: `team-a/endpoints`

`config-agent`Söker efter nya eller uppdaterade konfigurationer i Azure. Den här aktiviteten tar upp till 30 sekunder.

Om du associerar ett privat lager med konfigurationen slutför du stegen nedan i [tillämpa konfiguration från en privat git-lagringsplats](#apply-configuration-from-a-private-git-repository).

## <a name="use-azure-cli"></a>Använda Azure CLI
Använd Azure CLI-tillägget för `k8s-configuration` att länka ett anslutet kluster till [exempel git-lagringsplatsen](https://github.com/Azure/arc-k8s-demo). 
1. Namnge den här konfigurationen `cluster-config` .
1. Instruera agenten att distribuera operatorn i `cluster-config` namn området.
1. Ge operatörs `cluster-admin` behörighet.

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

### <a name="use-a-public-git-repository"></a>Använd en offentlig git-lagringsplats

| Parameter | Format |
| ------------- | ------------- |
| `--repository-url` | http [s]://Server/repo [. git] eller git://Server/repo [. git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>Använd en privat git-lagringsplats med SSH-och flöden-skapade nycklar

Lägg till den offentliga nyckeln som genereras av flöde till användar kontot i git-tjänstprovidern. Om nyckeln läggs till i lagrings platsen i stället för användar kontot använder `git@` du i stället för `user@` i URL: en. 

Gå till avsnittet [tillämpa konfiguration från en privat git-lagringsplats](#apply-configuration-from-a-private-git-repository) för mer information.


| Parameter | Format | Kommentarer
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[. git] eller user@server:repo [. git] | `git@` kan ersätta `user@`

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>Använd en privat git-lagringsplats med SSH-och användarspecifika nycklar

Ange din privata nyckel direkt eller i en fil. Nyckeln måste vara i [PEM-format](https://aka.ms/PEMformat) och sluta med rad matningen (\n). 

Lägg till den associerade offentliga nyckeln till användar kontot i din git-tjänstleverantör. Om nyckeln läggs till i lagrings platsen i stället för användar kontot använder du `git@` i stället för `user@` . 

Gå till avsnittet [tillämpa konfiguration från en privat git-lagringsplats](#apply-configuration-from-a-private-git-repository) för mer information.  

| Parameter | Format | Kommentarer |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] eller user@server:repo [. git] | `git@` kan ersätta `user@` |
| `--ssh-private-key` | Base64-kodad nyckel i [PEM-format](https://aka.ms/PEMformat) | Ange nyckel direkt |
| `--ssh-private-key-file` | fullständig sökväg till lokal fil | Ange en fullständig sökväg till en lokal fil som innehåller PEM-format-nyckeln

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Använd en privat git-värd med SSH-och användarspecifika kända värdar

Flödes operatorn har en lista över vanliga git-värdar i den kända värd filen för att autentisera git-lagringsplatsen innan SSH-anslutningen upprättas. Om du använder en *ovanlig* git-lagringsplats eller en egen git-värd kan du ange värd nyckeln så att flödet kan identifiera din lagrings platsen. 

Precis som med privata nycklar kan du ange known_hosts innehåll direkt eller i en fil. När du tillhandahåller ditt eget innehåll ska du använda [known_hosts innehålls formats specifikationer](https://aka.ms/KnownHostsFormat), tillsammans med någon av SSH-nyckel scenarierna ovan.

| Parameter | Format | Kommentarer |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] eller user@server:repo [. git] | `git@` kan ersätta `user@` |
| `--ssh-known-hosts` | Base64-kodad | Tillhandahålla kända värdar innehåll direkt |
| `--ssh-known-hosts-file` | fullständig sökväg till lokal fil | Tillhandahålla kända värdar innehåll i en lokal fil |

### <a name="use-a-private-git-repository-with-https"></a>Använd en privat git-lagringsplats med HTTPS

| Parameter | Format | Kommentarer |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[. git] | HTTPS med Basic auth |
| `--https-user` | RAW eller Base64-kodad | HTTPS-användarnamn |
| `--https-key` | RAW eller Base64-kodad | HTTPS personlig åtkomsttoken eller lösen ord

>[!NOTE]
>* Helm-operator diagram version 1.2.0 + har stöd för HTTPS Helm release Private auth.
>* HTTPS Helm-versionen stöds inte för AKS-hanterade kluster.
>* Om du behöver flöden för att få åtkomst till git-lagringsplatsen via proxyservern måste du uppdatera Azure Arc-agenterna med proxyinställningarna. Mer information finns i [ansluta med en utgående proxyserver](./connect-cluster.md#connect-using-an-outbound-proxy-server).


## <a name="additional-parameters"></a>Ytterligare parametrar

Anpassa konfigurationen med följande valfria parametrar:

| Parameter | Beskrivning |
| ------------- | ------------- |
| `--enable-helm-operator`| Växlar för att aktivera stöd för Helm-diagram distributioner. |
| `--helm-operator-params` | Diagram värden för Helm-operator (om aktive rad). Till exempel `--set helm.versions=v3`. |
| `--helm-operator-chart-version` | Diagram version för Helm-operator (om aktive rad). Använd version 1.2.0 +. Standard: ' 1.2.0 '. |
| `--operator-namespace` | Namn på namn området för operatorn. Standard: standard. Max: 23 tecken. |
| `--operator-params` | Parametrar för operatorn. Måste anges inom enkla citat tecken. Till exempel ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` 

### <a name="options-supported-in----operator-params"></a>Alternativ som stöds i  `--operator-params` :

| Alternativ | Beskrivning |
| ------------- | ------------- |
| `--git-branch`  | Gren av Git-lagringsplatsen som ska användas för Kubernetes-manifest. Standardvärdet är Master. Nyare databaser har rot grenen med namnet `main` , i vilket fall måste du ange `--git-branch=main` . |
| `--git-path`  | Relativ sökväg i git-lagringsplatsen för flöde för att hitta Kubernetes-manifest. |
| `--git-readonly` | Git-lagringsplatsen betraktas som skrivskyddad. Flödet försöker inte skriva till den. |
| `--manifest-generation`  | Om aktive rad kommer flödet att leta efter. flöde. yaml och köra Kustomize eller andra manifest generatorer. |
| `--git-poll-interval`  | Den period då git-lagringsplatsen ska avsökas för nya incheckningar. Standardvärdet är `5m` (5 minuter). |
| `--sync-garbage-collection`  | Om det här alternativet är aktiverat tas resurser som den skapade bort, men de finns inte längre i git. |
| `--git-label`  | Etikett för att hålla koll på synkroniseringens förlopp. Används för att tagga git-grenen.  Standardvärdet är `flux-sync`. |
| `--git-user`  | Användar namn för git-incheckning. |
| `--git-email`  | E-postmeddelande som ska användas för git-incheckning. 

Om du inte vill att flödet ska skrivas till lagrings platsen `--git-user` `--git-email` , eller inte anges, `--git-readonly` anges automatiskt.

Mer information finns i [flödes dokumentationen](https://aka.ms/FluxcdReadme).

> [!TIP]
> Du kan skapa en konfiguration i Azure Portal på fliken **GitOps** i den Azure Arc-aktiverade Kubernetes-resursen.

## <a name="validate-the-configuration"></a>Verifiera konfigurationen

Använd Azure CLI för att verifiera att konfigurationen har skapats.

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Konfigurations resursen uppdateras med kompatibilitetsstatus, meddelanden och fel söknings information.

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

1. Azure-bågen `config-agent` övervakar Azure Resource Manager för nya eller uppdaterade konfigurationer ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ) och ser den nya `Pending` konfigurationen.
1. `config-agent`Läser konfigurations egenskaperna och skapar mål namn rymden.
1. Azure-bågen `controller-manager` skapar ett Kubernetes-tjänstkonto och mappar det till [ClusterRoleBinding eller RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) för lämpliga behörigheter ( `cluster` eller `namespace` omfång). Den distribuerar sedan en instans av `flux` .
1. Om du använder alternativet för SSH med flödes genererade nycklar `flux` genererar en SSH-nyckel och loggar den offentliga nyckeln.
1. `config-agent`Rapporterar status tillbaka till konfigurations resursen i Azure.

När etablerings processen utförs går konfigurations resursen igenom några tillstånds ändringar. Övervaka förloppet med `az k8s-configuration show ...` kommandot ovan:

| Fas ändring | Beskrivning |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Representerar inledande och pågående tillstånd. |
| `complianceStatus` -> `Installed`  | `config-agent` klustret har kon figurer ATS och distribuerats `flux` utan fel. |
| `complianceStatus` -> `Failed` | `config-agent` ett fel uppstod vid distribution `flux` . Information finns i `complianceStatus.message` svars texten. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Tillämpa konfigurationen från en privat git-lagringsplats

Om du använder en privat git-lagringsplats måste du konfigurera den offentliga SSH-nyckeln i din lagrings plats. Antingen anger du eller flöden genererar den offentliga SSH-nyckeln. Du kan konfigurera den offentliga nyckeln antingen på den angivna git-lagringsplatsen eller på git-användaren som har åtkomst till lagrings platsen. 

### <a name="get-your-own-public-key"></a>Hämta din egen offentliga nyckel

Om du skapade dina egna SSH-nycklar har du redan privata och offentliga nycklar.

#### <a name="get-the-public-key-using-azure-cli"></a>Hämta den offentliga nyckeln med Azure CLI 

Använd följande i Azure CLI om flödet genererar nycklarna.

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Hämta den offentliga nyckeln från Azure Portal

Gå igenom följande i Azure Portal om flödet genererar nycklarna.

1. I Azure Portal navigerar du till den anslutna kluster resursen.
2. På sidan resurs väljer du "GitOps" och visar listan över konfigurationer för klustret.
3. Välj den konfiguration som använder den privata git-lagringsplatsen.
4. I kontext fönstret som öppnas längst ned i fönstret kopierar du **lagrings platsens offentliga nyckel**.

#### <a name="add-public-key-using-github"></a>Lägg till offentlig nyckel med GitHub

Välj ett av följande alternativ:

* Alternativ 1: Lägg till den offentliga nyckeln till ditt användar konto (gäller alla databaser i ditt konto):  
    1. Öppna GitHub och klicka på din profil ikon i det övre högra hörnet på sidan.
    2. Klicka på **Inställningar**.
    3. Klicka på **SSH-och GPG-nycklar**.
    4. Klicka på **ny SSH-nyckel**.
    5. Ange en rubrik.
    6. Klistra in den offentliga nyckeln utan omgivande citat tecken.
    7. Klicka på **Lägg till SSH-nyckel**.

* Alternativ 2: Lägg till den offentliga nyckeln som en distributions nyckel till git-lagringsplatsen (gäller endast den här lagrings platsen):  
    1. Öppna GitHub och navigera till din lagrings plats.
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

Vi kan se att `team-a` `team-b` `itops` namn områdena,, och `cluster-config` har skapats.

`flux`Operatören har distribuerats till `cluster-config` namn området enligt konfigurationen av konfigurations resursen:

```console
kubectl -n cluster-config get deploy  -o wide
```

```output
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
## <a name="clean-up-resources"></a>Rensa resurser

Ta bort en konfiguration med hjälp av Azure CLI eller Azure Portal. När du har kört kommandot Ta bort tas konfigurations resursen bort omedelbart i Azure. Fullständig borttagning av associerade objekt från klustret ska ske inom 10 minuter. Om konfigurationen är i ett felaktigt tillstånd när den tas bort kan den fullständiga borttagningen av associerade objekt ta upp till en timme.

När en konfiguration med `namespace` omfång tas bort, tas inte namn området bort av Azure-bågen för att undvika att befintliga arbets belastningar bryts. Om det behövs kan du ta bort det här namn området manuellt med `kubectl` .

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> Eventuella ändringar i klustret som resulterade i distributioner från den spårade git-lagringsplatsen tas inte bort när konfigurationen tas bort.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa självstudie och lär dig hur du implementerar CI/CD med GitOps.
> [!div class="nextstepaction"]
> [Implementera CI/CD med GitOps](./tutorial-gitops-ci-cd.md)
