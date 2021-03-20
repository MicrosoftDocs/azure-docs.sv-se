---
title: Kör kommandon i kör behållar instans
description: Lär dig hur du kör ett kommando i en behållare som för närvarande körs i Azure Container Instances
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: de48e6ac246e2b0751561b4c60bb63d88b599bdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "79247207"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Köra ett kommando i en Azure Container instance som körs

Azure Container Instances har stöd för att köra kommandon i aktiva containers. Under apputveckling och felsökning är det särskilt användbart att kunna köra kommandon i containers som redan har startats. Den här funktionen används oftast till att starta ett interaktivt gränssnitt så att du kan felsöka problem i en container som körs.

## <a name="run-a-command-with-azure-cli"></a>Köra ett kommando med Azure CLI

Köra ett kommando i en behållare som körs med [AZ container exec][az-container-exec] i [Azure CLI][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Till exempel för att starta ett bash-gränssnitt i en nginx-behållare:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

I exemplet nedan startas bash-gränssnittet i en Linux-behållare, vilket ger en Terminal i som körs `ls` :

```output
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

I det här exemplet startas kommando tolken i en Nanoserver-behållare som körs:

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

Om [behållar gruppen](container-instances-container-groups.md) har flera behållare, till exempel en program behållare och en loggning av sidvagn, anger du namnet på behållaren där kommandot ska köras `--container-name` .

I behållar gruppen *mynginx* finns till exempel två behållare, *nginx-app* och *loggar*. Starta ett gränssnitt i behållaren *nginx-app* :

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Begränsningar

Azure Container Instances stöder för närvarande att starta en enda process med [AZ container exec][az-container-exec]och du kan inte skicka kommando argument. Du kan till exempel inte kedje kommandon som i `sh -c "echo FOO && echo BAR"` eller köra `echo FOO` .

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra fel söknings verktyg och vanliga distributions problem i [fel sökning av problem med behållare och distribution i Azure Container instances](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure