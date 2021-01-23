---
title: Använd Visual Studio för att aktivera fjärr skrivbord för en roll (Azure Cloud Services Classic)
description: Så här konfigurerar du Azure Cloud Service-programmet för att tillåta fjärr skrivbords anslutningar
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ad95cefbdf839c28b0979b051e217a1dfec76eea
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743244"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-visual-studio"></a>Aktivera Anslutning till fjärrskrivbord för en roll i Azure Cloud Services (klassisk) med hjälp av Visual Studio

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Azure-portalen](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Med fjärr skrivbord kan du komma åt Skriv bordet för en roll som körs i Azure. Du kan använda en anslutning till fjärr skrivbord för att felsöka och diagnostisera problem med programmet medan det körs.

Publicerings guiden som Visual Studio tillhandahåller för Cloud Services innehåller ett alternativ för att aktivera fjärr skrivbord under publicerings processen med hjälp av autentiseringsuppgifter som du anger. Att använda det här alternativet är användbart när du använder Visual Studio 2017 version 15,4 och tidigare.

Med Visual Studio 2017 version 15,5 och senare rekommenderar vi dock att du undviker att aktivera fjärr skrivbord via publicerings guiden, såvida du inte arbetar bara som en enda utvecklare. I stället för en situation där projektet kan öppnas av andra utvecklare, aktiverar du i stället fjärr skrivbord via Azure Portal, via PowerShell eller från en versions pipeline i ett arbets flöde för kontinuerlig distribution. Den här rekommendationen beror på en förändring i hur Visual Studio kommunicerar med fjärr skrivbord på den virtuella moln tjänsten, enligt beskrivningen i den här artikeln.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Konfigurera fjärr skrivbord via Visual Studio 2017 version 15,4 och tidigare

När du använder Visual Studio 2017 version 15,4 och tidigare kan du använda alternativet **Aktivera fjärr skrivbord för alla roller** i publicerings guiden. Du kan fortfarande använda guiden med Visual Studio 2017 version 15,5 och senare, men Använd inte alternativet fjärr skrivbord.

1. Starta publicerings guiden i Visual Studio genom att högerklicka på ditt moln tjänst projekt i Solution Explorer och välja **publicera**.

2. Logga in på din Azure-prenumeration om det behövs och välj **Nästa**.

3. På sidan **Inställningar** väljer du **Aktivera fjärr skrivbord för alla roller** och väljer sedan länken **Inställningar...** för att öppna dialog rutan **konfiguration av fjärr skrivbord** .

4. Klicka på **fler alternativ** längst ned i dialog rutan. Det här kommandot visar en listruta där du skapar eller väljer ett certifikat så att du kan kryptera autentiseringsinformation när du ansluter via fjärr skrivbord.

   > [!Note]
   > De certifikat som du behöver för en fjärr skrivbords anslutning skiljer sig från de certifikat som du använder för andra Azure-åtgärder. Remote Access-certifikatet måste ha en privat nyckel.

5. Välj ett certifikat i listan eller Välj **&lt; skapa. &gt;**... Om du skapar ett nytt certifikat anger du ett eget namn för det nya certifikatet när du uppmanas till det och väljer **OK**. Det nya certifikatet visas i list rutan.

6. Ange ett användar namn och ett lösen ord. Du kan inte använda ett befintligt konto. Använd inte "administratör" som användar namn för det nya kontot.

7. Välj ett datum då kontot ska förfalla och efter vilket fjärr skrivbords anslutningar som ska blockeras.

8. När du har angett all information som krävs väljer du **OK**. Visual Studio lägger till fjärr skrivbords inställningar till ditt projekt `.cscfg` och `.csdef` filer, inklusive lösen ordet som krypteras med det valda certifikatet.

9. Slutför alla återstående steg med knappen **Nästa** och välj sedan **publicera** när du är redo att publicera din moln tjänst. Om du inte är redo att publicera väljer du **Avbryt** och svarar **Ja** när du uppmanas att spara ändringarna. Du kan publicera moln tjänsten senare med de här inställningarna.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Konfigurera fjärr skrivbord när du använder Visual Studio 2017 version 15,5 och senare

Med Visual Studio 2017 version 15,5 och senare kan du fortfarande använda publicerings guiden med ett moln tjänst projekt. Du kan också använda alternativet **Aktivera fjärr skrivbord för alla roller** om du arbetar bara som en enda utvecklare.

Om du arbetar som en del av ett team bör du istället aktivera fjärr skrivbord på Azures moln tjänst genom att använda antingen [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) eller [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

Den här rekommendationen beror på en förändring i hur Visual Studio 2017 version 15,5 och senare kommunicerar med den virtuella moln tjänsten. När du aktiverar fjärr skrivbord via publicerings guiden kommunicerar tidigare versioner av Visual Studio med den virtuella datorn via det som kallas RDP-plugin-programmet. Visual Studio 2017 version 15,5 och senare kommunicerar i stället med "RDP-tillägget" som är säkrare och mer flexibelt. Den här ändringen justeras också med det faktum att Azure Portal-och PowerShell-metoderna för att aktivera fjärr skrivbord också använder RDP-tillägget.

När Visual Studio kommunicerar med RDP-tillägget skickar det ett oformaterat text lösen ord via TLS. Projektets konfigurationsfiler lagrar dock bara ett krypterat lösen ord, som endast kan dekrypteras till oformaterad text med det lokala certifikat som ursprungligen användes för att kryptera det.

Om du distribuerar Cloud Service-projektet från samma utvecklings dator varje gång är det lokala certifikatet tillgängligt. I det här fallet kan du fortfarande använda alternativet **Aktivera fjärr skrivbord för alla roller** i publicerings guiden.

Om du eller andra utvecklare vill distribuera moln tjänst projektet från olika datorer, så har de andra datorerna inte det certifikat som krävs för att dekryptera lösen ordet. Därför visas följande fel meddelande:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Du kan ändra lösen ordet varje gång du distribuerar moln tjänsten, men den åtgärden blir inte lämplig för alla som behöver använda fjärr skrivbord.

Om du delar projektet med ett team, är det bäst att ta bort alternativet i publicerings guiden och i stället aktivera fjärr skrivbord direkt via [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) eller genom att använda [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Distribuera från en build-server med Visual Studio 2017 version 15,5 och senare

Du kan distribuera ett Cloud Service-projekt från en build-Server (till exempel med Azure DevOps Services) som Visual Studio 2017 version 15,5 eller senare är installerat i build-agenten. I den här ordningen sker distributionen från den dator där krypterings certifikatet är tillgängligt.

Om du vill använda RDP-tillägget från Azure DevOps Services inkluderar du följande information i din build-pipeline:

1. Ta med `/p:ForceRDPExtensionOverPlugin=true` i dina MSBuild-argument för att se till att distributionen fungerar med RDP-tillägget i stället för RDP-plugin-programmet. Ett exempel:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. När du har skapat stegen lägger du till **distributions steget Azure Cloud Service** och anger dess egenskaper.

1. Efter distributions steget lägger du till ett **Azure PowerShell** -steg, anger egenskapen **visnings namn** till "Azure-distribution: aktivera RDP-tillägg" (eller ett annat lämpligt namn) och väljer lämplig Azure-prenumeration.

1. Ange **skript typ** som "infogad" och klistra in koden nedan i fältet infogat **skript** . (Du kan också skapa en `.ps1` fil i projektet med det här skriptet, ange **skript typ** till "skript fil Sök väg" och ange **skript Sök vägen** så att den pekar på filen.)

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Ansluta till en Azure-roll med hjälp av fjärr skrivbord

När du har publicerat din moln tjänst på Azure och har aktiverat fjärr skrivbord kan du använda Visual Studio-Server Explorer för att logga in på den virtuella datorn för moln tjänsten:

1. I Server Explorer expanderar du **Azure** -noden och expanderar sedan noden för en moln tjänst och en av dess roller för att visa en lista över instanser.

2. Högerklicka på en instans-nod och välj **Anslut med fjärr skrivbord**.

3. Ange användar namn och lösen ord som du skapade tidigare. Du är nu inloggad i fjärrsessionen.

## <a name="additional-resources"></a>Ytterligare resurser

[Så här konfigurerar du Cloud Services](cloud-services-how-to-configure-portal.md)
