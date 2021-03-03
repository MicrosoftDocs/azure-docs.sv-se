---
title: 'Snabb start: Konfigurera Azure-attestering med hjälp av Azure Portal'
description: I den här snabb starten får du lära dig hur du konfigurerar och konfigurerar en attesterings-Provider med hjälp av Azure Portal.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d1310e3c4b4a56a27219cce613e8f6109d32c8c2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729407"
---
# <a name="quickstart-set-up-azure-attestation-by-using-the-azure-portal"></a>Snabb start: Konfigurera Azure-attestering med hjälp av Azure Portal

Följ den här snabb starten för att komma igång med Azure-attestering. Lär dig hur du hanterar en attesterings-Provider, en princip undertecknare och en princip med hjälp av Azure Portal.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="attestation-provider"></a>Attesterings leverantör

I det här avsnittet ska du skapa en attesterings-Provider och konfigurera den med antingen osignerade principer eller signerade principer. Du lär dig också hur du visar och tar bort en attesterings leverantör.

### <a name="create-and-configure-the-provider-with-unsigned-policies"></a>Skapa och konfigurera providern med osignerade principer

1. Gå till Azure Portal-menyn eller start sidan och välj **skapa en resurs**.
1. Skriv **attestering** i rutan Sök.
1. Välj **Microsoft Azure attestering** i listan resultat.
1. På sidan **Microsoft Azure attestering** väljer du **skapa**.
1. På sidan **skapa leverantör av attestering** anger du följande indata:

   - **Prenumeration**: Välj en prenumeration.
   - **Resurs grupp**: Välj en befintlig resurs grupp eller Välj **Skapa ny** och ange ett resurs grupp namn.
   - **Namn**: Ange ett unikt namn.
   - **Plats**: Välj en plats.
   - **Princip signerings certifikat fil**: Ladda inte upp filen med principens signerade certifikat för att konfigurera providern med osignerade principer.

1. När du har angett de nödvändiga indatana väljer du **Granska + skapa**.
1. Om det finns verifierings problem kan du åtgärda dem och sedan välja **skapa**.

### <a name="create-and-configure-the-provider-with-signed-policies"></a>Skapa och konfigurera providern med signerade principer

1. Gå till Azure Portal-menyn eller start sidan och välj **skapa en resurs**.
1. Skriv **attestering** i rutan Sök.
1. Välj **Microsoft Azure attestering** i listan resultat.
1. På sidan **Microsoft Azure attestering** väljer du **skapa**.
1. På sidan **skapa leverantör av attestering** anger du följande information:

   - **Prenumeration**: Välj en prenumeration.
   - **Resurs grupp**: Välj en befintlig resurs grupp eller Välj **Skapa ny** och ange ett resurs grupp namn.
   - **Namn**: Ange ett unikt namn.
   - **Plats**: Välj en plats.
   - **Princip signerings certifikat fil**: Ladda upp filen med principens signerade certifikat för att konfigurera attesterings leverantören med signerade principer. [Se exempel på princip signerings certifikat](./policy-signer-examples.md).

1. När du har angett de nödvändiga indatana väljer du **Granska + skapa**.
1. Om det finns verifierings problem kan du åtgärda dem och sedan välja **skapa**.

### <a name="view-the-attestation-provider"></a>Visa attesterings leverantören

1. Gå till Azure Portal-menyn eller start sidan och välj **alla resurser**.
1. I rutan Filter anger du leverantörs namnet för attestering och väljer det.

### <a name="delete-the-attestation-provider"></a>Ta bort attesterings leverantören

Det finns två sätt att ta bort attesterings leverantören. Du kan:

1. Gå till Azure Portal-menyn eller start sidan och välj **alla resurser**.
1. I rutan Filter anger du namnet på leverantören av attesteringen.
1. Markera kryss rutan och välj **ta bort**.
1. Ange **Ja** och välj **ta bort**.

Eller så kan du:

1. Gå till Azure Portal-menyn eller start sidan och välj **alla resurser**.
1. I rutan Filter anger du namnet på leverantören av attesteringen.
1. Välj providern för attestering och gå till översikts sidan.
1. Välj **ta bort** på Meny raden och välj **Ja**.

## <a name="attestation-policy-signers"></a>Registrerings princip för attestering

Följ stegen i det här avsnittet om du vill visa, lägga till och ta bort princip signerings certifikat.

### <a name="view-the-policy-signer-certificates"></a>Visa princip signerings certifikaten

1. Gå till Azure Portal-menyn eller start sidan och välj **alla resurser**.
1. I rutan Filter anger du namnet på leverantören av attesteringen.
1. Välj providern för attestering och gå till översikts sidan.
1. Välj **princip signerings certifikat** på resurs menyn på vänster sida i fönstret eller i det nedre fönstret. Du ser en prompt för att välja certifikat för autentisering. Välj lämpligt alternativ för att fortsätta.
1. Välj **Ladda ned princip signerings certifikat**. Knappen kommer att inaktive ras för attesterings leverantörer som skapats utan krav på policy signering.
1. Den nedladdade text filen har alla certifikat i ett JWS-format.
1. Kontrol lera antalet certifikat och de hämtade certifikaten.

### <a name="add-the-policy-signer-certificate"></a>Lägg till principens registrerings certifikat

1.  Gå till Azure Portal-menyn eller start sidan och välj **alla resurser**.
1.  I rutan Filter anger du namnet på leverantören av attesteringen.
1.  Välj providern för attestering och gå till översikts sidan.
1.  Välj **princip signerings certifikat** på resurs menyn på vänster sida i fönstret eller i det nedre fönstret.
1.  Välj **Lägg till** på den övre menyn. Knappen kommer att inaktive ras för attesterings leverantörer som skapats utan krav på policy signering.
1.  Ladda upp certifikat filen för princip signering och välj **Lägg till**. [Se exempel på princip signerings certifikat](./policy-signer-examples.md).

### <a name="delete-the-policy-signer-certificates"></a>Ta bort princip registrerings certifikat

1.  Gå till Azure Portal-menyn eller start sidan och välj **alla resurser**.
1.  I rutan Filter anger du namnet på leverantören av attesteringen.
1.  Välj providern för attestering och gå till översikts sidan.
1.  Välj **princip signerings certifikat** på resurs menyn på vänster sida i fönstret eller i det nedre fönstret.
1.  Välj **ta bort** på den övre menyn. Knappen kommer att inaktive ras för attesterings leverantörer som skapats utan krav på policy signering.
1.  Ladda upp filen med principens signerade certifikat och välj **ta bort**. [Se exempel på princip signerings certifikat](./policy-signer-examples.md). 

## <a name="attestation-policy"></a>Attesteringsprincip

I det här avsnittet beskrivs hur du visar en attesterings princip och hur du konfigurerar principer som har skapats med och utan ett krav för signering av principer.

### <a name="view-an-attestation-policy"></a>Visa en policy för attestering

1.  Gå till Azure Portal-menyn eller start sidan och välj **alla resurser**.
1.  I rutan Filter anger du namnet på leverantören av attesteringen.
1.  Välj providern för attestering och gå till översikts sidan.
1.  Välj **princip** på resurs menyn på vänster sida i fönstret eller i det nedre fönstret. Du ser en prompt för att välja certifikat för autentisering. Välj lämpligt alternativ för att fortsätta.
1.  Välj önskad **attesterings typ** och visa den **aktuella principen**.

### <a name="configure-an-attestation-policy"></a>Konfigurera en princip för attestering

Följ de här stegen för att överföra en princip i JWT-eller text format om attesteringsservern skapades utan krav på en princip signering.

1. Gå till Azure Portal-menyn eller start sidan och välj **alla resurser**.
1. I rutan Filter anger du namnet på leverantören av attesteringen.
1. Välj providern för attestering och gå till översikts sidan.
1. Välj **princip** på resurs menyn på vänster sida i fönstret eller i det nedre fönstret.
1. Välj **Konfigurera** på den övre menyn.
1. Välj **princip format** som **JWT** eller som **text**.

   Om attesterings leverantören har skapats utan krav på princip signering kan användaren överföra en princip i antingen **JWT** -eller **text** format.

      - Om du väljer JWT-format laddar du upp princip filen med princip innehållet i **osignerat/signerat JWT-** format och väljer **Spara**. [Se princip exempel](./policy-examples.md).
      - Om du väljer text format laddar du upp princip filen med innehållet i **text** format eller anger princip innehållet i text rutan och väljer **Spara**. [Se princip exempel](./policy-examples.md).

   För alternativet fil uppladdning visas princip för hands versionen i text format och kan inte redige ras.

1. Välj **Uppdatera** på den övre menyn för att visa den konfigurerade principen.


Om attesteringsservern har skapats med krav på signerings krav följer du dessa steg för att ladda upp en princip i JWT-format.

1.  Gå till Azure Portal-menyn eller start sidan och välj **alla resurser**.
1.  I rutan Filter anger du namnet på leverantören av attesteringen.
1.  Välj providern för attestering och gå till översikts sidan.
1.  Välj **princip** på resurs menyn på vänster sida i fönstret eller i det nedre fönstret.
1.  Välj **Konfigurera** på den övre menyn.
1.  Ladda upp princip filen i **signerat JWT-format** och välj **Spara**. [Se princip exempel](./policy-examples.md).

    Om attesteringsservern skapades med ett krav för princip signering kan användaren bara överföra en princip i **signerat JWT-format**.

    För alternativet fil uppladdning visas princip för hands versionen i text format och kan inte redige ras.
    
1.  Välj **Uppdatera** för att visa den konfigurerade principen.

## <a name="next-steps"></a>Nästa steg

- [Så här skapar och signerar du en policy för attestering](author-sign-policy.md)
- [Intyga en SGX-enklaven med hjälp av kod exempel](/samples/browse/?expanded=azure&terms=attestation)

