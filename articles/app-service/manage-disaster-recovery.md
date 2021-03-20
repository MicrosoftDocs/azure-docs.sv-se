---
title: Återställ från ett lands omfattande fel
description: Lär dig hur Azure App Service hjälper dig att underhålla funktioner för affärs kontinuitet och haveri beredskap (BCDR). Återställa din app från ett lands omfattande fel i Azure.
ms.topic: how-to
ms.date: 06/09/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1388dc11254324f74efcbaa55c97cac2ccd0c026
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87073739"
---
# <a name="move-an-app-service-app-to-another-region"></a>Flytta en App Service app till en annan region

I den här artikeln beskrivs hur du tar App Service-resurser online i en annan Azure-region under en katastrof som påverkar en hel Azure-region. När en katastrof tar en hel Azure-region offline, placeras alla App Service appar som finns i den regionen i haveri beredskaps läge. Funktionerna är tillgängliga för att hjälpa dig att återställa appen till en annan region eller återställa filer från den påverkade appen.

App Service-resurser är landsspecifika och kan inte flyttas mellan regioner. Du måste återställa appen till en ny app i en annan region och sedan skapa speglings konfiguration eller resurser för den nya appen.

## <a name="prerequisites"></a>Förutsättningar

- Inga. Att [återställa från ögonblicks bilder](app-service-web-restore-snapshots.md) kräver vanligt vis **Premium** -nivå, men i haveri beredskaps läge aktive ras den automatiskt för din påverkade app, oavsett vilken nivå den berörda appen är i.

## <a name="prepare"></a>Förbereda

Identifiera alla App Service resurser som den berörda appen använder för närvarande. Exempel:

- App Service-appar
- [App Service-planer](overview-hosting-plans.md)
- [Distributionsfack](deploy-staging-slots.md)
- [Anpassade domäner som köpts i Azure](manage-custom-dns-buy-domain.md)
- [SSL-certifikat](configure-ssl-certificate.md)
- [Azure Virtual Network-integrering](web-sites-integrate-with-vnet.md)
- [Hybrid anslutningar](app-service-hybrid-connections.md).
- [Hanterade identiteter](overview-managed-identity.md)
- [Inställningar för säkerhets kopiering](manage-backup.md)

Vissa resurser, t. ex. importerade certifikat eller hybrid anslutningar, innehåller integrering med andra Azure-tjänster. Information om hur du flyttar dessa resurser över flera regioner finns i dokumentationen för respektive tjänst.

## <a name="restore-app-to-a-different-region"></a>Återställ appen till en annan region

1. Skapa en ny App Service-app i en *annan* Azure-region än den påverkade appen. Det här är mål-appen i haveri beredskaps scenariot.

1. I [Azure Portal](https://portal.azure.com)navigerar du till sidan hantering av berörda appar. I en misslyckad Azure-region visar den påverkade appen en varnings text. Klicka på varnings texten.

    ![Skärm bild av sidan för den påverkade appen. Ett varnings meddelande visas som beskriver situationen och innehåller en länk till att återställa appen.](media/manage-disaster-recovery/restore-start.png)

1. På sidan **Återställ säkerhets kopia** konfigurerar du återställnings åtgärden enligt följande tabell. Klicka på **OK** när du är färdig.

   | Inställning | Värde | Beskrivning |
   |-|-|-|
   | **Ögonblicks bild (för hands version)** | Välj en ögonblicks bild. | De två senaste ögonblicks bilderna är tillgängliga. |
   | **Återställ mål** | **Befintlig app** | Klicka på kommentaren nedan om **du vill ändra appen för återställnings mål** och välja mål programmet. I ett katastrof scenario kan du bara återställa ögonblicks bilden till en app i en annan Azure-region. |
   | **Återställ plats konfiguration** | **Ja** | |

    ![Skärm bild av sidan Återställ säkerhets kopiering. En angiven ögonblicks bild, alternativen som föregående tabell listar och knappen OK är markerade.](media/manage-disaster-recovery/restore-configure.png)

3. Konfigurera [allt annat](#prepare) i mål programmet för att spegla den påverkade appen och verifiera konfigurationen.

4. När du är redo för den anpassade domänen att peka på mål programmet, [mappa om domän namnet](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

## <a name="recover-app-content-only"></a>Återställ endast app-innehåll

Om du bara vill återställa filerna från den påverkade appen utan att återställa den, så gör du följande:

1. I [Azure Portal](https://portal.azure.com)navigerar du till sidan hantering av påverkad app och klickar på **Hämta publicerings profil**.

    ![Skärm bild av sidan för den påverkade appen. Ett varnings meddelande visas men har inte marker ATS. Objektet Hämta publicerings profil markeras i stället.](media/manage-disaster-recovery/get-publish-profile.png)

1. Öppna den nedladdade filen och leta reda på den publicerings profil som innehåller `ReadOnly - FTP` i dess namn. Det här är en katastrof återställnings profil. Exempel:

    ```xml
    <publishProfile profileName="%app-name% - ReadOnly - FTP" publishMethod="FTP" publishUrl="ftp://%ftp-site%/site/wwwroot" ftpPassiveMode="True" userName="%app-name%\$%app-name%" userPWD="" destinationAppUrl="http://%app-name%.azurewebsites.net" SQLServerDBConnectionString="" mySQLDBConnectionString="" hostingProviderForumLink="" controlPanelLink="http://windows.azure.com" webSystem="WebSites">
        <databases />
    </publishProfile>
    ```
    
    Kopiera tre attributvärden: 
        
    - `publishUrl`: FTP-värdnamnet
    - `userName` och `userPWD` : FTP-autentiseringsuppgifterna

1. Använd valfri FTP-klient och Anslut till den berörda appens FTP-värd med hjälp av värdnamn och autentiseringsuppgifter.

1. När du har anslutit hämtar du hela */Site/wwwroot* -mappen. Följande skärm bild visar hur du hämtar i [FileZilla](https://filezilla-project.org/).

    ![Skärm bild av en FileZilla-filhierarki. Mappen wwwroot är markerad och dess snabb meny är synlig. I menyn är hämtningen markerad.](media/manage-disaster-recovery/download-content.png)

## <a name="next-steps"></a>Nästa steg
[Återställa en app i Azure från en ögonblicks bild](app-service-web-restore-snapshots.md)
