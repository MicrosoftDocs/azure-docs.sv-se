---
title: Revisioner i Azure API Management | Microsoft Docs
description: Lär dig mer om begreppet revisioner i Azure API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new, devx-track-azurepowershell
ms.openlocfilehash: bd837faaaa986659ad9b30aa3cf853ea490cec6d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812146"
---
# <a name="revisions-in-azure-api-management"></a>Revisioner i Azure API Management

Med revideringar kan du göra ändringar i dina API:er på ett kontrollerat och säkert sätt. När du vill göra ändringar skapar du en ny revision. Du kan sedan redigera och testa API:et utan att störa dina API-konsumenter. När du är klar gör du ändringen aktuell. Du kan också publicera en post i ändringsloggen för att hålla DINA API-användare uppdaterade med det som har ändrats. Ändringsloggen publiceras på utvecklarportalen.

> [!NOTE]
> Utvecklarportalen är inte tillgänglig på förbrukningsnivån.

Med revisioner kan du:

- Gör ändringar i API-definitionerna och principerna på ett säkert sätt utan att störa produktions-API:et.
- Prova ändringarna innan du publicerar dem.
- Dokumentera de ändringar du gör så att utvecklarna förstår vad som är nytt.
- Återställ om du hittar problem.

[Kom igång med revisioner genom att följa vår genomgång.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>Åtkomst till specifika revisioner

Varje revision av ditt API kan nås med hjälp av en särskilt utformad URL. Lägg till `;rev={revisionNumber}` i slutet av API-URL:en, men före frågesträngen, för att få åtkomst till en specifik revision av det API:et. Du kan till exempel använda den här URL:en för att få åtkomst till revision 3 av `customers` API:et:

`https://apis.contoso.com/customers;rev=3?customerId=123`

Som standard har varje revision samma säkerhetsinställningar som den aktuella revisionen. Du kan avsiktligt ändra principerna för en specifik revision om du vill tillämpa olika säkerhet för varje revision. Du kanske till exempel vill [](./api-management-access-restriction-policies.md#RestrictCallerIPs) lägga till en IP-filtreringsprincip för att förhindra att externa anropare kommer åt en revision som fortfarande är under utveckling.

En revision kan tas offline, vilket gör det otillgängligt för anropare även om de försöker komma åt revisionen via dess URL. Du kan markera en revision som offline med hjälp av Azure Portal. Om du använder PowerShell kan du använda `Set-AzApiManagementApiRevision` cmdleten och ange `Path` argumentet till `$null` .

> [!NOTE]
> Vi rekommenderar att du tar revisioner offline när du inte använder dem för testning.

## <a name="current-revision"></a>Aktuell revision

En enda revision kan anges som den *aktuella revisionen.* Den här revisionen är den som används för alla API-begäranden som inte anger ett explicit revisionsnummer i URL:en. Du kan återställa till en tidigare revision genom att ange den revisionen som aktuell.

Du kan ange en revision som aktuell med hjälp av Azure Portal. Om du använder PowerShell kan du använda `New-AzApiManagementApiRelease` cmdleten .

## <a name="revision-descriptions"></a>Revisionsbeskrivningar

När du skapar en revision kan du ange en beskrivning för dina egna spårningsändamål. Beskrivningar spelas inte upp för DINA API-användare.

När du ställer in en revision som aktuell kan du även ange en offentlig ändringslogganteckning om du vill. Ändringsloggen ingår i utvecklarportalen så att DINA API-användare kan visa den. Du kan ändra din ändringslogganteckning med hjälp av `Update-AzApiManagementApiRelease` PowerShell-cmdleten.

## <a name="versions-and-revisions"></a>Versioner och revisioner

Versioner och revisioner är distinkta funktioner. Varje version kan ha flera revisioner, precis som ett API som inte är versionat. Du kan använda revisioner utan att använda versioner eller tvärtom. Normalt används versioner för att separera API-versioner med icke-mindre ändringar, medan revisioner kan användas för mindre och icke-större ändringar i ett API.

Om du upptäcker att ändringen har större ändringar, eller om du formellt vill omvandla din revision till en beta-/testversion, kan du skapa en version från en revision. Med hjälp Azure Portal klickar du på Skapa version från revision på snabbmenyn revision på fliken Revisioner.
