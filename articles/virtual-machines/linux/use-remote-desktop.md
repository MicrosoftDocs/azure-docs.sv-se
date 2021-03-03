---
title: Använda xrdp med Linux
description: Lär dig hur du installerar och konfigurerar fjärr skrivbord (xrdp) för att ansluta till en virtuell Linux-dator i Azure med hjälp av grafiska verktyg
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/01/2021
ms.author: cynthn
ms.openlocfilehash: 448e9f6487b5afc51be9b3dee8e07007c8534a0b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695183"
---
# <a name="install-and-configure-xrdp-to-use-remote-desktop-with-a-linux-vm"></a>Installera och konfigurera xrdp för att använda fjärr skrivbord med en virtuell Linux-dator

Virtuella Linux-datorer (VM: ar) i Azure hanteras vanligt vis från kommando raden med hjälp av en SSH-anslutning (Secure Shell). När du har använt New to Linux eller för snabb fel söknings scenarier kan det vara enklare att använda fjärr skrivbord. Den här artikeln beskriver hur du installerar och konfigurerar en Skriv bords miljö ([xfce](https://www.xfce.org)) och fjärr skrivbord ([xrdp](http://xrdp.org)) för din virtuella Linux-dator med hjälp av distributions modellen för Resource Manager.


## <a name="prerequisites"></a>Förutsättningar
Den här artikeln kräver en befintlig Ubuntu 18,04 LTS-VM i Azure. Om du behöver skapa en virtuell dator kan du använda någon av följande metoder:

- [Azure CLI](quick-create-cli.md)
- [Azure Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Installera en Skriv bords miljö på din virtuella Linux-dator

De flesta virtuella Linux-datorer i Azure har inte någon Skriv bords miljö installerad som standard. Virtuella Linux-datorer hanteras ofta med SSH-anslutningar i stället för en Skriv bords miljö. Det finns olika Skriv bords miljöer i Linux som du kan välja mellan. Beroende på ditt val av Skriv bords miljö kan det förbruka en till 2 GB disk utrymme och ta 5 till 10 minuter att installera och konfigurera alla paket som krävs.

I följande exempel installeras den lätta [xfce4](https://www.xfce.org/) Desktop-miljön på en Ubuntu 18,04 LTS VM. Kommandon för andra distributioner varierar något (används `yum` för att installera på Red Hat Enterprise Linux och konfigurera lämpliga `selinux` regler, eller Använd `zypper` för att installera på SUSE, till exempel).

Först, SSH till den virtuella datorn. Följande exempel ansluter till den virtuella datorn med namnet *myvm.westus.cloudapp.Azure.com* med användar namnet *azureuser*. Använd dina egna värden:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du använder Windows och behöver mer information om hur du använder SSH, se [hur du använder SSH-nycklar med Windows](ssh-from-windows.md).

Installera sedan xfce med `apt` enligt följande:

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Installera och konfigurera en fjärr skrivbords server
Nu när du har installerat en Skriv bords miljö kan du konfigurera en fjärr skrivbords tjänst för att lyssna efter inkommande anslutningar. [xrdp](http://xrdp.org) är en RDP-server (open source Remote Desktop Protocol) som är tillgänglig på de flesta Linux-distributioner och fungerar bra med xfce. Installera xrdp på den virtuella Ubuntu-datorn enligt följande:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Berätta för xrdp vilken Skriv bords miljö som ska användas när du startar sessionen. Konfigurera xrdp så att xfce används som Skriv bords miljö enligt följande:

```bash
echo xfce4-session >~/.xsession
```

Starta om xrdp-tjänsten för att ändringarna ska börja gälla enligt följande:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Ange lösen ord för lokalt användar konto
Om du har skapat ett lösen ord för ditt användar konto när du skapade den virtuella datorn hoppar du över det här steget. Om du bara använder SSH-autentiseringsnyckel och inte har angett ett lokalt konto lösen ord anger du ett lösen ord innan du använder xrdp för att logga in på den virtuella datorn. xrdp kan inte acceptera SSH-nycklar för autentisering. Följande exempel anger ett lösen ord för användar kontot *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Att ange ett lösen ord uppdaterar inte SSHD-konfigurationen för att tillåta inloggningar för lösen ord om den inte gör det. Från ett säkerhets perspektiv kanske du vill ansluta till din virtuella dator med en SSH-tunnel med hjälp av nyckelbaserad autentisering och sedan ansluta till xrdp. I så fall kan du hoppa över följande steg för att skapa en regel för nätverks säkerhets grupper för att tillåta fjärr skrivbords trafik.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Skapa en regel för nätverks säkerhets grupp för fjärr skrivbords trafik
Om du vill tillåta fjärr skrivbords trafik att komma åt din virtuella Linux-dator måste en regel för nätverks säkerhets grupp skapas som tillåter TCP på port 3389 att komma åt den virtuella datorn. Mer information om regler för nätverks säkerhets grupper finns i [Vad är en nätverks säkerhets grupp?](../../virtual-network/network-security-groups-overview.md) Du kan också [använda Azure Portal för att skapa en regel för nätverks säkerhets grupper](../windows/nsg-quickstart-portal.md).

I följande exempel skapas en regel för nätverks säkerhets grupper med [AZ VM Open-port](/cli/azure/vm#az-vm-open-port) på port *3389*. Från Azure CLI, inte SSH-sessionen till den virtuella datorn, öppnar du följande regel för nätverks säkerhets grupp:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Ansluta din virtuella Linux-dator till en fjärr skrivbords klient

Öppna den lokala fjärr skrivbords klienten och Anslut till IP-adressen eller DNS-namnet för din virtuella Linux-dator. 

:::image type="content" source="media/use-remote-desktop/remote-desktop.png" alt-text="Skärm bild av fjärr skrivbords klienten.":::

Ange användar kontot och lösen ordet för användar kontot på den virtuella datorn enligt följande:

:::image type="content" source="media/use-remote-desktop/xrdp-login.png" alt-text="Skärm bild av xrdp-inloggnings skärmen.":::

Efter autentiseringen kommer xfce desktop-miljön att läsas in och likna följande exempel:

![xfce desktop-miljö via xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Om din lokala RDP-klient använder autentisering på nätverks nivå (NLA) kan du behöva inaktivera anslutnings inställningen. XRDP stöder för närvarande inte NLA. Du kan också titta på alternativa RDP-lösningar som stöder NLA, till exempel [FreeRDP](https://www.freerdp.com).


## <a name="troubleshoot"></a>Felsöka
Om du inte kan ansluta till din virtuella Linux-dator med hjälp av en fjärr skrivbords klient använder du den `netstat` virtuella Linux-datorn för att kontrol lera att den virtuella datorn lyssnar efter RDP-anslutningar på följande sätt:

```bash
sudo netstat -plnt | grep rdp
```

I följande exempel visas den virtuella datorn som lyssnar på TCP-port 3389 som förväntat:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Om tjänsten *xrdp-sesman* inte lyssnar går du till en virtuell Ubuntu-dator som startar om tjänsten på följande sätt:

```bash
sudo service xrdp restart
```

Granska loggarna i */var/log* på din Ubuntu-VM för instruktioner om varför tjänsten kanske inte svarar. Du kan också övervaka syslog under en fjärr skrivbords anslutning försök att visa eventuella fel:

```bash
tail -f /var/log/syslog
```

Andra Linux-distributioner som Red Hat Enterprise Linux och SUSE kan ha olika sätt att starta om tjänster och alternativa logg fils platser att granska.

Om du inte får några svar på fjärr skrivbords klienten och inte ser några händelser i system loggen visar det här beteendet att fjärr skrivbords trafik inte kan komma åt den virtuella datorn. Granska reglerna för nätverks säkerhets gruppen så att du har en regel för att tillåta TCP på port 3389. Mer information finns i [Felsöka problem med program anslutningen](../troubleshooting/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Nästa steg
Mer information om hur du skapar och använder SSH-nycklar med virtuella Linux-datorer finns i [skapa SSH-nycklar för virtuella Linux-datorer i Azure](mac-create-ssh-keys.md).

Information om hur du använder SSH från Windows finns i [så här använder du SSH-nycklar med Windows](ssh-from-windows.md).