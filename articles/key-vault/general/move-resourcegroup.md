---
title: Azure Key Vault flytta ett valv till en annan resursgrupp | Microsoft Docs
description: Vägledning om hur du flyttar ett nyckelvalv till en annan resursgrupp.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 14ecbcaa35153438601a3dabb70f5b38006ded1b
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749572"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>Flytta en Azure Key Vault-instans mellan resursgrupper

## <a name="overview"></a>Översikt

Att flytta ett nyckelvalv mellan resursgrupper är en nyckelvalvsfunktion som stöds. Att flytta ett nyckelvalv mellan resursgrupper påverkar inte key vault-brandväggen eller konfigurationer av åtkomstprinciper. Anslutna program och tjänstens huvudnamn bör fortsätta att fungera som avsett.

> [!IMPORTANT]
> **Nyckelvalv som används för diskkryptering kan inte flyttas.**
> Om du använder nyckelvalv med diskkryptering för en virtuell dator kan nyckelvalvet inte flyttas till en annan resursgrupp eller en prenumeration när diskkryptering är aktiverat. Du måste inaktivera diskkryptering innan du flyttar nyckelvalvet till en ny resursgrupp eller prenumeration. 

## <a name="design-considerations"></a>Designanmärkningar

Din organisation kan ha implementerat Azure Policy med tillämpning eller undantag på resursgruppsnivå. Det kan finnas en annan uppsättning principtilldelningar i resursgruppen där nyckelvalvet för närvarande finns och resursgruppen där du flyttar nyckelvalvet. En konflikt i principkraven kan bryta dina program.

### <a name="example"></a>Exempel

Du har ett program som är anslutet till nyckelvalvet som skapar certifikat som är giltiga i två år. Resursgruppen där du försöker flytta nyckelvalvet har en principtilldelning som blockerar skapandet av certifikat som är giltiga längre än ett år. När du har flyttat nyckelvalvet till den nya resursgruppen blockeras åtgärden för att skapa ett certifikat som är giltigt i två år av en Azure-principtilldelning.

### <a name="solution"></a>Lösning

Se till att du går till sidan Azure Policy på Azure Portal och tittar på principtilldelningarna för din aktuella resursgrupp samt den resursgrupp som du flyttar till och se till att det inte finns några matchningsfel.

## <a name="procedure"></a>Procedur

1. Logga in på Azure Portal
2. Gå till ditt nyckelvalv
3. Klicka på fliken Översikt
4. Välj knappen Flytta
5. Välj Flytta till en annan resursgrupp i listrutan
6. Välj den resursgrupp där du vill flytta nyckelvalvet
7. Bekräfta varningen om att flytta resurser
8. Välj OK

Key Vault utvärderar nu giltigheten för resursflyttningen och varnar dig om eventuella fel. Om inga fel hittas slutförs resursflyttningen. 
