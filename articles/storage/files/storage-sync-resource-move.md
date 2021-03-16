---
title: Azure File Sync ändringar i resurser och topologier
description: Lär dig hur du flyttar synkroniserade resurser över resurs grupper, prenumerationer och Azure Active Directory (AAD) klienter.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 3/10/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: acd5f9263a74d3273dc65522e77d83c90a719311
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555724"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>Flytta Azure File Sync resurser till en annan resurs grupp, prenumeration eller AAD-klient

Den här artikeln beskriver hur du gör ändringar i resurs grupps-, prenumerations-eller Azure Active Directory-klient (AAD) för dina Azure File Sync moln resurser och Azure Storage-konton.

När du planerar att göra ändringar i Azure File Sync moln resurser är det viktigt att överväga lagrings resurserna på samma gång. Följande resurser finns:

**Azure File Sync resurser (i hierarkisk ordning)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: Tjänsten för synkronisering av lagring
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: Registrerad Server
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: Synkronisera grupp
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: Moln slut punkt
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: Server slut punkt

I Azure File Sync är den enda resurs som kan flyttas till tjänsten Storage Sync service. Eventuella under resurser är kopplade till dess överordnade objekt och kan inte flyttas till en annan tjänst för synkronisering av lagring.

**Azure Storage-resurser (i hierarkisk ordning)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: Lagrings konto
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: Fil resurs

I Azure Storage är den enda resurs som kan flytta lagrings kontot. En Azure-filresurs, som en under resurs, kan inte flytta till ett annat lagrings konto.

## <a name="supported-combinations"></a>Kombinationer som stöds

När du planerar en resurs flytt, lagrings konto och Azure File Sync resursen på den översta nivån, som kallas *lagrings tjänsten för synkronisering*, måste anses tillsammans.

Som bästa praxis bör tjänsten för synkronisering av lagring och de lagrings konton som har synkronisering av fil resurser alltid finnas i samma prenumeration. Dessa kombinationer stöds:

* Tjänsten för synkronisering av lagring och lagrings konton finns i **olika resurs grupper** (samma Azure-klient)
* Tjänsten för synkronisering av lagring och lagrings konton finns i **olika prenumerationer** (samma Azure-klient)

> [!IMPORTANT]
> Genom olika kombinationer av flyttningar kan en tjänst för synkronisering av lagring och lagrings konton avslutas i olika prenumerationer, som styrs av olika AAD-klienter. Synkronisering verkar även fungera, men det är inte en konfiguration som stöds. Synkronisering kan stoppas i framtiden utan möjlighet att komma tillbaka till ett fungerande tillstånd.

När du planerar att flytta resurser finns det olika saker att tänka på när du [flyttar inom samma AAD-klient](#move-within-the-same-azure-active-directory-tenant) och flyttas över [till en annan AAD-klient](#move-to-a-new-azure-active-directory-tenant). När du flyttar AAD-klienter flyttar du alltid synkroniserings-och lagrings resurser tillsammans.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>Flytta inom samma Azure Active Directory-klient

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="En bild som visar Azure Portal för en resurs för synkroniseringstjänst för lagring med kommandot Flytta expanderat. Den visar resurs gruppens flytt-och prenumerations alternativ." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        Ett bekvämt sätt att flytta en resurs för synkroniseringstjänsten för lagring är att använda Azure Portal. Navigera till tjänsten Storage Sync som du vill flytta och välj **Flytta** från kommando fältet. Samma steg gäller för att flytta ett lagrings konto. Du kan också flytta alla resurser i en resurs grupp på det här sättet. Vi rekommenderar att du flyttar en hel resurs grupp när du har tjänsten Storage Sync och alla dess använda lagrings konton i den här resurs gruppen.
    :::column-end:::
:::row-end:::

> [!WARNING]
> När du flyttar en lagrings konto resurs kommer synkroniseringen att stoppas omedelbart. Du måste godkänna synkroniseringen manuellt för att komma åt relevanta lagrings konton i den nya prenumerationen. Avsnittet [Azure File Sync behörighet för lagrings åtkomst](#azure-file-sync-storage-access-authorization) innehåller de nödvändiga stegen.

### <a name="move-to-a-new-azure-active-directory-tenant"></a>Flytta till en ny Azure Active Directory-klient

Enskilda resurser som en tjänst för synkronisering eller lagrings konton kan inte flytta sig själva till en annan AAD-klient. Endast Azure-prenumerationer kan flytta AAD-klienter. Tänk på din prenumerations struktur i den nya AAD-klienten. Du kan använda en dedikerad prenumeration för Azure File Sync. 

1. Skapa en Azure-prenumeration (eller Fastställ en befintlig i den gamla klienten som ska flyttas.
1. [Flytta en prenumeration i samma AAD-klient](#move-within-the-same-azure-active-directory-tenant) som för lagrings tjänsten för synkronisering och alla tillhör ande lagrings konton.
1. Synkroniseringen stoppas. Slutför din klient genom att flytta direkt eller [återställa synkroniseringens möjlighet att komma åt de lagrings konton som flyttats](#azure-file-sync-storage-access-authorization). Du kan sedan flytta till den nya AAD-klienten senare.

När alla relaterade Azure File Syncs resurser har sequestered i sin egen prenumeration är du redo att flytta hela prenumerationen till AAD-klienten. Med [guiden Överför prenumeration](../../role-based-access-control/transfer-subscription.md) kan du planera och utföra en sådan överföring.

> [!WARNING]
> När du överför en prenumeration från en klient till en annan kommer synkroniseringen att stoppas omedelbart. Du måste godkänna synkroniseringen manuellt för att komma åt relevanta lagrings konton i den nya prenumerationen. Avsnittet [Azure File Sync behörighet för lagrings åtkomst](#azure-file-sync-storage-access-authorization) innehåller de nödvändiga stegen.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="En bild som visar bladet om Azure Portal, prenumerations översikt, som markerar kommandot ändra katalog i mitten, överst på sidan." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        Du är redo att starta migreringen när du har en plan och de behörigheter som krävs:
        1. I Azure Portal navigerar du till bladet prenumeration, **Översikt** .
        1. Välj **Ändra katalog**.
        1. Följ anvisningarna i guiden för att tilldela den nya AAD-klienten.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Azure File Sync auktorisering av åtkomst till lagring

När lagrings konton flyttas till antingen en ny prenumeration eller flyttas i en prenumeration till en ny Azure Active Directory-klient (AAD), stoppas synkroniseringen. Rollbaserad åtkomst (RBAC) används för att auktorisera Azure File Sync åtkomst till ett lagrings konto och dessa roll tilldelningar migreras inte med resurserna.

### <a name="azure-file-sync-service-principal"></a>Azure File Sync tjänstens huvud namn

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="En bild som visar Azure Portal, prenumerations hantering, registrerade resurs leverantörer." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        Azure File Sync tjänstens huvud namn måste finnas i din AAD-klient innan du kan auktorisera Sync-åtkomst till ett lagrings konto. </br></br> När du skapar en ny Azure-prenumeration idag registreras Azure File Sync Resource Provider *Microsoft. StorageSync* automatiskt med din prenumeration. Registrering av resurs leverantör kommer att skapa ett *huvud namn för tjänsten* som är tillgängligt i den Azure Active Directory klienten som styr prenumerationen. Ett tjänst huvud namn liknar ett användar konto i din AAD. Du kan använda Azure File Sync tjänstens huvud namn för att ge åtkomst till resurser via RBAC (rollbaserad åtkomst kontroll). Den enda resursens synkronisering behöver åtkomst till är dina lagrings konton som innehåller de fil resurser som ska synkroniseras. *Microsoft. StorageSync* måste tilldelas till den inbyggda roll **läsaren och data åtkomsten** på lagrings kontot. </br></br> Den här tilldelningen görs automatiskt via användar kontexten för den inloggade användaren när du lägger till en fil resurs i en Sync-grupp, eller med andra ord, skapar du en moln slut punkt. När ett lagrings konto flyttas till en ny prenumeration, eller AAD-klient, går den här roll tilldelningen förlorad och [måste återupprättas manuellt](#establish-sync-access-to-a-storage-account).
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Om Azure-prenumerationen nyligen har skapats kan du kontrol lera att *Microsoft. StorageSync* Resource provider har registrerats med prenumerationen. Om den inte är det lägger du till den manuellt på samma Portal-blad.

### <a name="establish-sync-access-to-a-storage-account"></a>Upprätta Sync-åtkomst till ett lagrings konto

[Azure File Sync tjänstens huvud namn](#azure-file-sync-service-principal) måste användas för att ge åtkomst till ett lagrings konto med hjälp av rollbaserad åtkomst kontroll (RBAC). *Microsoft. StorageSync* måste tilldelas till den inbyggda roll **läsaren och data åtkomsten** på lagrings kontot. 

Den här tilldelningen utförs vanligt vis automatiskt via användar kontexten för den inloggade användaren när du lägger till en fil resurs i en Sync-grupp, eller med andra ord, skapar du en moln slut punkt. Men när ett lagrings konto flyttas till en ny prenumeration eller AAD-klient går den här roll tilldelningen förlorad och måste återupprättas manuellt.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="En bild som visar Microsoft. StorageSync-tjänstens huvud namn som tilldelats rollen läsare och data åtkomst på ett lagrings konto":::
    :::column-end:::
    :::column:::
        I Azure Portal navigerar du till det lagrings konto du behöver för att omauktorisera Sync-åtkomst till. <ol><li>Välj **åtkomst kontroll (IAM)** i den vänstra innehålls förteckningen.</li><li>Välj fliken roll tilldelningar i listan över användare och program (tjänstens huvud namn) som har åtkomst till ditt lagrings konto.</li><li>Välj **Lägg till**</li><li>I **fältet roll** väljer du **läsare och data åtkomst**.</li><li>I fältet **Välj** skriver du *Microsoft. StorageSync*, väljer rollen och klickar på **Spara**.</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>Flytta till en annan Azure-region

Den Azure File Sync *Sync-tjänsten* för resurs lagring och de lagrings konton som innehåller fil resurser som synkroniseras, har en Azure-region som de distribueras i. Du fastställer den regionen när du skapar en resurs. Regionen för synkroniseringstjänsten för lagring och lagrings konto resurser måste matcha. Dessa regioner kan inte ändras antingen av resurs typen efter att de skapats.

Att tilldela en annan region till en resurs skiljer sig från en [regions växling](#region-fail-over)som kan användas, beroende på inställningen för redundans för lagrings kontot.

## <a name="region-fail-over"></a>Redundansväxla region

[Azure Storage erbjuder geo-redundanta alternativ](../common/storage-redundancy.md#geo-redundant-storage) för ett lagrings konto. Dessa alternativ för redundans kan medföra problem för lagrings konton som används med Azure File Sync. Huvud orsaken är att replikeringen mellan geografiskt avlägsna regioner inte utförs av Azure File Sync, men av en teknik för lagrings replikering som är inbyggd i underlag rings systemet i Azure. Det kan inte vara en förståelse för program tillstånd och Azure File Sync är ett program med filer som synkroniseras till och från Azure-filresurser vid ett givet tillfälle. Om du väljer något av dessa geografiskt utbetalade alternativ för lagring av redundans går det inte att förlora alla dina data i en storskalig katastrof. Du måste dock [förutse data förlust](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss).

> [!CAUTION]
> Redundans är inte en lämplig ersättning för att tillhandahålla resurser i rätt Azure-region. Om dina resurser är i fel region måste du överväga att stoppa synkroniseringen och ställa in synkroniseringen igen till nya Azure-filresurser som distribueras i din önskade region.

En regional redundans kan startas av Microsoft i en katastrof händelse som återger Data Center i en Azure-region incapacitated under en längre tid. Den definition av drift stopp som ditt företag kan hantera kan vara mindre än den tid som Microsoft är beredd att klara innan en regional redundans påbörjas. För en situation som t. ex. [kan redundans även initieras av kunder](../common/storage-initiate-account-failover.md).

> [!IMPORTANT]
> I händelse av redundans måste du ha en support biljett för dina berörda lagrings Sync-tjänster för att synkroniseringen ska fungera igen.

## <a name="see-also"></a>Se även

- [Översikt över guider för Azure-filresurs och synkronisering av synkronisering](storage-files-migration-overview.md)
- [Felsöka Azure File Sync](storage-sync-files-troubleshoot.md)
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
