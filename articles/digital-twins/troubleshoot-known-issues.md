---
title: Kända problem – Azure Digitals, dubbla
description: Få hjälp med att identifiera och åtgärda kända problem med Azure Digitals.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 641b44a5e21e6646c07e6e1511e1c4ff01707f79
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434108"
---
# <a name="known-issues-in-azure-digital-twins"></a>Kända problem i Azure Digitals, dubbla

Den här artikeln innehåller information om kända problem som är associerade med Azure Digitals.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400-klient fel: felaktig begäran" i Cloud Shell

Problembeskrivning **:** Kommandon i Cloud Shell som körs på *https://shell.azure.com* kan tillfälligt Miss lyckas med felet "400-klient fel: felaktig begäran för URL: http://localhost:50342/oauth2/token ", följt av fullständig stack spårning.

| Påverkar detta mig mig? | Orsak | Lösning |
| --- | --- | --- |
| I &nbsp; Azure &nbsp; Digitals &nbsp; , påverkar detta följande kommando grupper:<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | Detta är resultatet av ett känt problem i Cloud Shell: [*hämtning av token från Cloud Shell Miss lyckas tillfälligt med 400-klient fel: felaktig begäran*](https://github.com/Azure/azure-cli/issues/11749).<br><br>Detta ger ett problem med Azure Digitals-token Authentication auth-token och Cloud Shell standard- [hanterad identitets](../active-directory/managed-identities-azure-resources/overview.md) baserad autentisering. <br><br>Detta påverkar inte Azure Digitals dubbla kommandon från `az dt` `az dt endpoint` kommando grupperna eller, eftersom de använder en annan typ av autentiseringstoken (baserat på Azure Resource Manager), som inte har ett problem med Cloud Shells hanterade identitetsautentisering. | Ett sätt att lösa detta är att köra `az login` kommandot igen i Cloud Shell och slutföra de efterföljande inloggnings stegen. Detta växlar sessionen från hanterad identitetsautentisering, vilket undviker rot problemet. Därefter ska du kunna köra kommandot igen.<br><br>Du kan också öppna fönstret Cloud Shell i Azure Portal och slutföra din Cloud Shell arbete därifrån.<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Bild av ikonen Cloud Shell i Azure Portal ikon fältet" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>Slutligen är en annan lösning att [Installera Azure CLI](/cli/azure/install-azure-cli) på datorn så att du kan köra Azure CLI-kommandon lokalt. Den lokala CLI: en drabbas inte av det här problemet. |


## <a name="missing-role-assignment-after-scripted-setup"></a>Roll tilldelning saknas efter skriptad installation

Problembeskrivning **:** Vissa användare kan uppleva problem med roll tilldelnings delen i [*anvisningar: Konfigurera en instans och autentisering (skript)*](how-to-set-up-instance-scripted.md). Skriptet indikerar inte fel, men den *digitala data ägar* rollen för Azure har inte tilldelats till användaren, och det här problemet påverkar möjligheten att skapa andra resurser.

| Påverkar detta mig mig? | Orsak | Lösning |
| --- | --- | --- |
| Följ anvisningarna i avsnittet [*Verifiera användar roll tilldelning*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) i installations artikeln för att avgöra om din roll tilldelning har kon figurer ATS när skriptet har körts. Om användaren inte visas med den här rollen påverkar det här problemet. | För användare som är inloggade med en personlig [Microsoft-konto (MSA)](https://account.microsoft.com/account)kan användarens huvud-ID som identifierar dig i kommandon som det kan skilja sig från din användares inloggnings-e-post, vilket gör det svårt för skriptet att identifiera och använda för att tilldela rollen korrekt. | För att lösa problemet kan du konfigurera roll tilldelningen manuellt med hjälp av [CLI-instruktionerna](how-to-set-up-instance-cli.md#set-up-user-access-permissions) eller [Azure Portal instruktioner](how-to-set-up-instance-portal.md#set-up-user-access-permissions). |

## <a name="issue-with-interactive-browser-authentication-on-azureidentity-120"></a>Problem med interaktiv Browser-autentisering på Azure. Identity 1.2.0

Problembeskrivning **:** När du skriver authentication code i dina Azure Digital-program med version **1.2.0** av **[Azure. Identity](/dotnet/api/azure.identity) -biblioteket** kan det uppstå problem med [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) -metoden. Detta visas som ett felsvar på "Azure. Identity. AuthenticationFailedException" när du försöker autentisera i ett webbläsarfönster. Det kan hända att det inte går att starta webbläsarfönstret helt eller visas för att autentisera användaren, medan klient programmet fortfarande Miss lyckas med felet.

| Påverkar detta mig mig? | Orsak | Lösning |
| --- | --- | --- |
| Den &nbsp; berörda &nbsp; metoden &nbsp; &nbsp; används &nbsp; i &nbsp; &nbsp; följande artiklar:<br><br>[*Självstudie: koda en klient app*](tutorial-code.md)<br><br>[*Instruktion: skriva kod för app-autentisering*](how-to-authenticate-client.md)<br><br>[*Anvisningar: använda Azures digitala dubbla API: er och SDK: er*](how-to-use-apis-sdks.md) | Vissa användare har haft det här problemet med version **1.2.0** av `Azure.Identity` biblioteket. | Du löser problemet genom att uppdatera dina program så att de använder en [senare version](https://www.nuget.org/packages/Azure.Identity) av `Azure.Identity` . När du har uppdaterat biblioteks versionen bör webbläsaren läsa in och autentiseras som förväntat. |

## <a name="issue-with-default-azure-credential-authentication-on-azureidentity-130"></a>Problem med Azure Credential Authentication-autentisering på Azure. Identity 1.3.0

Problembeskrivning **:** När du skriver en autentiseringsmetod med **1.3.0** av **[Azure. Identity](/dotnet/api/azure.identity) -biblioteket**, har vissa användare drabbats av problem med [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) -metoden som används i många exempel i de här Azure Digitals-dokumenten. Detta visas som ett felsvar på "Azure. Identity. AuthenticationFailedException: SharedTokenCacheCredential-Autentiseringen misslyckades" när koden försöker autentisera sig.

| Påverkar detta mig mig? | Orsak | Lösning |
| --- | --- | --- |
| `DefaultAzureCredential` används i de flesta dokumentations exemplen för den här tjänsten som inkluderar autentisering. Om du skriver autentiserings kod med `DefaultAzureCredential` version 1.3.0 av `Azure.Identity` biblioteket och ser det här fel meddelandet, påverkar detta detta. | Detta beror troligen på vissa konfigurations problem med `Azure.Identity` . | En strategi för att lösa detta är att undanta `SharedTokenCacheCredential` från dina autentiseringsuppgifter, enligt beskrivningen i det här [DefaultAzureCredential-problemet](https://github.com/Azure/azure-sdk/issues/1970) som för närvarande är öppet för tillfället `Azure.Identity` .<br>Ett annat alternativ är att ändra ditt program till att använda en tidigare version av `Azure.Identity` , till exempel [version 1.2.3](https://www.nuget.org/packages/Azure.Identity/1.2.3). Detta har ingen funktionell påverkan på Azures digitala delar och är därför också en godkänd lösning. |

## <a name="next-steps"></a>Nästa steg

Läs mer om säkerhet och behörigheter på digitala Azure-objekt:
* [*Koncept: säkerhet för Azure Digitals dubbla lösningar*](concepts-security.md)