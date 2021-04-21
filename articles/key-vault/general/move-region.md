---
title: Flytta ett nyckelvalv till en annan region – Azure Key Vault | Microsoft Docs
description: Den här artikeln innehåller råd om hur du flyttar ett nyckelvalv till en annan region.
services: key-vault
author: msmbaldwin
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: ac2f6347776c2f5d230065b80b1c0336e21e181c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751804"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Flytta ett Azure-nyckelvalv mellan regioner

Azure Key Vault stöder inte en resursflyttningsåtgärd som tillåter flytt av ett nyckelvalv från en region till en annan. Den här artikeln beskriver lösningar för organisationer som har ett affärs behov av att flytta ett nyckelvalv till en annan region. Varje lösning har begränsningar. Det är viktigt att förstå konsekvenserna av dessa lösningar innan du försöker tillämpa dem i en produktionsmiljö.

Om du vill flytta ett nyckelvalv till en annan region skapar du ett nyckelvalv i den andra regionen och kopierar sedan manuellt varje enskild hemlighet från ditt befintliga nyckelvalv till det nya nyckelvalvet. Du kan göra detta med något av följande två alternativ.

## <a name="design-considerations"></a>Designöverväganden

Tänk på följande innan du börjar:

* Namn på nyckelvalv är globalt unika. Du kan inte återanvända ett valvnamn.
* Du måste konfigurera om åtkomstprinciperna och inställningarna för nätverkskonfiguration i det nya nyckelvalvet.
* Du måste konfigurera om mjuk borttagning och rensningsskydd i det nya nyckelvalvet.
* Säkerhetskopierings- och återställningsåtgärden bevarar inte inställningarna för automatiskrotation. Du kan behöva konfigurera om inställningarna.

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>Alternativ 1: Använd kommandona för säkerhetskopiering och återställning av nyckelvalvet

Du kan säkerhetskopiera varje enskild hemlighet, nyckel och certifikat i valvet med hjälp av säkerhetskopieringskommandot. Dina hemligheter laddas ned som en krypterad blob. Du kan sedan återställa bloben till det nya nyckelvalvet. En lista över kommandon finns i [Azure Key Vault kommandon](/powershell/module/azurerm.keyvault#key_vault).

Att använda kommandona för säkerhetskopiering och återställning har två begränsningar:

* Du kan inte valva upp ett nyckelvalv i ett geografiskt område och återställa det till ett annat geografiskt område. Mer information finns i [Azure-geografiska områden.](https://azure.microsoft.com/global-infrastructure/geographies/)

* Säkerhetskopieringskommandot säkerhetskopierar alla versioner av varje hemlighet. Om du har en hemlighet med ett stort antal tidigare versioner (fler än 10) kanske storleken på begäran överskrider maxgränsen och åtgärden misslyckas.

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>Alternativ 2: Ladda ned och ladda upp nyckelvalvshemligheterna manuellt

Du kan ladda ned vissa hemliga typer manuellt. Du kan till exempel ladda ned certifikat som en PFX-fil. Det här alternativet eliminerar geografiska begränsningar för vissa hemliga typer, till exempel certifikat. Du kan ladda upp PFX-filerna till val annat nyckelvalv i valfri region. Hemligheterna laddas ned i ett icke-lösenordsskyddat format. Du ansvarar för att skydda dina hemligheter under flytten.