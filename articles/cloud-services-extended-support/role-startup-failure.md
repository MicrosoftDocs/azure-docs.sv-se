---
title: Det gick inte att starta roll instansen för Azure Cloud Services (utökad support)
description: Felsöka start fel vid roll instans för Azure Cloud Services (utökad support)
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: ced7675350c0e0deadf77837cf0f13ba54fffab9
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382363"
---
# <a name="troubleshoot-azure-cloud-service-extended-support-roles-that-fail-to-start"></a>Felsöka Azure Cloud Service-roller (utökade support) som inte startar
Här följer några vanliga problem och lösningar som rör Cloud Services-roller (utökad support) som inte startar.

## <a name="cloud-service-operation-failed-with-roleinstancestartuptimeouterror"></a>Moln tjänst åtgärden misslyckades med RoleInstanceStartupTimeoutError
Det går inte att starta en eller flera roll instanser av din tjänst (utökad support) under den angivna tiden. De här roll instanserna kan ta tid att starta eller kan återkrävas och roll instansen kan Miss Miss RoleInstanceStartupTimeoutError detta är ett roll program fel. Roll programmet innehåller två huvud delar: "Start tasks" och "roll kod (implementering av RoleEntryPoint)", som båda kan orsaka roll åter användningen. Om rollen kraschar, kommer PaaS-agenten alltid att starta om den. 

Använd följande för att hämta aktuell status och information om roll instanserna i händelse av fel:

* PowerShell: Använd cmdleten [Get-AzCloudServiceRoleInstanceView](https://docs.microsoft.com/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) för att hämta information om körnings status för en roll instans i en moln tjänst.
```powershell
Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
 
Statuses           PlatformFaultDomain PlatformUpdateDomain
--------           ------------------- --------------------
{RoleStateStarting} 0                   0
```

* Azure Portal: gå till din moln tjänst och välj fliken roller och instanser. Klicka på roll instansen för att hämta statusinformation om tjänsten 
 
   :::image type="content" source="media/role-startup-failure-1.png" alt-text="Bild som visar att det inte gick att starta roller på portalen.":::
   
Här följer några vanliga problem och lösningar som är relaterade till Azure Cloud Services-roller (utökad support) som inte startar eller som går mellan initierings-, upptaget-och stopp tillstånd.

## <a name="missing-dlls-or-dependencies"></a>DLL-filer eller beroenden som saknas
Roller och roller som inte svarar mellan initiering, upptaget och stopp tillstånd kan orsakas av saknade DLL-filer eller sammansättningar.
Symtom på saknade DLL-filer eller sammansättningar kan vara:

* Din roll instans används för att **initiera**, **upptagen** och **stoppa** tillstånd.
* Din roll instans har flyttats till **klar** , men om du navigerar till ditt webb program visas inte sidan.

Det finns flera rekommenderade metoder för att undersöka de här problemen.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnostisera saknade DLL-problem i en webb roll
När du navigerar till en webbplats som har distribuerats i en webbroll och webbläsaren visar ett Server fel som liknar följande, kan det betyda att en DLL saknas.

:::image type="content" source="media/role-startup-failure-2.png" alt-text="Bild visar körnings fel vid roll start fel":::

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnostisera problem genom att stänga av anpassade fel
Mer fullständig fel information kan visas genom att konfigurera web.config för webb rollen för att ställa in det anpassade felläget för att stänga av och distribuera om tjänsten.
Så här visar du fullständiga fel utan att använda fjärr skrivbord:
1.  Öppna lösningen i Microsoft Visual Studio.
2.  Leta upp web.config-filen i Solution Explorer och öppna den.
3.  Leta upp avsnittet system. Web i web.config-filen och Lägg till följande rad:
 ```xml
<customErrors mode="Off" />
```
4.  Spara filen.
5.  Paketera om och distribuera om tjänsten.
När tjänsten har distribuerats om visas ett fel meddelande med namnet på den saknade sammansättningen eller DLL-filen.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnostisera problem genom att fjärrvisa felet
Du kan använda fjärr skrivbord för att få åtkomst till rollen och Visa mer fullständig fel information via en fjärr anslutning. Använd följande steg för att Visa felen med hjälp av fjärr skrivbord:
1.  Aktivera fjärr skrivbords tillägget för moln tjänsten (utökad support). Mer information finns i [tillämpa fjärr skrivbords tillägg för Cloud Services (utökad support) med Azure Portal](enable-rdp.md)
2.  När instansen i Azure Portal visar statusen klar, fjärranslutet till instansen. Mer information om hur du använder fjärr skrivbord med Cloud Services (utökad support) finns i [ansluta till roll instanser med fjärr skrivbord](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-rdp#connect-to-role-instances-with-remote-desktop-enabled)
3.  Logga in på den virtuella datorn med de autentiseringsuppgifter som angavs under konfigurationen av fjärr skrivbord.
4.  Öppna ett kommandofönster.
5.  Skriv IPconfig.
6.  Observera värdet för IPv4-adressen.
7.  Öppna Internet Explorer.
8.  Ange adress och namn för webb programmet. Till exempel http:// <IPV4 Address> /default.aspx.
Att navigera till webbplatsen kommer nu att returnera fler explicita fel meddelanden:
* Server fel i programmet '/'.
* Beskrivning: ett ohanterat undantag inträffade under körningen av den aktuella webb förfrågan. Läs stack spårningen om du vill ha mer information om felet och var det kom i koden.
* Undantags information: system. IO. FIleNotFoundException: det gick inte att läsa in filen eller sammansättningen "Microsoft. WindowsAzure. StorageClient, version = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35" eller något av dess beroenden. Det går inte att hitta den angivna filen.
Exempel:

  :::image type="content" source="media/role-startup-failure-3.png" alt-text="Bild som visar undantag vid roll start fel":::
  
## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnostisera problem med hjälp av beräknings-emulatorn
Du kan använda Azure Compute-emulatorn för att diagnostisera och felsöka problem med saknade beroenden och web.config fel.
För bästa resultat vid användning av den här diagnos metoden bör du använda en dator eller en virtuell dator som har en ren installation av Windows. 
1.  Installera [Azure SDK](https://azure.microsoft.com/downloads/) 
2.  Bygg Cloud Service-projektet på utvecklings datorn.
3.  I Utforskaren navigerar du till mappen bin\debug i Cloud Service-projektet.
4.  Kopiera. CSX-mappen och. cscfg-filen till den dator som du använder för att felsöka problemen.
5.  Öppna ett kommando tolks fönster för Azure SDK på den rena datorn och skriv csrun.exe/devstore: Start.
6.  I kommando tolken skriver du kör CSRun <sökväg till. CSX mapp> <sökväg till. cscfg-fil>/launchBrowser.
7.  När rollen startar visas detaljerad fel information i Internet Explorer. Du kan också använda vanliga fel söknings verktyg för Windows för att ytterligare diagnostisera problemet.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnostisera problem med hjälp av IntelliTrace
För arbets-och webb roller som använder .NET Framework 4 kan du använda [IntelliTrace](https://docs.microsoft.com/visualstudio/debugger/intellitrace), som finns i Microsoft Visual Studio Enterprise.
Följ de här stegen för att distribuera tjänsten med IntelliTrace aktiverat:
1.  Bekräfta att Azure SDK 1,3 eller senare är installerat.
2.  Distribuera lösningen med hjälp av Visual Studio. Under distributionen markerar du kryss rutan Aktivera IntelliTrace för .NET 4 roller.
3.  När instansen startar öppnar du Server Explorer.
4.  Expandera noden Azure\Cloud Services och leta upp distributionen.
5.  Expandera distributionen tills du ser roll instanserna. Högerklicka på en av instanserna.
6.  Välj Visa IntelliTrace-loggar. Sammanfattningen av IntelliTrace öppnas.
7.  Leta upp avsnittet undantag i sammanfattningen. Om det finns undantag får avsnittet en etikett med undantags data.
8.  Expandera undantags data och leta efter system. IO. FileNotFoundException-fel som liknar följande:

    :::image type="content" source="media/role-startup-failure-4.png" alt-text="Bild som visar undantags data vid roll start fel" lightbox="media/role-startup-failure-4.png":::

## <a name="address-missing-dlls-and-assemblies"></a>Adressera saknade DLL-filer och sammansättningar
Följ dessa steg om du vill åtgärda saknade DLL-och sammansättnings fel:
1.  Öppna lösningen i Visual Studio.
2.  Öppna mappen referenser i Solution Explorer.
3.  Klicka på den sammansättning som identifierades i fel meddelandet.
4.  I rutan Egenskaper letar du reda på Kopiera lokal egenskap och anger värdet till sant.
5.  Distribuera om moln tjänsten.
När du har kontrollerat att alla fel har åtgärd ATS kan du distribuera tjänsten utan att markera kryss rutan Aktivera IntelliTrace för .NET 4 roller.

## <a name="next-steps"></a>Nästa steg 
- Information om hur du felsöker problem med moln tjänst roller med hjälp av Azure PaaS Computer Diagnostics-data finns i [blogg serien för Kevin Williamson](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
