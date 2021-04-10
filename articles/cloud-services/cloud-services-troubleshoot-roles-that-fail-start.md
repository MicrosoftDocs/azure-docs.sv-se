---
title: Felsöka roller som inte startar | Microsoft Docs
description: Här följer några vanliga orsaker till varför en moln tjänst roll inte kan starta. Det finns också lösningar på de här problemen.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2453fa2d9b4e78b60d4922e09347799266a84cff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743227"
---
# <a name="troubleshoot-azure-cloud-service-classic-roles-that-fail-to-start"></a>Felsöka Azure Cloud Service-roller (klassiska) som inte startar

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Här följer några vanliga problem och lösningar som rör Azure Cloud Services roller som inte startar.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>DLL-filer eller beroenden som saknas
Roller och roller som inte svarar mellan **initiering**, **upptaget** och **stopp** tillstånd kan orsakas av saknade DLL-filer eller sammansättningar.

Symtom på saknade DLL-filer eller sammansättningar kan vara:

* Din roll instans används för att **initiera**, **upptagen** och **stoppa** tillstånd.
* Din roll instans har flyttats till **klar** , men om du navigerar till ditt webb program visas inte sidan.

Det finns flera rekommenderade metoder för att undersöka de här problemen.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnostisera saknade DLL-problem i en webb roll
När du navigerar till en webbplats som har distribuerats i en webbroll och webbläsaren visar ett Server fel som liknar följande, kan det betyda att en DLL saknas.

![Server fel i programmet '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnostisera problem genom att stänga av anpassade fel
Mer fullständig fel information kan visas genom att konfigurera web.config för webb rollen för att ställa in det anpassade felläget för att stänga av och distribuera om tjänsten.

Så här visar du fullständiga fel utan att använda fjärr skrivbord:

1. Öppna lösningen i Microsoft Visual Studio.
2. Leta upp web.config-filen i **Solution Explorer** och öppna den.
3. Leta upp avsnittet system. Web i web.config-filen och Lägg till följande rad:

    ```xml
    <customErrors mode="Off" />
    ```
4. Spara filen.
5. Paketera om och distribuera om tjänsten.

När tjänsten har distribuerats om visas ett fel meddelande med namnet på den saknade sammansättningen eller DLL-filen.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnostisera problem genom att fjärrvisa felet
Du kan använda fjärr skrivbord för att få åtkomst till rollen och Visa mer fullständig fel information via en fjärr anslutning. Använd följande steg för att Visa felen med hjälp av fjärr skrivbord:

1. Se till att Azure SDK 1,3 eller senare är installerat.
2. Aktivera fjärr skrivbord under distributionen av lösningen med hjälp av Visual Studio. Mer information finns i [aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services med Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. När instansen i Microsoft Azure-portalen visar statusen **klar**, fjärran slut till instansen. Mer information om hur du använder fjärr skrivbord med Cloud Services finns i [fjärran slut till roll instanser](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Logga in på den virtuella datorn med de autentiseringsuppgifter som angavs under konfigurationen av fjärr skrivbord.
6. Öppna ett kommandofönster.
7. Skriv `IPconfig`.
8. Observera värdet för IPV4-adressen.
9. Öppna Internet Explorer.
10. Ange adress och namn för webb programmet. Till exempel `http://<IPV4 Address>/default.aspx`.

Att navigera till webbplatsen kommer nu att returnera fler explicita fel meddelanden:

* Server fel i programmet '/'.
* Beskrivning: ett ohanterat undantag inträffade under körningen av den aktuella webb förfrågan. Läs stack spårningen om du vill ha mer information om felet och var det kom i koden.
* Undantags information: system. IO. FIleNotFoundException: det gick inte att läsa in filen eller sammansättningen "Microsoft. WindowsAzure. StorageClient, version = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35" eller något av dess beroenden. Det går inte att hitta den angivna filen.

Exempel:

![Explicit Server fel i programmet '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnostisera problem med hjälp av beräknings-emulatorn
Du kan använda Microsoft Azure Compute-emulatorn för att diagnostisera och felsöka problem med saknade beroenden och web.config fel.

För bästa resultat vid användning av den här diagnos metoden bör du använda en dator eller en virtuell dator som har en ren installation av Windows. För att simulera Azure-miljön bör du använda Windows Server 2008 R2 x64.

1. Installera den fristående versionen av [Azure SDK](https://azure.microsoft.com/downloads/).
2. Bygg Cloud Service-projektet på utvecklings datorn.
3. I Utforskaren navigerar du till mappen bin\debug i Cloud Service-projektet.
4. Kopiera. CSX-mappen och. cscfg-filen till den dator som du använder för att felsöka problemen.
5. Öppna ett kommando tolks fönster för Azure SDK på den rena datorn och skriv `csrun.exe /devstore:start` .
6. Skriv i kommando tolken `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser` .
7. När rollen startar visas detaljerad fel information i Internet Explorer. Du kan också använda vanliga fel söknings verktyg för Windows för att ytterligare diagnostisera problemet.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnostisera problem med hjälp av IntelliTrace
För arbets-och webb roller som använder .NET Framework 4 kan du använda [IntelliTrace](/visualstudio/debugger/intellitrace), som finns i Microsoft Visual Studio Enterprise.

Följ de här stegen för att distribuera tjänsten med IntelliTrace aktiverat:

1. Bekräfta att Azure SDK 1,3 eller senare är installerat.
2. Distribuera lösningen med hjälp av Visual Studio. Under distributionen markerar du kryss rutan **Aktivera IntelliTrace för .NET 4 roller** .
3. När instansen startar öppnar du **Server Explorer**.
4. Expandera **Azure \\ Cloud Services** -noden och leta upp distributionen.
5. Expandera distributionen tills du ser roll instanserna. Högerklicka på en av instanserna.
6. Välj **Visa IntelliTrace-loggar**. **Sammanfattningen av IntelliTrace** öppnas.
7. Leta upp avsnittet undantag i sammanfattningen. Om det finns undantag får avsnittet en etikett med **undantags data**.
8. Expandera **undantags data** och leta efter **system. io. FileNotFoundException** -fel som liknar följande:

![Undantags data, saknad fil eller sammansättning](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Adressera saknade DLL-filer och sammansättningar
Följ dessa steg om du vill åtgärda saknade DLL-och sammansättnings fel:

1. Öppna lösningen i Visual Studio.
2. Öppna mappen **referenser** i **Solution Explorer**.
3. Klicka på den sammansättning som identifierades i fel meddelandet.
4. I rutan **Egenskaper** letar du reda på **Kopiera lokal egenskap** och anger värdet till **Sant**.
5. Distribuera om moln tjänsten.

När du har kontrollerat att alla fel har åtgärd ATS kan du distribuera tjänsten utan att markera kryss rutan **Aktivera IntelliTrace för .NET 4 roller** .

## <a name="next-steps"></a>Nästa steg
Visa fler [fel söknings artiklar](../index.yml?product=cloud-services&tag=top-support-issue) för Cloud Services.

Information om hur du felsöker problem med moln tjänst roller med hjälp av Azure PaaS Computer Diagnostics-data finns i [blogg serien för Kevin Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
