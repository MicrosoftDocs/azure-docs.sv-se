---
title: 'Azure Digital Twins misslyckades med Status: 404 Sub-Domain hittades inte'
description: 'Orsaker och lösningar för "Tjänstbegäran misslyckades. Status: 404 Sub-Domain kunde inte hittas" på Azure Digital Twins.'
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/13/2021
ms.openlocfilehash: e3fe3ad22098d796faa309ff3509c318a7e1df4d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590286"
---
# <a name="service-request-failed-status-404-sub-domain-not-found"></a>Tjänstbegäran misslyckades. Status: 404 Sub-Domain hittades inte

Den här artikeln beskriver orsaker och lösningssteg för att ta emot ett 404-fel från tjänstbegäranden till Azure Digital Twins. 

## <a name="symptoms"></a>Symtom

Det här felet kan inträffa vid åtkomst till en Azure Digital Twins-instans med hjälp av ett huvudnamn för tjänsten eller ett användarkonto som tillhör en [annan Azure Active Directory-klientorganisation (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md) från instansen. Rätt roller [verkar](concepts-security.md) vara tilldelade till identiteten, men API-begäranden misslyckas med felstatusen `404 Sub-Domain not found` .

## <a name="causes"></a>Orsaker

### <a name="cause-1"></a>Orsak #1

Azure Digital Twins kräver att alla autentiserande användare tillhör samma Azure AD-klientorganisation som Azure Digital Twins instansen.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

## <a name="solutions"></a>Lösningar

### <a name="solution-1"></a>Lösning #1

Du kan lösa det här problemet genom att be varje federerad identitet från en annan klientorganisation att begära en **token** från Azure Digital Twins-instansens "hemklient". 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

### <a name="solution-2"></a>Lösning #2

Om du använder klassen i koden och du fortsätter att stöta på det här problemet när du har fått en token kan du ange hemklienten i alternativen för att klargöra klienten även när autentiseringen har en annan typ `DefaultAzureCredential` `DefaultAzureCredential` som standard.

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="next-steps"></a>Nästa steg

Läs mer om säkerhet och behörigheter för Azure Digital Twins:
* [*Koncept: Säkerhet för Azure Digital Twins lösningar*](concepts-security.md)
