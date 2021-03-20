---
title: Använda Active Directory anslutna tjänsten (Visual Studio)
description: Lägg till en Azure Active Directory med hjälp av dialog rutan Lägg till anslutna tjänster i Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: a1ba7db72743ac122a697bf271e783ec64e041e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88165489"
---
# <a name="add-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Lägga till en Azure Active Directory med hjälp av anslutna tjänster i Visual Studio

Genom att använda Azure Active Directory (Azure AD) kan du stödja enskilda Sign-On (SSO) för ASP.NET MVC-webbprogram, eller Active Directory autentisering i webb-API-tjänster. Med Azure AD-autentisering kan användarna använda sina konton från Azure Active Directory för att ansluta till dina webb program. Fördelarna med Azure AD-autentisering med webb-API inkluderar förbättrad data säkerhet när du exponerar ett API från ett webb program. Med Azure AD behöver du inte hantera ett separat autentiseringsschema med sitt eget konto och användar hantering.

Den här artikeln och dess tilläggs artiklar innehåller information om hur du använder funktionen för den anslutna tjänsten i Visual Studio för Active Directory. Funktionen är tillgänglig i Visual Studio 2015 och senare.

Den Active Directory anslutna tjänsten har för närvarande inte stöd för ASP.NET Core program.

## <a name="prerequisites"></a>Förutsättningar

- Azure-konto: om du inte har ett Azure-konto kan du [Registrera dig för en kostnads fri utvärderings version](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera dina förmåner för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Visual Studio 2015** eller senare. [Hämta Visual Studio nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Anslut till Azure Active Directory med hjälp av dialog rutan anslutna tjänster

1. Skapa eller öppna ett ASP.NET MVC-projekt eller ett ASP.NET Web API-projekt i Visual Studio. Du kan använda MVC, webb-API, Single-Page program, Azure API-app, Azure-mobilapp och Azure Mobile Service-mallar.

1. Välj **projekt > Lägg till Connected service...** meny kommando eller dubbelklicka på noden **anslutna tjänster** som finns under projektet i Solution Explorer.

1. På sidan **anslutna tjänster** väljer du **autentisering med Azure Active Directory**.

    ![Sidan anslutna tjänster](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. På sidan **Introduktion** väljer du **Nästa**. Om du ser fel på den här sidan kan du läsa om att [diagnostisera fel med den Azure Active Directory anslutna tjänsten](vs-active-directory-error.md).

    ![Introduktions sida](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Välj en domän från List rutan **domän** på sidan **enkel inloggning** . Listan innehåller alla domäner som är tillgängliga för de konton som visas i dialog rutan konto inställningar i Visual Studio (**fil > konto inställningar...**). Alternativt kan du ange ett domän namn om du inte hittar det du söker, till exempel `mydomain.onmicrosoft.com` . Du kan välja alternativet för att skapa en Azure Active Directory app eller använda inställningarna från en befintlig Azure Active Directory-app. Välj **Nästa** när du är färdig.

    ![Enkel inloggnings sida](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. På sidan **katalog åtkomst** väljer du alternativet **Läs katalog data** som du vill. Utvecklare inkluderar vanligt vis det här alternativet.

    ![Sidan katalog åtkomst](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Klicka på **Slutför** om du vill starta ändringarna i projektet för att aktivera Azure AD-autentisering. Visual Studio visar förloppet under den här tiden:

    ![Active Directory ansluten tjänst förloppet](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. När processen har slutförts öppnar Visual Studio webbläsaren till någon av följande artiklar, efter vad som är lämpligt för din projekt typ:

    - [Komma igång med .NET MVC-projekt](vs-active-directory-dotnet-getting-started.md)
    - [Komma igång med WebAPI-projekt](vs-active-directory-webapi-getting-started.md)

1. Du kan också se Active Directory-domänen på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Så här ändras ditt projekt

När du lägger till den anslutna tjänsten i guiden lägger Visual Studio till Azure Active Directory och associerade referenser till projektet. Konfigurationsfiler och kod filer i projektet ändras också för att lägga till stöd för Azure AD. De speciella ändringar som Visual Studio gör beroende av projekt typen. Mer information finns i följande artiklar:

- [Vad hände med mitt .NET MVC-projekt?](vs-active-directory-dotnet-what-happened.md)
- [Vad hände med mitt webb-API-projekt?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Nästa steg

- [Autentiserings scenarier för Azure Active Directory](./authentication-vs-authorization.md)
- [Lägga till inloggning med Microsoft till en ASP.NET-webbapp](quickstart-v2-aspnet-webapp.md)