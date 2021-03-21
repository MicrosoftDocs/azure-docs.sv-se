---
title: Installera IBM zD&T dev/test-miljö på Azure | Microsoft Docs
description: Distribuera IBM Z utvecklings-och test miljö (zD&T) på Azure Virtual Machine (VM) Infrastructure as a Service (IaaS).
services: virtual-machines
ms.service: virtual-machines
ms.subservice: workloads
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: abcaff5979aed38ce47df08cc953829f3003f7b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561559"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Installera IBM zD&T dev/test-miljö på Azure

Om du vill skapa en utvecklings-/test miljö för stordator belastningar i IBM Z-system kan du distribuera IBM Z-utveckling och test miljö (zD&T) på Azure Virtual Machine (VM) Infrastructure as a Service (IaaS).

Med zD&T kan du dra nytta av kostnads besparingarna för x86-plattformen för dina mindre viktiga utvecklings-och test miljöer och sedan skicka tillbaka uppdateringarna till en produktions miljö i Z-systemet. Mer information finns i [installations anvisningarna för IBM ZD&T](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Azure och Azure Stack stöd för följande versioner:

- zD&T personal Edition
- zD&T Parallel Sysplex
- zD&T Enterprise Edition

Alla utgåvor av zD&T körs bara på x86 Linux-system, inte Windows Server. Enterprise Edition stöds på antingen Red Hat Enterprise Linux (RHEL) eller Ubuntu/Debian. Både RHEL-och Debian VM-avbildningar är tillgängliga för Azure.

Den här artikeln visar hur du konfigurerar zD&T Enterprise Edition på Azure så att du kan använda zD&T Enterprise Edition för att skapa och hantera miljöer. Att installera zD&T installerar inte några miljöer. Du måste skapa dessa separat som installations paket. Till exempel är programutvecklare kontrollerade distributioner (ADCD) volym avbildningar av test miljöer. De finns i zip-avbildningar på den medie distribution som är tillgänglig från IBM. Se så [här konfigurerar du en ADCD-miljö i Azure](demo.md).

Mer information finns i [Översikt över zD&T](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) i IBM Knowledge Center.

Den här artikeln visar hur du konfigurerar Z-utveckling och test miljö (zD&T) Enterprise Edition på Azure. Sedan kan du använda webb servern zD&T Enterprise Edition för att skapa och hantera Z-baserade miljöer på Azure.

## <a name="prerequisites"></a>Förutsättningar

> [!NOTE]
> IBM tillåter att zD&T Enterprise Edition endast installeras i utvecklings-och test miljöer –*inte* produktions miljöer.

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Du behöver åtkomst till mediet, som endast är tillgängligt för IBM-kunder och-partner. Om du vill ha mer information kan du kontakta din IBM-representant eller gå till kontakt informationen på webbplatsen [zD&T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) .

- En [licensierings Server](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Detta krävs för åtkomst till miljöer. Hur du skapar det beror på hur du licensierar program varan från IBM:

     - **Maskinvarubaserad licens Server** kräver en USB-maskinvara som innehåller de rationella tokens som krävs för att få åtkomst till alla delar av program varan. Du måste hämta detta från IBM.

     - Med **programvarubaserad licens Server** måste du konfigurera en central server för hantering av licens nycklarna. Den här metoden är önskad och kräver att du konfigurerar de nycklar du får från IBM i hanterings servern.

## <a name="create-the-base-image-and-connect"></a>Skapa bas avbildningen och Anslut

1. I Azure Portal [skapar du en virtuell dator](../../../linux/quick-create-portal.md) med den konfiguration av operativ systemet som du vill använda. Den här artikeln förutsätter en B4ms VM (med 4 virtuella processorer och 16 GB minne) som kör Ubuntu 16,04.

2. När den virtuella datorn har skapats öppnar du inkommande portar 22 för SSH, 21 för FTP och 9443 för webb servern.

3. Hämta SSH-autentiseringsuppgifterna som visas på bladet **Översikt** på den virtuella datorn via knappen **Anslut** . Välj fliken **SSH** och kopiera SSH-inloggnings kommandot till Urklipp.

4. Logga in på ett [bash-gränssnitt](../../../../cloud-shell/quickstart.md) från den lokala datorn och klistra in kommandot. Den kommer att vara i formatet **SSH \<user id\> \@ \<IP Address\>**. När du uppmanas att ange dina autentiseringsuppgifter anger du dem för att upprätta en anslutning till din Hem Katalog.

## <a name="copy-the-installation-file-to-the-server"></a>Kopiera installations filen till servern

Installations filen för webb servern är **ZDT \_ install \_ ee \_ v 12.0.0.1. tgz**. Den ingår i mediet som tillhandahålls av IBM. Du måste ladda upp den här filen till din virtuella Ubuntu-dator.

1. På kommando raden anger du följande kommando för att kontrol lera att allt är uppdaterat i den nyligen skapade avbildningen:

    ```
    sudo apt-get update
    ```

2. Skapa den katalog som ska installeras på:

    ```
    mkdir ZDT
    ```

3. Kopiera filen från den lokala datorn till den virtuella datorn:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Detta kommando kopierar installations filen till katalogen ZDT i din arbets katalog, som varierar beroende på om din klient kör Windows eller Linux.

## <a name="install-the-enterprise-edition"></a>Installera Enterprise Edition

1. Gå till ZDT-katalogen och expandera filen ZDT \_ install \_ ee \_ v 12.0.0.1. tgz med följande kommandon:

    ```
    cd ZDT
    tar zxvf ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Kör installations programmet:

    ```
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.x86_64
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Välj **1** om du vill installera Enterprise Server.

4. Tryck på **RETUR** och Läs igenom licens avtalen noggrant. I slutet av licensen anger du **Ja** för att fortsätta.

5. När du uppmanas att ändra lösen ordet för den nyligen skapade **användaren, använder** du kommandot **sudo passwd ibmsys1** och anger det nya lösen ordet.

6. Så här kontrollerar du om installationen lyckades

    ```
    dpkg -l | grep zdtapp
    ```

7. Kontrol lera att utdata innehåller strängen **zdtapp 12.0.0.0**, vilket indikerar att paket gasen har installerats

### <a name="starting-enterprise-edition"></a>Starta Enterprise Edition

Tänk på att när webb servern startar körs den under zD&T-användare som skapades under installations processen.

1. Starta webb servern genom att använda rot användar-ID: t för att köra följande kommando:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Kopiera URL-utdata från skriptet, som ser ut så här:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Klistra in webb adressen i en webbläsare för att öppna hanterings komponenten för din zD&T-installation.

## <a name="next-steps"></a>Nästa steg

[Konfigurera en programutvecklare styrd distribution (ADCD) i IBM zD&T v1](./demo.md)
