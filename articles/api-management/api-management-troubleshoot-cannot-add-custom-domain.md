---
title: Det går inte att lägga till en anpassad domän med hjälp av Key Vault certifikat
titleSuffix: Azure API Management
description: Lär dig hur du felsöker problemet där du inte kan lägga till en anpassad domän i Azure API Management med hjälp av ett Key Vault-certifikat.
services: api-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: a09c15466a4a9f62b2696b087cb7ab23cc767379
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75430580"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>Det gick inte att uppdatera API Management tjänst värdnamn

I den här artikeln beskrivs fel meddelandet "Det gick inte att uppdatera API Management tjänst värdnamn" som du kan stöta på när du lägger till en anpassad domän för Azure API Management-tjänsten. Den här artikeln innehåller fel söknings steg som hjälper dig att lösa problemet.

## <a name="symptoms"></a>Symtom

När du försöker lägga till en anpassad domän för din API Management-tjänst genom att använda ett certifikat från Azure Key Vault visas följande fel meddelande:

- Det gick inte att uppdatera API Management tjänstens värdnamn. Begäran till resursen https://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0 misslyckades med StatusCode: förbjuden för RequestId:. Undantags meddelande: åtgärden returnerade en ogiltig status kod som är förbjuden.

## <a name="cause"></a>Orsak

API Management tjänsten har inte behörighet att komma åt nyckel valvet som du försöker använda för den anpassade domänen.

## <a name="solution"></a>Lösning

Följ dessa anvisningar för att lösa problemet:

1. Gå till [Azure Portal](Https://portal.azure.com), Välj API Management-instansen och välj sedan **hanterade identiteter**. Kontrol lera att alternativet **registrera med Azure Active Directory** är inställt på **Ja**. 
    ![Registrera med Azure Active Director](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. I Azure Portal öppnar du tjänsten **Key Vaults** och väljer det nyckel valv som du vill använda för den anpassade domänen.
1. Välj **åtkomst principer** och kontrol lera om det finns ett huvud namn för tjänsten som matchar namnet på API Management tjänst instansen. Om det finns väljer du tjänstens huvud namn och kontrollerar att det har behörigheten **Hämta** under **hemliga behörigheter**.  
    ![Lägger till åtkomst princip för tjänstens huvud namn](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. Om API Management-tjänsten inte finns i listan väljer du **Lägg till åtkomst princip** och skapar sedan följande åtkomst princip:
    - **Konfigurera från mall**: ingen
    - **Välj huvud** namn: sök efter API Management tjänstens namn och välj det sedan i listan
    - **Nyckel behörigheter**: ingen
    - **Hemliga behörigheter**: Hämta
    - **Certifikat behörigheter**: ingen
1. Välj **OK** för att skapa åtkomst principen.
1. Spara ändringarna genom att välja **Spara** .

Kontrol lera om problemet är löst. Det gör du genom att försöka skapa den anpassade domänen i API Management-tjänsten med hjälp av Key Vault certifikatet.

## <a name="next-steps"></a>Nästa steg
Läs mer om API Management-tjänsten:

- Se fler [videor](https://azure.microsoft.com/documentation/videos/index/?services=api-management) om API Management.
* Andra sätt att skydda Server dels tjänsten finns i [ömsesidig certifikatautentisering](api-management-howto-mutual-certificates.md).

* [Skapa en API Management tjänst instans](get-started-create-service-instance.md).
* [Hantera ditt första API](import-and-publish.md).