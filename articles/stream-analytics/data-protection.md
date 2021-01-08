---
title: Data skydd i Azure Stream Analytics
description: Den här artikeln beskriver hur du krypterar dina privata data som används av ett Azure Stream Analytics jobb.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 102b03ad4fe247ae0abc4e2312d7027c6170333f
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019473"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Data skydd i Azure Stream Analytics 

Azure Stream Analytics är en fullständigt hanterad plattform som en tjänst med vilken du kan bygga real tids analys pipeliner. All tung lyftning, till exempel kluster etablering, skalning av noder för att passa din användning och hantering av interna kontroll punkter, hanteras i bakgrunden.

## <a name="private-data-assets-that-are-stored"></a>Privata data till gångar som lagras

Azure Stream Analytics behåller följande metadata och data för att köra: 

* Frågedefinition och tillhör ande konfiguration  

* Användardefinierade funktioner eller agg regeringar  

* Kontroll punkter som krävs av Stream Analytics runtime

* Ögonblicks bilder av referens data 

* Anslutnings information för de resurser som används av ditt Stream Analyticss jobb

För att hjälpa dig att uppfylla dina krav på efterlevnad i en reglerad bransch eller miljö kan du läsa mer om [Microsofts erbjudanden för efterlevnad](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="in-region-data-residency"></a>In-Region data placering
Azure Stream Analytics lagrar kund information och andra metadata som beskrivs ovan. Kund information lagras av Azure Stream Analytics i en enda region som standard, så den här tjänsten uppfyller automatiskt placering-kraven för regions data, inklusive de som anges i [säkerhets Center](https://azuredatacentermap.azurewebsites.net/).
Dessutom kan du välja att lagra alla data till gångar (kund information och andra metadata) som är relaterade till ditt Stream Analytics-jobb i en enda region genom att kryptera dem i ett valfritt lagrings konto.

## <a name="encrypt-your-data"></a>Kryptera dina data

Stream Analytics använder automatiskt optimala krypterings standarder över hela infrastrukturen för att kryptera och skydda dina data. Du kan helt enkelt lita på Stream Analytics att lagra alla dina data på ett säkert sätt så att du inte behöver oroa dig för att hantera infrastrukturen.

Om du vill använda Kundhanterade nycklar för att kryptera dina data kan du använda ditt eget lagrings konto (generell användning v1 eller v2) för att lagra alla privata data till gångar som krävs av Stream Analytics Runtime. Ditt lagrings konto kan krypteras efter behov. Ingen av dina privata data till gångar lagras permanent av Stream Analytics-infrastrukturen. 

Den här inställningen måste konfigureras när Stream Analytics jobb skapas och kan inte ändras under jobbets livs cykel. Det rekommenderas inte att ändra eller ta bort lagrings utrymme som används av din Stream Analytics. Om du tar bort ditt lagrings konto kommer du att ta bort alla privata data till gångar permanent, vilket leder till att jobbet Miss fungerar. 

Det går inte att uppdatera eller rotera nycklar till ditt lagrings konto med hjälp av Stream Analytics portalen. Du kan uppdatera nycklarna med hjälp av REST-API: er.


### <a name="configure-storage-account-for-private-data"></a>Konfigurera lagrings konto för privata data 

Kryptera ditt lagrings konto för att skydda alla dina data och välj manuellt platsen för dina privata data. 

För att hjälpa dig att uppfylla dina krav på efterlevnad i en reglerad bransch eller miljö kan du läsa mer om [Microsofts erbjudanden för efterlevnad](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

Använd följande steg för att konfigurera ditt lagrings konto för privata data till gångar. Den här konfigurationen görs från ditt Stream Analytics jobb, inte från ditt lagrings konto.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen. 

1. Välj **analys**   >  **Stream Analytics jobb**   i resultat listan. 

1. Fyll i sidan Stream Analytics jobb med nödvändig information, till exempel namn, region och skala. 

1. Markera kryss rutan med texten *skydda alla privata data till gångar som krävs av det här jobbet i mitt lagrings konto*.

1. Välj ett lagrings konto från din prenumeration. Observera att den här inställningen inte kan ändras under jobbets livs cykel. Du kan inte heller lägga till det här alternativet när jobbet har skapats.

1. Om du vill autentisera med en anslutnings sträng väljer du **anslutnings sträng** i list rutan autentiseringsläge. Lagrings konto nyckeln fylls i automatiskt från din prenumeration.

   ![Inställningar för lagrings konto för privata data](./media/data-protection/storage-account-create.png)

1. Om du vill autentisera med hanterad identitet (förhands granskning) väljer du **hanterad identitet** i list rutan autentiseringsläge. Om du väljer hanterad identitet måste du lägga till ditt Stream Analytics jobb till lagrings kontots åtkomst kontrol lista med rollen *Storage BLOB data Contributor* . Om du inte ger ditt jobb åtkomst kommer jobbet inte att kunna utföra några åtgärder. Mer information om hur du beviljar åtkomst finns i [använda Azure RBAC för att tilldela en hanterad identitets åtkomst till en annan resurs](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md#use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource).

   :::image type="content" source="media/data-protection/storage-account-create-msi.png" alt-text="Inställningar för lagrings konto för privata data med hanterad identitets autentisering":::

## <a name="private-data-assets-that-are-stored-by-stream-analytics"></a>Privata data till gångar som lagras av Stream Analytics

Alla privata data som måste behållas av Stream Analytics lagras i ditt lagrings konto. Exempel på privata data till gångar är: 

* Frågor som du har skapat och tillhör ande konfigurationer  

* Användardefinierade funktioner 

* Kontroll punkter som krävs av Stream Analytics runtime

* Ögonblicks bilder av referens data 

Anslutnings information för dina resurser, som används av ditt Stream Analytics jobb, lagras också. Kryptera ditt lagrings konto för att skydda alla dina data. 

För att hjälpa dig att uppfylla dina krav på efterlevnad i en reglerad bransch eller miljö kan du läsa mer om [Microsofts erbjudanden för efterlevnad](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="enables-data-residency"></a>Aktiverar data placering 
Du kan använda den här funktionen för att genomdriva data placering-krav som du kan ha genom att ange ett lagrings konto på motsvarande sätt.

## <a name="known-issues"></a>Kända problem
Det finns ett känt problem där ett jobb som använder kundhanterad nyckel körs i fel när hanterad identitet används för att autentisera till eventuella indata eller utdata. 

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md)
* [Förstå indata för Azure Stream Analytics](stream-analytics-add-inputs.md)
* [Kontroll punkts-och repetitions begrepp i Azure Stream Analytics jobb](stream-analytics-concepts-checkpoint-replay.md)
* [Använda referens data för sökningar i Stream Analytics](stream-analytics-use-reference-data.md)
