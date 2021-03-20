---
title: Installera Office på en huvud hård disk avbildning – Azure
description: Installera och anpassa Office på en Windows-huvudavbildning för virtuella skriv bord till Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6369013d605ae538ad611a28a90e9c099bb7d80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91326056"
---
# <a name="install-office-on-a-master-vhd-image"></a>Installera Office på en VHD-huvudavbildning

Den här artikeln beskriver hur du installerar Microsoft 365-appar för företag, OneDrive och andra vanliga program på en virtuell hård disk avbildning (VHD) för uppladdning till Azure. Om dina användare behöver åtkomst till vissa LOB-program (Line of Business) rekommenderar vi att du installerar dem när du har slutfört instruktionerna i den här artikeln.

Den här artikeln förutsätter att du redan har skapat en virtuell dator (VM). Om inte, se [förbereda och anpassa en huvud-VHD-avbildning](set-up-customize-master-image.md#create-a-vm)

Den här artikeln förutsätter också att du har förhöjd åtkomst på den virtuella datorn, oavsett om den är etablerad i Azure eller Hyper-V Manager. Om inte, se [öka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper](../role-based-access-control/elevate-access-global-admin.md).

>[!NOTE]
>De här anvisningarna gäller för en Windows-konfiguration med en virtuell dator som kan användas med din organisations befintliga processer.

## <a name="install-office-in-shared-computer-activation-mode"></a>Installera Office i läget för delad dator aktivering

Med den delade dator aktiveringen kan du distribuera Microsoft 365 appar för företag till en dator i organisationen som används av flera användare. Mer information om aktivering av delade datorer finns i [Översikt över aktivering av delade datorer för Microsoft 365 appar](/deployoffice/overview-shared-computer-activation).

Använd [distributions verktyget för Office](https://www.microsoft.com/download/details.aspx?id=49117) för att installera Office. Windows 10 Enterprise multi-session stöder endast följande versioner av Office:

   - Microsoft 365-appar för företag
   - Microsoft 365 appar för företag som medföljer en Microsoft 365 Business Premium-prenumeration

Distributions verktyget för Office kräver en konfigurations-XML-fil. Information om hur du anpassar följande exempel finns i [konfigurations alternativen för distributions verktyget för Office](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/).

Den här exempel konfigurations-XML: en som vi har angett kommer att göra följande:

   - Installera Office från den månatliga Enterprise-kanalen och leverera uppdateringar från den månatliga företags kanalen.
   - Använd x64-arkitekturen.
   - Inaktivera automatiska uppdateringar.
   - Ta bort alla befintliga installationer av Office och migrera sina inställningar.
   - Aktivera aktivering av delad dator.

>[!NOTE]
>Sök funktionen i Visios stencil kanske inte fungerar som förväntat i Windows Virtual Desktop.

Det här är den här exempel konfigurations-XML: en:

   - Installera Skype för företag
   - Installera OneDrive i per användare-läge. Läs mer i [Installera OneDrive i per dator läge](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Aktivering av delade datorer kan konfigureras via grupprincip objekt (GPO) eller register inställningar. GRUPPRINCIPOBJEKTet finns på **dator konfigurations \\ principer \\ administrativa mallar \\ Microsoft Office 2016 (dator) \\ licens inställningar**

Distributions verktyget för Office innehåller setup.exe. Installera Office genom att köra följande kommando på en kommando rad:

```cmd
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Exempel configuration.xml

Följande XML-exempel kommer att installera den månatliga Enterprise Channel-versionen.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="MonthlyEnterprise">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level="Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Office-teamet rekommenderar att du använder 64-bitars installation för parametern **OfficeClientEdition** .

När du har installerat Office kan du uppdatera standard beteendet för Office. Kör följande kommandon individuellt eller i en kommando fil för att uppdatera beteendet.

```cmd
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>Installera OneDrive i per dator-läge

OneDrive installeras vanligt vis per användare. I den här miljön bör den installeras per dator.

Så här installerar du OneDrive i per dator läge:

1. Börja med att skapa en plats för att mellanlagra OneDrive-installationsprogrammet. En lokal disk-mapp eller [ \\ \\ UNC]-plats (File://UNC) är felfri.

2. Ladda ned OneDriveSetup.exe till din mellanlagrade plats med den här länken: <https://aka.ms/OneDriveWVD-Installer>

3. Om du har installerat Office med OneDrive genom att utesluta **\<ExcludeApp ID="OneDrive" /\>** avinstallerar du alla befintliga OneDrive-användarspecifika installationer från en upphöjd kommando tolk genom att köra följande kommando:

    ```cmd
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Kör det här kommandot från en upphöjd kommando tolk för att ange registervärdet **AllUsersInstall** :

    ```cmd
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Kör det här kommandot för att installera OneDrive i per dator-läge:

    ```cmd
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Kör det här kommandot för att konfigurera OneDrive för att starta vid inloggning för alla användare:

    ```cmd
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Aktivera **tyst konfiguration av användar konto** genom att köra följande kommando.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Omdirigera och flytta Windows-kända mappar till OneDrive genom att köra följande kommando.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="microsoft-teams-and-skype-for-business"></a>Microsoft Teams och Skype för företag

Virtuella Windows-datorer har inte stöd för Skype för företag.

Information om hur du installerar Microsoft Teams finns i [använda Microsoft Teams på Windows Virtual Desktop](teams-on-wvd.md). Medie optimering för Microsoft Teams i Windows Virtual Desktop finns i för hands version.

## <a name="next-steps"></a>Nästa steg

Nu när du har lagt till Office i avbildningen kan du fortsätta att anpassa din huvud-VHD-avbildning. Se [förbereda och anpassa en huvud-VHD-avbildning](set-up-customize-master-image.md).
