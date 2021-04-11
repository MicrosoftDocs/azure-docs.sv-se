---
title: Inbyggda roller Windows Virtual Desktop – Azure
description: En översikt över inbyggda roller för virtuella Windows-datorer som är tillgängliga för Azure RBAC.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: a1094004bc523a59c3fcf7eb77c6f11bc4554080
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445864"
---
# <a name="built-in-roles-for-windows-virtual-desktop"></a>Inbyggda roller för virtuella Windows-datorer

Windows Virtual Desktop använder Azures rollbaserade åtkomst kontroller (RBAC) för att tilldela roller till användare och administratörer. Dessa roller ger administratörer behörighet att utföra vissa uppgifter. Mer information om inbyggda roller för Azure RBAC finns i [inbyggda roller i Azure](../role-based-access-control/built-in-roles.md).

De inbyggda standard rollerna för Azure är ägare, deltagare och läsare. Windows Virtual Desktop har dock ytterligare roller som gör det möjligt att separera hanterings roller för värdar, appar och arbets ytor. Den här separationen gör att du kan ha mer detaljerad kontroll över administrativa uppgifter. De här rollerna namnges i enlighet med Azures standard roller och metoder med minsta behörighet.

Den virtuella Windows-skrivbordet har ingen angiven ägar roll. Du kan dock använda en standard ägar roll för tjänst objekt.

## <a name="desktop-virtualization-contributor"></a>Deltagare i Skriv bords virtualisering

Med rollen Virtualiseringsvärd för skriv bord kan du hantera alla aspekter av distributionen. Det ger dock inte åtkomst till beräknings resurser. Du behöver också rollen administratör för användar åtkomst för att publicera program grupper till användare eller användar grupper.


- Microsoft. DesktopVirtualization/\* 
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/Deployments/\*
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="desktop-virtualization-reader"></a>Läsare för Skriv bords virtualisering

Med rollen Virtualiseringsvärd för skriv bord kan du Visa allt i distributionen men du kan inte göra några ändringar.

- Microsoft. DesktopVirtualization/ \* /Read
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/Deployments/Read
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="host-pool-contributor"></a>Värd bassängs deltagare

Med rollen Host pool Contributor kan du hantera alla aspekter av värdbaserade pooler, inklusive åtkomst till resurser. Du behöver en extra deltagar roll, virtuell dator deltagare, för att skapa virtuella datorer. Du behöver AppGroup-och Workspace-deltagar roller för att skapa en adresspool med hjälp av portalen, eller så kan du använda rollen Virtualiseringsvärd för Station ära datorer.

I följande lista beskrivs vilka behörigheter den här rollen har åtkomst till:

- Microsoft. DesktopVirtualization/hostpools/\*
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/Deployments/\*
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="host-pool-reader"></a>Läsare för värd bassäng

Med rollen som värd bassängs läsare kan du Visa allt i poolen, men du kan inte göra några ändringar.

- Microsoft. DesktopVirtualization/hostpools/ \* /Read
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/Deployments/Read
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="application-group-contributor"></a>Program grupps deltagare

Med rollen program grupps deltagare kan du hantera alla aspekter av app-grupper. Om du vill publicera program grupper till användare eller användar grupper behöver du rollen administratör för användar åtkomst.

I följande lista beskrivs vilka behörigheter den här rollen har åtkomst till:

- Microsoft. DesktopVirtualization/applicationgroups/\*
- Microsoft. DesktopVirtualization/hostpools/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/Read
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/Deployments/\*
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="application-group-reader"></a>Program grupps läsare

Med rollen program grupp läsare kan du Visa allt i app-gruppen och du kan inte göra några ändringar.

I följande lista beskrivs vilka behörigheter den här rollen har åtkomst till:

- Microsoft. DesktopVirtualization/applicationgroups/ \* /Read
- Microsoft. DesktopVirtualization/applicationgroups/Read
- Microsoft. DesktopVirtualization/hostpools/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/Read
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/Deployments/Read
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="workspace-contributor"></a>Arbets ytans deltagare

Med rollen arbets ytans deltagare kan du hantera alla aspekter av arbets ytor. För att få information om program som har lagts till i app-grupperna, måste du också tilldelas rollen som program grupps läsare.

I följande lista beskrivs vilka behörigheter den här rollen har åtkomst till:

- Microsoft. DesktopVirtualization/arbets ytor/\*
- Microsoft. DesktopVirtualization/applicationgroups/Read
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/Deployments/\*
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="workspace-reader"></a>Arbets ytans läsare

Med rollen arbets ytans läsare kan du Visa allt i arbets ytan, men du kan inte göra några ändringar.

I följande lista beskrivs vilka behörigheter den här rollen har åtkomst till:

- Microsoft. DesktopVirtualization/arbets ytor/läsa
- Microsoft. DesktopVirtualization/applicationgroups/Read
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/Deployments/Read
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="user-session-operator"></a>Användarens sessions operator

Med rollen användarens session operatör kan du skicka meddelanden, koppla från sessioner och använda funktionen "LOGOFF" för att signera sessioner från sessions värden. Men den här rollen låter dig inte utföra hantering av sessionshantering som att ta bort värd, ändra dränerings läge och så vidare. Den här rollen kan se tilldelningar, men kan inte ändra administratörer. Vi rekommenderar att du tilldelar den här rollen till vissa värdar för pooler. Om du ger den här behörigheten på resurs grupps nivå har administratören Läs behörighet för alla lagringspooler under en resurs grupp.

I följande lista beskrivs vilka behörigheter den här rollen har åtkomst till:

- Microsoft. DesktopVirtualization/hostpools/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/usersessions/\*
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/Deployments/Read
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="session-host-operator"></a>Session Host-operatör

Med rollen Session Host-operatör kan du Visa och ta bort värdar, samt ändra dränerings läge. De kan inte lägga till sessionsnycklar med Azure Portal eftersom de inte har Skriv behörighet för objekt i värd pooler. Om registrerings-token är giltig (genererad och inte upphör att gälla) kan du använda den här rollen för att lägga till värddatorer i poolen utanför Azure Portal om administratören har beräknings behörigheter via rollen virtuell dator deltagare.

I följande lista beskrivs vilka behörigheter den här rollen har åtkomst till:

- Microsoft. DesktopVirtualization/hostpools/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/\*
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/Deployments/Read
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*
