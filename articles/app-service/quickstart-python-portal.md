---
title: 'Snabb start: skapa en python-app i Azure Portal'
description: Kom igång med Azure App Service genom att distribuera din första python-app till en Linux-behållare i App Service genom att använda Azure Portal.
ms.topic: quickstart
ms.date: 04/01/2021
ms.custom: devx-track-python
robots: noindex
ms.openlocfilehash: 42f29152521da7bf90a550248e8429e51b728b24
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012364"
---
# <a name="quickstart-create-a-python-app-using-azure-app-service-on-linux-azure-portal"></a>Snabb start: skapa en python-app med Azure App Service på Linux (Azure Portal)

I den här snabb starten distribuerar du en python-webbapp till [app service på Linux](overview.md#app-service-on-linux), Azures mycket skalbara, självkorrigerande webb värd tjänst. Du kan använda Azure Portal för att distribuera ett prov med antingen flaska eller django-ramverk. Webbappen du konfigurerar använder en Basic App Service-nivå som kostar en låg kostnad i din Azure-prenumeration.

## <a name="configure-accounts"></a>Konfigurera konton

- Om du ännu inte har ett Azure-konto med en aktiv prenumeration kan du [skapa ett konto utan kostnad](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Om du inte har ett GitHub-konto kan du gå till [GitHub.com](https://github.com) för att skapa ett. 

## <a name="fork-the-sample-github-repository"></a>Förgrena exempel GitHub-lagringsplatsen

1. Öppna [GitHub.com](https://github.com) och logga in.

1. Navigera till någon av följande exempel databaser:
    - [Kolv Hello World](https://github.com/Azure-Samples/python-docs-hello-world)
    - [Django Hello World](https://github.com/Azure-Samples/python-docs-hello-django)

1. I det övre högra hörnet på sidan GitHub väljer du **förgrening** för att göra en kopia av lagrings platsen i ditt eget GitHub-konto:

    ![GitHub gaffel-kommando](media/quickstart-python-portal/github-fork-command.png)

    Azure kräver att du har åtkomst till GitHub-organisationen som innehåller lagrings platsen. Genom att förgrena exemplet till ditt eget GitHub-konto har du automatiskt nödvändig åtkomst och kan också göra ändringar i koden.

## <a name="provision-the-app-service-web-app"></a>Etablera App Service-webbappen

En App Service webbapp är den webb server som du distribuerar din kod till.

1. Öppna Azure Portal vid [https://portal.azure.com](https://portal.azure.com) och logga in om det behövs.

1. I Sök fältet överst i Azure Portal anger du "App Service" och väljer sedan **app Services**.

    ![Portal Sök fältet och välja App Service](media/quickstart-python-portal/portal-search-bar.png)

1. På sidan **app Services** väljer du "**+ Lägg till**:

    ![Lägg till App Service kommando](media/quickstart-python-portal/add-app-service.png)

1. På sidan **skapa webbapp** utför du följande åtgärder:
    
    | Fält | Åtgärd |
    | --- | --- |
    | Prenumeration | Ange den prenumeration som du vill använda. |
    | Resursgrupp | Välj **Skapa ny** under List rutan. I popup-fönstret anger du "AppService-PythonQuickstart" och väljer **OK**. |
    | Name | Ange ett namn som är unikt för alla Azure, vanligt vis med en kombination av dina personliga eller företagets namn, till exempel *contoso-testapp-123*. |
    | Publicera | Välj **Kod**. |
    | Körningsstack | Välj **Python 3,8**. |
    | Operativsystem | Select **Linux** (python stöds bara på Linux). |
    | Region | Välj en region nära dig. |
    | Linux-plan | Välj en avsluta App Service-plan eller Använd **Skapa nytt** för att skapa en ny. Vi rekommenderar att du använder **Basic B1** -planen. |

    ![Skapa webb programs formulär på Azure Portal](media/quickstart-python-portal/create-web-app.png)

1. Längst ned på sidan väljer du **Granska + skapa**, granskar informationen och väljer sedan **skapa**.

1. När etableringen är klar väljer du **gå till resurs** för att navigera till den nya App Service sidan. Din webbapp innehåller bara en standard sida, så nästa steg är att distribuera exempel kod.

Har du problem? [Berätta för oss](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="deploy-the-sample-code"></a>Distribuera exempel koden

1. På sidan webb program på Azure Portal väljer du **distributions Center**:
    
    ![Kommandot Deployment Center på App Service-menyn](media/quickstart-python-portal/deployment-center-command.png)


1. På sidan **distributions Center** väljer du fliken **Inställningar** om den inte redan är öppen:

    ![Fliken Inställningar för distributions Center](media/quickstart-python-portal/deployment-center-settings-tab.png)

1. Under **källa** väljer du **GitHub**, sedan i formuläret **GitHub** som visas gör du följande:

    | Fält | Åtgärd |
    | --- | --- |
    | Loggade in som | Om du inte redan har loggat in på GitHub loggar du in nu eller väljer **Ändra konto* om det behövs. |
    | Organisation | Välj din GitHub-organisation om det behövs. |
    | Lagringsplats | Välj namnet på den exempel lagrings plats du förgrenade tidigare, antingen **python-dok-Hello-World** (kolv) eller **python-dok-Hello-django** (django). |
    | Gren | Välj **huvud**. |

    ![Käll konfiguration för GitHub i distributions Center](media/quickstart-python-portal/deployment-center-configure-github-source.png)

1. Klicka på **Spara** längst upp på sidan för att tillämpa inställningarna.:

    ![Spara GitHub-käll konfigurationen på distributions Center](media/quickstart-python-portal/deployment-center-configure-save.png)

1. Välj fliken **loggar** om du vill visa status för distributionen. Det tar några minuter att bygga och distribuera exemplet och ytterligare loggar visas under processen. Vid slut för ande bör loggarna Visa statusen **lyckades (aktiv)**:

    ![Fliken loggar för distributions Center](media/quickstart-python-portal/deployment-center-logs.png)

Har du problem? [Berätta för oss](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="browse-to-the-app"></a>Bläddra till appen

1. När distributionen är klar väljer du **Översikt** på menyn till vänster för att gå tillbaka till huvud sidan för webbappen.

1. Välj webb **adressen** som innehåller webbappens adress:

    ![Webbappens webb adress på översikts Sidan](media/quickstart-python-portal/web-app-url.png)

1. Kontrol lera att utdata från appen är "Hello, World!":

    ![App som körs efter den första distributionen](media/quickstart-python-portal/web-app-first-deploy-results.png)

Har du problem? Se först i [fel söknings guiden](configure-language-python.md#troubleshooting), annars kan du berätta för [oss](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="make-a-change-and-redeploy"></a>Gör en ändring och distribuera om

Eftersom du anslöt App Service till din lagrings plats, distribueras ändringar som du genomför i käll lagrings platsen automatiskt till webbappen.

1. Du kan göra ändringar direkt i den förgrenade lagrings platsen på GitHub, eller så kan du klona lagrings platsen lokalt, göra och genomföra ändringar och sedan skicka ändringarna till GitHub. Antingen resulterar metoden i en ändring i databasen som är ansluten till App Service.

1. Ändra appens meddelande från "Hello, World!" **i den förgrenade lagrings platsen** till "Hej, Azure!" så här:
    - Kolv (python-dok-Hello-World Sample): Ändra text strängen på rad 6 i *Application.py* -filen.
    - Django (python-dok-Hello-django Sample): Ändra text strängen på rad 5 i *views.py* -filen i *Hello* -mappen.

1. Genomför ändringen i lagrings platsen.

    Om du använder en lokal klon kan du också skicka ändringarna till GitHub.

1. Gå tillbaka till **distributions Center** på Azure Portal för webbappen, Välj fliken **loggar** och anteckna den nya distributions aktivitet som ska köras.

1. När distributionen är klar går du tillbaka till webbappens **översikts** sida, öppnar webbappens URL igen och studerar ändringarna i appen:

    ![App som körs efter omdistribution](media/quickstart-python-portal/web-app-second-deploy-results.png)

Har du problem? Se först i [fel söknings guiden](configure-language-python.md#troubleshooting), annars kan du berätta för [oss](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg skapade du Azure-resurser i en resurs grupp med namnet "AppService-PythonQuickstart", som visas på sidan *Översikt över* webbappen. Om du håller webbappen igång kommer du att få några pågående kostnader (se [App Service priser](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Om du inte tror att du behöver dessa resurser i framtiden väljer du namnet på resurs gruppen på **översikts** sidan för webbappar för att navigera till översikten över resurs grupper. Välj **ta bort resurs grupp** och följ anvisningarna.

![Ta bort resursgruppen](media/quickstart-python-portal/delete-resource-group.png)


Har du problem? [Berätta för oss](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: python (django)-webbapp med PostgreSQL](/azure/developer/python/tutorial-python-postgresql-app-portal)

> [!div class="nextstepaction"]
> [Konfigurera python-app](configure-language-python.md)

> [!div class="nextstepaction"]
> [Lägga till användar inloggning till en python-webbapp](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Självstudie: köra python-app i en anpassad behållare](tutorial-custom-container.md)
