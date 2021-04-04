---
title: Konfigurera tillgänglighets grupper för SQL Server på virtuella RHEL-datorer i Azure – Linux virtuella datorer | Microsoft Docs
description: Lär dig mer om att konfigurera hög tillgänglighet i en RHEL-kluster miljö och konfigurera STONITH
ms.service: virtual-machines-linux
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 06/25/2020
ms.openlocfilehash: 533f5c9e38818a8e37482cbbb3a90602366eca6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587221"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Självstudie: Konfigurera tillgänglighets grupper för SQL Server på virtuella RHEL-datorer i Azure 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> Vi använder SQL Server 2017 med RHEL 7,6 i den här självstudien, men det går att använda SQL Server 2019 i RHEL 7 eller RHEL 8 för att konfigurera hög tillgänglighet. Kommandona för att konfigurera pacemake-klustret och tillgänglighets grupps resurserna har ändrats i RHEL 8, och du vill titta på artikeln [skapa tillgänglighets grupps resurs](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) och RHEL 8-resurser för mer information om rätt kommandon.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> - Skapa en ny resurs grupp, tillgänglighets uppsättning och virtuella Linux-datorer (VM)
> - Aktivera hög tillgänglighet (HA)
> - Skapa ett pacemaker-kluster
> - Konfigurera en inhägnad-agent genom att skapa en STONITH-enhet
> - Installera SQL Server och MSSQL-tools på RHEL
> - Konfigurera SQL Server Always on-tillgänglighetsgrupper
> - Konfigurera resurs för tillgänglighets grupp (AG) i pacemaker-klustret
> - Testa en redundansväxling och staket-agenten

I den här självstudien används Azure CLI för att distribuera resurser i Azure.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.30 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om du har mer än en prenumeration [anger du den prenumeration](/cli/azure/manage-azure-subscriptions-azure-cli) som du vill distribuera resurserna till.

Använd följande kommando för att skapa en resurs grupp `<resourceGroupName>` i en region. Ersätt `<resourceGroupName>` med ett namn som du väljer. Vi använder `East US 2` i den här självstudien. Mer information finns i följande [snabb start](../../../application-gateway/quick-create-cli.md).

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning

Nästa steg är att skapa en tillgänglighets uppsättning. Kör följande kommando i Azure Cloud Shell och Ersätt `<resourceGroupName>` med namnet på din resurs grupp. Välj ett namn för `<availabilitySetName>` .

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Du bör få följande resultat när kommandot har slutförts:

```output
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/availabilitySets/<availabilitySetName>",
  "location": "eastus2",
  "name": "<availabilitySetName>",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 2,
  "proximityPlacementGroup": null,
  "resourceGroup": "<resourceGroupName>",
  "sku": {
    "capacity": null,
    "name": "Aligned",
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-rhel-vms-inside-the-availability-set"></a>Skapa virtuella RHEL-datorer i tillgänglighets uppsättningen

> [!WARNING]
> Om du väljer en PAYG-RHEL (betala per användning) och konfigurerar hög tillgänglighet (HA) kan du behöva registrera din prenumeration. Detta kan medföra att du betalar två gånger för prenumerationen, eftersom du debiteras för den Microsoft Azure RHEL-prenumerationen för den virtuella datorn och en prenumeration på Red Hat. Mer information finns i https://access.redhat.com/solutions/2458541.
>
> Använd en RHEL HA-avbildning när du skapar den virtuella Azure-datorn för att undvika att "dubbelt faktureras". Avbildningar som erbjuds som RHEL-HA-avbildningar är också PAYG-avbildningar med HA lagrings platsen pre-aktiverad.

1. Hämta en lista över avbildningar av virtuella datorer som erbjuder RHEL med HA:

    ```azurecli-interactive
    az vm image list --all --offer "RHEL-HA"
    ```

    Du bör se följande resultat:

    ```output
    [
      {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.4",
    "urn": "RedHat:RHEL-HA:7.4:7.4.2019062021",
    "version": "7.4.2019062021"
       },
       {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.5",
    "urn": "RedHat:RHEL-HA:7.5:7.5.2019062021",
    "version": "7.5.2019062021"
        },
        {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.6",
    "urn": "RedHat:RHEL-HA:7.6:7.6.2019062019",
    "version": "7.6.2019062019"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.0",
    "urn": "RedHat:RHEL-HA:8.0:8.0.2020021914",
    "version": "8.0.2020021914"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.1",
    "urn": "RedHat:RHEL-HA:8.1:8.1.2020021914",
    "version": "8.1.2020021914"
          },
          {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "80-gen2",
    "urn": "RedHat:RHEL-HA:80-gen2:8.0.2020021915",
    "version": "8.0.2020021915"
           },
           {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "81_gen2",
    "urn": "RedHat:RHEL-HA:81_gen2:8.1.2020021915",
    "version": "8.1.2020021915"
           }
    ]
    ```

    I den här självstudien väljer vi bilden `RedHat:RHEL-HA:7.6:7.6.2019062019` för RHEL 7-exemplet och väljer `RedHat:RHEL-HA:8.1:8.1.2020021914` för RHEL 8-exemplet.
    
    Du kan också välja SQL Server 2019 förinstallerade på RHEL8-HA-avbildningar. Kör följande kommando för att hämta listan över de här avbildningarna:  
    
    ```azurecli-interactive
    az vm image list --all --offer "sql2019-rhel8"
    ```

    Du bör se följande resultat:

    ```output
    [
      {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200317",
    "version": "15.0.200317"
       },
       }
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200512",
    "version": "15.0.200512"
       }
    ]
    ```

    Om du använder någon av ovanstående avbildningar för att skapa de virtuella datorerna har SQL Server 2019 förinstallerat. Hoppa över avsnittet [installera SQL Server och MSSQL-tools](#install-sql-server-and-mssql-tools) enligt beskrivningen i den här artikeln.
    
    
    > [!IMPORTANT]
    > Dator namn måste innehålla färre än 15 tecken för att du ska kunna konfigurera tillgänglighets gruppen. Användar namnet får inte innehålla versaler och lösen ord måste innehålla fler än 12 tecken.

1. Vi vill skapa tre virtuella datorer i tillgänglighets uppsättningen. Ersätt följande i kommandot nedan:

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>` – Ett exempel är "Standard_D16_v3"
    - `<username>`
    - `<adminPassword>`

    ```azurecli-interactive
    for i in `seq 1 3`; do
           az vm create \
             --resource-group <resourceGroupName> \
             --name <VM-basename>$i \
             --availability-set <availabilitySetName> \
             --size "<VM-Size>"  \
             --image "RedHat:RHEL-HA:7.6:7.6.2019062019" \
             --admin-username "<username>" \
             --admin-password "<adminPassword>" \
             --authentication-type all \
             --generate-ssh-keys
    done
    ```

Kommandot ovan skapar de virtuella datorerna och skapar ett virtuellt nätverk för de virtuella datorerna. Mer information om de olika konfigurationerna finns i artikeln [AZ VM Create](/cli/azure/vm) .

Du bör få resultat som liknar följande när kommandot har slutförts för varje virtuell dator:

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<VM1>",
  "location": "eastus2",
  "macAddress": "<Some MAC address>",
  "powerState": "VM running",
  "privateIpAddress": "<IP1>",
  "publicIpAddress": "",
  "resourceGroup": "<resourceGroupName>",
  "zones": ""
}
```

> [!IMPORTANT]
> Standard avbildningen som skapas med kommandot ovan skapar en 32 GB OS-disk som standard. Du kan eventuellt ta slut på utrymme med den här standard installationen. Du kan använda följande parameter som har lagts till i `az vm create` kommandot ovan för att skapa en OS-disk med 128 GB som exempel: `--os-disk-size-gb 128` .
>
> Du kan sedan [Konfigurera Logical Volume Manager (LVM)](/previous-versions/azure/virtual-machines/linux/configure-lvm) om du behöver expandera lämpliga volym volymer för att hantera installationen.

### <a name="test-connection-to-the-created-vms"></a>Testa anslutningen till de skapade virtuella datorerna

Anslut till VM1 eller de andra virtuella datorerna med hjälp av följande kommando i Azure Cloud Shell. Om du inte kan hitta dina VM IP-adresser följer du den här [snabb starten på Azure Cloud Shell](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm).

```azurecli-interactive
ssh <username>@publicipaddress
```

Om anslutningen lyckas bör du se följande utdata som representerar Linux-terminalen:

```output
[<username>@<VM1> ~]$
```

Skriv `exit` för att lämna SSH-sessionen.

## <a name="enable-high-availability"></a>Aktivera hög tillgänglighet

> [!IMPORTANT]
> För att kunna slutföra den här delen av självstudien måste du ha en prenumeration på RHEL och det hög tillgängliga tillägget. Om du använder en avbildning som rekommenderas i föregående avsnitt behöver du inte registrera någon annan prenumeration.
 
Anslut till varje VM-nod och följ anvisningarna nedan för att aktivera HA. Mer information finns i [Aktivera prenumeration med hög tillgänglighet för RHEL](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel).

> [!TIP]
> Det blir enklare om du öppnar en SSH-session till varje virtuell dator samtidigt som samma kommandon måste köras på varje virtuell dator i artikeln.
>
> Om du kopierar och klistrar in flera `sudo` kommandon och uppmanas att ange ett lösen ord, kommer de ytterligare kommandona inte att köras. Kör varje kommando separat.


1. Kör följande kommandon på varje virtuell dator för att öppna pacemaker-brand Väggs portarna:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. Uppdatera och installera pacemaker-paket på alla noder med följande kommandon:

    > [!NOTE]
    > **nmap** installeras som en del av det här kommando blocket som ett verktyg för att hitta tillgängliga IP-adresser i nätverket. Du behöver inte installera **nmap**, men det kommer att vara användbart senare i den här självstudien.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Ange lösen ordet för standard användaren som skapas när du installerar pacemaker-paket. Använd samma lösen ord på alla noder.

    ```bash
    sudo passwd hacluster
    ```

1. Använd följande kommando för att öppna hosts-filen och konfigurera värd namns matchning. Mer information finns i [Konfigurera AG](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) på Konfigurera värd filen.

    ```
    sudo vi /etc/hosts
    ```

    I redigeraren för **vi** skriver `i` du in text och lägger till den **privata IP-adressen** för motsvarande virtuella dator på en tom rad. Lägg sedan till namnet på den virtuella datorn efter ett blank steg bredvid IP-adressen. Varje rad måste ha en separat post.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > Vi rekommenderar att du använder din **privata IP** -adress ovan. Om du använder den offentliga IP-adressen i den här konfigurationen kommer installationen att Miss varnas och vi rekommenderar inte att du exponerar den virtuella datorn för externa nätverk.

    Om du vill avsluta **vi** -redigeraren trycker du först på **ESC** -tangenten och anger sedan kommandot `:wq` för att skriva filen och avsluta.

## <a name="create-the-pacemaker-cluster"></a>Skapa pacemaker-klustret

I det här avsnittet ska vi aktivera och starta pcsd-tjänsten och sedan konfigurera klustret. För SQL Server på Linux skapas inte kluster resurserna automatiskt. Vi måste aktivera och skapa pacemaker-resurserna manuellt. Mer information finns i artikeln om [att konfigurera en instans av redundanskluster för RHEL](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node)

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>Aktivera och starta pcsd-tjänsten och pacemaker

1. Kör kommandona på alla noder. De här kommandona gör att noderna kan ansluta till klustret igen efter omstart.

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. Ta bort eventuell befintlig kluster konfiguration från alla noder. Kör följande kommando:

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. På den primära noden kör du följande kommandon för att konfigurera klustret.

    - När du kör `pcs cluster auth` kommandot för att autentisera klusternoderna uppmanas du att ange ett lösen ord. Ange lösen ordet för **hacluster** -användaren som skapades tidigare.

    **RHEL7**

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

    **RHEL8**

    För RHEL 8 måste du autentisera noderna separat. Ange manuellt användar namn och lösen ord för **hacluster** när du uppmanas till det.

    ```bash
    sudo pcs host auth <node1> <node2> <node3>
    sudo pcs cluster setup <clusterName> <node1> <node2> <node3>
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. Kör följande kommando för att kontrol lera att alla noder är online.

    ```bash
    sudo pcs status
    ```

   **RHEL 7** 
   
    Om alla noder är online visas utdata som liknar följande:

    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Stack: corosync
    Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    Last updated: Fri Aug 23 18:27:57 2019
    Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
     
    3 nodes configured
    0 resources configured
     
    Online: [ <VM1> <VM2> <VM3> ]
     
    No resources
     
     
    Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    ```
   
   **RHEL 8** 
   
    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Cluster Summary:
    * Stack: corosync
    * Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    * Last updated: Fri Aug 23 18:27:57 2019
    * Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
    * 3 nodes configured
    * 0 resource instances configured
     
   Node List:
    * Online: [ <VM1> <VM2> <VM3> ]
   
   Full List of Resources:
   * No resources
     
   Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    
    ```
    
1. Ange förväntade röster i Live-klustret till 3. Det här kommandot påverkar endast det aktiva klustret och ändrar inte konfigurationsfilerna.

    Ange de förväntade rösterna med följande kommando på alla noder:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>Konfigurera staket-agenten

En STONITH-enhet tillhandahåller en inhägnad-agent. Anvisningarna nedan har ändrats för den här självstudien. Mer information finns i [skapa en STONITH-enhet](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device).
 
[Kontrol lera versionen av Azure-stängsel-agenten för att säkerställa att den uppdateras](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation). Ange följande kommando:

```bash
sudo yum info fence-agents-azure-arm
```

Du bör se samma utdata som i exemplet nedan.

```output
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
Installed Packages
Name        : fence-agents-azure-arm
Arch        : x86_64
Version     : 4.2.1
Release     : 11.el7_6.8
Size        : 28 k
Repo        : installed
From repo   : rhel-ha-for-rhel-7-server-eus-rhui-rpms
Summary     : Fence agent for Azure Resource Manager
URL         : https://github.com/ClusterLabs/fence-agents
License     : GPLv2+ and LGPLv2+
Description : The fence-agents-azure-arm package contains a fence agent for Azure instances.
```

### <a name="register-a-new-application-in-azure-active-directory"></a>Registrera ett nytt program i Azure Active Directory
 
 1. Gå till https://portal.azure.com
 2. Öppna [bladet Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties). Gå till egenskaper och skriv ner katalog-ID: t. Detta är `tenant ID`
 3. Klicka på [ **Appregistreringar**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. Klicka på **ny registrering**
 5. Ange ett **namn** som `<resourceGroupName>-app` , Välj **endast konton i den här organisations katalogen**
 6. Välj program typ **webb**, ange en inloggnings-URL (till exempel http://localhost) och klicka på Lägg till. Inloggnings-URL: en används inte och kan vara en giltig URL. När du är färdig klickar du på **Registrera**
 7. Välj **certifikat och hemligheter** för din nya app-registrering och klicka sedan på **ny klient hemlighet**
 8. Ange en beskrivning för en ny nyckel (klient hemlighet), Välj **aldrig upphör att gälla** och klicka på **Lägg till**
 9. Skriv ned värdet för hemligheten. Den används som lösen ord för tjänstens huvud namn
10. Välj **Översikt**. Anteckna program-ID: t. Den används som användar namn (inloggnings-ID i stegen nedan) för tjänstens huvud namn
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Skapa en anpassad roll för stängsel-agenten

Följ själv studie kursen för att [skapa en anpassad Azure-roll med hjälp av Azure CLI](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role).

Din JSON-fil bör se ut ungefär så här:

- Ersätt `<username>` med ett valfritt namn. Detta är för att undvika duplicering när du skapar den här roll definitionen.
- Ersätt `<subscriptionId>` med ditt Azure-prenumerations-ID.

```json
{
  "Name": "Linux Fence Agent Role-<username>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscriptionId>"
  ]
}
```

Kör följande kommando för att lägga till rollen:

- Ersätt `<filename>` med namnet på filen.
- Om du kör kommandot från en annan sökväg än den mapp som filen sparas i, inkluderar du mappsökvägen för filen i kommandot.

```bash
az role definition create --role-definition "<filename>.json"
```

Du bör se följande utdata:

```output
{
  "assignableScopes": [
    "/subscriptions/<subscriptionId>"
  ],
  "description": "Allows to power-off and start virtual machines",
  "id": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<roleNameId>",
  "name": "<roleNameId>",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/virtualMachines/powerOff/action",
        "Microsoft.Compute/virtualMachines/start/action"
      ],
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Linux Fence Agent Role-<username>",
  "roleType": "CustomRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="assign-the-custom-role-to-the-service-principal"></a>Tilldela tjänstens huvud namn den anpassade rollen

Tilldela den anpassade rollen `Linux Fence Agent Role-<username>` som skapades i det sista steget till tjänstens huvud namn. Använd inte ägar rollen längre!
 
1. Gå till https://portal.azure.com
2. Öppna [bladet alla resurser](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)
3. Välj den virtuella datorn för den första klusternoden
4. Klicka på **åtkomst kontroll (IAM)**
5. Klicka på **Lägg till en roll tilldelning**
6. Välj rollen `Linux Fence Agent Role-<username>` från **roll** listan
7. I listan **Välj** anger du namnet på det program som du skapade ovan. `<resourceGroupName>-app`
8. Klicka på **Spara**
9. Upprepa stegen ovan för noden alla klusternoder.

### <a name="create-the-stonith-devices"></a>Skapa STONITH-enheter

Kör följande kommandon på nod 1:

- Ersätt `<ApplicationID>` med ID-värdet från program registreringen.
- Ersätt `<servicePrincipalPassword>` med värdet från klient hemligheten.
- Ersätt `<resourceGroupName>` med resurs gruppen från din prenumeration som används för den här självstudien.
- Ersätt `<tenantID>` och `<subscriptionId>` från din Azure-prenumeration.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

Eftersom vi redan har lagt till en regel i brand väggen för att tillåta HA-tjänsten ( `--add-service=high-availability` ) behöver du inte öppna följande brand Väggs portar på alla noder: 2224, 3121, 21064, 5405. Men om du har problem med en typ av anslutnings problem med HA, använder du följande kommando för att öppna portarna som är associerade med HA.

> [!TIP]
> Du kan också lägga till alla portar i den här självstudien på en gång för att spara tid. De portar som måste öppnas förklaras i de relativa avsnitten nedan. Om du vill lägga till alla portar nu lägger du till de ytterligare portarna: 1433 och 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>Installera SQL Server och MSSQL-tools

> [!NOTE]
> Om du har skapat de virtuella datorerna med SQL Server 2019 förinstallerat på RHEL8-HA kan du hoppa över stegen nedan för att installera SQL Server och MSSQL-verktyg och starta avsnittet **Konfigurera en tillgänglighets grupp** när du har konfigurerat sa-lösenordet på alla virtuella datorer genom att köra kommandot `sudo /opt/mssql/bin/mssql-conf set-sa-password` på alla virtuella datorer.

Använd avsnittet nedan för att installera SQL Server och MSSQL-verktyg på de virtuella datorerna. Du kan välja ett av nedanstående exempel för att installera SQL Server 2017 på RHEL 7 eller SQL Server 2019 på RHEL 8. Utför var och en av dessa åtgärder på alla noder. Mer information finns i [installera SQL Server på en Red Hat-dator](/sql/linux/quickstart-install-connect-red-hat).


### <a name="installing-sql-server-on-the-vms"></a>Installera SQL Server på de virtuella datorerna

Följande kommandon används för att installera SQL Server:

**RHEL 7 med SQL Server 2017** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

**RHEL 8 med SQL Server 2019** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/8/mssql-server-2019.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```
### <a name="open-firewall-port-1433-for-remote-connections"></a>Öppna brand Väggs port 1433 för fjärr anslutningar

Du måste öppna port 1433 på den virtuella datorn för att kunna fjärrans luta. Använd följande kommandon för att öppna port 1433 i brand väggen för varje virtuell dator:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>Installera SQL Server kommando rads verktyg

Följande kommandon används för att installera SQL Server kommando rads verktyg. Mer information finns i [installera SQL Server kommando rads verktyg](/sql/linux/quickstart-install-connect-red-hat#tools).

**RHEL 7** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```

**RHEL 8** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/8/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> För enkelhetens skull lägger du till/opt/MSSQL-tools/bin/i miljövariabeln PATH. På så sätt kan du köra verktygen utan att ange den fullständiga sökvägen. Kör följande kommandon för att ändra PATH för både inloggningssessioner och interaktiva/icke-inloggningssessioner:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>Kontrol lera status för SQL Server

När du är färdig med konfigurationen kan du kontrol lera status för SQL Server och kontrol lera att den körs:

```bash
systemctl status mssql-server --no-pager
```

Du bör se följande utdata:

```output
● mssql-server.service - Microsoft SQL Server Database Engine
   Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-12-05 17:30:55 UTC; 20min ago
     Docs: https://docs.microsoft.com/en-us/sql/linux
 Main PID: 11612 (sqlservr)
   CGroup: /system.slice/mssql-server.service
           ├─11612 /opt/mssql/bin/sqlservr
           └─11640 /opt/mssql/bin/sqlservr
```

## <a name="configure-an-availability-group"></a>Konfigurera en tillgänglighetsgrupp

Använd följande steg för att konfigurera en SQL Server Always on-tillgänglighetsgrupper för dina virtuella datorer. Mer information finns i [konfigurera SQL Server Always on-tillgänglighetsgrupper för hög tillgänglighet i Linux](/sql/linux/sql-server-linux-availability-group-configure-ha)

### <a name="enable-always-on-availability-groups-and-restart-mssql-server"></a>Aktivera Always on-tillgänglighetsgrupper och starta om MSSQL-Server

Aktivera Always on-tillgänglighetsgrupper på varje nod som är värd för en SQL Server instans. Starta sedan om MSSQL-Server. Kör följande skript:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Skapa ett certifikat

Vi stöder för närvarande inte AD-autentisering till AG-slutpunkten. Därför måste vi använda ett certifikat för AG-slutpunktens kryptering.

1. Anslut till **alla noder** med SQL Server Management Studio (SSMS) eller SQL cmd. Kör följande kommandon för att aktivera en AlwaysOn_health-session och skapa en huvud nyckel:

    > [!IMPORTANT]
    > Om du ansluter till en fjärran sluten SQL Server-instans måste port 1433 vara öppen i brand väggen. Du måste också tillåta inkommande anslutningar till port 1433 i din NSG för varje virtuell dator. Mer information finns i [skapa en säkerhets regel](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) för att skapa en inkommande säkerhets regel.

    - Ersätt `<Master_Key_Password>` med ditt eget lösen ord.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. Anslut till den primära repliken med SSMS eller SQL CMD. De kommandon som visas nedan skapar ett certifikat till `/var/opt/mssql/data/dbm_certificate.cer` och en privat nyckel på `var/opt/mssql/data/dbm_certificate.pvk` den primära SQL Server repliken:

    - Ersätt `<Private_Key_Password>` med ditt eget lösen ord.
    
    ```sql
    CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
    GO
    
    BACKUP CERTIFICATE dbm_certificate
       TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
       WITH PRIVATE KEY (
               FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
               ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
           );
    GO
    ```

Avsluta SQL CMD-sessionen genom att köra `exit` kommandot och återgå tillbaka till SSH-sessionen.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Kopiera certifikatet till de sekundära replikerna och skapa certifikaten på servern

1. Kopiera de två filerna som har skapats till samma plats på alla servrar som ska vara värdar för tillgänglighets repliker.
 
    På den primära servern kör du följande `scp` kommando för att kopiera certifikatet till mål servrarna:

    - Ersätt `<username>` och `<VM2>` med det användar namn och den virtuella mål datorns namn som du använder.
    - Kör det här kommandot för alla sekundära repliker.

    > [!NOTE]
    > Du behöver inte köra `sudo -i` , vilket ger dig rot miljön. Du kan bara köra `sudo` kommandot framför varje kommando som vi tidigare gjorde i den här självstudien.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. Kör följande kommando på mål servern:

    - Ersätt `<username>` med ditt användar namn.
    - `mv`Kommandot flyttar filerna eller katalogen från en plats till en annan.
    - `chown`Kommandot används för att ändra ägare och grupp av filer, kataloger eller länkar.
    - Kör de här kommandona för alla sekundära repliker.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. Följande Transact-SQL-skript skapar ett certifikat från säkerhets kopian som du skapade på den primära SQL Server repliken. Uppdatera skriptet med starka lösen ord. Krypterings lösen ordet är samma lösen ord som du använde för att skapa. PVK-filen i föregående steg. Skapa certifikatet genom att köra följande skript med hjälp av SQL CMD eller SSMS på alla sekundära servrar:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    GO
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>Skapa slut punkter för databas spegling på alla repliker

Kör följande skript på alla SQL Server-instanser med SQL CMD eller SSMS:

```sql
CREATE ENDPOINT [Hadr_endpoint]
    AS TCP (LISTENER_PORT = 5022)
    FOR DATABASE_MIRRORING (
    ROLE = ALL,
    AUTHENTICATION = CERTIFICATE dbm_certificate,
ENCRYPTION = REQUIRED ALGORITHM AES
);
GO

ALTER ENDPOINT [Hadr_endpoint] STATE = STARTED;
GO
```

### <a name="create-the-availability-group"></a>Skapa tillgänglighets gruppen

Anslut till SQL Server-instansen som är värd för den primära repliken med SQL CMD eller SSMS. Skapa tillgänglighets gruppen genom att köra följande kommando:

- Ersätt `ag1` med det önskade tillgänglighets grupp namnet.
- Ersätt `<VM1>` värdena, `<VM2>` och `<VM3>` med namnen på de SQL Server instanser som är värdar för replikerna.

```sql
CREATE AVAILABILITY GROUP [ag1]
     WITH (DB_FAILOVER = ON, CLUSTER_TYPE = EXTERNAL)
     FOR REPLICA ON
         N'<VM1>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM1>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM2>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM2>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM3>'
         WITH(
            ENDPOINT_URL = N'tcp://<VM3>:5022',
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
            FAILOVER_MODE = EXTERNAL,
            SEEDING_MODE = AUTOMATIC
            );
GO

ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
GO
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>Skapa en SQL Server inloggning för pacemaker

På alla SQL Server-instanser skapar du en SQL Server inloggning för pacemaker. Följande Transact-SQL skapar en inloggning.

- Ersätt `<password>` med ditt eget komplexa lösen ord.

```sql
USE [master]
GO

CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO

ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
GO
```

På alla SQL Server-instanser sparar du de autentiseringsuppgifter som används för SQL Server inloggningen. 

1. Skapa filen:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Lägg till följande 2 rader till filen:

    ```bash
    pacemakerLogin
    <password>
    ```

    Om du vill avsluta **vi** -redigeraren trycker du först på **ESC** -tangenten och anger sedan kommandot `:wq` för att skriva filen och avsluta.

1. Gör så att filen bara läsbar av roten:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>Koppla sekundära repliker till tillgänglighets gruppen

1. För att kunna ansluta de sekundära replikerna till AG måste du öppna port 5022 i brand väggen för alla servrar. Kör följande kommando i SSH-sessionen:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. Kör följande kommandon på dina sekundära repliker för att ansluta dem till AG:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    GO
    ```

1. Kör följande Transact-SQL-skript på den primära repliken och varje sekundär replik:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    GO
    ```

1. När de sekundära replikerna har anslutits kan du se dem i SSMS Object Explorer genom att expandera noden **Always on** -noden med hög tillgänglighet:

    ![Skärm bild som visar de primära och sekundära tillgänglighets replikerna.](./media/rhel-high-availability-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Lägg till en databas i tillgänglighets gruppen

Vi följer den [Konfigurera tillgänglighets grupp artikeln för att lägga till en databas](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group).

Följande Transact-SQL-kommandon används i det här steget. Kör de här kommandona på den primära repliken:

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
GO
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Kontrol lera att databasen har skapats på de sekundära servrarna

Kör följande fråga på varje sekundär SQL Server replik för att se om DB1-databasen har skapats och är i ett SYNKRONISERAt tillstånd:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

Om `synchronization_state_desc` listorna har synkroniserats `db1` innebär det att replikerna är synkroniserade. Sekundärerna visas `db1` i den primära repliken.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Skapa tillgänglighets grupps resurser i pacemaker-klustret

Vi kommer att följa guiden för att [skapa tillgänglighets grupps resurserna i pacemaker-klustret](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only).

> [!NOTE]
> Den här artikeln innehåller referenser till termen Slav, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.

### <a name="create-the-ag-cluster-resource"></a>Skapa kluster resursen AG

1. Använd något av följande kommandon som baseras på den miljö som valdes tidigare för att skapa resursen `ag_cluster` i tillgänglighets gruppen `ag1` .

    **RHEL 7**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

    **RHEL 8**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s promotable notify=true
    ```

2. Kontrol lera din resurs och se till att de är online innan du fortsätter med följande kommando:

    ```bash
    sudo pcs resource
    ```

    Du bör se följande utdata:
    
    **RHEL 7** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    ```
    
    **RHEL 8** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    * Clone Set: ag_cluster-clone [ag_cluster] (promotable):
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel3 (Monitoring) 
    * ag_cluster             (ocf::mssql:ag) :            Master VMrhel1 (Monitoring)
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel2 (Monitoring)
    ```


### <a name="create-a-virtual-ip-resource"></a>Skapa en virtuell IP-resurs

1. Använd en tillgänglig statisk IP-adress från nätverket för att skapa en virtuell IP-resurs. Du kan hitta en med hjälp av kommando verktyget `nmap` .

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

2. Ange egenskapen **stonith-Enabled** till false

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

3. Skapa den virtuella IP-resursen med hjälp av följande kommando:

    - Ersätt `<availableIP>` värdet nedan med en oanvänd IP-adress.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Lägg till begränsningar

1. För att se till att IP-adressen och AG-resursen körs på samma nod måste en begränsning för samplacering konfigureras. Kör följande kommando:

   **RHEL 7**
  
    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

   **RHEL 8**
   
    ```bash
     sudo pcs constraint colocation add virtualip with master ag_cluster-clone INFINITY with-rsc-role=Master
    ```
  
2. Skapa en ordnings begränsning för att se till att AG-resursen är igång före IP-adressen. Även om den samplacerings begränsningen indikerar en ordnings begränsning används den.

   **RHEL 7**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-clone then start virtualip
    ```
  
3. Verifiera begränsningarna genom att köra följande kommando:

    ```bash
    sudo pcs constraint list --full
    ```

    Du bör se följande utdata:
    
    **RHEL 7**

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```
    
    **RHEL 8**
    
    ```output
    Location Constraints:
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>Återaktivera stonith

Vi är redo för testning. Återaktivera stonith i klustret genom att köra följande kommando på nod 1:

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>Kontrollera klusterstatus

Du kan kontrol lera status för dina kluster resurser med följande kommando:

```output
[<username>@VM1 ~]$ sudo pcs status
Cluster name: az-hacluster
Stack: corosync
Current DC: <VM3> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
Last updated: Sat Dec  7 00:18:38 2019
Last change: Sat Dec  7 00:18:02 2019 by root via cibadmin on VM1

3 nodes configured
5 resources configured

Online: [ <VM1> <VM2> <VM3> ]

Full list of resources:

 Master/Slave Set: ag_cluster-master [ag_cluster]
     Masters: [ <VM2> ]
     Slaves: [ <VM1> <VM3> ]
 virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
 rsc_st_azure   (stonith:fence_azure_arm):      Started <VM1>

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```

## <a name="test-failover"></a>Redundanstest

För att säkerställa att konfigurationen har lyckats kommer vi att testa en redundansväxling. Mer information finns i [Always on redundans för tillgänglighets grupp på Linux](/sql/linux/sql-server-linux-availability-group-failover-ha).

1. Kör följande kommando för att redundansväxla den primära repliken manuellt till `<VM2>` . Ersätt `<VM2>` med värdet för Server namnet.

   **RHEL 7**
   
    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master
    ```

   Du kan också ange ytterligare ett alternativ så att den tillfälliga begränsningen som skapas för att flytta resursen till en önskad nod är inaktive rad automatiskt och du behöver inte utföra steg 2 och 3 nedan.

   **RHEL 7**

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master lifetime=30S
    ```

   **RHEL 8**

    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master lifetime=30S
    ```

   Ett annat alternativ till att automatisera steg 2 och 3 nedan som rensar den tillfälliga begränsningen i själva resurs flyttnings kommandot genom att kombinera flera kommandon på en enda rad. 

   **RHEL 7**

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master && sleep 30 && pcs resource clear ag_cluster-master
    ```

   **RHEL 8**

    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master && sleep 30 && pcs resource clear ag_cluster-clone
    ```
    
2. Om du kontrollerar begränsningarna igen ser du att en annan begränsning har lagts till på grund av manuell redundans:
    
    **RHEL 7**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-master)
    Ordering Constraints:
            promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

    **RHEL 8**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-clone)
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```
    
3. Ta bort begränsningen med ID `cli-prefer-ag_cluster-master` med hjälp av följande kommando:

    **RHEL 7**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

    **RHEL 8**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-clone
    ```

1. Kontrol lera kluster resurserna med kommandot `sudo pcs resource` och se till att den primära instansen är nu `<VM2>` .

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         ag_cluster (ocf::mssql:ag):        FAILED <VM1> (Monitoring)
         Masters: [ <VM2> ]
         Slaves: [ <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         Masters: [ <VM2> ]
         Slaves: [ <VM1> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

## <a name="test-fencing"></a>Test staket

Du kan testa STONITH genom att köra följande kommando. Prova att köra kommandot nedan från `<VM1>` för `<VM3>` .

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> Avgränsnings åtgärden tar som standard noden av och sedan på. Om du bara vill ta noden offline använder du alternativet `--off` i kommandot.

Du bör se följande utdata:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
Mer information om hur du testar en stängsel-enhet finns i följande [Red Hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar) -artikel.

## <a name="next-steps"></a>Nästa steg

Du måste skapa och konfigurera en belastningsutjämnare för att kunna använda en lyssnare för tillgänglighets grupper för dina SQL Server-instanser.

> [!div class="nextstepaction"]
> [Självstudie: Konfigurera en tillgänglighets grupps lyssnare för SQL Server på virtuella RHEL-datorer i Azure](rhel-high-availability-listener-tutorial.md)
