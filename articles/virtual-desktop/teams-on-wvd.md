---
title: Microsoft Teams på Windows Virtual Desktop – Azure
description: Använda Microsoft Teams på Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 07/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2032a7c9d9cd9b17da956dc829234462f8b9e726
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509611"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Använd Microsoft Teams på Windows Virtual Desktop

>[!IMPORTANT]
>Medie optimering för team stöds inte för Microsoft 365 myndighets miljöer.

>[!NOTE]
>Medie optimering för Microsoft Teams är bara tillgänglig för Windows Skriv bords klient på Windows 10-datorer. Medie optimeringar kräver Windows Desktop Client version 1.2.1026.0 eller senare.

Microsoft Teams på Windows Virtual Desktop stöder chatt och samarbete. Med medie optimeringar har den också stöd för anropande och mötes funktion. Mer information om hur du använder Microsoft Teams i VDI-miljöer (Virtual Desktop Infrastructure) finns i [Teams for virtualiserad Desktop Infrastructure](/microsoftteams/teams-for-vdi/).

Med medie optimering för Microsoft team, hanterar Windows Skriv bords klienten ljud och video lokalt för team-samtal och-möten. Du kan fortfarande använda Microsoft Teams på virtuella Windows-datorer med andra klienter utan optimerad samtal och möten. Teams chatt-och samarbets funktioner stöds på alla plattformar. Om du vill omdirigera lokala enheter i fjärrsessionen kan du läsa [anpassa Remote Desktop Protocol egenskaper för en adresspool](#customize-remote-desktop-protocol-properties-for-a-host-pool).

## <a name="prerequisites"></a>Krav

Innan du kan använda Microsoft Teams på Windows Virtual Desktop måste du göra följande saker:

- [Förbered ditt nätverk](/microsoftteams/prepare-network/) för Microsoft-team.
- Installera [Windows Skriv bords klient](connect-windows-7-10.md) på en Windows 10-eller Windows 10 IoT Enterprise-enhet som uppfyller Microsoft Teams [maskin varu krav för team på en Windows-dator](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/).
- Anslut till en virtuell Windows 10-dator med flera sessioner eller Windows 10 Enterprise (VM).
- Installera Teams Desktop-appen på värden med hjälp av installation per dator. Medie optimering för Microsoft Teams kräver Teams Desktop app-version 1.3.00.4461 eller senare.

## <a name="install-the-teams-desktop-app"></a>Installera Teams Desktop-appen

I det här avsnittet visas hur du installerar Teams Desktop-appen på en VM-avbildning för Windows 10 multi-session eller Windows 10 Enterprise. Om du vill veta mer kan du läsa [Installera eller uppdatera Teams Desktop-appen på VDI](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi).

### <a name="prepare-your-image-for-teams"></a>Förbered din avbildning för team

Om du vill aktivera medie optimering för team anger du följande register nyckel på värden:

1. Från Start-menyn kör du **regedit** som administratör. Navigera till **HKEY_LOCAL_MACHINE \software\microsoft\teams**.
2. Skapa följande värde för team nyckeln:

| Namn             | Typ   | Data/värde  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>Installera Teams WebSocket-tjänsten

Installera den senaste [WebSocket-tjänsten](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4AQBt) på den virtuella dator avbildningen. Om ett installations fel uppstår installerar du den [senaste versionen av Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) och försöker igen.

#### <a name="latest-websocket-service-versions"></a>Senaste WebSocket service-versioner

I följande tabell visas de senaste versionerna av WebSocket-tjänsten:

|Version        |Utgivningsdatum  |
|---------------|--------------|
|1.0.2006.11001 |07/28/2020    |
|0.11.0         |05/29/2020    |

#### <a name="updates-for-version-10200611001"></a>Uppdateringar för version 1.0.2006.11001

- Ett problem har åtgärd ATS där team-appen minimeras under ett samtal eller ett möte som orsakade att inkommande video togs bort.
- Stöd har lagts till för att välja en övervakare att dela i sessioner med flera bildskärmar.

### <a name="install-microsoft-teams"></a>Installera Microsoft Teams

Du kan distribuera Skriv bords appen för team med en installation per dator eller per användare. Så här installerar du Microsoft Teams i din Windows Virtual Desktop-miljö:

1. Ladda ned [Teams MSI-paketet](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) som matchar din miljö. Vi rekommenderar att du använder 64-bitars installations programmet på ett 64-bitars operativ system.

      > [!IMPORTANT]
      > Den senaste uppdateringen av Teams Skriv bords version 1.3.00.21759 har åtgärdat ett problem där team visade UTC-tidszonen i chatt, kanaler och kalender. I den nya versionen av klienten visas tids zonen för fjärrsessionen.

2. Kör något av följande kommandon för att installera MSI på den virtuella värddatorn:

    - Installation per användare

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name>
        ```

        Den här processen är standard installationen, som installerar team till mappen **% AppData%** User. Team fungerar inte korrekt med installation per användare på en icke-beständig installation.

    - Installation per dator

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1
        ```

        Detta installerar Teams till mappen programfiler (x86) på ett 64-bitars operativ system och i mappen program på ett 32-bitars operativ system. I det här läget är installationen av gyllene bilder klar. Installation av team per dator krävs för icke-beständiga installationer.

        Det finns två flaggor som kan anges när du installerar Team, **ALLUSER = 1** och **allusers = 1**. Det är viktigt att förstå skillnaden mellan dessa parametrar. Parametern **ALLUSER = 1** används endast i VDI-miljöer för att ange en installation per dator. Parametern **allusers = 1** kan användas i non-VDI-och VDI-miljöer. När du anger den här parametern visas alla Teams datorövergripande installations program i program och funktioner i kontroll panelen samt appar & funktioner i Windows-inställningar. Alla användare med administratörs behörighet på datorn kan avinstallera team. 
       
        > [!NOTE]
        > Användare och administratörer kan inte inaktivera automatisk start för team under inloggningen just nu.

3. Kör det här kommandot om du vill avinstallera MSI från den virtuella värddatorn:

      ```powershell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      Detta avinstallerar team från mappen programfiler (x86) eller mappen program filer, beroende på operativ systemets miljö.

      > [!NOTE]
      > När du installerar team med MSI-inställningen ALLUSER = 1 kommer automatiska uppdateringar att inaktive ras. Vi rekommenderar att du uppdaterar team minst en gång i månaden. Om du vill veta mer om hur du distribuerar Teams Desktop-appen kan du läsa [distribuera Skriv bords appen till den virtuella datorn](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/).

### <a name="verify-media-optimizations-loaded"></a>Verifiera inlästa medie optimeringar

När du har installerat WebSocket-tjänsten och Teams Desktop-appen följer du dessa steg för att kontrol lera att team medie optimeringar har lästs in:

1. Välj din användar profil avbildning och välj sedan **om**.
2. Välj **version**.

      Om medie optimeringar läses in visar banderollen att **WVD-mediet är optimerat**. Om banderollen visar att du **WVD media inte är ansluten**avslutar du team-appen och försöker igen.

3. Välj användar profil avbildningen och välj sedan **Inställningar**.

      Om medie optimeringar läses in räknas ljud enheterna och kamerorna som är tillgängliga lokalt i enhets menyn. Om menyn visar **fjärrljud**avslutar du team-appen och försöker igen. Om enheterna fortfarande inte visas i menyn kontrollerar du sekretess inställningarna på den lokala datorn. Se till att **Settings**  >  **Privacy**  >  inställningen **Tillåt att appar får åtkomst till mikrofonen** är **aktiverat**under Inställningar sekretess**program behörigheter** . Koppla från fjärrsessionen och Anslut sedan igen och kontrol lera ljud-och video enheterna igen. Om du vill ansluta till samtal och möten med video måste du också bevilja behörighet för appar för att få åtkomst till kameran.

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

Att använda team i en virtualiserad miljö skiljer sig från att använda team i en miljö som inte är virtualiserad. Mer information om begränsningarna för team i virtualiserade miljöer finns i [Teams for virtualiserad Desktop-infrastruktur](/microsoftteams/teams-for-vdi#known-issues-and-limitations).

### <a name="client-deployment-installation-and-setup"></a>Klient distribution, installation och installation

- Med installation per dator uppdateras inte team på VDI automatiskt på samma sätt som icke-VDI-team klienter är. Om du vill uppdatera klienten måste du uppdatera den virtuella dator avbildningen genom att installera en ny MSI.
- Medie optimering för team stöds bara för Windows Desktop-klienten på datorer som kör Windows 10.
- Det finns inte stöd för användning av explicita HTTP-proxyservrar definierade i en slut punkt.

### <a name="calls-and-meetings"></a>Samtal och möten

- Teams Skriv bords klienten i Windows-miljöer för virtuella skriv bord stöder inte Live-händelser. För närvarande rekommenderar vi att du ansluter Live-händelser från [team webb klienten](https://teams.microsoft.com) i fjärrsessionen i stället.
- Anrop eller möten stöder för närvarande inte program delning. Desktop-sessioner stöder Skriv bords delning.
- Det finns för närvarande inte stöd för att ge kontrollen och kontrollen ta kontroll.
- Teams på virtuella Windows-datorer stöder endast en inkommande video ingång i taget. Det innebär att när någon försöker dela skärmen visas skärmen i stället för Mötes ledarens skärm.
- På grund av WebRTC-begränsningar är inkommande och utgående video Ströms upplösning begränsad till 720p.
- Team-appen stöder inte HID-knappar eller leda kontroller med andra enheter.

För kända team problem som inte är relaterade till virtualiserade miljöer, se [support Teams i din organisation](/microsoftteams/known-issues)

## <a name="uservoice-site"></a>UserVoice-webbplats

Ge feedback för Microsoft Teams på det virtuella Windows-skrivbordet på teamets [UserVoice-webbplats](https://microsoftteams.uservoice.com/).

## <a name="collect-teams-logs"></a>Samla in team loggar

Om du stöter på problem med Teams Desktop-appen i din Windows-miljö för virtuella datorer samlar du in klient loggar under **% AppData% \Microsoft\Teams\logs.txt** på den virtuella värddatorn.

Om du stöter på problem med samtal och möten ska du samla in Teams webb klient loggar med tangentkombinationen **CTRL**  +  **Alt**  +  **Shift**  +  **1**. Loggar skrivs till%USERPROFILE%\Downloads\MSTeams- **DATE_TIME.txt** på den virtuella värddatorn.

## <a name="contact-microsoft-teams-support"></a>Kontakta supporten för Microsoft Teams

Om du vill kontakta supporten för Microsoft team går du till [Microsoft 365 administrations Center](/microsoft-365/admin/contact-support-for-business-products).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Anpassa Remote Desktop Protocol egenskaper för en värd pool

Genom att anpassa en värd Pools Remote Desktop Protocol egenskaper (RDP), t. ex. för flera skärmar eller för att aktivera mikrofon och omdirigering av ljud, kan du leverera en optimal upplevelse för dina användare utifrån deras behov.

Det krävs ingen aktivering av enhets omdirigering när du använder team med medie optimering. Om du använder team utan medie optimering anger du följande RDP-egenskaper för att aktivera omdirigering av mikrofon och kamera:

- `audiocapturemode:i:1` aktiverar ljud fångst från den lokala enheten och dirigerar om ljud program i fjärrsessionen.
- `audiomode:i:0` spelar upp ljud på den lokala datorn.
- `camerastoredirect:s:*` omdirigerar alla kameror.

Läs mer i [anpassa Remote Desktop Protocol egenskaper för en adresspool](customize-rdp-properties.md).
