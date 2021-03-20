---
title: Funktioner för begäran om kund data för Azure Digitals, dubbla
titleSuffix: Azure Digital Twins
description: Den här artikeln visar processer för att exportera och ta bort personliga data i Azure Digitals.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: digital-twins
services: digital-twins
ms.openlocfilehash: 9942056e59a46c61b4e9da32aab191d4b72db968
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92461767"
---
# <a name="azure-digital-twins-customer-data-request-features"></a>Azure Digitals dubbla funktioner för förfrågan om kund information

Azure Digital-dubbla är en utvecklings plattform för att skapa säkra digitala representationer av en affärs miljö. Representationer drivs av Live State-data från data källor som valts av användare.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Digitala representationer som kallas *digitala dubbla* , i Azure Digitals, representerar entiteter i verkliga miljöer och är associerade med identifierare. Microsoft har ingen information och har ingen åtkomst till data som tillåter att identifierare korreleras till användarna. 

Många av de digitala dubblarna i Azures digitala dubbla är inte direkt representerade personliga entiteter – typiska objekt som representeras kan vara ett kontors mötesrum eller ett fabriks lager. Användare kan dock anse att vissa entiteter är personligt identifierbara och att de kan behålla sina egna till gångar eller inventerings spårnings metoder som knyter digitala delar till individer. Azure Digitals, som hanterar och lagrar alla data som är kopplade till digitala dubbla, som om de var personliga data.

Om du vill visa, exportera och ta bort personliga data som kan refereras till i en begäran om data subjekt, kan en Azure Digital-administratör använda [**Azure Portal**](https://portal.azure.com/) för användare och roller eller [**Azure Digitals REST-API: er**](/rest/api/azure-digitaltwins/) för digitala dubbla. Azure Portal-och REST-API: erna tillhandahåller olika metoder för att användarna ska kunna betjäna sådana data ämnes förfrågningar.

## <a name="identifying-customer-data"></a>Identifiera kund information

Azure Digitals dubbla, ser till att *person uppgifter* är associerade med sina administratörer och användare. 

Azure Digitals innehåller [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) *objekt-ID* för användare med åtkomst till miljön. Azure Digitals dubbla i Azure Portal visar användarens e-postadresser, men dessa e-postadresser lagras inte i Azures digitala dubbla. De slås upp dynamiskt i Azure Active Directory med hjälp av Azure Active Directory objekt-ID.

## <a name="deleting-customer-data"></a>Tar bort kund information

Azure Digitals dubbla administratörer kan använda Azure Portal för att ta bort data som rör användare. Det går också att utföra borttagnings åtgärder på enskilda digitala delar med hjälp av Azure Digitals REST-API: er. Mer information om tillgängliga API: er finns i [dokumentationen om Azure Digitals REST-API: er](/rest/api/azure-digitaltwins/).

## <a name="exporting-customer-data"></a>Exportera kund information

Azure Digitals innehåller data som rör digitala dubbla. Användare kan hämta och visa dessa data via REST-API: er och exportera dessa data med hjälp av kopiera och klistra in. 

Kund information, inklusive användar roller och roll tilldelningar, kan väljas, kopieras och klistras in från Azure Portal. 

## <a name="links-to-additional-documentation"></a>Länkar till ytterligare dokumentation

En fullständig lista över API: er för Azure Digitals dubbla tjänster finns i [dokumentationen för Azure Digitals REST-API: er](/rest/api/azure-digitaltwins/).