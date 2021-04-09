---
title: Skydda PaaS-program med Azure Storage | Microsoft Docs
description: Lär dig mer om Azure Storage säkerhets metoder för att skydda dina PaaS webb-och mobil program.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: tomsh
ms.openlocfilehash: 12cce0eac3827046ae9171f3dd5696ae5905c802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093990"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Metod tips för att skydda PaaS-webb program och mobilappar med hjälp av Azure Storage
I den här artikeln diskuterar vi en samling Azure Storage säkerhets metod tips för att skydda dina webb-och mobil program för PaaS-tjänster (Platform-as-a-Service). Dessa bästa metoder härleds från vår erfarenhet av Azure och våra kunders upplevelser som du själv har.

Azure gör det möjligt att distribuera och använda lagring på ett sätt som inte kan nås lokalt. Med Azure Storage kan du uppnå höga nivåer av skalbarhet och tillgänglighet med relativt lite ansträngning. Det är inte bara Azure Storage grunden för Windows och Linux Azure Virtual Machines, men det kan också ha stöd för stora distribuerade program.

Azure Storage tillhandahåller följande fyra tjänster: Blob Storage, Table Storage, Queue Storage och File Storage. Mer information finns i [Introduktion till Microsoft Azure Storage](../../storage/common/storage-introduction.md).

[Azure Storage säkerhets guide](../../storage/blobs/security-recommendations.md) är en bra källa för detaljerad information om Azure Storage och säkerhet. I den här artikeln får du tips på en hög nivå av de begrepp som finns i säkerhets hand boken och länkar till säkerhets hand boken, samt andra källor, för mer information.

Den här artikeln handlar om följande bästa praxis:

- Signaturer för delad åtkomst (SAS)
- Azure RBAC (rollbaserad åtkomstkontroll)
- Kryptering på klient sidan för höga värde data
- Kryptering för lagringstjänst


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Använd en signatur för delad åtkomst i stället för en lagrings konto nyckel
Åtkomst kontrollen är kritisk. För att hjälpa dig att styra åtkomsten till Azure Storage genererar Azure 2 512-bitars lagrings konto nycklar (SAKs) när du skapar ett lagrings konto. Nivån av nyckel redundans gör det möjligt för dig att undvika avbrott i tjänsten under en rutinmässig nyckel rotation. 

Lagrings åtkomst nycklar är hög prioritets hemligheter och bör endast vara tillgängliga för de som ansvarar för åtkomst kontroll för lagring. Om fel personer får åtkomst till dessa nycklar har de fullständig kontroll över lagringen och kan ersätta, ta bort eller lägga till filer i lagrings utrymmet. Detta inkluderar skadlig kod och andra typer av innehåll som potentiellt kan kompromettera din organisation eller dina kunder.

Du behöver fortfarande ett sätt att ge åtkomst till objekt i lagringen. För att ge mer detaljerad åtkomst kan du dra nytta av signaturen för delad åtkomst (SAS). SAS gör det möjligt för dig att dela vissa objekt i lagringen under ett fördefinierat tidsintervall och med vissa behörigheter. Med signaturen för delad åtkomst kan du definiera:

- Intervallet då SAS är giltigt, inklusive start tiden och förfallo tiden.
- De behörigheter som beviljats av SAS. En SAS på en BLOB kan till exempel ge en användare läs-och Skriv behörighet till denna BLOB, men inte ta bort behörigheter.
- En valfri IP-adress eller ett intervall med IP-adresser som Azure Storage accepterar SAS. Du kan till exempel ange ett intervall med IP-adresser som tillhör din organisation. Detta ger ett annat mått på säkerhet för din SAS.
- Det protokoll som Azure Storage accepterar SAS. Du kan använda den här valfria parametern för att begränsa åtkomsten till klienter med hjälp av HTTPS.

SAS gör att du kan dela innehåll på det sätt som du vill dela det utan att lämna dina lagrings konto nycklar. Att alltid använda SAS i ditt program är ett säkert sätt att dela dina lagrings resurser utan att kompromissa med dina lagrings konto nycklar.

Mer information om signaturer för delad åtkomst finns i [använda signaturer för delad åtkomst](../../storage/common/storage-sas-overview.md). 

## <a name="use-azure-role-based-access-control"></a>Använd rollbaserad åtkomst kontroll i Azure
Ett annat sätt att hantera åtkomst är att använda [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md). Med Azure RBAC fokuserar du på att ge anställda de exakta behörigheter som de behöver, baserat på behovet av att känna till och minsta behörighets säkerhets principer. För många behörigheter kan exponera ett konto för angripare. För få behörigheter innebär det att anställda inte kan få jobbet gjort effektivt. Azure RBAC hjälper till att lösa det här problemet genom att erbjuda detaljerad åtkomst hantering för Azure. Detta är nödvändigt för organisationer som vill tillämpa säkerhets principer för data åtkomst.

Du kan använda inbyggda Azure-roller i Azure för att tilldela behörigheter till användare. Använd till exempel lagrings konto deltagare för moln operatörer som behöver hantera lagrings konton och den klassiska rollen lagrings konto deltagare för att hantera klassiska lagrings konton. För moln operatörer som behöver hantera virtuella datorer, men inte det virtuella nätverk eller lagrings konto som de är anslutna till, kan du lägga till dem i rollen virtuell dator deltagare.

Organisationer som inte tillämpar data åtkomst kontroll genom att använda funktioner som Azure RBAC kan ge fler privilegier än vad som krävs för sina användare. Detta kan leda till data kompromisser genom att ge vissa användare åtkomst till data som de inte behöver ha på den första platsen.

Om du vill veta mer om Azure RBAC ser du:

- [Tilldela Azure-roller med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md)
- [Säkerhetsguiden för Azure Storage](../../storage/blobs/security-recommendations.md) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Använd kryptering på klient sidan för data med hög värde
Kryptering på klient sidan gör att du kan kryptera data via programmering innan du överför till Azure Storage och program mässigt dekryptera data när de hämtas. Detta ger kryptering av data under överföring, men ger även kryptering av data i vila. Kryptering på klient sidan är den säkraste metoden för att kryptera dina data, men det kräver att du gör program ändringar i programmet och lägger till nyckel hanterings processer på plats.

Med kryptering på klient sidan kan du också ha en enda kontroll över dina krypterings nycklar. Du kan skapa och hantera dina egna krypterings nycklar. Den använder en kuvert teknik där klient biblioteket för Azure Storage genererar en innehålls krypterings nyckel (CEK) som sedan omsluts (krypteras) med nyckel krypterings nyckeln (KEK). KEK identifieras av en nyckel identifierare och kan vara ett asymmetriskt nyckel par eller en symmetrisk nyckel och kan hanteras lokalt eller lagras i [Azure Key Vault](../../key-vault/general/overview.md).

Kryptering på klient sidan är inbyggd i Java-och .NET-lagrings klient biblioteken. Se [kryptering på klient sidan och Azure Key Vault för Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md) för information om hur du krypterar data i klient program och genererar och hanterar dina egna krypterings nycklar.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Aktivera Kryptering för lagringstjänst för vilande data
När [kryptering för lagringstjänst](../../storage/common/storage-service-encryption.md) för fil lagring har Aktiver ATS krypteras data automatiskt med AES-256-kryptering. Microsoft hanterar all kryptering, dekryptering och nyckel hantering. Den här funktionen är tillgänglig för LRS-och GRS-redundanta typer.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har vi introducerat en samling av Azure Storage säkerhets metoder för att skydda dina PaaS webb-och mobil program. Mer information om hur du skyddar dina PaaS-distributioner finns i:

- [Skydda PaaS-distributioner](paas-deployments.md)
- [Skydda PaaS-webb-och mobil program med hjälp av Azure App Services](paas-applications-using-app-services.md)
- [Skydda PaaS-databaser i Azure](paas-applications-using-sql.md)