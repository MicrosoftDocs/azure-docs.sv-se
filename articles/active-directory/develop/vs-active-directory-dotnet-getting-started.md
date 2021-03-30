---
title: Kom igång med Azure AD i .NET MVC-projekt | Azure
description: Komma igång med att använda Azure Active Directory i .NET MVC-projekt efter anslutning till eller skapa en Azure AD med hjälp av Visual Studio Connected Services
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: 15a7c873e4d1e5c962a89b03f2a5cafc88843192
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88165472"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Komma igång med Azure Active Directory (ASP.NET MVC-projekt)

> [!div class="op_single_selector"]
> - [Kom igång](vs-active-directory-dotnet-getting-started.md)
> - [Vad hände](vs-active-directory-dotnet-what-happened.md)

Den här artikeln ger ytterligare vägledning när du har lagt till Active Directory i ett ASP.NET MVC-projekt via kommandot **project > Connected Services** i Visual Studio. Om du inte redan har lagt till tjänsten i projektet kan du göra det när som helst.

Se [vad som hände med mitt MVC-projekt?](vs-active-directory-dotnet-what-happened.md) för de ändringar som gjorts i projektet när du lade till den anslutna tjänsten.

## <a name="requiring-authentication-to-access-controllers"></a>Kräver autentisering för åtkomst till kontrollanter

Alla kontrollanter i projektet har dekorationer med `[Authorize]` attributet. Det här attributet kräver att användaren autentiseras innan de kan komma åt dessa kontrollanter. Om du vill tillåta att styrenheten används anonymt tar du bort attributet från kontroll enheten. Om du vill ange behörigheterna på en mer detaljerad nivå använder du attributet för varje metod som kräver auktorisering i stället för att använda den i kontroll enhets klassen.

## <a name="adding-signin--signout-controls"></a>Lägger till kontroller för inloggning/utloggning

Om du vill lägga till kontrollerna logga in/logga in i vyn kan du använda den `_LoginPartial.cshtml` partiella vyn för att lägga till funktionen i en av dina vyer. Här är ett exempel på funktionerna som läggs till i standardvyn `_Layout.cshtml` . (Observera det sista elementet i div med klass navigerings fält – minimera):

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>Nästa steg

- [Autentiserings scenarier för Azure Active Directory](./authentication-vs-authorization.md)
- [Lägga till inloggning med Microsoft till en ASP.NET-webbapp](quickstart-v2-aspnet-webapp.md)