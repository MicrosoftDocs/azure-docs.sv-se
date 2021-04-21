---
title: Fel vid start av rollinstans i Azure Cloud Services (utökat stöd)
description: Felsöka startfel för rollinstanser i Azure Cloud Services (utökat stöd).
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f4892fe50c1832628181a11a5166c8cb705f79aa
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748963"
---
# <a name="troubleshoot-azure-cloud-services-extended-support-roles-that-fail-to-start"></a>Felsöka Azure Cloud Services roller (utökat stöd) som inte kan startas

Här är några vanliga problem och lösningar som rör Azure Cloud Services roller (utökad support) som inte startar.

## <a name="cloud-service-operation-fails-with-roleinstancestartuptimeouterror"></a>Molntjänståtgärden misslyckas med RoleInstanceStartupTimeoutError

En eller flera av dina rollinstanser i Azure Cloud Services (utökat stöd) kan ta lång tid att starta, eller så kanske de återanvänds och rollinstansen misslyckas. Rollprogramfelet `RoleInstanceStartupTimeoutError` visas.

Rollprogrammet innehåller två delar som kan orsaka rollåterutnyttjande: *startaktiviteter* och *rollkod (implementering av RoleEntryPoint).* 

Om rollen stoppas startar PaaS-agenten (plattform som en tjänst) om rollen.

Du kan hämta det aktuella tillståndet och information för en rollinstans för att diagnostisera fel med hjälp av PowerShell eller Azure Portal:

* **PowerShell:** Använd cmdleten [Get-AzCloudServiceRoleInstanceView](/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) för att hämta information om körningstillståndet för rollinstansen:

    ```powershell
    Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
     
    Statuses           PlatformFaultDomain PlatformUpdateDomain
    --------           ------------------- --------------------
    {RoleStateStarting} 0                   0
    ```

* **Azure Portal:** Gå till molntjänstinstansen i portalen. Om du vill visa statusinformation **väljer du Roller och** instanser och sedan rollinstansen.

  :::image type="content" source="media/role-startup-failure-portal.png" alt-text="Skärmbild som visar ett fel vid rollstart i Azure Portal.":::

## <a name="missing-dlls-or-dependencies"></a>DLL:er eller beroenden saknas

Roller och roller som inte svarar och som växlas mellan tillstånd kan orsakas av saknade DLL:er eller sammansättningar.

Här är några symptom på saknade DLL:er eller sammansättningar:

* Rollinstansen växlar mellan tillstånden **Initiera**, **Upptagen** och **Stoppa.**
* Rollinstansen har flyttats **till** statusen Klar, men om du går till webbappen visas inte sidan.


En webbplats som har distribuerats i en webbroll och saknar en DLL-fil kan visa följande serverkörningsfel:

  :::image type="content" source="media/role-startup-failure-runtime-error.png" alt-text="Skärmbild som visar ett körningsfel efter ett rollstartfel.":::

### <a name="resolve-missing-dlls-and-assemblies"></a>Lösa saknade DLL:er och sammansättningar

Så här löser du fel med saknade DLL:er och sammansättningar:

1. I Visual Studio du lösningen.
2. I Solution Explorer du *mappen* Referenser.
3. Välj den sammansättning som identifieras i felet.
4. I **Egenskaper** anger du **egenskapen Kopiera lokal** till **Sant.**
5. Distribuera om molntjänsten.

När du har verifierat att felen inte längre visas distribuerar du om tjänsten. När du ställer in distributionen markerar du inte kryssrutan **Aktivera IntelliTrace för .NET 4-roller.**

## <a name="diagnose-role-instance-errors"></a>Diagnostisera rollinstansfel

Välj bland följande alternativ för att diagnostisera problem med rollinstanser.

### <a name="turn-off-custom-errors"></a>Inaktivera anpassade fel

Om du vill visa fullständig felinformation *gårweb.config* för webbrollen och ställer in det anpassade felläget på `off` och distribuerar sedan om tjänsten:

1. I Visual Studio du lösningen.
2. I Solution Explorer öppnar du *web.config* fil.
3. I avsnittet `system.web` lägger du till följande kod:

   ```xml
   <customErrors mode="Off" />
   ```

4. Spara filen.
5. Paketera om och distribuera om tjänsten.

När tjänsten omdistribueras innehåller ett felmeddelande namnet på saknade sammansättningar eller DLL:er.

### <a name="use-remote-desktop"></a>Använd Fjärrskrivbord

Använd Fjärrskrivbord för att komma åt rollen och visa fullständig felinformation:

1. [Lägg till fjärrskrivbordstillägget för Azure Cloud Services (utökat stöd).](enable-rdp.md)
2. När molntjänstinstansen i Azure Portal visar statusen **Klar** använder du Fjärrskrivbord för att logga in på molntjänsten. Mer information finns i [Ansluta till rollinstanser med hjälp av Fjärrskrivbord.](enable-rdp.md#connect-to-role-instances-with-remote-desktop-enabled)
3. Logga in på den virtuella datorn med de autentiseringsuppgifter som du använde för att konfigurera Fjärrskrivbord.
4. Öppna ett kommandotolksfönster.
5. Ange **ipconfig i kommandotolken.** Observera det returnerade värdet för IPv4-adressen.
6. Öppna Microsoft Internet Explorer.
7. I adressfältet anger du IPv4-adressen följt av ett snedstreck och namnet på webbappens standardfil. Till exempel `http://<IPv4 address>/default.aspx`.

Om du går till webbplatsen nu visas felmeddelanden som innehåller mer information. Här är ett exempel:

:::image type="content" source="media/role-startup-failure-error-message.png" alt-text="Skärmbild som visar ett exempel på ett felmeddelande.":::
  
### <a name="use-the-compute-emulator"></a>Använda beräkningsemulatorn

Du kan använda Azure-beräkningsemulatorn för att diagnostisera och felsöka problem med saknade *beroendenweb.config* fel. När du använder den här metoden för att diagnostisera problem, för bästa resultat, använder du en dator eller virtuell dator som har en ren installation av Windows.

Så här diagnostiserar du problem med azure-beräkningsemulatorn:

1. Installera [Azure SDK.](https://azure.microsoft.com/downloads/)
2. Skapa molntjänstprojektet på utvecklingsdatorn.
3. I Utforskaren i molntjänstprojektet går du till mappen *bin\debug.*
4. Kopiera *.csx-mappen* och *.cscfg-filen* till den dator som du använder för att felsöka problem.
5. Öppna ett kommandotolkfönster för Azure SDK på den rena datorn.
6. I kommandotolken anger du **csrun.exe /devstore:start**.
7. Ange sedan **run csrun \<path to .csx folder\> \<path to .cscfg file\> /launchBrowser**.
8. När rollen startar visas Internet Explorer detaljerad felinformation.

Om du behöver mer diagnos kan du använda vanliga felsökningsverktyg för Windows.

### <a name="use-intellitrace"></a>Använda IntelliTrace

För arbets- och webbroller som använder .NET Framework 4 kan du använda [IntelliTrace](/visualstudio/debugger/intellitrace). IntelliTrace är tillgängligt i Visual Studio Enterprise.

Så här distribuerar du din molntjänst med IntelliTrace aktiverat:

1. Kontrollera att Azure SDK 1.3 eller senare är installerat.
2. I Visual Studio du lösningen. När du ställer in distributionen markerar du **kryssrutan Aktivera IntelliTrace för .NET 4-roller.**
3. När rollinstansen startar öppnar du Server Explorer.
4. Expandera **noden Azure\Cloud Services.**
5. Expandera distributionen för att visa rollinstanser. Högerklicka på en rollinstans.
6. Välj **Visa IntelliTrace-loggar**.
7. I **IntelliTrace Summary (IntelliTrace-sammanfattning)** går **du till Exception Data (Undantagsdata).**
8. Expandera **Undantagsdata** och leta efter ett `System.IO.FileNotFoundException` fel:

   :::image type="content" source="media/role-startup-failure-exception-data.png" alt-text="Skärmbild av undantagsdata för ett rollstartfel." lightbox="media/role-startup-failure-exception-data.png":::

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [felsöker problem med molntjänstroller med hjälp av Diagnostikdata för Azure PaaS-datorer.](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)
