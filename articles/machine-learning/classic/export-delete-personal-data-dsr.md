---
title: 'ML Studio (klassisk): exportera & ta bort dina data – Azure'
description: 'Produkt data som lagras av Azure Machine Learning Studio (klassisk) är tillgängliga för export och borttagning via Azure Portal och även via autentiserade REST-API: er. Telemetri-data kan nås via Azures sekretess Portal. Den här artikeln visar hur du gör.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: b2d797cc6cb45a76a61a235e7c4c66ac6f243580
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519872"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Exportera och ta bort användar data i produkten från Azure Machine Learning Studio (klassisk)

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  




Du kan ta bort eller exportera data från en produkt som lagras av Azure Machine Learning Studio (klassisk) med hjälp av Azure Portal, Studio (klassiskt) gränssnitt, PowerShell och autentiserade REST-API: er. Den här artikeln beskriver hur du gör. 

Telemetri-data kan nås via Azures sekretess Portal. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Vilka typer av användar data samlar Studio (klassisk) in?

För den här tjänsten består användar data av information om användare som har behörighet att komma åt arbets ytor och telemetri poster för användar interaktioner med tjänsten.

Det finns två typer av användar data i Machine Learning Studio (klassisk):
- **Personliga konto data:** Konto-ID och e-postadresser kopplade till ett konto.
- **Kund information:** Data som du överförde för att analysera.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Studio (klassiska) konto typer och hur data lagras

Det finns tre typer av konton i Machine Learning Studio (klassisk). Vilken typ av konto du har bestämmer hur dina data lagras och hur du kan ta bort eller exportera dem.

- En **gäst arbets yta** är ett kostnads fritt, anonymt konto. Du registrerar dig utan att ange autentiseringsuppgifter, till exempel en e-postadress eller ett lösen ord.
    -  Data rensas när gäst arbets ytan upphör att gälla.
    - Gäst användare kan exportera kund information via användar gränssnittet, REST-API: erna eller PowerShell-paketet.
- En **kostnads fri arbets yta** är ett kostnads fritt konto som du loggar in på med Microsoft-konto autentiseringsuppgifter – en e-postadress och ett lösen ord.
    - Du kan exportera och ta bort personliga och kunddata som omfattas av DSR-begäranden (data subject Rights).
    - Du kan exportera kund information via användar gränssnittet, REST-API: erna eller PowerShell-paketet.
    - För kostnads fria arbets ytor som inte använder Azure AD-konton kan telemetri exporteras med hjälp av sekretess portalen.
    - När du tar bort arbets ytan tar du bort all personlig kund information.
- En **standard arbets yta** är ett betalt konto som du kommer åt med inloggnings uppgifter.
    - Du kan exportera och ta bort personliga och kunddata, vilka är föremål för DSR-begäranden.
    - Du kan komma åt data via Azures sekretess Portal
    - Du kan exportera personliga och kunddata via användar gränssnittet, REST-API: erna eller PowerShell-paketet
    - Du kan ta bort dina data i Azure Portal.

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>Ta bort arbets ytans data i Studio (klassisk) 

### <a name="delete-individual-assets"></a>Ta bort enskilda till gångar

Användare kan ta bort till gångar i en arbets yta genom att markera dem och sedan välja knappen Ta bort.

![Ta bort till gångar i Machine Learning Studio (klassisk)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Ta bort en hel arbets yta

Användare kan också ta bort hela arbets ytan:
- Betald arbets yta: ta bort via Azure Portal.
- Kostnads fri arbets yta: Använd knappen Ta bort i fönstret **Inställningar** .

![Ta bort en kostnads fri arbets yta i Machine Learning Studio (klassisk)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Exportera Studio (klassiska) data med PowerShell
Använd PowerShell för att exportera all information till ett bärbart format från Azure Machine Learning Studio (klassisk) med hjälp av kommandon. Mer information finns i [PowerShell-modulen för Azure Machine Learning Studio (klassisk)](powershell-module.md) artikel.

## <a name="next-steps"></a>Nästa steg

Dokumentation som omfattar fakturering av webb tjänster och åtagande plan finns i [Azure Machine Learning Studio (klassisk) REST API referens](/rest/api/machinelearning/).