---
title: Katalog behörigheter (förhands granskning)
description: Den här artikeln ger en översikt över hur du konfigurerar Role-Based Access Control (RBAC) i Azure-avdelningens kontroll
author: yarong
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: b351be1e7212dc9923f701599dd951a73254afe0
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610377"
---
# <a name="role-based-access-control-in-azure-purviews-data-plane"></a>Rollbaserad åtkomst kontroll i Azure dataavdelningens kontrolls data plan

I den här artikeln beskrivs hur Role-Based Access Control (RBAC) implementeras i Azure dataavdelningens kontrolls [data plan](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane).

> [!IMPORTANT]
> Huvudobjektet som skapade ett avdelningens kontroll-konto tilldelas automatiskt alla data Plans behörigheter oavsett vilka data Plans roller de kan eller inte är i. För alla andra användare att göra vad som helst i Azure avdelningens kontroll måste de finnas i minst en av de fördefinierade data planet rollerna.

## <a name="azure-purviews-pre-defined-data-plane-roles"></a>Fördefinierade data Plans roller för Azure avdelningens kontroll

Azure avdelningens kontroll definierar en uppsättning fördefinierade data Plans roller som kan användas för att styra vem som kan komma åt vad, i Azure avdelningens kontroll. Dessa roller är:

* **Avdelningens kontroll data Reader-roll** – har åtkomst till avdelningens kontroll-portalen och kan läsa allt innehåll i Azure-avdelningens kontroll utom för genomsöknings bindningar
* **Avdelningens kontroll data curator-rollen** – har åtkomst till avdelningens kontroll-portalen och kan läsa allt innehåll i Azure avdelningens kontroll utom för genomsöknings bindningar, kan redigera information om till gångar, redigera klassificerings definitioner och ord lista och kan tillämpa klassificeringar och ord uppsättningar till till gångar.
* **Avdelningens kontroll-rollen som administratör för data källa** -har inte åtkomst till avdelningens kontroll-portalen (användaren måste också vara i data läsar-eller data curator-roller) och kan hantera alla aspekter av att söka igenom data i Azure avdelningens kontroll, men har inte Läs-eller skriv åtkomst till innehåll i Azure avdelningens kontroll utöver de som är relaterade till genomsökning.

## <a name="understanding-how-to-use-azure-purviews-data-plane-roles"></a>Förstå hur du använder data Plans roller i Azure avdelningens kontroll

När ett Azure avdelningens kontroll-konto skapas behandlas skaparen som om de befinner sig i båda rollerna avdelningens kontroll data curator och avdelningens kontroll Data Source Administrator. Men skaparen tilldelas inte dessa roller i rollarkivet. Azure Purview vet att skaparen är huvudansvarig för kontot och utökar dessa funktioner till skaparen utifrån skaparens identitet.

Alla andra användare kan bara använda Azure Purview-kontot om de har tilldelats minst en av dessa roller. Det innebär att när ett Azure avdelningens kontroll-konto skapas, kan ingen av de som skapar åtkomst till kontot eller använda dess API: er tills de placeras i en eller flera av de tidigare definierade rollerna.

Observera att avdelningens kontroll-rollen administratör för data källa har två scenarier som stöds. Det första scenariot är för användare som redan avdelningens kontroll data läsare eller avdelningens kontroll data Curators som också måste kunna skapa genomsökningar. Dessa användare måste finnas i två roller, minst en av avdelningens kontroll data Reader-eller avdelningens kontroll-data curator och placeras i rollen avdelningens kontroll data source administratör.

Det andra scenariot för avdelningens kontroll-administratören för data källan är för program mässiga processer, som tjänstens huvud namn, som måste kunna konfigureras och övervaka genomsökningar, men ska inte ha åtkomst till någon av katalogens data.

Det här scenariot kan implementeras genom att placera tjänstens huvud namn i administratörs rollen avdelningens kontroll-datakälla utan att placeras i någon av de andra två rollerna. Huvudobjektet har inte åtkomst till avdelningens kontroll-portalen, men det är o.k. eftersom det är ett program mässigt huvud konto och bara kommunicerar via API: er.

## <a name="putting-users-into-roles"></a>Placera användare i roller

Den första verksamhets ordningen när du har skapat ett Azure avdelningens kontroll-konto är att tilldela personer till dessa roller.

Roll tilldelningen hanteras via [Azures RBAC](../role-based-access-control/overview.md).

Endast två inbyggda kontroll Plans roller i Azure kan tilldela användar roller, de är antingen ägare eller användar åtkomst administratörer. För att kunna ställa in personer i de här rollerna för Azure avdelningens kontroll måste du antingen hitta någon som är ägare eller administratör för användar åtkomst eller bli en oneself.

### <a name="an-example-of-assigning-someone-to-a-role"></a>Ett exempel på att tilldela någon till en roll

1. Gå till https://portal.azure.com och navigera till ditt Azure avdelningens kontroll-konto
1. Klicka på "åtkomst kontroll (IAM)" på vänster sida
1. Följ sedan de allmänna anvisningarna [här](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group)

## <a name="role-definitions-and-actions"></a>Roll definitioner och åtgärder

En roll definieras som en samling åtgärder. Mer information om hur roller definieras finns [här](../role-based-access-control/role-definitions.md) . Och se [här](../role-based-access-control/built-in-roles.md) för roll definitionerna för Azure avdelningens kontroll-roller.

## <a name="getting-added-to-a-data-plane-role-in-an-azure-purview-account"></a>Lägga till en data Plans roll i ett Azure avdelningens kontroll-konto

Om du vill få åtkomst till ett Azure avdelningens kontroll-konto så att du kan använda dess Studio eller anropa de API: er som du behöver lägga till i en data Plans roll för Azure avdelningens kontroll. De enda personer som kan göra detta är de som är ägare eller användares åtkomst administratörer på Azure avdelningens kontroll-kontot. För de flesta användare är nästa steg att hitta en lokal administratör som kan hjälpa dig att hitta rätt personer som kan ge dig åtkomst.

För användare som har till gång till företagets [Azure Portal](https://portal.azure.com) de kan leta upp det specifika Azure avdelningens kontroll-konto som de vill ansluta till, klicka på fliken "åtkomst kontroll (IAM)" och se vem ägare eller användar åtkomst administratörer (UAAs) är. Men Observera att i vissa fall Azure Active Directory grupper eller tjänstens huvud namn kan användas som ägare eller UAAs, vilket innebär att det inte är möjligt att kontakta dem direkt. I stället måste det finnas en administratör som kan hjälpa dig.

## <a name="who-should-be-assigned-to-what-role"></a>Vem ska tilldelas till vilken roll?

|Användar scenario|Lämplig (a) roll (er)|
|-------------|-----------------|
|Jag behöver bara hitta till gångar, jag vill inte redigera något|Avdelningens kontroll data läsar roll|
|Jag behöver redigera information om till gångar, ställa in klassificeringar på dem, associera dem med ord lista osv.|Avdelningens kontroll data curator-roll|
|Jag behöver redigera ord listan eller ställa in nya klassificerings definitioner|Avdelningens kontroll data curator-roll|
|Mitt programs tjänstens huvud namn måste skicka data till Azure-avdelningens kontroll|Avdelningens kontroll data curator-roll|
|Jag måste konfigurera genomsökningar via avdelningens kontroll Studio|Avdelningens kontroll-rollen som data källans administratörs roll plus minst en av rollen avdelningens kontroll data läsare eller avdelningens kontroll data curator|
|Jag måste aktivera ett huvud namn för tjänsten eller annan programmerings identitet för att kunna konfigurera och övervaka genomsökningar i Azure avdelningens kontroll utan att ge program identiteten åtkomst till katalogens information |Avdelningens kontroll rollen som administratör för data Källa|
|Jag måste ställa in användare i roller i Azure avdelningens kontroll | Ägare eller administratör för användar åtkomst |

Mer information om hur du lägger till ett säkerhets objekt i en roll finns i [snabb start: skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md) .

## <a name="next-steps"></a>Nästa steg

* [Datainsikter](concept-insights.md)
