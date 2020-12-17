---
title: Azure AD Connect-redigeraren för molnbaserad attribut
description: Den här artikeln beskriver hur du använder redigeraren för attribut.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fabad618233e8866c545e1c5ccbcc8cb7508ebf
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652105"
---
# <a name="azure-ad-connect-cloud-provisioning-attribute-mapping"></a>Azure AD Connect mappning av attributmappning för moln etablering

Azure AD Connect Cloud etableringen har introducerat en ny funktion som gör att du enkelt kan mappa attribut mellan dina lokala användar-och grupp objekt och objekten i Azure AD.  Den här funktionen har lagts till i konfigurationen för moln etablering.

Du kan anpassa standardattributen – mappningar efter dina affärs behov. Så du kan ändra eller ta bort befintliga attribut-mappningar eller skapa nya attribut-mappningar.  En lista över attribut som synkroniseras finns i [attribut som synkroniseras](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

## <a name="understanding-attribute-mapping-types"></a>Förstå attribut-mappnings typer
Med attribut-mappningar kan du kontrol lera hur attributen fylls i i Azure AD.
Det finns fyra olika mappnings typer som stöds:

- **Direct** – målattributet är ifyllt med värdet för ett attribut för det länkade objektet i AD.
- **Konstant** – målattributet är ifyllt med en specifik sträng som du har angett.
- **Uttryck** – målattributet är ifyllt baserat på resultatet av ett skript som liknar uttryck.
  Mer information finns i [skriva uttryck för attribut-mappningar](reference-expressions.md).
- **Inget** – målattributet lämnas oförändrat. Men om målattributet skulle vara tomt fylls det med det standardvärde som du anger.

Tillsammans med de här fyra grundläggande typerna stöder anpassade attribut-mappningar konceptet med en valfri **Standardvärde** tilldelning. Standardvärdes tilldelningen säkerställer att ett målattribut fylls med ett värde om det inte finns ett värde i Azure AD eller på målobjektet. Den vanligaste konfigurationen är att lämna detta tomt.

## <a name="understanding-attribute-mapping-properties"></a>Förstå attribut-mappnings egenskaper

I föregående avsnitt har du redan lanserats till egenskaps typen attribut-mappning.
Tillsammans med den här egenskapen stöder attribut-mappningar även följande attribut:

- **Källattribut** – attributet användare från käll systemet (exempel: Active Directory).
- **Target** -attribut – användarattribut i mål systemet (exempel: Azure Active Directory).
- **Standardvärde om null (valfritt)** – det värde som skickas till mål systemet om källattributet är null. Det här värdet kommer endast att tillhandahållas när en användare skapas. "Standardvärdet när null" kommer inte att tillhandahållas när en befintlig användare uppdateras.  
- **Använd den här mappningen**
  - **Alltid** – Använd den här mappningen för både skapande av användare och uppdaterings åtgärder.
  - **Endast vid skapande** – Använd endast den här mappningen för åtgärder för att skapa användare.

> [!NOTE]
> Det här dokumentet beskriver hur du använder Azure Portal för att mappa attribut.  Information om hur du använder Graph finns i [transformeringar](how-to-transformation.md)

## <a name="using-attribute-mapping"></a>Använda Attribute-mappning

Följ stegen nedan om du vill använda den nya funktionen.

1.  Välj **Azure Active Directory** i Azure Portal.
2.  Välj **Azure AD Connect**.
3.  Välj **Hantera etablering**.

    ![Hantera etablering](media/how-to-configure/manage1.png)

4. Under **konfiguration** väljer du din konfiguration.
5. Välj **Klicka om du vill redigera mappningar**.  Då öppnas skärmen mappning av attribut.

    ![Lägger till attribut](media/how-to-attribute-mapping/mapping6.png)

6.  Klicka på **Lägg till attribut**.

    ![Mappnings typ](media/how-to-attribute-mapping/mapping1.png)

7. Välj **mappnings typ**.  I det här exemplet använder vi Expression.
8.  Ange uttrycket i rutan.  I det här exemplet använder vi: `Replace([mail], "@contoso.com", , ,"", ,).`
9.  Ange Target-attributet.  I det här exemplet använder vi ExtensionAttribute15.
10. Välj när du vill tillämpa detta och klicka sedan på **Använd**

    ![Redigera mappningar](media/how-to-attribute-mapping/mapping2a.png)

11. Tillbaka på skärmen mappning av attribut bör du se den nya mappningen för attribut.  
12. Klicka på **Spara schema**.

    ![Spara schema](media/how-to-attribute-mapping/mapping3.png)

## <a name="test-your-attribute-mapping"></a>Testa din attributmappning

Du kan testa din attributmappning genom att använda [etablering på begäran](how-to-on-demand-provision.md).  Från 

1. Välj **Azure Active Directory** i Azure Portal.
2. Välj **Azure AD Connect**.
3. Välj **Hantera etablering**.
4. Under **konfiguration** väljer du din konfiguration.
5. Under **Verifiera** klickar du på knappen **Tillhandahåll en användare** . 
6. På sidan etablering på begäran.  Ange det **unika namnet** för en användare eller grupp och klicka på knappen **Tillhandahåll** .  
7. När den är klar bör du se en lyckad skärm och en grön kryss ruta som anger att den har kon figurer ATS.  

    ![Lyckad etablering](media/how-to-attribute-mapping/mapping4.png)

8. Under **utför åtgärd** klickar du på **Visa information**.  Till höger bör du se det nya attributet syncrhonized och uttrycket som används.

  ![Utför åtgärd](media/how-to-attribute-mapping/mapping5.png)

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
- [Skriva uttryck för attribut-mappningar](reference-expressions.md)
- [Attribut som har synkroniserats](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)