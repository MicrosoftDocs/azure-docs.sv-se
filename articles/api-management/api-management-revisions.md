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
ms.custom: fasttrack-new
ms.openlocfilehash: 9a20a7966daff372cf5c0abc9b7b1dbbfd459838
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91403246"
---
# <a name="revisions-in-azure-api-management"></a>Revisioner i Azure API Management

Med hjälp av revisioner kan du göra ändringar i dina API: er på ett kontrollerat och säkert sätt. Skapa en ny revision när du vill göra ändringar. Du kan sedan redigera och testa API utan att störa dina API-konsumenter. När du är klar gör du ändringen aktuell. Samtidigt kan du välja att skicka en post till ändrings loggen för att hålla dina API-konsumenter uppdaterade med vad som har ändrats. Ändrings loggen publiceras i Developer-portalen.

> [!NOTE]
> Developer-portalen är inte tillgänglig i förbruknings nivån.

Med revideringar kan du:

- Gör en säker ändring av API-definitionerna och principerna, utan att störa ditt produktions-API.
- Prova ändringar innan du publicerar dem.
- Dokumentera de ändringar du gör så att dina utvecklare kan förstå vad som är nytt.
- Återställ igen om du hittar problem.

[Kom igång med revisioner genom att följa vår genom gång.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>Åtkomst till vissa revisioner

Varje revidering av ditt API kan nås med hjälp av en särskilt utformad URL. Lägg till `;rev={revisionNumber}` i slutet av din API-URL, men före frågesträngen för att få åtkomst till en viss revision av detta API. Du kan till exempel använda den här URL: en för att komma åt revision 3 av `customers` API: et:

`https://apis.contoso.com/customers;rev=3?customerId=123`

Som standard har varje revision samma säkerhets inställningar som den aktuella revisionen. Du kan avsiktligt ändra principerna för en särskild revision om du vill att en annan säkerhet ska tillämpas för varje revision. Du kanske till exempel vill lägga till en [IP-filtrerings princip](./api-management-access-restriction-policies.md#RestrictCallerIPs) för att förhindra att externa anropare får åtkomst till en revision som fortfarande är under utveckling.

En revision kan kopplas från, vilket gör den oåtkomlig för anropare även om de försöker få åtkomst till den via dess URL. Du kan markera en revision som offline med hjälp av Azure Portal. Om du använder PowerShell kan du använda `Set-AzApiManagementApiRevision` cmdleten och ange `Path` argumentet till `$null` .

> [!NOTE]
> Vi rekommenderar att du börjar med att göra ändringar offline när du inte använder dem för testning.

## <a name="current-revision"></a>Aktuell revision

En enda revision kan anges som den *aktuella* revisionen. Den här ändringen är den som används för alla API-begäranden som inte anger ett explicit revisions nummer i URL: en. Du kan återställa till en tidigare revision genom att ställa in ändringen som aktuell.

Du kan ställa in en revidering som aktuell med hjälp av Azure Portal. Om du använder PowerShell kan du använda `New-AzApiManagementApiRelease` cmdleten.

## <a name="revision-descriptions"></a>Reviderings beskrivningar

När du skapar en revision kan du ange en beskrivning för ditt eget spårnings syfte. Beskrivningar spelas inte till dina API-användare.

När du ställer in en revidering som aktuell kan du också välja att ange en offentlig ändrings logg anteckning. Ändrings loggen ingår i Developer-portalen för att dina API-användare ska kunna se den. Du kan ändra din ändrings logg anteckning med `Update-AzApiManagementApiRelease` PowerShell-cmdleten.

## <a name="versions-and-revisions"></a>Versioner och revideringar

Versioner och revideringar är distinkta funktioner. Varje version kan ha flera revisioner, precis som ett icke-versions-API. Du kan använda revideringar utan att använda versioner, eller tvärtom. Normalt används versioner för att separera API-versioner med större ändringar, medan revisioner kan användas för mindre och icke-brytande ändringar i ett API.

Om du upptäcker att din ändring har fått större ändringar, eller om du vill förvandla din revision till en beta-/test version, kan du skapa en version från en revidering. Använd Azure Portal och klicka på "Skapa version från revision" på snabb menyn för omarbetning på fliken revisioner.
