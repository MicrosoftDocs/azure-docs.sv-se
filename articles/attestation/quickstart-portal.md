---
title: Konfigurera Azure-attestering med Azure Portal
description: Konfigurera och konfigurera en attesterings leverantör med Azure Portal.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 52c0dd6825c8576d97e10d3f0568ca5fb81e789e
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210822"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-portal"></a>Snabb start: Konfigurera Azure-attestering med Azure Portal

Följ stegen nedan för att hantera en attesterings leverantör med hjälp av Azure Portal.

## <a name="attestation-provider"></a>Attesterings leverantör

### <a name="create-an-attestation-provider"></a>Skapa en attesterings leverantör

#### <a name="to-configure-the-provider-with-unsigned-policies"></a>Konfigurera providern med osignerade principer

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

#### <a name="to-configure-the-provider-with-signed-policies"></a>Konfigurera providern med signerade principer

1.  Från Azure Portal-menyn eller på Start sidan väljer du **skapa en resurs**
2.  I rutan Sök anger du **attestering**
3.  I resultat listan väljer du **Microsoft Azure attestering**
4.  På sidan Microsoft Azure attestering väljer du **skapa**
5.  På sidan Skapa leverantör av attestering anger du följande information:
    
    a. **Prenumeration**: Välj en prenumeration
    
    b. **Resurs grupp**: Välj en befintlig resurs grupp eller Välj **Skapa ny** och ange ett resurs grupp namn
    
    c. **Namn**: ett unikt namn krävs

    d. **Plats**: Välj en plats 
    
    e. **Princip signerings certifikat fil**: om du vill konfigurera attesterings leverantören med princip signerings certifikat, laddar du upp certifikat filen. Se exemplen [här](/azure/attestation/policy-signer-examples) 
6.  När du har angett nödvändiga indata klickar du på **Granska + skapa**
7.  Åtgärda verifierings problem om det finns några och klicka på **skapa**.

### <a name="view-attestation-provider"></a>Visa attesterings leverantör

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attestering Provider och väljer det

### <a name="delete-attestation-provider"></a>Ta bort attesterings leverantör

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Markera kryss rutan och klicka på **ta bort**
4.  Skriv Ja och klicka på **ta bort** [eller]
1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Välj providern för attestering och gå till översikts Sidan
4.  Klicka på **ta bort** på den översta menyn och klicka på **Ja**


## <a name="attestation-policy-signers"></a>Registrerings princip för attestering

### <a name="view-policy-signer-certificates"></a>Visa princip signerings certifikat

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Välj providern för attestering och gå till översikts Sidan
4.  Klicka på **princip signerings certifikat** på resurs-menyn på vänster sida eller i det nedre fönstret
5.  Klicka på **Ladda ned princip signerings certifikat** (knappen kommer att inaktive ras för de attesterings leverantörer som skapats utan krav på policy signering)
6.  Den hämtade text filen kommer att ha alla certifikat i ett JWS-format.
a.  Verifiera antalet certifikat och hämtade certifikat.

### <a name="add-policy-signer-certificate"></a>Lägg till princip signerings certifikat

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Välj providern för attestering och gå till översikts Sidan
4.  Klicka på **princip signerings certifikat** på resurs-menyn på vänster sida eller i det nedre fönstret
5.  Klicka på **Lägg till** på den översta menyn (knappen kommer att inaktive ras för de attesterings leverantörer som skapats utan krav på policy signering)
6.  Ladda upp princip signerings certifikat fil och klicka på **Lägg till**. Se exemplen [här](/azure/attestation/policy-signer-examples)

### <a name="delete-policy-signer-certificate"></a>Ta bort princip signerings certifikat

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Välj providern för attestering och gå till översikts Sidan
4.  Klicka på **princip signerings certifikat** på resurs-menyn på vänster sida eller i det nedre fönstret
5.  Klicka på **ta bort** på den översta menyn (knappen kommer att inaktive ras för de attesterings leverantörer som skapats utan krav på policy signering)
6.  Ladda upp princip signerings certifikat filen och klicka på **ta bort**. Se exemplen [här](/azure/attestation/policy-signer-examples) 

## <a name="attestation-policy"></a>Attesteringsprincip

### <a name="view-attestation-policy"></a>Visa attesterings princip

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Välj providern för attestering och gå till översikts Sidan
4.  Klicka på **princip** på resurs-menyn på vänster sida eller i det nedre fönstret
5.  Välj önskad **attesterings typ** och visa den **aktuella principen**

### <a name="configure-attestation-policy"></a>Konfigurera attesterings princip

#### <a name="when-attestation-provider-is-created-without-policy-signing-requirement"></a>När attesterings leverantören skapas utan krav på policy signering

##### <a name="upload-policy-in-jwt-format"></a>Ladda upp princip i JWT-format

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Välj providern för attestering och gå till översikts Sidan
4.  Klicka på **princip** på resurs-menyn på vänster sida eller i det nedre fönstret
5.  Klicka på **Konfigurera** på den översta menyn
6.  När providern för attestering skapas utan krav på princip signering kan användaren ladda upp en princip i **JWT** -eller **text** format
7.  Välj **princip format** som **JWT**
8.  Ladda upp princip filen med princip innehåll i ett **osignerat/signerat JWT-** format och klicka på **Spara**. Se exemplen [här](/azure/attestation/policy-examples)
    
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
8.  Ladda upp princip filen med innehåll i **text** format eller ange princip innehåll i text områden och klicka på **Spara**. Se exemplen [här](/azure/attestation/policy-examples)

    För fil överförings alternativ visas princip för hands versionen i text format och för hands versionen av principen kan inte redige ras.

8.  Klicka på **Uppdatera** om du vill visa den konfigurerade principen

#### <a name="when-attestation-provider-is-created-with-policy-signing-requirement"></a>När attesterings leverantören har skapats med krav på policy signering

##### <a name="upload-policy-in-jwt-format"></a>Ladda upp princip i JWT-format

1.  Från Azure Portal-menyn eller från start sidan väljer du **alla resurser**
2.  I rutan Filter anger du namn på attesterings leverantör
3.  Välj providern för attestering och gå till översikts Sidan
4.  Klicka på **princip** på resurs-menyn på vänster sida eller i det nedre fönstret
5.  Klicka på **Konfigurera** på den översta menyn
6.  När providern för attestering skapas med krav på princip signering kan användaren bara ladda upp en princip i ett **signerat JWT-format**
7.  Ladda upp princip filen är **signerat i JWT-format** och klicka på **Spara**. Se exemplen [här](/azure/attestation/policy-examples)

    För fil överförings alternativ visas princip för hands versionen i text format och för hands versionen av principen kan inte redige ras.
    
8.  Klicka på **Uppdatera** om du vill visa den konfigurerade principen

 










