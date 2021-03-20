---
title: Vanliga frågor om distribution – Azure App Service | Microsoft Docs
description: Få svar på vanliga frågor om distribution för den Web Apps funktionen i Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 163a6940e50d1f8beacc23855fd1e6f9daad0085
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88080481"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Vanliga frågor om distribution för Web Apps i Azure

Den här artikeln innehåller svar på vanliga frågor och svar om distributions problem för [Web Apps funktionen i Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Jag kommer precis att komma igång med App Service-webbappar. Hur gör jag för att publicera min kod?

Här följer några alternativ för att publicera din webb program kod:

*   Distribuera med hjälp av Visual Studio. Om du har Visual Studio-lösningen högerklickar du på projektet för webb programmet och väljer sedan **publicera**.
*   Distribuera med hjälp av en FTP-klient. I Azure Portal laddar du ned publicerings profilen för den webbapp som du vill distribuera din kod till. Ladda sedan upp filerna till \site\wwwroot med samma publicerings profil FTP-autentiseringsuppgifter.

Mer information finns i [distribuera din app till App Service](deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Jag får ett fel meddelande när jag försöker distribuera från Visual Studio. Hur gör jag för att lösa det här felet?

Om du ser följande meddelande kanske du använder en äldre version av SDK: "fel vid distribution för resursen" YourResourceName "i resurs gruppen" YourResourceGroup ": MissingRegistrationForLocation: prenumerationen har inte registrerats för resurs typens komponenter på platsen" Central US ". Registrera den här providern igen för att få åtkomst till den här platsen. " 

Lös problemet genom att uppgradera till den [senaste SDK: n](https://azure.microsoft.com/downloads/). Om du ser det här meddelandet och du har den senaste SDK: n skickar du en support förfrågan.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Hur gör jag för att du distribuera ett ASP.NET-program från Visual Studio till App Service?
<a id="deployasp"></a>

Självstudien [skapa din första ASP.NET-webbapp i Azure på fem minuter](quickstart-dotnetcore.md) visar hur du distribuerar ett ASP.NET-webbprogram till en webbapp i App Service med hjälp av Visual Studio.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Vilka är de olika typerna av distributions uppgifter?

App Service stöder två typer av autentiseringsuppgifter för lokal Git-distribution och FTP/S-distribution. Mer information om hur du konfigurerar autentiseringsuppgifter för distribution finns i [Konfigurera autentiseringsuppgifter för distribution för App Service](deploy-configure-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Vad är fil-eller katalog strukturen för min App Service-webbapp?

Information om fil strukturen för din App Service-app finns i [fil strukturen i Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Hur gör jag för att lösa "FTP-fel 550 – det finns inte tillräckligt med utrymme på disken" när jag försöker FTP-filer?

Om det här meddelandet visas är det troligt att du kör en diskkvot i tjänste planen för din webbapp. Du kan behöva skala upp till en högre tjänst nivå baserat på disk utrymmes behoven. Mer information om pris planer och resurs gränser finns [App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Hur gör jag för att du konfigurera kontinuerlig distribution för min App Service-webbapp?

Du kan ställa in kontinuerlig distribution från flera resurser, inklusive Azure DevOps, OneDrive, GitHub, BitBucket, Dropbox och andra git-databaser. Dessa alternativ är tillgängliga i portalen. [Kontinuerlig distribution till App Service](deploy-continuous-deployment.md) är en praktisk självstudie som förklarar hur du konfigurerar kontinuerlig distribution.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Hur gör jag för att felsöka problem med kontinuerlig distribution från GitHub och Bitbucket?

Information om hur du undersöker problem med kontinuerlig distribution från GitHub eller BitBucket finns i [undersöka kontinuerlig distribution](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>Jag kan inte FTP till min webbplats och publicera min kod. Hur gör jag för att du lösa det här problemet?

Så här löser du FTP-problem:

1. Kontrol lera att du har angett rätt värdnamn och autentiseringsuppgifter. Detaljerad information om olika typer av autentiseringsuppgifter och hur du använder dem finns i [distributions uppgifter](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Kontrol lera att FTP-portarna inte blockeras av en brand vägg. Portarna måste ha följande inställningar:
    * Anslutningsport för FTP-kontroll: 21
    * Anslutnings port för FTP-data: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Hur gör jag för att publicera min kod till App Service?

Azure snabb starten är utformad för att hjälpa dig att distribuera din app med hjälp av distributions stacken och den metod som du väljer. Om du vill använda Azure Portal snabb starten går du till App Service under **distribution** och väljer **snabb start**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Varför startar min app ibland om efter distributionen till App Service?

Om du vill veta mer om under vilka omständigheter en program distribution kan leda till en omstart, se [distribution jämfört med körnings problem](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). I artikeln beskrivs App Service distribuerar filer till mappen wwwroot. Appen startas aldrig om direkt.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Hur integrerar jag Azure DevOps-kod med App Service?

Du har två alternativ för att använda kontinuerlig distribution med Azure DevOps:

*   Använd ett git-projekt. Anslut via App Service med hjälp av distributions Center.
*   Använd ett TFVC-projekt (Team Foundation Version Control). Distribuera med hjälp av build-agenten för App Service.

Kontinuerlig kod distribution för båda dessa alternativ beror på befintliga arbets flöden för utvecklare och inchecknings procedurer. Mer information finns i de här artiklarna: 

*   [Implementera kontinuerlig distribution av din app till en Azure-webbplats](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Konfigurera en Azure DevOps-organisation så att den kan distribueras till en webbapp](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Hur gör jag för att använda FTP eller FTPS för att distribuera appen till App Service?

Information om hur du använder FTP eller FTPS för att distribuera webbappen till App Service finns i [distribuera din app för att app service med FTP/S](deploy-ftp.md).
