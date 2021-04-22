---
title: SSH-åtkomst för Linux-containrar
description: Du kan öppna en SSH-session till en Linux-container i Azure App Service. Anpassade Linux-containrar stöds med vissa ändringar i din anpassade avbildning.
keywords: azure app service, web app, linux, oss
author: msangapu-msft
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.topic: article
ms.date: 02/23/2021
ms.author: msangapu
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3610c4a571d73631ed39d416c72d0d6004dd170d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871767"
---
# <a name="open-an-ssh-session-to-a-linux-container-in-azure-app-service"></a>Öppna en SSH-session till en Linux-container i Azure App Service

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) används ofta för att köra administrativa kommandon via fjärrstyrning från en kommandoradsterminal. App Service på Linux SSH-stöd i appcontainern. 

![Linux App Service SSH](./media/configure-linux-open-ssh-session/app-service-linux-ssh.png)

Du kan också ansluta till containern direkt från din lokala utvecklingsdator med hjälp av SSH och SFTP.

## <a name="open-ssh-session-in-browser"></a>Öppna en SSH-session i webbläsaren

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Använda SSH-stöd med anpassade Docker-avbildningar

Se [Konfigurera SSH i en anpassad container.](configure-custom-container.md#enable-ssh)

## <a name="open-ssh-session-from-remote-shell"></a>Öppna SSH-session från fjärrgränssnittet

> [!NOTE]
> Den här funktionen är för närvarande i förhandsversion.
>

Med TCP-tunneltrafik kan du skapa en nätverksanslutning mellan utvecklingsdatorn och Web App for Containers över en autentiserad WebSocket-anslutning. Det gör att du kan öppna en SSH-session med din container som körs App Service från valfri klient.

För att komma igång måste du installera [Azure CLI](/cli/azure/install-azure-cli). Om du vill se hur det fungerar utan att installera Azure CLI öppnar [du Azure Cloud Shell](../cloud-shell/overview.md). 

Öppna en fjärranslutning till din app med [kommandot az webapp remote-connection](/cli/azure/ext/webapp/webapp/remote-connection#ext-webapp-az-webapp-remote-connection-create) create. Ange _\<subscription-id>_ och _ för din _\<group-name>_ \_ \<app-name> app.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&` i slutet av kommandot är bara för enkelhetens skull om du använder Cloud Shell. Processen körs i bakgrunden så att du kan köra nästa kommando i samma gränssnitt.

> [!NOTE]
> Om det här kommandot misslyckas kontrollerar du [att fjärrfelsökning är](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0) *inaktiverat* med följande kommando:
>
> ```azurecli-interactive
> az webapp config set --resource-group <resource-group-name> -n <app-name> --remote-debugging-enabled=false
> ```

Kommandoutdata ger dig den information du behöver för att öppna en SSH-session.

```output
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Öppna en SSH-session med din container med valfri klient med hjälp av den lokala porten. I följande exempel används [standardkommandot ssh:](https://ss64.com/bash/ssh.html)

```bash
ssh root@127.0.0.1 -p <port>
```

När du uppmanas till det skriver `yes` du för att fortsätta ansluta. Du uppmanas sedan att ange lösenordet. Använd `Docker!` , som visades tidigare.

<pre>
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
</pre>

När du har autentiserats bör du se sessionens välkomstskärm.

<pre>
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  &gt;
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
</pre>

Nu är du ansluten till din anslutningsapp.  

Prova att köra [det översta](https://ss64.com/bash/top.html) kommandot. Du bör kunna se appens process i processlistan. I exempelutdata nedan är det det som har `PID 263` .

<pre>
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
</pre>

## <a name="next-steps"></a>Nästa steg

Du kan ställa frågor och problem i [Azure-forumet.](/answers/topics/azure-webapps.html)

Mer information om Web App for Containers finns i:

* [Introduktion till fjärrfelsökning av Node.js appar på Azure App Service från VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Snabbstart: Köra en anpassad container på App Service](quickstart-custom-container.md?pivots=container-linux)
* [Använda Ruby i Azure App Service i Linux](quickstart-ruby.md)
* [Azure App Service – Vanliga frågor om Web App for Containers](faq-app-service-linux.md)
