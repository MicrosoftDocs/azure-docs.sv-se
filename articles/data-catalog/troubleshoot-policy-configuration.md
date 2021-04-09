---
title: Så här felsöker du Azure Data Catalog
description: I den här artikeln beskrivs vanliga fel söknings problem för Azure Data Catalog-resurser.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 201a7d1fce00323ef3f8eec1bdd415c4064d49ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674996"
---
# <a name="troubleshooting-azure-data-catalog"></a>Felsöka Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

I den här artikeln beskrivs vanliga fel söknings problem för Azure Data Catalog-resurser. 

## <a name="functionality-limitations"></a>Funktions begränsningar

När du använder Azure Data Catalog är följande funktioner begränsade:

- Konton av typen **gäst roll** stöds inte. Du kan inte lägga till gäst konton som användare av Azure Data Catalog, och gäst användare kan inte använda portalen på [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com) .

- Det går inte att skapa Azure Data Catalog-resurser med hjälp av Azure Resource Manager mallar eller Azure PowerShell kommandon.

- Det går inte att flytta den Azure Data Catalog resursen mellan Azure-klienter.

## <a name="azure-active-directory-policy-configuration"></a>Konfiguration av Azure Active Directory-principer

Det kan uppstå en situation då du kan logga in på Azure Data Catalog-portalen, men då du får ett felmeddelande när du försöker logga in till registreringsverktyget för datakällan. Det här felet kan inträffa när du är ansluten till företagets nätverk eller när du ansluter utanför företags nätverket.

Registreringsverktyget använder *formulärautentisering* för att validera användarinloggningar mot Azure Active Directory. För att inloggningen ska lyckas måste en Azure Active Directory-administratör aktivera formulärautentisering i *den globala autentiseringsprincipen*.

Med den globala autentiseringsprincipen kan du aktivera autentisering separat för intranät- och extranätsanslutningar, som du ser i följande bild. Inloggnings fel kan inträffa om formulärautentisering inte är aktiverat för det nätverk som du ansluter från.

 ![Global Azure Active Directory-autentiseringsprincip](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Mer information finns i [Konfigurera autentiseringsprinciper](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Nästa steg

* [Skapa Azure Data Catalog](data-catalog-get-started.md)
