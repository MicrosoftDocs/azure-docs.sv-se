---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 3ebe1ec4c0292a530e5ef2c754e9b002e931300e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994336"
---
#### <a name="app-registrations"></a>[Appregistreringar](#tab/app-reg-ga/) 

1. Välj **Appregistreringar**.
1. Välj *webapi1* -programmet för att öppna dess **översikts** sida.
1. Under **Hantera** väljer du **exponera ett API**.
1. Välj länken **mängd** bredvid **program-ID-URI**.
1. Ersätt standardvärdet (ett GUID) med `api` och välj sedan **Spara**. Den fullständiga URI: n visas och ska ha formatet `https://your-tenant-name.onmicrosoft.com/api` . När ditt webb program begär en åtkomsttoken för API: et ska den lägga till denna URI som prefix för varje omfång som du definierar för API: et.
1. Under **omfattningar som definieras av detta API** väljer du **Lägg till ett omfång**.
1. Ange följande värden för att skapa ett omfång som definierar Läs åtkomst till API: et och välj sedan **Lägg till omfång**:
    1. **Omfattnings namn**: `demo.read`
    1. **Visnings namn för administratörs medgivande**: `Read access to demo API`
    1. **Beskrivning av administratörs medgivande**: `Allows read access to the demo API`
1. Välj **Lägg till ett omfång**, ange följande värden för att lägga till ett omfång som definierar skriv åtkomst till API: et och välj sedan **Lägg till omfång**:
    1. **Omfattnings namn**: `demo.write`
    1. **Visnings namn för administratörs medgivande**: `Write access to demo API`
    1. **Beskrivning av administratörs medgivande**: `Allows write access to the demo API`

#### <a name="applications-legacy"></a>[Program (bakåtkompatibelt)](#tab/applications-legacy/)

1. Välj **program (bakåtkompatibelt)**.
1. Välj *webapi1* -programmet för att öppna dess **egenskaps** sida.
1. Välj **Publicerade omfång**. Publicerade omfattningar kan användas för att ge ett klient program vissa behörigheter till webb-API: et.
1. För **omfattning**, ange `demo.read` och för **Beskrivning** anger du `Read access to the web API` .
1. För **omfattning**, ange `demo.write` och för **Beskrivning** anger du `Write access to the web API` .
1. Välj **Spara**.