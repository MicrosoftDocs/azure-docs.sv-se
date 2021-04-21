---
title: Azure File Sync resursflyttningar och topologiändringar
description: Lär dig hur du flyttar synkroniseringsresurser mellan resursgrupper, prenumerationer Azure Active Directory klientorganisationer (AAD).
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fb59b8d48d8411ade620304fbd143d86ac5ae919
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797283"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>Flytta Azure File Sync resurser till en annan resursgrupp, prenumeration eller AAD-klientorganisation

Den här artikeln beskriver hur du gör ändringar i en AAD-klientorganisation (resursgrupp, prenumeration eller Azure Active Directory) för dina Azure File Sync-molnresurser och Azure Storage-konton.

När du planerar att göra ändringar i Azure File Sync molnresurser är det viktigt att du överväger lagringsresurserna samtidigt. Följande resurser finns:

**Azure File Sync resurser (i hierarkisk ordning)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: Tjänst för synkronisering av lagring
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: Registrerad server
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: Synkroniseringsgrupp
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: Molnslutpunkt
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: Serverslutpunkt

I Azure File Sync den enda resursen som kan flyttas är resursen för tjänsten för synkronisering av lagring. Alla underkällor är bundna till dess överordnade och kan inte flyttas till en annan tjänst för synkronisering av lagring.

**Azure Storage-resurser (i hierarkisk ordning)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: Lagringskonto
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: Filresurs

I Azure Storage är lagringskontot den enda resurs som kan flyttas. En Azure-filresurs, som en underresurs, kan inte flyttas till ett annat lagringskonto.

## <a name="supported-combinations"></a>Kombinationer som stöds

När du planerar en resursflyttning måste lagringskontot och den Azure File Sync resurs, som kallas tjänsten för synkronisering av *lagring,* övervägas tillsammans.

Som bästa praxis bör tjänsten för synkronisering av lagring och de lagringskonton som har synkroniserade filresurser alltid finnas i samma prenumeration. Dessa kombinationer stöds:

* Tjänsten för synkronisering av lagring och lagringskonton finns i **olika resursgrupper** (samma Azure-klient)
* Tjänsten för synkronisering av lagring och lagringskonton finns i **olika prenumerationer** (samma Azure-klient)

> [!IMPORTANT]
> Genom olika kombinationer av förflyttningar kan en tjänst för synkronisering av lagring och lagringskonton hamna i olika prenumerationer som styrs av olika AAD-klienter. Synkroniseringen verkar till och med fungera, men det här är inte en konfiguration som stöds. Synkroniseringen kan stoppas i framtiden utan att det går att komma tillbaka till ett fungerande tillstånd.

När du planerar din resursflyttning finns det olika överväganden för att flytta inom samma [AAD-klientorganisation](#move-within-the-same-azure-active-directory-tenant) och flytta [över till en annan AAD-klientorganisation.](#move-to-a-new-azure-active-directory-tenant) När du flyttar AAD-klienter ska du alltid flytta synkroniserings- och lagringsresurser tillsammans.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>Flytta inom samma Azure Active Directory klientorganisation

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="En bild som visar Azure Portal för en tjänstresurs för synkronisering av lagring, med kommandot Flytta expanderat. Den visar alternativen för flytt av resursgrupp och prenumerationsflyttning." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        Ett praktiskt sätt att flytta en resurs för tjänsten för synkronisering av lagring är att använda Azure Portal. Gå till den tjänst för synkronisering av lagring som du vill flytta och **välj** Flytta från kommandofältet. Samma steg gäller för att flytta ett lagringskonto. Du kan också flytta alla resurser i en resursgrupp på det här sättet. Vi rekommenderar att du flyttar en hel resursgrupp när du har tjänsten för synkronisering av lagring och alla lagringskonton som används i den här resursgruppen.
    :::column-end:::
:::row-end:::

> [!WARNING]
> När du flyttar en lagringskontoresurs stoppas synkroniseringen omedelbart. Du måste auktorisera synkroniseringen manuellt för att få åtkomst till relevanta lagringskonton i den nya prenumerationen. Avsnittet [Azure File Sync auktorisering för lagringsåtkomst](#azure-file-sync-storage-access-authorization) innehåller de nödvändiga stegen.

### <a name="move-to-a-new-azure-active-directory-tenant"></a>Flytta till en ny Azure Active Directory klientorganisation

Enskilda resurser som en tjänst för synkronisering av lagring eller lagringskonton kan inte flyttas själva till en annan AAD-klientorganisation. Endast Azure-prenumerationer kan flytta AAD-klienter. Tänk på din prenumerationsstruktur i den nya AAD-klientorganisationen. Du kan använda en dedikerad prenumeration för Azure File Sync. 

1. Skapa en Azure-prenumeration (eller fastställ en befintlig i den gamla klientorganisationen som ska flyttas.
1. [Utför en prenumerationsflyttning inom samma AAD-klientorganisation för](#move-within-the-same-azure-active-directory-tenant) din tjänst för synkronisering av lagring och alla associerade lagringskonton.
1. Synkroniseringen stoppas. Slutför klientflytet omedelbart [eller återställ synkroniseringens möjlighet att komma åt de lagringskonton som flyttade](#azure-file-sync-storage-access-authorization). Du kan sedan gå över till den nya AAD-klientorganisationen senare.

När alla relaterade Azure File Sync resurser har kopplats till sin egen prenumeration är du redo att flytta hela prenumerationen till AAD-målklienten. I [guiden för överföringsprenumeration](../../role-based-access-control/transfer-subscription.md) kan du planera och genomföra en sådan överföring.

> [!WARNING]
> När du överför en prenumeration från en klientorganisation till en annan stoppas synkroniseringen omedelbart. Du måste auktorisera synkroniseringen manuellt för att få åtkomst till relevanta lagringskonton i den nya prenumerationen. Avsnittet [Azure File Sync auktorisering för lagringsåtkomst](#azure-file-sync-storage-access-authorization) innehåller de nödvändiga stegen.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="En bild som visar Azure Portal, Prenumerationsöversikt, med kommandot Ändra katalogverktygsfält i mitten, överst på sidan." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        Du är redo att påbörja migreringen när du har en plan och de behörigheter som krävs:
        1. I Azure Portal går du till prenumerationen, **bladet** Översikt.
        1. Välj **Ändra katalog**.
        1. Följ anvisningarna i guiden för att tilldela den nya AAD-klienten.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Azure File Sync åtkomstauktorisering för lagring

När lagringskonton flyttas till antingen en ny prenumeration eller flyttas inom en prenumeration till en ny Azure Active Directory-klientorganisation (AAD) stoppas synkroniseringen. Rollbaserad åtkomst (RBAC) används för att ge Azure File Sync åtkomst till ett lagringskonto och dessa rolltilldelningar migreras inte med resurserna.

### <a name="azure-file-sync-service-principal"></a>Azure File Sync tjänstens huvudnamn

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="En bild som visar Azure Portal, prenumerationshantering och registrerade resursproviders." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        Tjänstens Azure File Sync måste finnas i din AAD-klientorganisation innan du kan auktorisera synkroniseringsåtkomst till ett lagringskonto. </br></br> När du skapar en ny Azure-prenumeration idag registreras Azure File Sync-resursprovidern *Microsoft.StorageSync* automatiskt med din prenumeration. Registreringen av resursprovidern gör *tjänstens* huvudnamn för synkronisering tillgängligt Azure Active Directory klient som styr prenumerationen. Ett huvudnamn för tjänsten liknar ett användarkonto i din AAD. Du kan använda Azure File Sync tjänstens huvudnamn för att ge åtkomst till resurser via rollbaserad åtkomstkontroll (RBAC). Den enda resurssynkronisering som behöver åtkomst till är dina lagringskonton som innehåller de filresurser som ska synkroniseras. *Microsoft.StorageSync* måste tilldelas den inbyggda rollen **Läsare och Dataåtkomst** på lagringskontot. </br></br> Den här tilldelningen görs automatiskt via användarkontexten för den inloggade användaren när du lägger till en filresurs i en synkroniseringsgrupp, eller med andra ord skapar du en molnslutpunkt. När ett lagringskonto flyttas till en ny prenumeration, eller AAD-klientorganisation, går den här rolltilldelningen förlorad och måste [återupprättas manuellt.](#establish-sync-access-to-a-storage-account)
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Om Azure-målprenumerationen inte har skapats nyligen kontrollerar du att resursprovidern *Microsoft.StorageSync* är registrerad i prenumerationen. Om den inte är det lägger du till den manuellt på samma portalblad.

### <a name="establish-sync-access-to-a-storage-account"></a>Upprätta synkroniseringsåtkomst till ett lagringskonto

Tjänstens [Azure File Sync måste](#azure-file-sync-service-principal) användas för att auktorisera åtkomst till ett lagringskonto via rollbaserad åtkomstkontroll (RBAC). *Microsoft.StorageSync* måste tilldelas den inbyggda rollen **Läsare och Dataåtkomst** på lagringskontot. 

Den här tilldelningen görs vanligtvis automatiskt via användarkontexten för den inloggade användaren när du lägger till en filresurs i en synkroniseringsgrupp, eller med andra ord skapar du en molnslutpunkt. Men när ett lagringskonto flyttas till en ny prenumeration eller AAD-klientorganisation går den här rolltilldelningen förlorad och måste återupprättas manuellt.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="En bild som visar tjänsthuvudnamn för Microsoft.StorageSync som tilldelats rollen Läsare och Dataåtkomst på ett lagringskonto":::
    :::column-end:::
    :::column:::
        I Azure Portal navigerar du till det lagringskonto som du behöver för att omauktorisera synkroniseringsåtkomst till. <ol><li>Välj **Åtkomstkontroll (IAM)** i den vänstra innehållsförteckningen.</li><li>Välj fliken Rolltilldelningar i listan över användare och program (tjänstens huvudnamn) som har åtkomst till ditt lagringskonto.</li><li>Välj **Lägg till**</li><li>I fältet **Roll väljer** du Läsare **och Dataåtkomst.**</li><li>I fältet **Välj skriver** du *Microsoft.StorageSync, väljer* rollen och klickar på **Spara.**</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>Flytta till en annan Azure-region

Tjänsten Azure File Sync storage *sync och* de lagringskonton som innehåller filresurser som synkroniseras har en Azure-region som de distribueras i. Du fastställer den regionen när du skapar en resurs. Regionen för lagringssynkroniseringstjänsten och lagringskontoresurserna måste matcha. Dessa regioner kan inte ändras för någon av resurstypen när de har skapats.

Att tilldela en annan region till [](#region-fail-over)en resurs skiljer sig från en regionsredundans, vilket kan stödjas, beroende på redundansinställningen för lagringskontot.

## <a name="region-fail-over"></a>Redundans för region

[Azure Storage erbjuder alternativ för geo-redundans](../common/storage-redundancy.md#geo-redundant-storage) för ett lagringskonto. Dessa redundansalternativ kan utgöra problem för lagringskonton som används med Azure File Sync. Huvudorsaken är att replikering mellan geografiskt avlägsna regioner inte utförs av Azure File Sync, utan av en teknik för lagringsreplikering som är inbyggd i lagringsundersystemet i Azure. Den kan inte ha en förståelse för programtillståndet och Azure File Sync ett program med filer som synkroniseras till och från Azure-filresurser vid en viss tidpunkt. Om du väljer något av dessa geografiskt bortsagda alternativ för lagringsredundans förlorar du inte alla dina data i en storskalig katastrof. Du måste dock förutse [dataförlust](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss).

> [!CAUTION]
> Redundans är aldrig en lämplig ersättning för att etablera dina resurser i rätt Azure-region. Om dina resurser finns i fel region måste du överväga att stoppa synkroniseringen och konfigurera synkroniseringen igen till nya Azure-filresurser som distribueras i önskad region.

En regional redundans kan startas av Microsoft i en oåterkallelig händelse som gör att datacenter i en Azure-region inte kan tas ur funktion under en längre tid. Definitionen av driftstopp som ditt företag kan klara av kan vara mindre än den tid som Microsoft är beredd att släppa igenom innan en regional redundans. I en situation som denna [kan redundans även initieras av kunder](../common/storage-initiate-account-failover.md).

> [!IMPORTANT]
> Vid en redundans måste du skapa en supportbiljett för de tjänster för synkronisering av lagring som påverkas för att synkroniseringen ska fungera igen.

## <a name="see-also"></a>Se även

- [Översikt över migreringsguider för Azure-filresurs och synkronisering](../files/storage-files-migration-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
- [Felsöka Azure File Sync](file-sync-troubleshoot.md)
- [Planera för distribution av Azure File Sync](file-sync-planning.md)