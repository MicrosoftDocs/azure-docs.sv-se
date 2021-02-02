---
title: Konfigurera Azure-attestering med Azure Portal
description: Konfigurera och konfigurera en attesterings leverantör med Azure Portal.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 86adac557c6de133e95e97bfedbd302cc6a2b27e
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429167"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-portal"></a>Snabb start: Konfigurera Azure-attestering med Azure Portal

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Följ stegen nedan för att hantera en attesterings leverantör med hjälp av Azure Portal.

## <a name="1-attestation-provider"></a>1. attesterings leverantör

### <a name="11-create-an-attestation-provider"></a>1,1 Skapa en attesterings leverantör

#### <a name="111-to-configure-the-provider-with-unsigned-policies"></a>1.1.1 för att konfigurera providern med osignerade principer

1.  Från Azure Portal-menyn eller på Start sidan väljer du **skapa en resurs**
2.  I rutan Sök anger du **attestering**
3.  I resultat listan väljer du **Microsoft Azure attestering**
4.  På sidan Microsoft Azure attestering väljer du **skapa**
5.  På sidan Skapa leverantör av attestering anger du följande indata:
    
    **Prenumeration**: Välj en prenumeration
    
    **Resurs grupp**: Välj en befintlig resurs grupp eller Välj **Skapa ny** och ange ett resurs grupp namn
    
    **Namn**: ett unikt namn krävs

    **Plats**: Välj en plats 
    
    **Princip signerings certifikat fil**: överför inte princip signerings certifikat fil för att konfigurera providern med osignerade principer 
6.  När du har angett nödvändiga indata klickar du på **Granska + skapa**
7.  Åtgärda verifierings problem om det finns några och klicka på **skapa**.

#### <a name="112-to-configure-the-provider-with-signed-policies"></a>1.1.2 för att konfigurera providern med signerade principer

1.  Från Azure Portal-menyn eller på Start sidan väljer du **skapa en resurs**
2.  I rutan Sök anger du **attestering**
3.  I resultat listan väljer du **Microsoft Azure attestering**
4.  På sidan Microsoft Azure attestering väljer du **skapa**
5.  På sidan Skapa leverantör av attestering anger du följande information:
    
    a. **Prenumeration**: Välj en prenumeration
    
    b. **Resurs grupp**: Välj en befintlig resurs grupp eller Välj **Skapa ny** och ange ett resurs grupp namn
    
    c. **Namn**: ett unikt namn krävs

    d. **Plats**: Välj en plats 
    
    e. **Princip signerings certifikat fil**: om du vill konfigurera attesterings leverantören med princip signerings certifikat, laddar du upp certifikat filen. Se exemplen [här](./policy-signer-examples.md) 
6.  När du har angett nödvändiga indata klickar du på **Granska + skapa**
7.  Åtgärda verifierings problem om det finns några och klicka på **skapa**.

### <a name="12-view-attestation-provider"></a>1,2 Visa attesterings leverantör

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attestering Provider och väljer det

### <a name="13-delete-attestation-provider"></a>1,3 ta bort attesterings leverantör

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Markera kryss rutan och klicka på **ta bort**
4.  Skriv Ja och klicka på **ta bort** [eller]
1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Välj providern för attestering och gå till översikts Sidan
4.  Klicka på **ta bort** på den översta menyn och klicka på **Ja**


## <a name="2-attestation-policy-signers"></a>2. registrerings princip för attestering

### <a name="21-view-policy-signer-certificates"></a>2,1 Visa princip signerings certifikat

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Välj providern för attestering och gå till översikts Sidan
4.  Klicka på **princip signerings certifikat** på resurs-menyn på vänster sida eller i det nedre fönstret
5.  Klicka på **Ladda ned princip signerings certifikat** (knappen kommer att inaktive ras för de attesterings leverantörer som skapats utan krav på policy signering)
6.  Den hämtade text filen kommer att ha alla certifikat i ett JWS-format.
a.  Verifiera antalet certifikat och hämtade certifikat.

### <a name="22-add-policy-signer-certificate"></a>2,2 Lägg till princip registrerings certifikat

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Välj providern för attestering och gå till översikts Sidan
4.  Klicka på **princip signerings certifikat** på resurs-menyn på vänster sida eller i det nedre fönstret
5.  Klicka på **Lägg till** på den översta menyn (knappen kommer att inaktive ras för de attesterings leverantörer som skapats utan krav på policy signering)
6.  Ladda upp princip signerings certifikat fil och klicka på **Lägg till**. Se exemplen [här](./policy-signer-examples.md)

### <a name="23-delete-policy-signer-certificate"></a>2,3 ta bort princip signerings certifikat

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Välj providern för attestering och gå till översikts Sidan
4.  Klicka på **princip signerings certifikat** på resurs-menyn på vänster sida eller i det nedre fönstret
5.  Klicka på **ta bort** på den översta menyn (knappen kommer att inaktive ras för de attesterings leverantörer som skapats utan krav på policy signering)
6.  Ladda upp princip signerings certifikat filen och klicka på **ta bort**. Se exemplen [här](./policy-signer-examples.md) 

## <a name="3-attestation-policy"></a>3. attesterings princip

### <a name="31-view-attestation-policy"></a>3,1 Visa attesterings princip

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Välj providern för attestering och gå till översikts Sidan
4.  Klicka på **princip** på resurs-menyn på vänster sida eller i det nedre fönstret
5.  Välj önskad **attesterings typ** och visa den **aktuella principen**

### <a name="32-configure-attestation-policy"></a>3,2 Konfigurera attesterings princip

#### <a name="321-when-attestation-provider-is-created-without-policy-signing-requirement"></a>3.2.1 när attesterings leverantören skapas utan krav på policy signering

##### <a name="upload-policy-in-jwt-format"></a>Ladda upp princip i JWT-format

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Välj providern för attestering och gå till översikts Sidan
4.  Klicka på **princip** på resurs-menyn på vänster sida eller i det nedre fönstret
5.  Klicka på **Konfigurera** på den översta menyn
6.  När providern för attestering skapas utan krav på princip signering kan användaren ladda upp en princip i **JWT** -eller **text** format
7.  Välj **princip format** som **JWT**
8.  Ladda upp princip filen med princip innehåll i ett **osignerat/signerat JWT-** format och klicka på **Spara**. Se exemplen [här](./policy-examples.md)
    
    För fil överförings alternativ visas princip för hands versionen i text format och för hands versionen av principen kan inte redige ras.

7.  Klicka på **Uppdatera** på översta menyn för att visa den konfigurerade principen

##### <a name="upload-policy-in-text-format"></a>Ladda upp princip i text format

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Välj providern för attestering och gå till översikts Sidan
4.  Klicka på **princip** på resurs-menyn på vänster sida eller i det nedre fönstret
5.  Klicka på **Konfigurera** på den översta menyn
6.  När providern för attestering skapas utan krav på princip signering kan användaren ladda upp en princip i **JWT** -eller **text** format
7.  Välj **princip format** som **text**
8.  Ladda upp princip filen med innehåll i **text** format eller ange princip innehåll i text områden och klicka på **Spara**. Se exemplen [här](./policy-examples.md)

    För fil överförings alternativ visas princip för hands versionen i text format och för hands versionen av principen kan inte redige ras.

8.  Klicka på **Uppdatera** om du vill visa den konfigurerade principen

#### <a name="322-when-attestation-provider-is-created-with-policy-signing-requirement"></a>3.2.2 när en attesterings leverantör skapas med krav på policy signering

##### <a name="upload-policy-in-jwt-format"></a>Ladda upp princip i JWT-format

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Välj providern för attestering och gå till översikts Sidan
4.  Klicka på **princip** på resurs-menyn på vänster sida eller i det nedre fönstret
5.  Klicka på **Konfigurera** på den översta menyn
6.  När providern för attestering skapas med krav på princip signering kan användaren bara ladda upp en princip i ett **signerat JWT-format**
7.  Ladda upp princip filen är **signerat i JWT-format** och klicka på **Spara**. Se exemplen [här](./policy-examples.md)

    För fil överförings alternativ visas princip för hands versionen i text format och för hands versionen av principen kan inte redige ras.
    
8.  Klicka på **Uppdatera** om du vill visa den konfigurerade principen

## <a name="next-steps"></a>Nästa steg

- [Så här skapar och signerar du en policy för attestering](author-sign-policy.md)
- [Intyga en SGX-enklaven med hjälp av kod exempel](/samples/browse/?expanded=azure&terms=attestation)

