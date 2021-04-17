---
title: Azure Digital Twins Explorer autentiseringsfel
description: Orsaker och lösningar för "Autentiseringen misslyckades". i Azure Digital Twins Explorer.
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/8/2021
ms.openlocfilehash: 1f8373130fbead2204dd0ac2515595d68dd3b2e8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495087"
---
# <a name="authentication-failed"></a>Autentiseringen misslyckades

Den här artikeln beskriver orsaker och lösningssteg för att ta emot felet "Autentiseringen misslyckades" [när du kör Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) exempel på den lokala datorn. 

## <a name="symptoms"></a>Symtom

När du inställningar och kör Azure Digital Twins Explorer-programmet uppfylls försök att autentisera med appen med följande felmeddelande:

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="Skärmbild av ett felmeddelande i Azure Digital Twins utforskaren med följande text:Autentiseringen misslyckades. Om du kör appen lokalt kontrollerar du att du är inloggad på Azure på värddatorn, eller genom att köra &quot;az login&quot; i en kommandotolk genom att logga in på Visual Studio eller VS Code eller genom att ange miljövariabler. Om du behöver mer information kan du läsa viktigt eller leta upp DefaultAzureCredential i dokumentationen för Azure.Identity. Om du kör adt-explorer som finns i molnet kontrollerar du att din värdbaserade Azure-funktion har en system tilldelad hanterad identitet konfigurerad. Mer information finns i readme.":::

## <a name="causes"></a>Orsaker

### <a name="cause-1"></a>Orsak #1

Programmet Azure Digital Twins Explorer använder [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (en del av biblioteket), som söker efter autentiseringsuppgifter `Azure.Identity` i din lokala miljö.

När feltexten visas kan det här felet inträffa om du inte har angett lokala autentiseringsuppgifter för `DefaultAzureCredential` att hämta upp.

Mer information om hur du använder lokala autentiseringsuppgifter med Azure Digital Twins Explorer finns i avsnittet Konfigurera lokala [*Azure-autentiseringsuppgifter*](quickstart-adt-explorer.md#set-up-local-azure-credentials) i Azure Digital Twins *snabbstart: Utforska ett exempelscenario*.

### <a name="cause-2"></a>Orsak #2

Det här felet kan också inträffa om ditt Azure-konto inte har de azure-behörigheter för rollbaserad åtkomst (Azure RBAC) som krävs för din Azure Digital Twins instans. För att komma åt data i din instans måste du ha **rollen Azure Digital Twins Data Reader** eller Azure Digital Twins Data **Owner** på den instans som du försöker läsa eller hantera. 

Mer information om säkerhet och roller i Azure Digital Twins finns i [*Begrepp: Säkerhet för Azure Digital Twins lösningar*](concepts-security.md).

## <a name="solutions"></a>Lösningar

### <a name="solution-1"></a>Lösning #1

Kontrollera först att du har angett nödvändiga autentiseringsuppgifter för programmet.

#### <a name="provide-local-credentials"></a>Ange lokala autentiseringsuppgifter

`DefaultAzureCredential` autentiserar till tjänsten med hjälp av informationen från en lokal Azure-inloggning. Du kan ange dina Azure-autentiseringsuppgifter genom att logga in på ditt Azure-konto i ett lokalt [Azure CLI-fönster](/cli/azure/install-azure-cli) eller i Visual Studio eller Visual Studio Code.

Du kan visa de typer av autentiseringsuppgifter som godkänner, samt i vilken ordning de görs, i Azure Identity-dokumentationen för `DefaultAzureCredential` [DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential).

Om du redan är inloggad lokalt på rätt Azure-konto och problemet inte är löst fortsätter du till nästa lösning.

### <a name="solution-2"></a>Lösning #2

Kontrollera att din Azure-användare har **rollen Azure Digital Twins-dataläsare** på Azure Digital Twins-instansen om du bara försöker läsa dess data eller **rollen Azure Digital Twins-dataägare** på instansen om du försöker hantera dess data.

Observera att den här rollen skiljer sig från...
* det tidigare namnet på den här rollen *under förhandsversionen, Azure Digital Twins ägare (förhandsversion) (rollen* är densamma, men namnet har ändrats)
* rollen *Ägare* för hela Azure-prenumerationen. *Azure Digital Twins dataägare är* en roll inom Azure Digital Twins och är begränsad till den här enskilda Azure Digital Twins instansen.
* rollen *Ägare* i Azure Digital Twins. Det här är två Azure Digital Twins för *hanteringsroller och Azure Digital Twins dataägare* är den roll som ska användas för hantering.

 Om du inte har den här rollen kan du konfigurera den för att lösa problemet.

#### <a name="check-current-setup"></a>Kontrollera den aktuella konfigurationen

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Åtgärda problem 

Om du inte har den här rolltilldelningen bör någon med rollen Ägare i din **Azure-prenumeration** köra följande kommando för att ge Azure-användaren lämplig roll på **Azure Digital Twins instansen**. 

Om du är ägare till prenumerationen kan du köra det här kommandot själv. Om du inte är det kontaktar du en ägare för att köra det här kommandot för din räkning. Rollnamnet är antingen **Azure Digital Twins dataägare för** redigeringsåtkomst eller **Azure Digital Twins dataläsare för** läsbehörighet.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "<role-name>"
```

Mer information om det här rollkravet och [](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) tilldelningsprocessen finns i avsnittet Konfigurera användarens åtkomstbehörigheter i Konfigurera en instans och autentisering *(CLI eller portal).*

## <a name="next-steps"></a>Nästa steg

Läs installationsstegen för att skapa och autentisera en ny Azure Digital Twins instans:
* [*Gör så här: Konfigurera en instans och autentisering (CLI)*](how-to-set-up-instance-cli.md)

Läs mer om säkerhet och behörigheter för Azure Digital Twins:
* [*Koncept: Säkerhet för Azure Digital Twins lösningar*](concepts-security.md)
