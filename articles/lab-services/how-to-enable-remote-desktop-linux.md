---
title: Aktivera grafiskt fjärr skrivbord för Linux i Azure Lab Services | Microsoft Docs
description: Lär dig hur du aktiverar fjärr skrivbord för virtuella Linux-datorer i ett labb i Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 34c940fec388bb0e79ab5e1db9be6d52fb223873
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647961"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>Aktivera grafiskt fjärr skrivbord för virtuella Linux-datorer i Azure Lab Services
Den här artikeln visar hur du utför följande uppgifter:

- Aktivera grafiska fjärr skrivbords-sessioner för en virtuell Linux-dator
- Så här ansluter du till en virtuell Linux-dator med RDP (Remote Desktop Protocol) eller X2Go fjärr skrivbords klienter

## <a name="set-up-graphical-remote-desktop-solution"></a>Konfigurera en grafisk fjärr skrivbords lösning
När ett labb skapas från en **Linux** -avbildning konfigureras **SSH** (Secure Shell)-åtkomst automatiskt så att instruktören kan ansluta till mallen VM från kommando raden med hjälp av SSH.  På samma sätt kan studenter även ansluta till sina virtuella datorer med SSH när mallen VM publiceras.

För att ansluta till en virtuell Linux-dator med ett **grafiskt** användar gränssnitt (grafiskt användar gränssnitt) rekommenderar vi att du använder antingen **RDP** eller **X2Go**.  Båda dessa alternativ kräver att instruktören gör några ytterligare inställningar på mallen VM:

### <a name="rdp-setup"></a>RDP-konfiguration
För att använda RDP måste instruktören:
  - Aktivera anslutning till fjärr skrivbord; det behövs specifikt för att öppna den virtuella datorns port för RDP.
  - Installera RDP fjärr skrivbords server.
  - Installera en grafisk stationär Linux-miljö (till exempel MATE, XFCE och så vidare).

### <a name="x2go-setup"></a>X2Go-installation
För att använda X2Go måste instruktören:
- Installera X2Go fjärr skrivbords server.
- Installera en grafisk stationär Linux-miljö (till exempel MATE, XFCE och så vidare).

X2Go använder samma port som redan är aktive rad för SSH.  Därför krävs ingen extra konfiguration för att öppna en port på den virtuella datorn för X2Go.

> [!NOTE]
> I vissa fall, till exempel med Ubuntu LTS 18,04, ger X2Go bättre prestanda.  Om du använder RDP och meddelande fördröjning när du interagerar med den grafiska Skriv bords miljön kan du försöka med att testa X2Go eftersom det kan förbättra prestandan.

> [!IMPORTANT]
>  Vissa Marketplace-avbildningar har redan en grafisk Skriv bords miljö och fjärr skrivbords server installerad.  Till exempel har [data science Virtual Machine för Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) redan xfce- [och X2Go-servern installerad och kon figurer ATS för att acceptera klient anslutningar](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#x2go).

## <a name="enable-remote-desktop-connection-for-rdp"></a>Aktivera fjärr skrivbords anslutning för RDP

Det här steget behövs bara för att ansluta via RDP.  Om du i stället planerar att använda X2Go kan du gå vidare till nästa avsnitt eftersom X2Go använder SSH-porten.

1.  Under labb skapandet har instruktören möjlighet att **aktivera anslutning till fjärrskrivbord**.  Instruktören måste **Aktivera** det här alternativet för att öppna porten på den virtuella Linux-datorn som behövs för en RDP-fjärrskrivbordssession.  Annars, om det här alternativet lämnas **inaktiverat**, öppnas bara porten för SSH.
  
    ![Skärm bild som visar fönstret "nytt labb" med alternativet "Aktivera Anslutning till fjärrskrivbord".](./media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. I rutan **aktivera anslutning till fjärrskrivbord** meddelande väljer du **Fortsätt med fjärr skrivbord**. 

    ![Aktivera anslutning till fjärr skrivbord för en Linux-avbildning](./media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>Installera RDP eller X2Go

När labbet har skapats måste instruktören se till att en grafisk Skriv bords miljö och fjärr skrivbords server är installerade på mallen VM.  Lärare måste först ansluta till mallen VM med hjälp av SSH för att installera paketen för:
- Antingen RDP-eller X2Go-fjärrskrivbord-servern.
- En grafisk Skriv bords miljö, till exempel MATE, XFCE osv.

När den har kon figurer ATS kan instruktören ansluta till den virtuella dator mal len med hjälp av antingen **Microsoft fjärrskrivbord-klienten (RDP)** eller **X2Go** -klienten.

Följ stegen nedan för att konfigurera mallen VM:

1. Om du ser **Anpassa mall** i verktygsfältet väljer du den. Välj sedan **Fortsätt** i dialog rutan **Anpassa mall** . Den här åtgärden startar mallen VM.  

    ![Anpassa mall](./media/how-to-enable-remote-desktop-linux/customize-template.png)
1. När mallen VM har startats kan du välja **Anslut mall** och sedan **ansluta via SSH** i verktygsfältet. 

    ![Anslut till Mall via RDP när labbet har skapats](./media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. Du ser följande dialog ruta **för att ansluta till din virtuella dator** . Välj **kopierings** knappen bredvid text rutan för att kopiera den till Urklipp. Spara informationen om SSH-anslutningen. Använd den här anslutnings informationen från en SSH-Terminal (t. ex. [SparaTillFil](https://www.putty.org/)) för att ansluta till den virtuella datorn.
 
    ![SSH-anslutningssträng](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Installera antingen RDP eller X2Go tillsammans med den grafiska Skriv bords miljö som du föredrar.  Se följande instruktioner:
    - [Installera och konfigurera RDP](../virtual-machines/linux/use-remote-desktop.md)
    - [Installera och konfigurera X2Go](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop)

## <a name="connect-to-the-template-vm-via-the-gui"></a>Ansluta till mallen VM via det grafiska användar gränssnittet

När mallen VM har kon figurer ATS kan instruktören ansluta via det grafiska användar gränssnittet med hjälp av antingen **Microsoft fjärrskrivbord-klienten (RDP)** eller **X2Go** -klienten.  Vilken klient du använder beror på om RDP-eller X2Go har kon figurer ATS som fjärr skrivbords server på mallen VM.  

### <a name="microsoft-remote-desktop-rdp-client"></a>Microsoft Fjärrskrivbord-klient (RDP)

Microsoft Fjärrskrivbord-klienten (RDP) används för att ansluta till en mall för virtuella datorer som har RDP konfigurerat.  Fjärr skrivbords klienten kan användas på Windows, Chromebooks, Mac med mera.  Mer information finns i artikeln om [fjärr skrivbords klienter](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) .

Följ stegen nedan beroende på vilken typ av dator som används för att ansluta till mallen VM:

- Windows
  1. Klicka på **Anslut till mall** i ditt labbs verktygsfält och välj **Anslut via RDP** för att ansluta till mallen VM. 
  1. Spara RDP-filen och Använd den för att ansluta till den virtuella dator mal len med hjälp av fjärr skrivbords klienten. 
  1. Normalt är fjärr skrivbords klienten redan installerad och konfigurerad i Windows.  Därför behöver du bara klicka på RDP-filen för att öppna den och starta fjärrsessionen.

- Mac
  1. Klicka på **Anslut till mall** i Labbets verktygsfält och välj sedan **Anslut via RDP** för att spara RDP-filen.  
  1. Läs sedan artikeln om hur du [ansluter till en virtuell dator med RDP på en Mac](connect-virtual-machine-mac-remote-desktop.md).

- Chromebook
  1. Klicka på **Anslut till mall** i Labbets verktygsfält och välj sedan **Anslut via RDP** för att spara RDP-filen.  
  1. Läs sedan artikeln om hur du [ansluter till en virtuell dator med RDP på en Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).

### <a name="x2go-client"></a>X2Go-klient

X2Go-klienten används för att ansluta till en virtuell mall som har X2Go konfigurerat.  Följ anvisningarna i instruktionen så här [ansluter du till en virtuell dator med](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go)hjälp av X2Go i mallen för den virtuella datorns ssh-anslutning.

## <a name="next-steps"></a>Nästa steg
När en instruktör har ställt in antingen RDP-eller X2Go på sin mall-VM och publicerar, kan eleverna ansluta till sina virtuella datorer via GUI-fjärrskrivbord eller SSH.

Mer information finns i:
 - [Ansluta till en virtuell Linux-dator](how-to-use-remote-desktop-linux-student.md)