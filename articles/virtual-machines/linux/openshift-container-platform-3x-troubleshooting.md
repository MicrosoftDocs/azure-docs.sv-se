---
title: Felsök distribution av OpenShift container Platform 3,11 i Azure
description: Felsök distribution av OpenShift container Platform 3,11 i Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible
ms.openlocfilehash: b6dfb4cb697e7d66c7db323df9786ff7ad45e1d7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672336"
---
# <a name="troubleshoot-openshift-container-platform-311-deployment-in-azure"></a>Felsök distribution av OpenShift container Platform 3,11 i Azure

Om OpenShift-klustret inte distribueras korrekt, kommer Azure Portal att ge fel utdata. Det kan vara svårt att läsa utdata som gör det svårt att identifiera problemet. Sök snabbt igenom dessa utdata för slutkod 3, 4 eller 5. Nedan visas information om de här tre avslutnings koderna:

- Slutkod 3: ditt användar namn/lösen ord för Red Hat-prenumerationen eller din organisations-ID/aktiverings nyckel är felaktigt
- Slutkod 4: ditt Red Hat-pool-ID är felaktigt eller så finns inga rättigheter tillgängliga
- Slutkod 5: det går inte att etablera Docker tunn pool volym

För alla andra slut koder ansluter du till värdarna via SSH för att Visa loggfilerna.

**OpenShift Container Platform 3.11**

SSH till Ansible Spelbok-värden. För mallen eller Marketplace-erbjudandet använder du skydds-värden. Från skydds kan du SSH till alla andra noder i klustret (Master, infrarött, CNS, Compute). Du måste vara rot för att kunna visa loggfilerna. Roten är inaktive rad för SSH-åtkomst som standard, så Använd inte roten till SSH till andra noder.

**OKD**

SSH till Ansible Spelbok-värden. För OKD-mallen (version 3,9 och tidigare) använder du huvud-0-värden. För OKD-mallen (version 3,10 och senare) använder du skydds-värden. Från Ansible Spelbok-värden kan du SSH till alla andra noder i klustret (Master,, CNS, Compute). Du måste vara rot (sudo su-) om du vill visa loggfilerna. Roten är inaktive rad för SSH-åtkomst som standard, så Använd inte roten till SSH till andra noder.

## <a name="log-files"></a>Loggfiler

Loggfilerna (STDERR och STDOUT) för värd förberedelse skript finns `/var/lib/waagent/custom-script/download/0` på alla värdar. Om ett fel uppstod under förberedelsen av värden kan du se dessa loggfiler för att fastställa felet.

Om förberedelse skripten har körts måste loggfilerna i `/var/lib/waagent/custom-script/download/1` katalogen för Ansible Spelbok-värden undersökas. Om felet uppstod under den faktiska installationen av OpenShift visar STDOUT-filen felet. Använd den här informationen för att kontakta supporten för ytterligare hjälp.

Exempel på utdata

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

De vanligaste felen under installationen är:

1. Den privata nyckeln har en lösen fras
2. Nyckel valv hemligheten med privat nyckel skapades inte korrekt
3. Autentiseringsuppgifterna för tjänstens huvud namn angavs felaktigt
4. Tjänstens huvud namn har inte deltagar åtkomst till resurs gruppen

### <a name="private-key-has-a-passphrase"></a>Den privata nyckeln har en lösen fras

Du ser ett fel om att behörigheten nekades för SSH. SSH till Ansible Spelbok-värden för att söka efter en lösen fras på den privata nyckeln.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Nyckel valv hemligheten med privat nyckel skapades inte korrekt

Den privata nyckeln kopieras till Ansible Spelbok Host-~/.ssh/id_rsa. Bekräfta att filen är korrekt. Testa genom att öppna en SSH-session till en av klusternoderna från Ansible Spelbok-värden.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>Autentiseringsuppgifterna för tjänstens huvud namn angavs felaktigt

När du anger indata till mallen eller Marketplace-erbjudandet tillhandahölls felaktig information. Kontrol lera att du använder rätt appId (clientId) och lösen ord (clientSecret) för tjänstens huvud namn. Verifiera genom att utfärda följande Azure CLI-kommando.

```azurecli
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Tjänstens huvud namn har inte deltagar åtkomst till resurs gruppen

Om Azure Cloud-providern är aktive rad måste tjänstens huvud namn ha deltagar åtkomst till resurs gruppen. Verifiera genom att utfärda följande Azure CLI-kommando.

```azurecli
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Fler verktyg

För vissa fel kan du också använda följande kommandon för att få mer information:

1. status för systemctl \<service>
2. journalctl-XE
