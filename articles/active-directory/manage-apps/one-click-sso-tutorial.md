---
title: Konfiguration med enkel inloggning (SSO) med ett klick för din Azure Marketplace-| Microsoft Docs
description: Steg för konfiguration av enkel inloggning med ett klick för ditt program från Azure Marketplace.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.reviewer: kenwith
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: e14944bc92b0d728a917402a1bd2f01b8b9012e4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375652"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Appkonfiguration med ett klick för enkel inloggning

 I den här självstudien får du lära dig hur du utför en konfiguration med enkel inloggning (SSO) med ett klick för SAML-stöd, Azure Active Directory-program (Azure AD) från Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Introduktion till enkel inloggning med ett klick

Funktionen enkel inloggning med ett klick är utformad för att konfigurera enkel inloggning för Azure Marketplace som stöder SAML-protokoll. På konfigurationssidan för Enkel inloggning med Azure AD kan du med det här alternativet automatiskt konfigurera Azure AD-metadata på programsidan. På så sätt kan du snabbt konfigurera enkel inloggning med minimal manuell ansträngning.

## <a name="advantages-of-one-click-sso"></a>Fördelar med enkel inloggning med ett klick

- Snabb konfiguration av enkel inloggning Azure Marketplace program som kräver manuell installation på programsidan.
- Mer effektiv och korrekt konfiguration av enkel inloggning.
- Ingen partnerkommunikation eller support krävs för installationen. Programmet tillhandahåller användargränssnittet för SAML-konfiguration.

## <a name="prerequisites"></a>Förutsättningar

- En aktiv prenumeration på programmet som ska konfigureras med enkel inloggning. Du behöver också administratörsautentiseringsuppgifter.
- Tillägget **Mina appar Säker inloggning från** Microsoft installerat i webbläsaren. Mer information finns i [Åtkomst till och användning av appar på Mina appar portalen.](../user-help/my-apps-portal-end-user-access.md)

## <a name="one-click-sso-configuration-steps"></a>Konfigurationssteg för enkel inloggning med ett klick

1. Lägg till programmet från Azure Marketplace.

2. Välj **Enkel inloggning.**

3. Välj **Aktivera enkel inloggning.**

4. Fyll i de obligatoriska konfigurationsvärdena i **avsnittet Grundläggande SAML-konfiguration.**

    > [!NOTE]
    > Om programmet har anpassade anspråk som du behöver konfigurera kan du hantera dem innan du utför enkel inloggning med ett klick.

5. Om funktionen enkel inloggning med ett klick är tillgänglig för ditt Azure Marketplace program visas följande skärm. Du kan behöva installera Mina appar **för säker inloggning genom** att välja Installera **tillägget**.

   ![Installera Mina appar secure sign-in browser extension (Säkert webbläsartillägg för inloggning)](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. När du har lagt till tillägget i webbläsaren väljer du **Konfigurera \<Application Name\>**. När du har omdirigerats till programadministratörsportalen loggar du in som administratör.

   ![Konfigurera programnamn](./media/one-click-sso-tutorial/setup-sso.png)

7. Webbläsartillägget konfigurerar automatiskt enkel inloggning i programmet. Bekräfta genom att välja **Ja.**

   ![Spara automatiskt ifyllda data](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Om konfigurationen av enkel inloggning för ditt program kräver ytterligare steg följer du anvisningarna för att utföra stegen.

8. När konfigurationen är klar väljer du **OK för** att spara ändringarna.

   ![Spara data som fylls i automatiskt](./media/one-click-sso-tutorial/save-data.png)

9. Ett bekräftelsefönster visas som visar att inställningarna för enkel inloggning har konfigurerats.

   ![Konfigurerad enkel inloggning](./media/one-click-sso-tutorial/sso-configured.png)

10. När konfigurationen har lyckats loggas du ut från programmet och returneras till Azure Portal.

11. Du kan välja **Testa** för att testa enkel inloggning.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](../saas-apps/tutorial-list.md)
* [Vilket är Mina appar webbläsartillägget för säker inloggning?](../user-help/my-apps-portal-end-user-access.md)
