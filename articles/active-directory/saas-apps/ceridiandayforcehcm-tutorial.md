---
title: 'Självstudie: Azure Active Directory integration med Ceridian Dayforce HCM | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Ceridian Dayforce HCM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 4468340dbeeeb67b736d9fd2d227d9c7b2ecbeb6
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99427725"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Självstudie: Azure Active Directory integrering med Ceridian Dayforce HCM

I den här självstudien får du lära dig att integrera Ceridian Dayforce-HCM med Azure Active Directory (Azure AD). När du integrerar Ceridian Dayforce-HCM med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Ceridian Dayforce HCM.
* Gör det möjligt för användarna att logga in automatiskt till Ceridian Dayforce-HCM med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Ceridian Dayforce HCM enkel inloggning (SSO)-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Ceridian Dayforce HCM stöder **SP**-initierad enkel inloggning

## <a name="add-ceridian-dayforce-hcm-from-the-gallery"></a>Lägg till Ceridian Dayforce HCM från galleriet

För att konfigurera integreringen av Ceridian Dayforce HCM med Azure AD måste du lägga till Ceridian Dayforce HCM från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Ceridian Dayforce HCM** i sökrutan.
1. Välj **Ceridian DAYFORCE HCM** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-ceridian-dayforce-hcm"></a>Konfigurera och testa Azure AD SSO för Ceridian Dayforce HCM

Konfigurera och testa Azure AD SSO med Ceridian Dayforce HCM med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Ceridian Dayforce HCM.

Utför följande steg för att konfigurera och testa Azure AD SSO med Ceridian Dayforce HCM:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Ceridian DAYFORCE HCM SSO](#configure-ceridian-dayforce-hcm-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Ceridian DAYFORCE HCM test User](#create-ceridian-dayforce-hcm-test-user)** – om du vill ha en motsvarighet till B. Simon i CERIDIAN Dayforce HCM som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO 

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Ceridian DAYFORCE HCM** Application Integration hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Ceridian Dayforce HCM-domäner och -URL:er](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du URL:en som användarna använder för att logga in till Ceridian Dayforce HCM-programmet.

    | Miljö | URL |
    | :-- | :-- |
    | För produktion | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | För testning | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. I text rutan **identifierare** skriver du URL: en med följande mönster:

    | Miljö | URL |
    | :-- | :-- |
    | För produktion | `https://ncpingfederate.dayforcehcm.com/sp` |
    | För testning | `https://fs-test.dayforcehcm.com/sp` |

    c. I textrutan **Svars-URL** skriver du URL:en som används av Azure AD för att publicera svaret.

    | Miljö | URL |
    | :-- | :-- |
    | För produktion | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | För testning | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Ceridian Dayforce HCM-kundsupporten](https://www.ceridian.com/support) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Ceridian Dayforce HCM-programmet förväntar sig att SAML-försäkran har ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![Skärm bild som visar användarattribut med redigerings ikonen vald.](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Name | Källattribut|
    | ---------| --------- |
    | name  | user.extensionattribute2 |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![Skärm bild som visar användar anspråk med alternativet att lägga till ett nytt anspråk.](common/new-save-attribute.png)

    ![Skärm bild som visar dialog rutan hantera användar anspråk där du kan ange de värden som beskrivs.](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan **Källattribut** väljer du det användarattribut som du vill använda för implementeringen. Om du t.ex. vill använda EmployeeID som unikt användar-ID och du har lagrat attributvärdet i ExtensionAttribute2 väljer du sedan user.extensionattribute2.

    f. Klicka på **OK**

    ex. Klicka på **Spara**.

7. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Metadata XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

8. I avsnittet **Konfigurera Ceridian Dayforce HCM** kopierar du lämpliga URL:er baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Ceridian Dayforce HCM.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. Välj **Ceridian Dayforce HCM** i listan med program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-ceridian-dayforce-hcm-sso"></a>Konfigurera Ceridian Dayforce HCM SSO

För att konfigurera enkel inloggning på **Ceridian Dayforce HCM**-sidan måste du skicka **XML:en med metadata** som du laddat ned och relevanta kopierade URL:er från Azure-portalen till [Ceridian Dayforce HCM-supportteamet](https://www.ceridian.com/support). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Skapa Ceridian Dayforce HCM-testanvändare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i Ceridian Dayforce HCM. Arbeta med [Ceridian DAYFORCE HCM support team](https://www.ceridian.com/support) för att lägga till användare i CERIDIAN Dayforce HCM-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Ceridian Dayforce HCM inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Ceridian Dayforce HCM inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Ceridian Dayforce HCM i Mina appar omdirigeras det till Ceridian Dayforce HCM inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Ceridian Dayforce HCM kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
