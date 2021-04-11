---
title: SSH-åtkomst för Linux-behållare
description: Du kan öppna en SSH-session till en Linux-behållare i Azure App Service. Anpassade Linux-behållare stöds med vissa ändringar av din anpassade avbildning.
keywords: Azure App Service, webbapp, Linux, oss
author: msangapu-msft
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.topic: article
ms.date: 02/23/2021
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8e9dd76b60d05b9fa5e3a4aaf7ccc6663f4a969b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709044"
---
# <a name="open-an-ssh-session-to-a-linux-container-in-azure-app-service"></a>Öppna en SSH-session till en Linux-behållare i Azure App Service

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) används ofta för att fjärrköra administrativa kommandon från en kommando rad Terminal. App Service på Linux ger SSH-stöd till app-behållaren. 

![Linux App Service SSH](./media/configure-linux-open-ssh-session/app-service-linux-ssh.png)

Du kan också ansluta till behållaren direkt från den lokala utvecklings datorn med SSH och SFTP.

## <a name="open-ssh-session-in-browser"></a>Öppna en SSH-session i webbläsaren

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Använda SSH-stöd med anpassade Docker-avbildningar

Se [Konfigurera SSH i en anpassad behållare](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>Öppna SSH-session från fjärrgränssnitt

> [!NOTE]
> Den här funktionen är för närvarande en för hands version.
>

Med TCP-tunnlar kan du skapa en nätverks anslutning mellan din utvecklings dator och Web App for Containers över en autentiserad WebSocket-anslutning. Du kan öppna en SSH-session med din behållare som körs i App Service från valfri klient.

För att komma igång måste du installera [Azure CLI](/cli/azure/install-azure-cli). Öppna [Azure Cloud Shell](../cloud-shell/overview.md)om du vill se hur det fungerar utan att installera Azure CLI. 

Öppna en fjärr anslutning till din app med kommandot [AZ webapp Remote-Connection Create](/cli/azure/ext/webapp/webapp/remote-connection#ext-webapp-az-webapp-remote-connection-create) . Ange _\<subscription-id>_ _\<group-name>_ och \_ \<app-name> _ för din app.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&` i slutet av kommandot är det bara för bekvämlighet om du använder Cloud Shell. Processen körs i bakgrunden så att du kan köra nästa kommando i samma gränssnitt.

> [!NOTE]
> Om det här kommandot Miss lyckas kontrollerar du att [fjärrfelsökning](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0) är *inaktiverat* med följande kommando:
>
> ```azurecli-interactive
> az webapp config set --resource-group <resource-group-name> -n <app-name> --remote-debugging-enabled=false
> ```

Kommandots utdata ger dig den information du behöver för att öppna en SSH-session.

```output
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Öppna en SSH-session med din behållare med den valda klienten med hjälp av den lokala porten. I följande exempel används standard- [SSH](https://ss64.com/bash/ssh.html) -kommandot:

```bash
ssh root@127.0.0.1 -p <port>
```

När du uppmanas till det skriver `yes` du för att fortsätta ansluta. Du uppmanas sedan att ange lösen ordet. Använd `Docker!` , som visades tidigare.

<pre>
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
</pre>

När du har autentiserats bör du se välkomst skärmen för sessionen.

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

Du är nu ansluten till din anslutning.  

Försök köra det [översta](https://ss64.com/bash/top.html) kommandot. Du bör kunna se appens process i process listan. I exemplet nedan är det en med `PID 263` .

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

Du kan publicera frågor och problem i [Azure-forumet](/answers/topics/azure-webapps.html).

Mer information om Web App for Containers finns i:

* [Introduktion till fjärrfelsökning av Node.js appar på Azure App Service från VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Snabb start: kör en anpassad behållare på App Service](quickstart-custom-container.md?pivots=container-linux)
* [Använda Ruby i Azure App Service i Linux](quickstart-ruby.md)
* [Azure App Service – Vanliga frågor om Web App for Containers](faq-app-service-linux.md)
