---
title: Aktivera en hanterad identitet för vägvals händelser (för hands version) – Portal
titleSuffix: Azure Digital Twins
description: Se hur du aktiverar en tilldelad identitet för Azure Digitals och använder den för att vidarebefordra händelser med hjälp av Azure Portal.
author: baanders
ms.author: baanders
ms.date: 1/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1211cd306f10fb349bd42568697443ff103a171c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "101703101"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-portal"></a>Aktivera en hanterad identitet för routning av Azure Digitals sammanflätade händelser (för hands version): Azure Portal

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

Den här artikeln beskriver hur du aktiverar en [systemtilldelad identitet för en Azure Digitals-instans](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (för närvarande i för hands version) och använder identiteten vid vidarebefordran av händelser till destinationer som stöds, till exempel [händelsehubben](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   destinationer och [Azure Storage container](../storage/blobs/storage-blobs-introduction.md).

Den här artikeln vägleder dig genom processen med hjälp av [**Azure Portal**](https://portal.azure.com).

Här följer de steg som beskrivs i den här artikeln: 

1. Skapa en digital Azure-instans med en tilldelad identitet eller aktivera systemtilldelad identitet på en befintlig Azure Digitals-instans. 
1. Lägg till en lämplig roll eller roller i identiteten. Du kan till exempel tilldela identiteten för **data avsändaren för Azure Event Hub** till identiteten om slut punkten är händelsehubben eller **Azure Service Bus data avsändarens roll** om slut punkten är Service Bus.
1. Skapa en slut punkt i Azures digitala dubbla nätverk som kan använda systemtilldelade identiteter för autentisering.

## <a name="enable-system-managed-identities-for-an-instance"></a>Aktivera systemhanterade identiteter för en instans 

När du aktiverar en tilldelad identitet på din Azure Digital-instansen skapar Azure automatiskt en identitet för den i [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Identiteten kan sedan användas för att autentisera till Azure Digitals-slutpunkter för vidarebefordran av händelser.

Du kan aktivera systemhanterade identiteter för en digital Azure-instans **som en del av instansens första installation**, eller **Aktivera den senare på en instans som redan finns**.

Någon av dessa metoder för att skapa kommer att ge samma konfigurations alternativ och samma slut resultat för din instans. I det här avsnittet beskrivs hur du gör båda.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Lägg till en Systemhanterad identitet när instansen skapas

I det här avsnittet får du lära dig hur du aktiverar en Systemhanterad identitet på en digital Azure-instans som håller på att skapas. Det här avsnittet fokuserar på det hanterade identitets steget i skapande processen. en fullständig genom gång av hur du skapar en ny Azure Digital-instansen finns i [*instruktion: Konfigurera en instans och autentisering*](how-to-set-up-instance-portal.md).

Alternativet system-hanterad identitet finns på fliken **Avancerat** i instans inställningar.

På den här fliken väljer du **alternativet för** **system-hanterad identitet** för att aktivera den här funktionen.

:::image type="content" source="media/how-to-enable-managed-identities/create-instance-advanced.png" alt-text="Skärm bild av Azure Portal som visar fliken Avancerat i dialog rutan skapa resurs för Azure digitala dubbla. Det finns en markering runt flikens namn, alternativet vid för hanterad identitet i systemet och navigerings knapparna (granska + skapa, föregående, nästa: Avancerat).":::

Du kan sedan använda de nedre navigerings knapparna för att fortsätta med resten av instans installationen.

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Lägga till en Systemhanterad identitet i en befintlig instans

I det här avsnittet ska du lägga till en Systemhanterad identitet till en digital Azure-instans som redan finns.

1. Börja med att navigera till [Azure Portal](https://portal.azure.com) i en webbläsare.

1. Sök efter namnet på din instans i portalens sökfält och välj den för att visa information om den.

1. Välj **identitet (förhands granskning)** i den vänstra menyn.

1. På den här sidan väljer du alternativet **för** att aktivera den här funktionen.

1. Tryck på knappen **Spara** och **Ja** för att bekräfta.

    :::image type="content" source="media/how-to-enable-managed-identities/identity-digital-twins.png" alt-text="Skärm bild av Azure Portal som visar sidan för identitet (förhands granskning) för en digital Azure-instans. Det finns en markering runt sidans namn på menyn Digital Digitals-instans i Azure, alternativet på för status, knappen Spara och knappen Ja.":::

När ändringen har sparats visas fler fält på den här sidan för den nya identitetens **objekt-ID** och **behörigheter**.

Du kan kopiera **objekt-ID: t** härifrån om det behövs och använda knappen **behörigheter** för att visa de Azure-roller som har tilldelats identiteten. Fortsätt till nästa avsnitt om du vill konfigurera vissa roller.

## <a name="assign-azure-roles-to-the-identity"></a>Tilldela Azure-roller till identiteten 

När en systemtilldelad identitet har skapats för din Azure Digital-instansen måste du tilldela den lämpliga roller för att autentisera med olika typer av [slut punkter](concepts-route-events.md) för att vidarebefordra händelser till stödda destinationer. I det här avsnittet beskrivs roll alternativen och hur du tilldelar dem till den systemtilldelade identiteten.

>[!NOTE]
> Det här är ett viktigt steg – utan den, kan identiteten inte komma åt dina slut punkter och händelser levereras inte.

### <a name="supported-destinations-and-azure-roles"></a>Destinationer och Azure-roller som stöds 

Här är de minsta roller som en identitet behöver för att få åtkomst till en slut punkt, beroende på typen av mål. Roller med högre behörigheter (t. ex. data ägar roller) kommer också att fungera.

| Mål | Azure-roll |
| --- | --- |
| Azure Event Hubs | Azure Event Hubs data avsändare |
| Azure Service Bus | Azure Service Bus data avsändare |
| Azure Storage-behållare | Storage Blob Data-deltagare |

Mer information om slut punkter, vägar och typer av destinationer som stöds för routning i digitala Azure-platser finns i [*begrepp: händelse vägar*](concepts-route-events.md).

### <a name="assign-the-role"></a>Tilldela rollen

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Om du vill tilldela en roll till identiteten börjar du med att öppna [Azure Portal](https://portal.azure.com).

1. Navigera till din slut punkts resurs (händelsehubben, Service Bus ämne eller lagrings behållare) genom att söka efter dess namn i portalens Sök fält. 
1. Välj **åtkomst kontroll (IAM)** på den vänstra menyn.
1. Lägg till en ny roll tilldelning genom att klicka på knappen **+ Lägg till** .

    :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-1.png" alt-text="Skärm bild av Azure Portal som visar IAM-sidan (Access Control) för en Event Hub. Knappen + Lägg till är markerad." lightbox="media/how-to-enable-managed-identities/add-role-assignment-1.png":::

1. På följande sida för **Lägg till roll tilldelning** fyller du i värdena:
    * **Roll**: Välj önskad roll i list menyn.
    * **Tilldela åtkomst till**: Välj **digitala garn** under **systemtilldelad hanterad identitet**.
    * **Prenumeration**: Välj din prenumeration. Då visas alla Azure Digital-hanterade identiteter i den valda prenumerationen.
    * **Välj**: här väljer du den hanterade identiteten för din Azure Digital-instans som tilldelas rollen. Namnet på den hanterade identiteten matchar namnet på instansen. Välj sedan namnet på din Azure Digital-instansen. När du väljer den, visas identiteten för instansen i avsnittet **markerade medlemmar** längst ned i fönstret.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-2.png" alt-text="Fylla i listade fält i dialog rutan Lägg till roll tilldelning":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

När du är klar med att ange informationen väljer du **Spara**.

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Skapa en slut punkt med Identity-baserad autentisering

När du har konfigurerat en Systemhanterad identitet för din Azure Digital-instansen och tilldelar den rätt roll (er) kan du skapa Azure Digital- [slut punkter](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) som kan använda identiteten för autentisering. Det här alternativet är endast tillgängligt för Event Hub-och Service Bus-typ slut punkter (det stöds inte för Event Grid).

>[!NOTE]
> Du kan inte redigera en slut punkt som redan har skapats med nyckelbaserad identitet för att ändra till Identity-baserad autentisering. Du måste välja autentiseringstypen när slut punkten skapas först.

Följ [anvisningarna för att skapa en Azure Digitals-slutpunkt](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins).

När du kommer till steget för att slutföra den information som krävs för din slut punkts typ måste du se till att välja **Identity-baserad** för autentiseringstypen.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Skärm bild som visar hur du skapar en slut punkt av typen Event Hub." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Slutför konfigurationen av slut punkten och välj **Spara**.

## <a name="considerations-for-disabling-system-managed-identities"></a>Att tänka på när du inaktiverar systemhanterade identiteter

Eftersom en identitet hanteras separat från slut punkterna som använder den, är det viktigt att överväga vilka effekter som alla ändringar i identiteten eller dess roller kan ha på slut punkterna i din Azure Digital-instansen. Om identiteten är inaktive rad eller om en nödvändig roll för en slut punkt tas bort från den, kan slut punkten bli otillgänglig och händelse flödet avbryts.

Om du vill fortsätta att använda en slut punkt som har kon figurer ATS med en hanterad identitet som nu har inaktiverats måste du ta bort slut punkten och återskapa [den](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) med en annan autentiseringstyp. Det kan ta upp till en timme för händelser att återuppta leverans till slut punkten efter den här ändringen.

## <a name="next-steps"></a>Nästa steg

Läs mer om hanterade identiteter i Azure AD: 
* [*Hanterade identiteter för Azure-resurser*](../active-directory/managed-identities-azure-resources/overview.md)