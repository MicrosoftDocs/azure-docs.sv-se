---
title: Azure-prenumerationstillstånd
description: I den här artikeln beskrivs olika tillstånd och status för en Azure-prenumeration.
keywords: status tillstånd för azure-prenumeration
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: andalmia
ms.openlocfilehash: 5267b333e66a0ae7b2ad05399406fecc32af74b0
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430134"
---
# <a name="azure-subscription-states"></a>Azure-prenumerationstillstånd

I den här artikeln beskrivs de olika tillstånd en Azure-prenumeration kan ha. Dessa tillstånd visas som **Status** i Azure-portalens prenumerationsområden.

| Prenumerationstillstånd | Beskrivning |
|-------------| ----------------|
| **Aktiv**/**aktiverad** | Din Azure-prenumeration är aktiv. Du kan använda den här prenumerationen för att distribuera nya resurser och hantera befintliga.<br><br>Alla åtgärder (PUT, PATCH, DELETE, POST, GET) är tillgängliga för de resursprovidrar som har [registrerats för din prenumeration](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| **Borttaget** | Din Azure-prenumeration har tagits bort tillsammans med alla underliggande resurser/data.<br><br>Inga åtgärder är tillgängliga. |
| **Inaktiverad** | Din Azure-prenumeration är inaktiverad och kan inte längre användas för att skapa eller hantera Azure-resurser. I det här tillståndet frigörs dina virtuella datorer och temporära IP-adresser, lagringen skrivskyddas och andra tjänster inaktiveras. En prenumeration kan inaktiveras på grund av följande: Din kredit kan ha gått ut. Du kanske har nått utgiftsgränsen. Du har en förfallen faktura. Kreditkortsgränsen har överskridits. Eller så har den uttryckligen inaktiverats eller avbrutits. Beroende på prenumerationstypen kan en prenumeration vara inaktiverad mellan 1 och 90 dagar. Efter det raderas den permanent. Mer information finns i artikeln [Återaktivera en inaktiverad Azure-prenumeration](subscription-disabled.md).<br><br>Åtgärder för att skapa eller uppdatera resurser (PUT, PATCH) är inaktiverade. Åtgärder som vidtar en åtgärd (POST) är också inaktiverade. Du kan hämta eller ta bort resurser (GET, DELETE). Dina resurser är fortfarande tillgängliga. |
| **Upphört** | Din Azure-prenumeration har upphört eftersom den avbröts. Du kan återaktivera en förfallen prenumeration. Mer information finns i artikeln [Återaktivera en inaktiverad Azure-prenumeration](subscription-disabled.md).<br><br>Åtgärder för att skapa eller uppdatera resurser (PUT, PATCH) är inaktiverade. Åtgärder som vidtar en åtgärd (POST) är också inaktiverade. Du kan hämta eller ta bort resurser (GET, DELETE).|
| **Förfallodatum har passerat** | Din Azure-prenumeration har en väntande utestående betalning. Prenumerationen är fortfarande aktiv men prenumerationen kan komma att inaktiveras om de förfallna beloppen inte betalas. Mer information finns i [Så här löser du förfallet belopp för din Azure-prenumeration.](resolve-past-due-balance.md).<br><br>Alla åtgärder är tillgängliga. |
| **Varnad** | Din Azure-prenumeration är i ett varnings tillstånd och kommer att inaktive ras strax om varnings orsaken inte åtgärdas. En prenumeration kan till exempel vara i ett varningstillstånd om betalningen har förfallit, avbrutits av användaren eller om prenumerationsperioden har gått ut.<br><br>Du kan hämta eller ta bort resurser (Hämta/ta bort), men du kan inte skapa några resurser (skicka/korrigera/publicera) |
