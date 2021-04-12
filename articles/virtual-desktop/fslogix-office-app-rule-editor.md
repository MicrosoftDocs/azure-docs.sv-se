---
title: Installera Microsoft Office FSLogix program behållare i Windows Virtual Desktop – Azure
description: Så här använder du appens regel redigerare för att skapa en FSLogix program behållare med Office i Windows Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a144adcfbf6c7cefc6b946f95bdb734868de801f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446816"
---
# <a name="install-microsoft-office-using-fslogix-application-containers"></a>Installera Microsoft Office med FSLogix program behållare

Du kan installera Microsoft Office snabbt och effektivt genom att använda en FSLogix program behållare som en mall för de andra virtuella datorerna (VM) i din värd bassäng.

Här är anledningen till att användning av en FSLogix app-behållare kan göra installationen snabbare:

- Genom att avlasta dina Office-appar till en app-behållare minskas kraven för C-enhetens storlek.
- Ögonblicks bilder eller säkerhets kopieringar av din virtuella dator tar mindre resurser.
- Att ha en automatiserad pipeline genom att uppdatera en enda avbildning gör det enklare att uppdatera dina virtuella datorer.
- Du behöver bara en avbildning för att installera Office (och andra appar) på alla virtuella datorer i Windows-distributionen för virtuella datorer.

I den här artikeln visas hur du konfigurerar en FSLogix program behållare med Office.

## <a name="requirements"></a>Krav

Du behöver följande saker för att konfigurera regel redigeraren:

- en virtuell dator som kör Windows utan Office installerat
- en kopia av Office
- en kopia av FSLogix som installerats på din distribution
- en nätverks resurs som alla virtuella datorer i din värd pool har skrivskyddad åtkomst till

## <a name="install-office"></a>Installera Office

Om du vill installera Office på den virtuella hård disken eller VHDX aktiverar du Remote Desktop Protocol i den virtuella datorn och följer sedan anvisningarna i [installera Office på en VHD-huvudavbildning](install-office-on-wvd-master-image.md). Kontrol lera att du använder [rätt licenser](overview.md#requirements)när du installerar.

>[!NOTE]
>För virtuella Windows-datorer måste du ha resurs aktivering (SCA).

## <a name="install-fslogix"></a>Installera FSLogix

Installera FSLogix och regel redigeraren genom att följa anvisningarna i [Hämta och installera FSLogix](/fslogix/install-ht).

## <a name="create-and-prepare-a-vhd-to-store-office"></a>Skapa och Förbered en virtuell hård disk för att lagra Office

Därefter måste du skapa och förbereda en VHD-avbildning för att använda regel redigeraren på:

1. Öppna en kommandotolk som administratör. och kör följande kommando:

    ```cmd
        taskkill /F /IM MicrosoftEdge.exe /T
    ```

    >[!NOTE]
    > Se till att du behåller de tomma blank stegen i det här kommandot.

2. Kör sedan följande kommando:

    ```cmd
    sc queryex type=service state=all | find /i "ClickToRunSvc"
    ```
    
   Om du hittar tjänsten startar du om den virtuella datorn innan du fortsätter med steg 3.

    ```cmd
    net stop ClickToRunSvc
    ```

3. Sedan går du till **programfiler**  >  **FSLogix**-  >  **appar** och kör följande kommando för att skapa den virtuella mål hård disken:

    ```cmd
    frx moveto-vhd -filename <path to network share>\office.vhdx -src "C:\Program Files\Microsoft Office" -size-mbs 5000 
    ```

    Den virtuella hård disk som du skapar med det här kommandot ska innehålla mappen C: \\ programfiler \\ Microsoft Office.

    >[!NOTE]
    >Om du ser några fel avinstallerar du Office och börjar om från steg 1.

## <a name="configure-the-rule-editor"></a>Konfigurera regel redigeraren

Nu när du har för berett din avbildning måste du konfigurera regel redigeraren och skapa en fil för att lagra reglerna i.

1. Gå till **program filer**  >  **FSLogix**  >  **appar** och kör **RuleEditor.exe**.

2. Välj **Arkiv**  >  **ny**  >  **skapa** för att skapa en ny regel uppsättning och spara sedan regel uppsättningen till en lokal mapp.

3. Välj **tom regel uppsättning** och välj sedan **OK**.

4. Välj knappen **+**. Fönstret **Lägg till regel** öppnas. Då ändras alternativen i dialog rutan **Lägg till regel** .

5. I den nedrullningsbara menyn väljer du **regel för app container (VHD)**.

6. Ange **C: \\ programfiler \\ Microsoft Office** i fältet **mapp** .

7. I fältet **disk fil** väljer du **\<path\> \\ Office. VHD** från avsnittet **skapa mål-VHD** .

8. Välj **OK**.

9. Gå till arbetsmappen på **C: \\ Users \\ \<username\> \\ Documents \\ FSLogix Rule Sets** och leta efter filerna. FRX och. FXa. Du måste flytta filerna till mappen regler som finns i **C: \\ Program Files \\ FSLogix \\ app \\ Rules** för att reglerna ska börja fungera.

10. Välj **tillämpa regler på systemet** för att reglerna ska börja gälla.

     >[!NOTE]
     > Du måste tillämpa app Rule-filerna måste vara värd för alla sessioner.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om FSLogix kan du läsa vår [FSLogix-dokumentation](/fslogix/).