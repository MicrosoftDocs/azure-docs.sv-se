---
title: Mappning av attribut i Azure AD Connect molnbaserad synkronisering
description: Den här artikeln beskriver hur du använder funktionen för moln synkronisering i Azure AD Connect att mappa attribut.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb043374cf6252da3929c8f0cda6c0a4be558b7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555218"
---
# <a name="attribute-mapping-in-azure-ad-connect-cloud-sync"></a>Mappning av attribut i Azure AD Connect molnbaserad synkronisering

Du kan använda funktionen Cloud Sync i Azure Active Directory (Azure AD) Anslut för att mappa attribut mellan dina lokala användar-eller grupp objekt och objekten i Azure AD. Den här funktionen har lagts till i konfigurationen för moln synkronisering.

Du kan anpassa (ändra, ta bort eller skapa) standardattributen för mappning enligt dina affärs behov. En lista över attribut som synkroniseras finns i [attribut som synkroniseras till Azure Active Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

## <a name="understand-types-of-attribute-mapping"></a>Förstå typer av attributmappning
Med attributmappning styr du hur attribut fylls i i Azure AD. Azure AD stöder fyra mappnings typer:

- **Direct**: målattributet fylls med värdet för ett attribut för det länkade objektet i Active Directory.
- **Konstant**: målattributet är ifyllt med en angiven sträng som du anger.
- **Uttryck**: målattributet är ifyllt baserat på resultatet av ett skript som liknar uttryck. Mer information finns i [skriva uttryck för mappningar av attribut i Azure Active Directory](reference-expressions.md).
- **Ingen**: målattributet är oförändrat. Men om målattributet skulle vara tomt fylls det med det standardvärde som du anger.

Tillsammans med de här grundläggande typerna stöder anpassade mappningar av attribut begreppet för *en valfri standardvärdes* tilldelning. Med standardvärde tilldelningen säkerställs att ett målattribut är ifyllt med ett värde om Azure AD eller målobjektet inte har något värde. Den vanligaste konfigurationen är att lämna detta tomt.

## <a name="understand-properties-of-attribute-mapping"></a>Förstå egenskaper för Attribute-mappning

Tillsammans med egenskapen type stöder attributet mappning följande attribut:

- **Källattribut**: attributet User från käll systemet (exempel: Active Directory).
- **Target-attribut**: användarattribut i mål systemet (exempel: Azure Active Directory).
- **Standardvärde om null (valfritt)**: det värde som skickas till mål systemet om källattributet är null. Det här värdet kommer endast att tillhandahållas när en användare skapas. Den tillhandahålls inte när du uppdaterar en befintlig användare.  
- **Använd den här mappningen**:
  - **Always**: Använd den här mappningen för både användar skapande och uppdaterings åtgärder.
  - **Endast under skapande**: Använd endast den här mappningen för åtgärder som skapar användaren.

> [!NOTE]
> Den här artikeln beskriver hur du använder Azure Portal för att mappa attribut.  Information om hur du använder Microsoft Graph finns i [transformeringar](how-to-transformation.md).

## <a name="add-an-attribute-mapping"></a>Lägga till en attributmappning

Följ dessa steg om du vill använda den nya funktionen:

1.  Välj **Azure Active Directory** i Azure Portal.
2.  Välj **Azure AD Connect**.
3.  Välj **hantera synkronisering av moln**.

    ![Skärm bild som visar länken för att hantera synkronisering av molnet.](media/how-to-install/install-6.png)

4. Under **konfiguration** väljer du din konfiguration.
5. Välj **Klicka om du vill redigera mappningar**.  Den här länken öppnar sidan **mappningar för attribut** .

    ![Skärm bild som visar länken för att lägga till attribut.](media/how-to-attribute-mapping/mapping-6.png)

6.  Välj **Lägg till attribut**.

    ![Skärm bild som visar knappen för att lägga till ett attribut, tillsammans med listor över attribut och mappnings typer.](media/how-to-attribute-mapping/mapping-1.png)

7. Välj mappnings typ. I det här exemplet använder vi **Expression**.
8. Ange uttrycket i rutan. I det här exemplet använder vi `Replace([mail], "@contoso.com", , ,"", ,)` .
9. Ange Target-attributet. I det här exemplet använder vi **ExtensionAttribute15**.
10. Välj när du vill tillämpa mappningen och välj sedan **Använd**.

    ![Skärm bild som visar de ifyllda rutorna för att skapa en attributmappning.](media/how-to-attribute-mapping/mapping-2a.png)

11. Tillbaka på skärmen **mappningar för attribut** bör du se den nya mappningen för attribut.  
12. Välj **Spara schema**.

    ![Skärm bild som visar knappen Spara schema.](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>Testa din attributmappning

Om du vill testa din attributmappning kan du använda [etablering på begäran](how-to-on-demand-provision.md): 

1. Välj **Azure Active Directory** i Azure Portal.
2. Välj **Azure AD Connect**.
3. Välj **Hantera etablering**.
4. Under **konfiguration** väljer du din konfiguration.
5. Under **Verifiera** väljer du knappen **Tillhandahåll en användare** . 
6. På skärmen **etablera på begäran** anger du det unika namnet för en användare eller grupp och klickar på knappen **Tillhandahåll** . 

   Skärmen visar att etableringen pågår.

   ![Skärm bild som visar att etableringen pågår.](media/how-to-attribute-mapping/mapping-4.png)

8. När etableringen har slutförts visas skärmen lyckades med fyra gröna kryss rutor. 

   Under **utför åtgärd** väljer du **Visa information**. Till höger bör du se det nya attributet Synchronized och uttrycket som används.

   ![Skärm bild som visar information om lyckade och exporterade uppgifter.](media/how-to-attribute-mapping/mapping-5.png)

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
- [Skriver uttryck för mappningar av attribut](reference-expressions.md)
- [Attribut som synkroniseras till Azure Active Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
