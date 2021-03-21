---
title: Azure Key Vault säkerhets världar | Microsoft Docs
description: 'Azure Key Vault är en tjänst för flera innehavare. Den använder en pool med HSM: er på varje Azure-plats. Alla platser i en geografisk region delar en kryptografisk avgränsning.'
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 07/03/2017
ms.openlocfilehash: d21deea13aac3d40c452a183c340d3108a1a01f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936336"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault säkerhets världar och geografiska gränser

Azure Key Vault är en tjänst för flera innehavare och använder en pool med HSM: er (Hardware Security modules) på varje Azure-plats. 

Alla HSM: er på Azure-platser i samma geografiska region delar samma kryptografiska gränser (Thales Security World). Till exempel, östra USA och västra USA delar samma säkerhets värld eftersom de hör till den amerikanska geo-platsen. På samma sätt delar alla Azure-platser i Japan samma säkerhets värld och alla Azure-platser i Australien, Indien och så vidare. 

## <a name="backup-and-restore-behavior"></a>Beteende för säkerhets kopiering och återställning

En säkerhets kopia som tar en nyckel från ett nyckel valv på en Azure-plats kan återställas till ett nyckel valv på en annan Azure-plats, förutsatt att båda dessa villkor är uppfyllda:

- Båda Azure-platserna tillhör samma geografiska plats
- Båda nyckel valvena tillhör samma Azure-prenumeration

Till exempel kan en säkerhets kopia som tas av en specifik prenumeration av en nyckel i ett nyckel valv i västra Indien bara återställas till ett annat nyckel valv i samma prenumeration och Geo-plats; Västra Indien, centrala Indien eller södra Indien.

## <a name="regions-and-products"></a>Regioner och produkter

- [Azure-regioner](https://azure.microsoft.com/regions/)
- [Microsoft-produkter efter region](https://azure.microsoft.com/regions/services/)

Regioner mappas till säkerhets världar som visas som huvud rubriker i tabellerna:

I artikeln produkter per region, t. ex., innehåller fliken **Amerika** östra USA, centrala USA, västra USA all mappning till Amerika-regionen. 

>[!NOTE]
>Ett undantag är att US DOD öst och US DOD CENTRAL har sina egna säkerhets världar. 

På samma sätt kan du på fliken **Europa** , norra Europa och västra Europa, mappa båda till regionen Europa. Samma sak gäller även på fliken **Asien och Stillahavsområdet** .
