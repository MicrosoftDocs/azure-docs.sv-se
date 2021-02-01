---
title: Skapa Linux-avbildningar utan en etablerings agent
description: Skapa generaliserade Linux-avbildningar utan en etablerings agent i Azure.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 1c9ac872587804adbd9e62a3dc3ef3daed9e0c25
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223059"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>Skapa generaliserade avbildningar utan en etablerings agent

Microsoft Azure tillhandahåller etablerings agenter för virtuella Linux-datorer i form av [walinuxagent](https://github.com/Azure/WALinuxAgent) eller [Cloud-Init](https://github.com/canonical/cloud-init) (rekommenderas). Men det kan finnas ett scenario när du inte vill använda något av dessa program för etablerings agenten, till exempel:

- Linux-distribution/-versionen stöder inte Cloud-Init/Linux-agenten.
- Du måste ange vissa egenskaper för virtuella datorer, till exempel hostname.

> [!NOTE] 
>
> Om du inte kräver att några egenskaper anges eller om någon form av etableringen sker bör du överväga att skapa en specialiserad avbildning.

Den här artikeln visar hur du kan konfigurera VM-avbildningen så att den uppfyller kraven för Azure-plattformen och ange värd namnet, utan att installera en etablerings agent.

## <a name="networking-and-reporting-ready"></a>Nätverks-och rapporterings klar

För att din virtuella Linux-dator ska kunna kommunicera med Azure-komponenter behöver du en DHCP-klient för att hämta en värd-IP från det virtuella nätverket, samt hantering av DNS-matchning och routning. De flesta distributioner levereras med de här verktygen direkt. Verktyg som har testats på Azure av Linux distribution-leverantörer `dhclient` innehåller `network-manager` , `systemd-networkd` och andra.

> [!NOTE]
>
> För närvarande skapas endast generaliserade avbildningar utan en etablerings agent som stöder DHCP-aktiverade virtuella datorer.

När nätverket har kon figurer ATS och kon figurer ATS måste du "rapportera redo". Detta meddelar Azure att den virtuella datorn har etablerats.

> [!IMPORTANT]
>
> Om du inte kan rapportera redo till Azure leder det till att den virtuella datorn startas om!

## <a name="demosample"></a>Demo/exempel

Den här demon visar hur du kan ta en befintlig Marketplace-avbildning (i det här fallet en Debian Buster VM) och ta bort Linux-agenten (walinuxagent), men du kan också skapa den mest grundläggande processen för att rapportera till Azure att den virtuella datorn är "klar".

### <a name="create-the-resource-group-and-base-vm"></a>Skapa resurs gruppen och den virtuella bas datorn:

```bash
$ az group create --location eastus --name demo1
```

Skapa den virtuella bas datorn:

```bash
$ az vm create \
    --resource-group demo1 \
    --name demo1 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo1 \
    --image "debian:debian-10:10:latest"
```

### <a name="remove-the-image-provisioning-agent"></a>Ta bort avbildnings etablerings agenten

När den virtuella datorn har etableringen kan du SSH till den och ta bort Linux-agenten:

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### <a name="add-required-code-to-the-vm"></a>Lägg till nödvändig kod till den virtuella datorn

Även inuti den virtuella datorn, eftersom vi har tagit bort Azure Linux-agenten, behöver vi tillhandahålla en mekanism för att rapportera klart. 

#### <a name="python-script"></a>Python-skript

```python
import http.client
import sys
from xml.etree import ElementTree

wireserver_ip = '168.63.129.16'
wireserver_conn = http.client.HTTPConnection(wireserver_ip)

print('Retrieving goal state from the Wireserver')
wireserver_conn.request(
    'GET',
    '/machine?comp=goalstate',
    headers={'x-ms-version': '2012-11-30'}
)

resp = wireserver_conn.getresponse()

if resp.status != 200:
    print('Unable to connect with wireserver')
    sys.exit(1)

wireserver_goalstate = resp.read().decode('utf-8')

xml_el = ElementTree.fromstring(wireserver_goalstate)

container_id = xml_el.findtext('Container/ContainerId')
instance_id = xml_el.findtext('Container/RoleInstanceList/RoleInstance/InstanceId')
print(f'ContainerId: {container_id}')
print(f'InstanceId: {instance_id}')

# Construct the XML response we need to send to Wireserver to report ready.
health = ElementTree.Element('Health')
goalstate_incarnation = ElementTree.SubElement(health, 'GoalStateIncarnation')
goalstate_incarnation.text = '1'
container = ElementTree.SubElement(health, 'Container')
container_id_el = ElementTree.SubElement(container, 'ContainerId')
container_id_el.text = container_id
role_instance_list = ElementTree.SubElement(container, 'RoleInstanceList')
role = ElementTree.SubElement(role_instance_list, 'Role')
instance_id_el = ElementTree.SubElement(role, 'InstanceId')
instance_id_el.text = instance_id
health_second = ElementTree.SubElement(role, 'Health')
state = ElementTree.SubElement(health_second, 'State')
state.text = 'Ready'

out_xml = ElementTree.tostring(
    health,
    encoding='unicode',
    method='xml'
)
print('Sending the following data to Wireserver:')
print(out_xml)

wireserver_conn.request(
    'POST',
    '/machine?comp=health',
    headers={
        'x-ms-version': '2012-11-30',
        'Content-Type': 'text/xml;charset=utf-8',
        'x-ms-agent-name': 'custom-provisioning'
    },
    body=out_xml
)

resp = wireserver_conn.getresponse()
print(f'Response: {resp.status} {resp.reason}')

wireserver_conn.close()
```

#### <a name="generic-steps-without-using-python"></a>Generiska steg (utan att använda python)

Om den virtuella datorn inte har python installerat eller tillgängligt kan du återge skriptet på ett program med ovanstående skript logik med följande steg:

1. Hämta `ContainerId` och `InstanceId` genom att parsa svaret från WireServer: `curl -X GET -H 'x-ms-version: 2012-11-30' http://168.63.129.16/machine?comp=goalstate` .

2. Skapa följande XML-data, och injicera parsade `ContainerId` och `InstanceId` från ovanstående steg:
   ```xml
   <Health>
     <GoalStateIncarnation>1</GoalStateIncarnation>
     <Container>
       <ContainerId>CONTAINER_ID</ContainerId>
       <RoleInstanceList>
         <Role>
           <InstanceId>INSTANCE_ID</InstanceId>
           <Health>
             <State>Ready</State>
           </Health>
         </Role>
       </RoleInstanceList>
     </Container>
   </Health>
   ```

3. Publicera dessa data till WireServer: `curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health`

### <a name="automating-running-the-code-at-first-boot"></a>Automatisera körningen av koden vid första starten

Den här demon använder system som är det vanligaste initierings systemet i moderna Linux-distributioner. Det enklaste och mest inbyggda sättet att se till att den här rapporten är redo att köras vid rätt tidpunkt är att skapa en system tjänst enhet. Du kan lägga till följande enhets fil i `/etc/systemd/system` (det här exemplet namnger enhets filen `azure-provisioning.service` ):

```bash
[Unit]
Description=Azure Provisioning

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /usr/local/azure-provisioning.py
ExecStart=/bin/bash -c "hostnamectl set-hostname $(curl \
    -H 'metadata: true' \
    'http://169.254.169.254/metadata/instance/compute/name?api-version=2019-06-01&format=text')"
ExecStart=/usr/bin/systemctl disable azure-provisioning.service

[Install]
WantedBy=multi-user.target
```

Den här system tjänsten gör tre saker för grundläggande etablering:

1. Rapporter som är klara för Azure (för att visa att den har slutförts).
1. Byter namn på den virtuella datorn baserat på det användardefinierade VM-namnet genom att hämta dessa data från [Azure instance metadata service (IMDS)](./instance-metadata-service.md). **Obs!** IMDS innehåller också andra [metadata för instans](./instance-metadata-service.md#access-azure-instance-metadata-service), till exempel offentliga SSH-nycklar, så att du kan ange fler än värd namnet.
1. Inaktiverar sig själv så att de bara körs vid första starten och inte vid efterföljande omstarter.

Använd enheten i fil systemet och kör följande för att aktivera det:

```bash
$ sudo systemctl enable azure-provisioning.service
```

Nu är den virtuella datorn klar att generaliseras och har en avbildning som skapats från den. 

#### <a name="completing-the-preparation-of-the-image"></a>Förbereda avbildningen

Gå tillbaka till utvecklings datorn och kör följande för att förbereda avbildnings skapande från den virtuella bas datorn:

```bash
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

Och skapa avbildningen från den här virtuella datorn:

```bash
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

Nu är vi redo att skapa en ny virtuell dator (eller flera virtuella datorer) från avbildningen:

```bash
$ IMAGE_ID=$(az image show -g demo1 -n demo1img --query id -o tsv)
$ az vm create \
    --resource-group demo12 \
    --name demo12 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo12 \
    --image "$IMAGE_ID" 
    --enable-agent false
```

> [!NOTE]
>
> Det är viktigt att ställa in `--enable-agent` på `false` eftersom walinuxagent inte finns på den virtuella datorn som ska skapas från avbildningen.

Den här virtuella datorn ska kunna etablering. Logga in på den virtuella datorn med den virtuella datorn. du bör kunna se resultatet av rapportens färdiga system tjänster:

```bash
$ sudo journalctl -u azure-provisioning.service
-- Logs begin at Thu 2020-06-11 20:28:45 UTC, end at Thu 2020-06-11 20:31:24 UTC. --
Jun 11 20:28:49 thstringnopa systemd[1]: Starting Azure Provisioning...
Jun 11 20:28:54 thstringnopa python3[320]: Retrieving goal state from the Wireserver
Jun 11 20:28:54 thstringnopa python3[320]: ContainerId: 7b324f53-983a-43bc-b919-1775d6077608
Jun 11 20:28:54 thstringnopa python3[320]: InstanceId: fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2
Jun 11 20:28:54 thstringnopa python3[320]: Sending the following data to Wireserver:
Jun 11 20:28:54 thstringnopa python3[320]: <Health><GoalStateIncarnation>1</GoalStateIncarnation><Container><ContainerId>7b324f53-983a-43bc-b919-1775d6077608</ContainerId><RoleInstanceList><Role><InstanceId>fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2</InstanceId><Health><State>Ready</State></Health></Role></RoleInstanceList></Container></Health>
Jun 11 20:28:54 thstringnopa python3[320]: Response: 200 OK
Jun 11 20:28:56 thstringnopa bash[472]:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Jun 11 20:28:56 thstringnopa bash[472]:                                  Dload  Upload   Total   Spent    Left  Speed
Jun 11 20:28:56 thstringnopa bash[472]: [158B blob data]
Jun 11 20:28:56 thstringnopa2 systemctl[475]: Removed /etc/systemd/system/multi-user.target.wants/azure-provisioning.service.
Jun 11 20:28:56 thstringnopa2 systemd[1]: azure-provisioning.service: Succeeded.
Jun 11 20:28:56 thstringnopa2 systemd[1]: Started Azure Provisioning.
```

## <a name="support"></a>Support

Om du implementerar din egen etablerings kod/agent, äger du support för den här koden, Microsoft Support undersöker bara problem som rör etablerings gränssnitten som inte är tillgängliga. Vi gör kontinuerligt förbättringar och ändringar i det här avsnittet, så du måste övervaka ändringar i Cloud-Init-och Azure Linux-agenten för etablering av API-ändringar.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Linux-etablering](provisioning.md).
