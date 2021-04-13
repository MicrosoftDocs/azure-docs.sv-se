---
title: Begränsa utgående trafik i ett Azure Red Hat OpenShift-kluster (ARO)
description: Lär dig vilka portar och adresser som krävs för att styra utgående trafik i Azure Red Hat OpenShift (ARO)
author: sakthi-vetrivel
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 04/09/2021
ms.openlocfilehash: 24c4686306aff9d84fe7bf74ddfdccff987244d9
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368733"
---
# <a name="control-egress-traffic-for-your-azure-red-hat-openshift-aro-cluster-preview"></a>Kontrollera utgående trafik för ditt Azure Red Hat OpenShift(ARO)-kluster (förhandsversion)

Den här artikeln innehåller nödvändig information som gör att du kan skydda utgående trafik från Azure Red Hat OpenShift kluster (ARO). Den innehåller klusterkraven för en grundläggande ARO-distribution och fler krav för valfria Red Hat- och komponenter från tredje part. I [slutet](#private-aro-cluster-setup) ges ett exempel på hur du konfigurerar dessa krav med Azure Firewall. Tänk på att du kan tillämpa den här informationen på Azure Firewall eller på någon utgående begränsningsmetod eller apparat.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du skapar ett nytt kluster. Om du behöver ett grundläggande ARO-kluster kan du gå till [ARO-snabbstarten.](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster)

> [!IMPORTANT]
> Förhandsfunktionerna i ARO är tillgängliga via självbetjäning och avanmälning. Förhandsversioner tillhandahålls i "i sin befintliga omfattning" och "i den omfattning som är tillgänglig", och de undantas från serviceavtalen och den begränsade garantin. ARO-förhandsversioner omfattas delvis av kundsupporten efter bästa möjliga resultat.

## <a name="minimum-required-fqdn--application-rules"></a>Minsta obligatoriska FQDN/programregler

Den här listan baseras på listan över FQDN som finns i OpenShift-dokument här: https://docs.openshift.com/container-platform/4.6/installing/install_config/configuring-firewall.html

Följande FQDN/programregler krävs:

| Mål-FQDN | Port | Användning |
| ----------- | ----------- | ------------- |
| **`quay.io`** | **HTTPS:443** | Obligatoriskt för installationen som används av klustret. Detta används av klustret för att ladda ned plattformscontaineravbildningarna. |
| **`registry.redhat.io`** | **HTTPS:443** | Obligatoriskt för kärn-tillägg. Detta används av klustret för att ladda ned kärnkomponenter som utvecklingsverktyg, operatörsbaserade tillägg och Red Hat-tillhandahållna containeravbildningar.
| **`mirror.openshift.com`** | **HTTPS:443** | Detta krävs i VDI-miljön eller din bärbara dator för att få åtkomst till speglat installationsinnehåll och bilder. Det krävs i klustret för att ladda ned signaturer för plattformsutgåningar för att veta vilka avbildningar som ska hämtas från quay.io. |
| **`api.openshift.com`** | **HTTPS:443** | Krävs av klustret för att kontrollera om det finns tillgängliga uppdateringar innan avbildningssignaturerna laddas ned. |
| **`arosvc.azurecr.io`** | **HTTPS:443** | Internt privat register för ARO-operatörer.  Krävs om du inte tillåter tjänstslutpunkterna Microsoft.ContainerRegistry på dina undernät. |
| **`management.azure.com`** | **HTTPS:443** | Detta används av klustret för att få åtkomst till Azure-API:er. |
| **`login.microsoftonline.com`** | **HTTPS:443** | Detta används av klustret för autentisering till Azure. |
| **`gcs.prod.monitoring.core.windows.net`** | **HTTPS:443** | Detta används för Microsoft Genève-övervakning så att ARO-teamet kan övervaka kundens kluster. |
| **`*.blob.core.windows.net`** | **HTTPS:443** | Detta används för Microsoft Genève-övervakning så att ARO-teamet kan övervaka kundens kluster. |
| **`*.servicebus.windows.net`** | **HTTPS:443** | Detta används för Microsoft Genève-övervakning så att ARO-teamet kan övervaka kundens kluster. |
| **`*.table.core.windows.net`** | **HTTPS:443** | Detta används för Microsoft Genève-övervakning så att ARO-teamet kan övervaka kundens kluster. |

> [!NOTE] 
> För många kunder som exponerar *.blob skapar *.table och andra stora adressutrymmen ett potentiellt problem med datafiltrering. Du kan överväga att använda [brandväggen för utgående OpenShift](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/configuring-egress-firewall.html) för att skydda program som distribueras i klustret från att nå dessa mål och använda Azure Private Link för specifika programbehov.

---

## <a name="complete-list-of-required-and-optional-fqdns"></a>Fullständig lista över obligatoriska och valfria FQDN

### <a name="first-group-installing-and-downloading-packages-and-tools"></a>FÖRSTA GRUPPEN: INSTALLERA OCH LADDA NED PAKET OCH VERKTYG

- **`quay.io`**: Obligatoriskt för installationen som används av klustret. Detta används av klustret för att ladda ned plattformscontaineravbildningarna.
- **`registry.redhat.io`**: Obligatoriskt för kärn-tillägg. Det här används av klustret för att ladda ned kärnkomponenter som utvecklingsverktyg, operatörsbaserade tillägg eller Red Hat-tillhandahållna containeravbildningar som vårt mellanprogram, Universal Base Image...
- **`sso.redhat.com`**: Den här krävs i VDI-miljön eller din bärbara dator för att ansluta till cloud.redhat.com. Det här är den webbplats där vi kan ladda ned pull-hemligheten och använda några av de SaaS-lösningar som vi erbjuder i Red Hat för att underlätta övervakning av dina prenumerationer, klusterinventering, återbetalningsrapportering med flera.
- **`openshift.org`**: Det här krävs i VDI-miljön eller din bärbara dator för att kunna ladda ned RH CoreOS-avbildningar, men i Azure behöver de inte ladda ned OS-avbildningar från marknadsplatsen.

---

### <a name="second-group-telemetry"></a>ANDRA GRUPPEN: TELEMETRI

Allt det här avsnittet kan väljas bort, men innan vi vet hur du gör bör du kontrollera vad det är: https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/about-remote-health-monitoring.html
- **`cert-api.access.redhat.com`**: Använd i din VDI- eller bärbara miljö.
- **`api.access.redhat.com`**: Använd i din VDI- eller bärbara miljö.
- **`infogw.api.openshift.com`**: Använd i din VDI- eller bärbara miljö.
- **`https://cloud.redhat.com/api/ingress`**: Använd i klustret för insiktsoperatorn som integreras med aaS Red Hat Insights.
I OpenShift Container Platform kan kunder välja att inte rapportera hälso- och användningsinformation. Anslutna kluster gör dock att Red Hat kan reagera snabbare på problem och ge bättre stöd till våra kunder och bättre förstå hur kluster för produktuppgraderingar fungerar. Kontrollera informationen här: https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/opting-out-of-remote-health-reporting.html .

---

### <a name="third-group-cloud-apis"></a>TREDJE GRUPPEN: MOLN-API:ER

- **`management.azure.com`**: Detta används av klustret för att få åtkomst till Azure-API:er.

---

### <a name="fourth-group-other-openshift-requirements"></a>FJÄRDE GRUPPEN: ANDRA OPENSHIFT-KRAV

- **`mirror.openshift.com`**: Den här krävs i VDI-miljön eller din bärbara dator för att få åtkomst till speglat installationsinnehåll och bilder och krävs i klustret för att ladda ned signaturer för plattformsutgåvningar som används av klustret för att veta vilka avbildningar som ska hämtas från quay.io.
- **`storage.googleapis.com/openshift-release`**: Alternativ webbplats för att ladda ned signaturer för plattformsutgågå tillbaka, som används av klustret för att veta vilka avbildningar som ska hämtas quay.io.
- **`*.apps.<cluster_name>.<base_domain>`** (ELLER MOTSVARANDE ARO-URL): När domäner tillåts används detta i företagsnätverket för att nå program som distribueras i OpenShift eller för att få åtkomst till OpenShift-konsolen.
- **`api.openshift.com`**: Krävs av klustret för att kontrollera om det finns tillgängliga uppdateringar innan avbildningssignaturerna laddas ned.
- **`registry.access.redhat.com`**: Registeråtkomst krävs i din VDI- eller bärbara miljö för att ladda ned dev-avbildningar när du använder ODO CLI-verktyget. (Det här CLI-verktyget är ett alternativt CLI-verktyg för utvecklare som inte är bekanta med kubernetes). https://docs.openshift.com/container-platform/4.6/cli_reference/developer_cli_odo/understanding-odo.html

---

### <a name="fifth-group-microsoft--red-hat-aro-monitoring-service"></a>FEMTE GRUPPEN: MICROSOFT & RED HAT ARO MONITORING SERVICE

- **`login.microsoftonline.com`**: Detta används av klustret för autentisering till Azure.
- **`gcs.prod.monitoring.core.windows.net`**: Detta används för Microsoft Genève-övervakning så att ARO-teamet kan övervaka kundens kluster.
- **`*.blob.core.windows.net`**: Detta används för Microsoft Genève-övervakning så att ARO-teamet kan övervaka kundens kluster.
- **`*.servicebus.windows.net`**: Detta används för Microsoft Genève-övervakning så att ARO-teamet kan övervaka kundens kluster.
- **`*.table.core.windows.net`**: Detta används för Microsoft Genève-övervakning så att ARO-teamet kan övervaka kundens kluster.

## <a name="aro-integrations"></a>ARO-integreringar

### <a name="azure-monitor-for-containers"></a>Azure Monitor för containrar

Det finns två alternativ för att ge åtkomst till Azure Monitor för containrar. Du kan tillåta Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags)  eller ge åtkomst till de FQDN/programregler som krävs.  Här följer anvisningar [om](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-azure-redhat4-setup) hur du lägger till Azure Monitor ditt befintliga ARO-kluster.

#### <a name="required-network-rules"></a>Nätverksregler som krävs

Följande FQDN/programregler krävs:

| Målslutpunkt                                                             | Protokoll | Port    | Användning  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Den här slutpunkten används för att skicka måttdata och loggar till Azure Monitor och Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Obligatoriskt FQDN/programregler

Följande FQDN/programregler krävs för ARO-kluster som har Azure Monitor för containrar aktiverade:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| **`dc.services.visualstudio.com`** | **`HTTPS:443`**    | Den här slutpunkten används för mått och övervakning av telemetri med hjälp av Azure Monitor. |
| **`*.ods.opinsights.azure.com`**    | **`HTTPS:443`**    | Den här slutpunkten används av Azure Monitor för att mata in Log Analytics-data. |
| **`*.oms.opinsights.azure.com`** | **`HTTPS:443`** | Den här slutpunkten används av omsagent, som används för att autentisera Log Analytics-tjänsten. |
| **`*.monitoring.azure.com`** | **`HTTPS:443`** | Den här slutpunkten används för att skicka måttdata till Azure Monitor. |


## <a name="private-aro-cluster-setup"></a>Konfiguration av privat ARO-kluster
Målet är att skydda ARO-kluster genom att dirigera utgående trafik via en Azure Firewall
### <a name="before"></a>Innan:
![Före](media/concepts-networking/aro-private.jpg)
### <a name="after"></a>Efter:
![Efter](media/concepts-networking/aro-fw.jpg)

## <a name="create-a-private-aro-cluster"></a>Skapa ett privat ARO-kluster

### <a name="set-up-vars-for-your-environment"></a>Konfigurera VARS för din miljö
```bash

CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster
AROVNET=aro-vnet # The name of your vnet from your created ARO cluster
JUMPSUBNET=jump-subnet
LOCATION=eastus # The location where ARO cluster is deployed

```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
```bash
az group create -g "$RESOURCEGROUP" -l $LOCATION
```

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket
```bash
az network vnet create \
  -g $RESOURCEGROUP \
  -n $AROVNET \
  --address-prefixes 10.0.0.0/8
```

### <a name="add-two-empty-subnets-to-your-virtual-network"></a>Lägg till två tomma undernät i det virtuella nätverket
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "$CLUSTER-master" \
    --address-prefixes 10.10.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry

  az network vnet subnet create \
    -g $RESOURCEGROUP \
    --vnet-name $AROVNET \
    -n "$CLUSTER-worker" \
    --address-prefixes 10.20.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```

### <a name="disable-network-policies-for-private-link-service-on-your-virtual-network-and-subnets-this-is-a-requirement-for-the-aro-service-to-access-and-manage-the-cluster"></a>Inaktivera nätverksprinciper för Private Link Service i ditt virtuella nätverk och undernät. Detta är ett krav för att ARO-tjänsten ska kunna komma åt och hantera klustret.
```bash
az network vnet subnet update \
  -g "$RESOURCEGROUP" \
  --vnet-name $AROVNET \
  -n "$CLUSTER-master" \
  --disable-private-link-service-network-policies true
```
### <a name="create-a-firewall-subnet"></a>Skapa ett brandväggsundernät
```bash
az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "AzureFirewallSubnet" \
    --address-prefixes 10.100.1.0/26
```

## <a name="create-a-jump-host-vm"></a>Skapa en virtuell jump-host-dator
### <a name="create-a-jump-subnet"></a>Skapa ett jump-subnet
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n $JUMPSUBNET \
    --address-prefixes 10.30.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```
### <a name="create-a-jump-host-vm"></a>Skapa en virtuell jump-host-dator
```bash
VMUSERNAME=aroadmin

az vm create --name ubuntu-jump \
             --resource-group $RESOURCEGROUP \
             --ssh-key-values ~/.ssh/id_rsa.pub \
             --admin-username $VMUSERNAME \
             --image UbuntuLTS \
             --subnet $JUMPSUBNET \
             --public-ip-address jumphost-ip \
             --vnet-name $AROVNET 
```

## <a name="create-an-azure-red-hat-openshift-cluster"></a>Skapa ett Azure Red Hat OpenShift-kluster
### <a name="get-a-red-hat-pull-secret-optional"></a>Hämta en Red Hat-pull-hemlighet (valfritt)

En Red Hat-pull-hemlighet gör att ditt kluster kan komma åt Red Hat-containerregister tillsammans med annat innehåll. Det här steget är valfritt men rekommenderas.

1. **[Gå till klusterhanterare-portalen för Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) och logga in.**

   Du måste logga in på ditt Red Hat-konto eller skapa ett nytt Red Hat-konto med din företags-e-postadress och godkänna villkoren.

2. **Klicka på Hämta hämtningshemlighet.**

Förvara den sparade `pull-secret.txt` filen på en säker plats – den används vid varje skapande av kluster.

När du kör `az aro create` kommandot kan du referera till din pull-hemlighet med hjälp av `--pull-secret @pull-secret.txt` parametern . Kör `az aro create` från katalogen där du lagrade `pull-secret.txt` filen. Annars ersätter du `@pull-secret.txt` med `@<path-to-my-pull-secret-file` .

Om du kopierar pull-hemligheten eller refererar till den i andra skript bör pull-hemligheten formateras som en giltig JSON-sträng.

```bash
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet $AROVNET \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --apiserver-visibility Private \
  --ingress-visibility Private \
  --pull-secret @pull-secret.txt
```

## <a name="create-an-azure-firewall"></a>Skapa en Azure Firewall

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress
```bash
az network public-ip create -g $RESOURCEGROUP -n fw-ip --sku "Standard" --location $LOCATION
```
### <a name="update-install-azure-firewall-extension"></a>Uppdatera Azure Firewall installation
```bash
az extension add -n azure-firewall
az extension update -n azure-firewall
```

### <a name="create-azure-firewall-and-configure-ip-config"></a>Skapa Azure Firewall och konfigurera IP-konfiguration
```bash
az network firewall create -g $RESOURCEGROUP -n aro-private -l $LOCATION
az network firewall ip-config create -g $RESOURCEGROUP -f aro-private -n fw-config --public-ip-address fw-ip --vnet-name $AROVNET

```

### <a name="capture-azure-firewall-ips-for-a-later-use"></a>Avbilda Azure Firewall-IP-adresser för senare användning
```bash
FWPUBLIC_IP=$(az network public-ip show -g $RESOURCEGROUP -n fw-ip --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RESOURCEGROUP -n aro-private --query "ipConfigurations[0].privateIpAddress" -o tsv)

echo $FWPUBLIC_IP
echo $FWPRIVATE_IP
```

### <a name="create-a-udr-and-routing-table-for-azure-firewall"></a>Skapa en UDR och routningstabell för Azure Firewall
```bash
az network route-table create -g $RESOURCEGROUP --name aro-udr

az network route-table route create -g $RESOURCEGROUP --name aro-udr --route-table-name aro-udr --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP
```

### <a name="add-application-rules-for-azure-firewall"></a>Lägga till programregler för Azure Firewall
Regel för att OpenShift ska fungera baserat på den här [listan:](https://docs.openshift.com/container-platform/4.3/installing/install_config/configuring-firewall.html#configuring-firewall_configuring-firewall)
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'ARO' \
 --action allow \
 --priority 100 \
 -n 'required' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns 'registry.redhat.io' '*.quay.io' 'sso.redhat.com' 'management.azure.com' 'mirror.openshift.com' 'api.openshift.com' 'quay.io' '*.blob.core.windows.net' 'gcs.prod.monitoring.core.windows.net' 'registry.access.redhat.com' 'login.microsoftonline.com' '*.servicebus.windows.net' '*.table.core.windows.net' 'grafana.com'
```
Valfria regler för Docker-avbildningar:
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'Docker' \
 --action allow \
 --priority 200 \
 -n 'docker' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns '*cloudflare.docker.com' '*registry-1.docker.io' 'apt.dockerproject.org' 'auth.docker.io'
```

### <a name="associate-aro-subnets-to-fw"></a>Associera ARO-undernät med FW
```bash
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-master" --route-table aro-udr
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-worker" --route-table aro-udr
```

## <a name="test-the-configuration-from-the-jumpbox"></a>Testa konfigurationen från Jumpbox
De här stegen fungerar bara om du har lagt till regler för Docker-avbildningar. 
### <a name="configure-the-jumpbox"></a>Konfigurera jumpboxen
Logga in på en virtuell jumpbox-dator `azure-cli` och installera - och `oc-cli` `jq` -verktyg. Om du vill installera openshift-cli går du till Red Hat-kundportalen.
```bash
#Install Azure-cli
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
#Install jq
sudo apt install jq -y
```
### <a name="log-into-the-aro-cluster"></a>Logga in på ARO-klustret
Lista klusterautentiseringsuppgifter:
```bash

# Login to Azure
az login
# Set Vars in Jumpbox
CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster

#Get the cluster credentials
ARO_PASSWORD=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminPassword')
ARO_USERNAME=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminUsername')
```
Hämta en API-serverslutpunkt:
```bash
ARO_URL=$(az aro show -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.apiserverProfile.url')
```

### <a name="download-the-oc-cli-to-the-jumpbox"></a>Ladda ned oc CLI till jumpboxen
```bash
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

Logga in med `oc login` :
```bash
oc login $ARO_URL -u $ARO_USERNAME -p $ARO_PASSWORD
```

### <a name="run-centos-to-test-outside-connectivity"></a>Kör CentOS för att testa utanför anslutningen
Skapa en podd
```bash
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: centos
spec:
  containers:
  - name: centos
    image: centos
    ports:
    - containerPort: 80
    command:
    - sleep
    - "3600"
EOF
```
När podden körs kan du exec into it and test outside connectivity (testa utanför anslutningen).

```bash
oc exec -it centos -- /bin/bash
curl microsoft.com
```

## <a name="access-the-web-console-of-the-private-cluster"></a>Få åtkomst till webbkonsolen för det privata klustret

### <a name="set-up-ssh-forwards-commands"></a>Konfigurera ssh-kommandon för vidarebefordran

```bash
sudo ssh -i $SSH_PATH -L 443:$CONSOLE_URL:443 aroadmin@$JUMPHOST

example:
sudo ssh -i /Users/jimzim/.ssh/id_rsa -L 443:console-openshift-console.apps.d5xm5iut.eastus.aroapp.io:443 aroadmin@104.211.18.56
```

### <a name="modify-the-etc-hosts-file-on-your-local-machine"></a>Ändra värdfilen med mera på den lokala datorn
```bash
##
# Host Database
#
127.0.0.1 console-openshift-console.apps.d5xm5iut.eastus.aroapp.io
127.0.0.1 oauth-openshift.apps.d5xm5iut.eastus.aroapp.io
```

### <a name="use-sshuttle-as-another-option"></a>Använd sshutong som ett annat alternativ

[SSHuttle](https://github.com/sshuttle/sshuttle)


## <a name="clean-up-resources"></a>Rensa resurser

```bash

# Clean up the ARO cluster, vnet, firewall and jumpbox

# Remove udr from master and worker subnets first or will get error when deleting ARO cluster
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-master -g $RESOURCEGROUP --route-table aro-udr --remove routeTable
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-worker -g $RESOURCEGROUP --route-table aro-udr --remove routeTable

# Remove ARO Cluster
az aro delete -n $CLUSTER -g $RESOURCEGROUP

# Remove the resource group that contains the firewall, jumpbox and vnet
az group delete -n $RESOURCEGROUP
```