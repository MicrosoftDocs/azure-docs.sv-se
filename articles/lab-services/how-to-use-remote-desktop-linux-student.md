---
title: Ansluta till en virtuell Linux-dator i Azure Lab Services | Microsoft Docs
description: Lär dig hur du använder fjärr skrivbord för virtuella Linux-datorer i ett labb i Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d13868477ff2e3378d87d7785789a7498ed17e59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85443425"
---
# <a name="connect-to-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Ansluta till virtuella Linux-datorer i ett klass rums labb i Azure Lab Services
Den här artikeln visar hur studenter kan ansluta till en virtuell Linux-dator (VM) i ett labb med hjälp av:
- SSH (Secure Shell Protocol) Terminal
- GRAFISKT användar gränssnitt (grafiskt användar gränssnitt) fjärr skrivbord

> [!IMPORTANT] 
> SSH konfigureras automatiskt så att både studenter och instruktören kan användas av SSH i virtuella Linux-datorer utan ytterligare konfiguration. Men om eleverna behöver ansluta till med ett GUI-fjärrskrivbord kan instruktören behöva göra ytterligare inställningar.  Mer information finns i [Aktivera fjärr skrivbord för virtuella Linux-datorer](how-to-enable-remote-desktop-linux.md).

## <a name="connect-to-the-student-vm-using-ssh"></a>Ansluta till den virtuella student datorn med SSH

1. När en student loggar in på labb portalen direkt ( `https://labs.azure.com` ) eller genom att använda en registrerings länk ( `https://labs.azure.com/register/<registrationCode>` ), visas en panel för varje labb som eleven har åtkomst till. 
   
1. På panelen växlar du knappen för att starta den virtuella datorn om den är i stoppat läge. 

2. Välj **Anslut**. Du ser två alternativ för att ansluta till den virtuella datorn: **SSH** och **RDP**.

    ![Elev VM – anslutnings alternativ](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

3. Välj **SSH** -alternativet så visas dialog rutan **Anslut till din virtuella dator** :  

    ![SSH-anslutningssträng](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Klicka på **kopierings** knappen bredvid text rutan för att kopiera ssh-anslutningen till Urklipp. 

5. Spara informationen om SSH-anslutningen, till exempel i textfältet, så att du kan använda den här anslutnings informationen i nästa steg.

6. Anslut till den virtuella datorn från en SSH-Terminal (t. ex. [SparaTillFil](https://www.putty.org/)).

## <a name="connect-to-the-student-vm-using-gui-remote-desktop"></a>Ansluta till den virtuella student datorn med GUI-fjärr skrivbord
Instruktören kan välja att konfigurera virtuella datorer så att studenter även kan ansluta med hjälp av ett GUI-fjärrskrivbord.  I det här fallet behöver eleverna ta reda på från deras instruktör om de ska ansluta till sina virtuella datorer med hjälp av **Microsoft fjärrskrivbord (RDP)** eller **X2Go** -klient programmet.  Båda programmen gör det möjligt för en student att fjärrans luta till sin virtuella dator och visa det grafiska Linux-skrivbordet på den lokala datorn.

### <a name="connect-to-the-student-vm-using-microsoft-remote-desktop-rdp"></a>Ansluta till den virtuella student datorn med Microsoft Fjärrskrivbord (RDP)
Studenter kan använda Microsoft Fjärrskrivbord (RDP) för att ansluta till sina virtuella Linux-datorer när deras instruktör ställer in sitt labb med RDP-och GUI-paket för en grafisk Skriv bords miljö i Linux (till exempel MATE, XFCE och så vidare). Här är stegen för att ansluta: 

1. Kontrol lera att den virtuella datorn körs på panelen för din virtuella dator och klicka på **Anslut**. Du ser två alternativ för att ansluta till den virtuella datorn: **SSH** och **RDP**.

    ![Elev VM – anslutnings alternativ](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)
2. Välj alternativet **RDP** .  När RDP-filen har laddats ned till datorn sparar du den på den virtuella datorn.

3. Om du ansluter från en Windows-dator är det vanligt vis den Microsoft Fjärrskrivbord (RDP) som redan är installerad och konfigurerad.  Därför behöver du bara klicka på RDP-filen för att öppna den och starta fjärrsessionen.

    Om du ansluter från antingen en Mac-eller Chromebook, se följande steg:
   - [Anslut till en virtuell dator med RDP på en Mac](connect-virtual-machine-mac-remote-desktop.md).
   - [Anslut till en virtuell dator med RDP på en Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).  

### <a name="connect-to-the-student-vm-using-x2go"></a>Ansluta till den virtuella student datorn med X2Go
Studenter kan använda X2Go för att ansluta till sina virtuella Linux-datorer när deras instruktör ställer in sitt labb med X2Go och GUI-paket för en grafisk stationär Linux-dator miljö (till exempel MATE, XFCE och så vidare).

Studenterna behöver ta reda på från deras instruktörer som deras lärare har installerat i den grafiska Linux-miljön.  Den här informationen behövs i nästa steg för att ansluta med X2Go-klienten.

1. Installera [X2Go-klienten](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) på den lokala datorn.

1. Följ anvisningarna i det [första avsnittet](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh) om hur du kopierar SSH-anslutningssträngen för den virtuella datorn.  Du behöver den här informationen för att ansluta med X2Go-klienten.

1. När du har information om ssh-anslutningen öppnar du X2Go-klienten och väljer **sessionen**  >  **ny session**.
   ![X2Go skapa ny session](./media/how-to-use-classroom-lab/x2go-new-session.png)

1. Ange värdena i fönstret **Inställningar för arbets** ytan baserat på informationen om ssh-anslutningen.  Till exempel ser din anslutnings information ut ungefär så här:

    ```bash
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
    ```

    I det här exemplet anges följande värden:

   - **Sessionsnamn** – ange ett namn, till exempel namnet på den virtuella datorn.
   - **Värd** -ID: t för den virtuella datorn. till exempel **`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`** .
   - **Logga in** – användar namnet för den virtuella datorn. till exempel **student**.
   - **SSH-port** – den unika port som tilldelats den virtuella datorn. till exempel **12345**.
   - **Typ av session** – Välj den grafiska Linux-skrivbordet som din instruktör konfigurerade din virtuella dator.  Du måste hämta den här informationen från instruktören.

    Klicka slutligen på **OK** för att skapa sessionen.

    ![Inställningar för X2Go-session](./media/how-to-use-classroom-lab/x2go-session-preferences.png)

1.  Klicka på sessionen i den högra rutan.

    ![X2Go starta ny session](./media/how-to-use-classroom-lab/x2go-start-session.png)

    > [!NOTE] 
    > Om du uppmanas att ange ett liknande meddelande väljer du **Ja** för att fortsätta att ange ditt lösen ord: **det går inte att upprätta äktheten på värden [ `00000000-0000-0000-0000-000000000000.eastus2.cloudapp.eastus.cloudapp.azure.com` ]: 12345.  ECDSA Key finger avtryck är SHA256:00000000000000000000000000000000000000000000. är du säker på att du vill fortsätta ansluta (Ja/Nej)?**

2. Ange ditt lösen ord när du uppmanas till det och klicka på **OK**.  Du kommer nu att vara fjärran sluten till den virtuella datorns grafiska dator miljö.

## <a name="next-steps"></a>Nästa steg
Information om hur du aktiverar funktionen fjärr skrivbords anslutning för virtuella Linux-datorer i ett klass rums labb finns i [Aktivera fjärr skrivbord för virtuella Linux-datorer](how-to-enable-remote-desktop-linux.md). 

