---
title: Köra kommandon i containerinstans som körs
description: Lär dig hur du kör ett kommando i en container som för närvarande körs i Azure Container Instances
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: 42832910efff67f111c669793798d9ff0e413536
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790787"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Köra ett kommando i en Azure-containerinstans som körs

Azure Container Instances har stöd för att köra kommandon i aktiva containers. Under apputveckling och felsökning är det särskilt användbart att kunna köra kommandon i containers som redan har startats. Den här funktionen används oftast till att starta ett interaktivt gränssnitt så att du kan felsöka problem i en container som körs.

## <a name="run-a-command-with-azure-cli"></a>Köra ett kommando med Azure CLI

Kör ett kommando i en container som körs [med az container exec][az-container-exec] i [Azure CLI:][azure-cli]

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Så här startar du till exempel ett Bash-gränssnitt i en Nginx-container:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

I exempelutdata nedan startas Bash-gränssnittet i en Linux-container som körs, vilket ger en terminal `ls` där körs:

```output
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

I det här exemplet startas kommandotolken i en nanoservercontainer som körs:

```azurecli
az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
```

```output
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>Grupper med flera container

Om [containergruppen har](container-instances-container-groups.md) flera containrar, till exempel en programcontainer och en sidovagn för loggning, anger du namnet på containern där kommandot ska köras med `--container-name` .

I containergruppen är till exempel *mynginx två* containrar, *nginx-app* och *loggning.* Så här startar du ett gränssnitt på *nginx-app-containern:*

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Begränsningar

Azure Container Instances stöder för närvarande start av en enda process [med az container exec][az-container-exec], och du kan inte skicka kommandoargument. Du kan till exempel inte länka kommandon som i `sh -c "echo FOO && echo BAR"` eller köra `echo FOO` .

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra felsökningsverktyg och vanliga distributionsproblem [i Felsöka container- och distributionsproblem i Azure Container Instances](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[azure-cli]: /cli/azure