---
title: Azure Key Vault att flytta ett valv till en annan resurs grupp | Microsoft Docs
description: Vägledning om hur du flyttar ett nyckel valv till en annan resurs grupp.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/29/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another resource group.
ms.openlocfilehash: fe8051d551077666c06ac033f22303fd643ac602
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585741"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>Flytta en Azure Key Vault över resurs grupper

## <a name="overview"></a>Översikt

Att flytta ett nyckel valv över resurs grupper är en nyckel valvs funktion som stöds. Att flytta ett nyckel valv mellan resurs grupper påverkar inte konfiguration av Key Vault-brandvägg eller åtkomst principer. Anslutna program och tjänstens huvud namn bör fortsätta att fungera som avsett.

## <a name="design-considerations"></a>Designanmärkningar

Din organisation kan ha implementerat Azure Policy med tvång eller undantag på resurs grupps nivå. Det kan finnas en annan uppsättning princip tilldelningar i resurs gruppen där ditt nyckel valv finns och resurs gruppen där du flyttar nyckel valvet. En konflikt i princip krav har möjlighet att bryta dina program.

### <a name="example"></a>Exempel

Du har ett program anslutet till nyckel valv som skapar certifikat som är giltiga i två år. Resurs gruppen där du försöker flytta nyckel valvet har en princip tilldelning som blockerar skapandet av certifikat som är giltiga i mer än ett år. När du har flyttat nyckel valvet till den nya resurs gruppen, blockeras Åtgärden för att skapa ett certifikat som är giltigt under två år av en Azure policy-tilldelning.

### <a name="solution"></a>Lösning

Se till att gå till Azure Policy sidan på Azure Portal och titta på princip tilldelningarna för din aktuella resurs grupp samt den resurs grupp som du flyttar till och se till att det inte finns några fel matchningar.

## <a name="procedure"></a>Procedur

1. Logga in på Azure Portal
2. Navigera till ditt nyckel valv
3. Klicka på fliken "Översikt"
4. Välj knappen "flytta"
5. Välj "flytta till en annan resurs grupp" från List Rute alternativen
6. Välj den resurs grupp där du vill flytta nyckel valvet
7. Bekräfta varningen om att flytta resurser
8. Välj OK

Key Vault kommer nu att utvärdera resurs flyttningens giltighet och varnar dig om eventuella fel. Om inga fel hittas slutförs resurs flytten. 
