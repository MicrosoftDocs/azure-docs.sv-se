---
title: 'Azure Digitals dubblare-begäran misslyckades med status: 403 (tillåts inte)'
description: 'Det gick inte att utföra service förfrågningar. Status: 403 (ej tillåtet) på Azure Digital-dubbla.'
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: ad28b3300e9107c7f6d4b1987205583ef60ed658
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100545708"
---
# <a name="service-request-failed-status-403-forbidden"></a>Tjänstbegäran misslyckades. Status: 403 (ej tillåtet)

I den här artikeln beskrivs orsaker och lösnings steg för att ta emot ett 403-fel från tjänst begär anden till Azure Digitals. 

## <a name="symptoms"></a>Symtom

Det här felet kan inträffa på många typer av tjänst begär Anden som kräver autentisering. Detta är att API-begäran Miss lyckas, vilket returnerar fel statusen `403 (Forbidden)` .

## <a name="causes"></a>Orsaker

### <a name="cause-1"></a>Orsak #1

Oftast indikerar det här felet att din Azure-rollbaserade åtkomst kontroll (Azure RBAC)-behörigheter för tjänsten inte har ställts in korrekt. Många åtgärder för en digital Azure Digitals-instans kräver att du har rollen *Azure Digitals dubbla data ägare* **på den instans som du försöker hantera**. 

### <a name="cause-2"></a>Orsak #2

Om du använder en-app för att kommunicera med Azure Digitals, som autentiserar med en [registrerad app-registrering](how-to-create-app-registration.md), kan det här felet inträffa eftersom registreringen av appen inte har behörighet att konfigurera för Azure Digitals-tjänsten.

App-registreringen måste ha åtkomst behörighet som kon figurer ATS för Azure Digitals dubbla API: er. När klient programmet autentiserar mot appens registrering beviljas den de behörigheter som appens registrering har konfigurerat.

## <a name="solutions"></a>Lösningar

### <a name="solution-1"></a>Lösnings #1

Den första lösningen är att kontrol lera att din Azure-användare har rollen _**Azure Digitals data ägare**_ på den instans som du försöker hantera. Om du inte har den här rollen ställer du in den.

Observera att rollen skiljer sig från...
* det tidigare namnet för den här rollen under för hands versionen, *Azure Digitals-ägare (för hands version)* (rollen är densamma, men namnet har ändrats)
* *ägar* rollen för hela Azure-prenumerationen. *Azure Digitals sammanflätade data ägare* är en roll i Azure Digitals och är begränsad till den här enskilda Azure Digital-instansen.
* *ägar* rollen i Azure Digitals dubbla. Det här är två unika hanterings roller för Azure Digitals, och *data ägaren till Azure Digital* är den roll som ska användas för hantering.

#### <a name="check-current-setup"></a>Kontrol lera aktuell installation

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Åtgärda problem 

Om du inte har den här roll tilldelningen ska någon med en ägar roll i din **Azure-prenumeration** köra följande kommando för att ge din Azure-användare Azures *digitala data ägar* roll på **Azure Digitals-instansen**. 

Om du är ägare till prenumerationen kan du köra det här kommandot själv. Om du inte är det kan du kontakta en ägare för att köra det här kommandot åt dig.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Data Owner"
```

Mer information om det här roll kravet och tilldelnings processen finns i avsnittet [ *Konfigurera din användares åtkomst behörigheter*](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) i *instruktion: Konfigurera en instans och autentisering (CLI eller Portal)*.

Om du redan har den här roll tilldelningen *och* du använder en Azure AD-App-registrering för att autentisera en klient app, kan du fortsätta till nästa lösning om lösningen inte löste 403-problemet.

### <a name="solution-2"></a>Lösnings #2

Om du använder en Azure AD-App-registrering för att autentisera en klient app, är den andra möjliga lösningen att verifiera att appens registrering har behörigheter som har kon figurer ATS för tjänsten Azure Digitals dubbla. Om dessa inte är konfigurerade ställer du in dem.

#### <a name="check-current-setup"></a>Kontrol lera aktuell installation

Om du vill kontrol lera om behörigheterna har kon figurer ATS korrekt går du till [översikts sidan för Azure AD-appen registrering](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) i Azure Portal. Du kan komma åt den här sidan själv genom att söka efter *Appregistreringar* i portalens Sök fält.

Växla till fliken *alla program* för att se alla app-registreringar som har skapats i din prenumeration.

Du bör se den app-registrering som du nyss skapade i listan. Välj den för att öppna informationen.

:::image type="content" source="media/troubleshoot-error-403/app-registrations.png" alt-text="Appregistreringar sida i Azure Portal":::

Kontrol lera först att inställningarna för Azure Digitals-inställningar för digital har kon figurer ATS korrekt i registreringen. Det gör du genom att välja *manifest* från meny raden för att visa appens registrerings manifest kod. Bläddra till slutet av kod fönstret och leta efter dessa fält under `requiredResourceAccess` . Värdena ska matcha dem i skärm bilden nedan:

:::image type="content" source="media/troubleshoot-error-403/verify-manifest.png" alt-text="Portal visning av manifestet för Azure AD-appens registrering":::

Välj sedan *API-behörigheter* från meny raden för att kontrol lera att den här program registreringen innehåller Läs-/Skriv behörighet för Azure Digitals. Du bör se en post så här:

:::image type="content" source="media/troubleshoot-error-403/verify-api-permissions.png" alt-text="Portal visning av API-behörigheterna för Azure AD-appens registrering, med Läs-/skriv åtkomst för Azure digitala dubbla":::

#### <a name="fix-issues"></a>Åtgärda problem

Om något av detta visas på ett annat sätt än det som beskrivs, följer du anvisningarna om hur du konfigurerar en app-registrering i [*anvisningar: skapa en app-registrering*](how-to-create-app-registration.md).

## <a name="next-steps"></a>Nästa steg

Läs installations stegen för att skapa och autentisera en ny Azure Digital-instansen:
* [*Anvisningar: Konfigurera en instans och autentisering (CLI)*](how-to-set-up-instance-cli.md)

Läs mer om säkerhet och behörigheter på digitala Azure-objekt:
* [*Koncept: säkerhet för Azure Digitals dubbla lösningar*](concepts-security.md)
