---
title: Migrera manuellt från Windows Virtual Desktop (klassisk) – Azure
description: Hur du migrerar manuellt från Windows Virtual Desktop (klassisk) till Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 350b7306b6d39b42fbfebcc6566762df305b11b4
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445983"
---
# <a name="migrate-manually-from-windows-virtual-desktop-classic"></a>Migrera manuellt från Windows Virtual Desktop (klassisk)

Windows Virtual Desktop (klassisk) skapar sin tjänst miljö med PowerShell-cmdletar, REST-API: er och tjänst objekt. Ett "objekt" i en Windows Virtual Desktop Service-miljö är en sak som skapas av virtuella Windows-datorer. Tjänst objekt är klienter, värdar för pooler, program grupper och värdar för sessioner.

Windows Virtual Desktop (klassisk) är dock inte integrerat med Azure. Utan Azure-integrering hanteras inga objekt som du skapar automatiskt av Azure Portal eftersom de inte är anslutna till din Azure-prenumeration.

Den senaste större uppdateringen av det virtuella Windows-skrivbordet markerar ett skift i tjänsten mot fullständig Azure-integrering. Objekt som du skapar i virtuella Windows-datorer hanteras automatiskt av Azure Portal.

I den här artikeln förklarar vi varför du bör överväga att migrera till den senaste versionen av det virtuella Windows-skrivbordet. Därefter meddelar vi dig hur du migrerar manuellt från Windows Virtual Desktop (klassisk) till den senaste uppdateringen av det virtuella Windows-skrivbordet.

## <a name="why-migrate"></a>Varför ska man migrera?

Större uppdateringar kan vara praktiska, särskilt de som du måste göra manuellt. Det finns dock några orsaker till varför du inte kan migrera automatiskt:

- Befintliga tjänst objekt som har skapats med den klassiska versionen har ingen representation i Azure. Deras omfattning sträcker sig inte utanför Windows Virtual Desktop-tjänsten.
- Med den senaste uppdateringen har tjänstens program-ID ändrats för att ta bort medgivande för appar på det sätt som det gjorde för Windows Virtual Desktop (klassisk). Du kommer inte att kunna skapa nya Azure-objekt med det virtuella Windows-skrivbordet om de inte autentiseras med det nya program-ID: t.

Trots besväret är det fortfarande viktigt att migrera bort från den klassiska versionen. Det här kan du göra efter migreringen:

- Hantera virtuella Windows-datorer via Azure Portal.
- Tilldela Azure Active Directory (AD) användar grupper till program grupper.
- Använd den förbättrade Log Analytics funktionen för att felsöka distributionen.
- Använd Azure-inbyggd rollbaserad åtkomst kontroll (Azure RBAC) för att hantera administrativ åtkomst.

## <a name="when-should-i-migrate"></a>När ska jag migrera?

När du frågar dig själv om du bör migrera bör du också ta hänsyn till distributionens aktuella och framtida situation.

Det finns några scenarier där vi rekommenderar att du migrerar manuellt:

- Du har en inställning för att konfigurera en testvärd med ett litet antal användare.
- Du har en konfiguration av en adresspool för produktions värdar med ett litet antal användare, men du bör planera till att lägga upp till hundratals användare.
- Du har en enkel installation som enkelt kan replikeras. Om dina virtuella datorer exempelvis använder en Galleri avbildning.

> [!IMPORTANT]
> Om du använder en avancerad konfiguration som tog lång tid att stabilisera eller har många användare rekommenderar vi inte att du migrerar manuellt.

## <a name="prepare-for-migration"></a>Förbereda för migrering

Innan du börjar måste du kontrol lera att din miljö är redo att migrera.

Det här behöver du för att starta migreringsprocessen:

- En Azure-prenumeration där du skapar nya Azure Service-objekt.
- Se till att du har tilldelats följande roller:
    
    - Deltagare
    - Administratör för användaråtkomst
    
    Med deltagar rollen kan du skapa Azure-objekt i din prenumeration och rollen administratör för användar åtkomst gör att du kan tilldela användare till program grupper.

## <a name="how-to-migrate-manually"></a>Migrera manuellt

Nu när du har för berett för migreringen är det dags att migrera.

Migrera manuellt från Windows Virtual Desktop (klassisk) till Windows Virtual Desktop:

1. Följ instruktionerna i [skapa en adresspool med Azure Portal](create-host-pools-azure-marketplace.md) för att skapa alla hög nivå objekt med Azure Portal.
2. Om du vill ta över de virtuella datorer som du redan använder följer du anvisningarna i [registrera de virtuella datorerna i Windows-adresspoolen för virtuella skriv bord](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) för att manuellt registrera dem i den nya poolen som du skapade i steg 1.
3. Skapa nya program grupper för RemoteApp.
4. Publicera användare eller användar grupper i de nya apparna för Skriv bordet och RemoteApp.
5. Uppdatera principen för villkorlig åtkomst för att tillåta de nya objekten genom att följa anvisningarna i [Konfigurera Multi-Factor Authentication](set-up-mfa.md).

För att förhindra drift stopp bör du först registrera dina befintliga värdar för värdbaserade Azure Resource Manager-integrerade lagringspooler i små grupper i taget. Efter det kan du sakta in dina användare till de nya Azure Resource Manager-integrerade app-grupperna.

## <a name="next-steps"></a>Nästa steg

När du har migrerat får du veta hur Windows Virtual Desktop fungerar genom att titta på [våra självstudier](create-host-pools-azure-marketplace.md). Lär dig mer om avancerade hanterings funktioner på [expandera en befintlig värdbaserad pool](expand-existing-host-pool.md) och [Anpassa RDP-egenskaperna](customize-rdp-properties.md).

Om du vill veta mer om tjänst objekt kan du gå till [Windows Virtual Desktop-miljö](environment-setup.md).
