---
title: Definiera global användar åtkomst kontroll
description: I stora organisationer kan användar behörigheter vara komplexa och kan bestämmas av en global organisations struktur, förutom standard platsen och zon strukturen.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/08/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: de3ff6ead1f0dd86e07c86b992a720a676a2095c
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97843290"
---
# <a name="define-global-access-control"></a>Definiera global åtkomst kontroll

I stora organisationer kan användar behörigheter vara komplexa och kan bestämmas av en global organisations struktur, förutom standard platsen och zon strukturen.

För att ge stöd åt behovet av åtkomst behörigheter som är globala och mer komplexa kan du skapa en global affärstopologi som baseras på affär senheter, regioner och platser. Sedan kan du definiera användar åtkomst behörigheter runt dessa entiteter.

Arbeta med Access-verktyg för Business Topology hjälper organisationer att implementera en strategi med Tom förtroende genom att bättre styra var användarna hanterar och analyserar till gångar i Azure Defender för IoT-plattformen.

## <a name="about-access-groups"></a>Om åtkomst grupper

Global åtkomst kontroll upprättas genom att skapa användar åtkomst grupper. Åtkomst grupper består av regler för vilka användare som har åtkomst till specifika affär senheter. Genom att arbeta med grupper kan du kontrol lera visnings-och konfigurations åtkomst till Defender för IoT för specifika användar roller i relevanta affär senheter, regioner och platser.

Du kan till exempel tillåta säkerhets analyser från en Active Directory grupp för att få åtkomst till alla europeiska bil-och glas produktions linjer, tillsammans med en plast linje i en region.

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="Diagram över säkerhetsanalytiker Active Directorys grupp.":::

Innan du skapar åtkomst grupper rekommenderar vi att du:

- Konfigurera din affärstopologi noggrant. Mer information om Business Topology finns i [arbeta med plats karta vyer](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views).

- Planera vilka användare som är associerade med de åtkomst grupper som du skapar. Det finns två alternativ för att tilldela användare åtkomst till grupper:

  - **Tilldela grupper med Active Directory grupper**: kontrol lera att du konfigurerar en Active Directory instans att integrera med den lokala hanterings konsolen.
  
  - **Tilldela lokala användare**: verifiera att du har skapat användare. Mer information finns i [definiera användare](how-to-create-and-manage-users.md#define-users).

Administratörs användare kan inte tilldelas åtkomst grupper. Dessa användare har till gång till alla entiteter i Business Topology som standard.

## <a name="create-access-groups"></a>Skapa åtkomst grupper

I det här avsnittet beskrivs hur du skapar åtkomst grupper. Globala standardenheter för företag och regioner skapas för den första gruppen som du skapar. Du kan redigera standardentiteterna när du definierar din första grupp.

Skapa grupper:

1. Välj **åtkomst grupper** från menyn i den lokala hanterings konsolen.

2. Välj :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false":::. I dialog rutan **Lägg till åtkomst grupp** anger du ett namn för åtkomst gruppen. Konsolen stöder 64 tecken. Tilldela namnet på ett sätt som gör det enkelt att skilja den här gruppen från andra grupper.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Dialog rutan Lägg till åtkomst grupp där du skapar åtkomst grupper.":::

3. Om alternativet **tilldela en Active Directory grupp** visas kan du tilldela en Active Directory grupp användare till den här åtkomst gruppen.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Tilldela en Active Directory-grupp i dialog rutan skapa åtkomst grupp.":::

   Om alternativet inte visas och du vill inkludera Active Directory grupper i åtkomst grupper väljer du **Systeminställningar**. Definiera grupperna i fönstret **integrations** . Ange ett grupp namn exakt som det visas i Active Directory konfigurationer och i gemener.

5. I fönstret **användare** tilldelar du så många användare som krävs för gruppen. Du kan också tilldela användare till olika grupper. Om du arbetar på det här sättet måste du skapa och spara åtkomst gruppen och reglerna, och sedan tilldela användare till gruppen från fönstret **användare** .

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="Hantera dina användar roller och tilldela dem efter behov.":::

6. Skapa regler i dialog rutan **Lägg till regler för *namn*** baserat på din affärs topologis åtkomst krav. De alternativ som visas här baseras på den topologi som du har utformat i **vyn Enterprise** och **Site Management** Windows. 

   Du kan skapa mer än en regel per grupp. Du kan behöva skapa mer än en regel per grupp när du arbetar med komplex åtkomst kornig het på flera platser. 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="Lägg till en regel i systemet.":::

De regler som du skapar visas i dialog rutan **Lägg till åtkomst grupp** . Där kan du ta bort eller redigera dem.

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="Visa och redigera alla åtkomst grupper från det här fönstret.":::

### <a name="about-rules"></a>Om regler

När du skapar regler bör du vara medveten om följande information:

- När en åtkomst grupp innehåller flera regler aggregerar regel logiken alla regler. Reglerna använder exempelvis logik, inte eller logik.

- För att en regel ska kunna tillämpas måste du tilldela sensorer till zoner i fönstret **plats hantering** .

- Du kan bara tilldela ett element per regel. Du kan till exempel tilldela en affär senhet, en region och en plats för varje regel. Skapa fler regler för gruppen om du till exempel vill att användare i en Active Directory grupp ska ha åtkomst till olika affär senheter i olika regioner.

- Om du ändrar en entitet och ändringen påverkar regel logiken tas regeln bort. Om ändringar som görs i en topologi-entitet påverkar regel logiken, till exempel att alla regler tas bort, förblir åtkomst gruppen men användarna kan inte logga in på den lokala hanterings konsolen. Användarna uppmanas att kontakta sin administratör för att logga in.

- Om ingen affär senhet eller region har valts får användarna åtkomst till alla definierade affär senheter och regioner.

## <a name="see-also"></a>Se även

[Om Defender för IoT Console-användare](how-to-create-and-manage-users.md)
