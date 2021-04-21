---
title: Använda xrdp med Linux
description: Lär dig hur du installerar och konfigurerar fjärrskrivbord (xrdp) för att ansluta till en virtuell Linux-dator i Azure med hjälp av grafiska verktyg
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cynthn
ms.openlocfilehash: 309b106d2141c8257c5163efe7ff45a7bae5d5c3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759659"
---
# <a name="install-and-configure-xrdp-to-use-remote-desktop-with-ubuntu"></a>Installera och konfigurera xrdp för att använda Fjärrskrivbord med Ubuntu

Virtuella Linux-datorer (VM) i Azure hanteras vanligtvis från kommandoraden med hjälp av en SSH-anslutning (Secure Shell). När du är nybörjare på Linux eller vid snabb felsökning kan det vara enklare att använda fjärrskrivbord. Den här artikeln beskriver hur du installerar och konfigurerar en skrivbordsmiljö[(xfce)](https://www.xfce.org)och fjärrskrivbord[(xrdp)](http://xrdp.org)för din virtuella Linux-dator som kör Ubuntu.

Artikeln skrevs och testades med hjälp av en virtuell Ubuntu 18.04-dator. 

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln kräver en befintlig virtuell Ubuntu 18.04 LTS-dator i Azure. Om du behöver skapa en virtuell dator använder du någon av följande metoder:

- [Azure CLI](quick-create-cli.md)
- Följande [Azure Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Installera en skrivbordsmiljö på din virtuella Linux-dator

De flesta virtuella Linux-datorer i Azure har ingen skrivbordsmiljö installerad som standard. Virtuella Linux-datorer hanteras ofta med hjälp av SSH-anslutningar i stället för en skrivbordsmiljö. Det finns olika skrivbordsmiljöer i Linux som du kan välja. Beroende på vilken skrivbordsmiljö du väljer kan det ta en till 2 GB diskutrymme och ta 5 till 10 minuter att installera och konfigurera alla nödvändiga paket.

I följande exempel installeras den förenklade [xfce4-skrivbordsmiljön](https://www.xfce.org/) på en virtuell Ubuntu 18.04 LTS-dator. Kommandon för andra distributioner varierar något (använd för att installera på Red Hat Enterprise Linux och konfigurera lämpliga regler, eller använd till `yum` `selinux` att installera på `zypper` SUSE, till exempel).

Först SSH till den virtuella datorn. I följande exempel ansluter till den virtuella *datorn myvm.westus.cloudapp.azure.com* med användarnamnet *för azureuser*. Använd dina egna värden:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du använder Windows och behöver mer information om hur du använder SSH kan du läsa [Använda SSH-nycklar med Windows](ssh-from-windows.md).

Installera sedan xfce med `apt` hjälp av följande:

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Installera och konfigurera en fjärrskrivbordsserver
Nu när du har installerat en skrivbordsmiljö konfigurerar du en fjärrskrivbordstjänst för att lyssna efter inkommande anslutningar. [xrdp](http://xrdp.org) är en RDP-server (open source Remote Desktop Protocol) som är tillgänglig på de flesta Linux-distributioner och fungerar bra med xfce. Installera xrdp på din virtuella Ubuntu-dator på följande sätt:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Berätta för xrdp vilken skrivbordsmiljö som ska användas när du startar sessionen. Konfigurera xrdp för att använda xfce som skrivbordsmiljö på följande sätt:

```bash
echo xfce4-session >~/.xsession
```

Starta om xrdp-tjänsten för att ändringarna ska börja gälla på följande sätt:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Ange ett lösenord för ett lokalt användarkonto
Om du skapade ett lösenord för ditt användarkonto när du skapade den virtuella datorn kan du hoppa över det här steget. Om du bara använder SSH-nyckelautentisering och inte har angett något lokalt kontolösenord anger du ett lösenord innan du använder xrdp för att logga in på den virtuella datorn. xrdp kan inte acceptera SSH-nycklar för autentisering. I följande exempel anges ett lösenord för användarkontot *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Om du anger ett lösenord uppdateras inte din SSHD-konfiguration för att tillåta lösenordsinloggningar om det inte gör det för närvarande. Ur ett säkerhetsperspektiv kanske du vill ansluta till den virtuella datorn med en SSH-tunnel med nyckelbaserad autentisering och sedan ansluta till xrdp. I så fall hoppar du över följande steg för att skapa en regel för nätverkssäkerhetsgrupp som tillåter fjärrskrivbordstrafik.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Skapa en regel för nätverkssäkerhetsgrupp för fjärrskrivbordstrafik
Om du vill tillåta fjärrskrivbordstrafik att nå din virtuella Linux-dator måste en regel för nätverkssäkerhetsgrupp skapas som tillåter TCP på port 3389 att nå den virtuella datorn. Mer information om regler för nätverkssäkerhetsgrupp finns i [Vad är en nätverkssäkerhetsgrupp?](../../virtual-network/network-security-groups-overview.md) Du kan också [använda Azure Portal för att skapa en regel för nätverkssäkerhetsgruppen](../windows/nsg-quickstart-portal.md).

I följande exempel skapas en regel för nätverkssäkerhetsgrupp [med az vm open-port](/cli/azure/vm#az_vm_open_port) på port *3389*. Från Azure CLI, inte SSH-sessionen till den virtuella datorn, öppnar du följande regel för nätverkssäkerhetsgrupp:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Ansluta din virtuella Linux-dator med en fjärrskrivbordsklient

Öppna din lokala fjärrskrivbordsklient och anslut till IP-adressen eller DNS-namnet för den virtuella Linux-datorn. 

:::image type="content" source="media/use-remote-desktop/remote-desktop.png" alt-text="Skärmbild av fjärrskrivbordsklienten.":::

Ange användarnamnet och lösenordet för användarkontot på den virtuella datorn på följande sätt:

:::image type="content" source="media/use-remote-desktop/xrdp-login.png" alt-text="Skärmbild av xrdp-inloggningsskärmen.":::

Efter autentiseringen läses xfce-skrivbordsmiljön in och ser ut ungefär som i följande exempel:

![xfce-skrivbordsmiljö via xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Om din lokala RDP-klient använder autentisering på nätverksnivå (NLA) kan du behöva inaktivera den anslutningsinställningen. XRDP stöder för närvarande inte NLA. Du kan också titta på alternativa RDP-lösningar som stöder NLA, till exempel [FreeRDP](https://www.freerdp.com).


## <a name="troubleshoot"></a>Felsöka
Om du inte kan ansluta till den virtuella Linux-datorn med en fjärrskrivbordsklient använder du på den virtuella Linux-datorn för att kontrollera att den virtuella datorn lyssnar efter `netstat` RDP-anslutningar på följande sätt:

```bash
sudo netstat -plnt | grep rdp
```

I följande exempel visas den virtuella dator som lyssnar på TCP-port 3389 som förväntat:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Om *tjänsten xrdp-sesman* inte lyssnar startar du om tjänsten på en virtuell Ubuntu-dator på följande sätt:

```bash
sudo service xrdp restart
```

Granska loggarna *i /var/log* på din virtuella Ubuntu-dator för att få information om varför tjänsten kanske inte svarar. Du kan också övervaka syslog under ett fjärrskrivbordsanslutningsförsök för att visa eventuella fel:

```bash
tail -f /var/log/syslog
```

Andra Linux-distributioner som Red Hat Enterprise Linux och SUSE kan ha olika sätt att starta om tjänster och alternativa loggfilsplatser att granska.

Om du inte får något svar i fjärrskrivbordsklienten och inte ser några händelser i systemloggen indikerar det här beteendet att fjärrskrivbordstrafiken inte kan nå den virtuella datorn. Granska reglerna för nätverkssäkerhetsgruppen för att se till att du har en regel som tillåter TCP på port 3389. Mer information finns i Felsöka [anslutningsproblem för program.](/troubleshoot/azure/virtual-machines/troubleshoot-app-connection)


## <a name="next-steps"></a>Nästa steg
Mer information om hur du skapar och använder SSH-nycklar med virtuella Linux-datorer finns i [Skapa SSH-nycklar för virtuella Linux-datorer i Azure](mac-create-ssh-keys.md).

Information om hur du använder SSH från Windows finns i [Använda SSH-nycklar med Windows](ssh-from-windows.md).