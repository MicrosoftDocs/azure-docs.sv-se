---
title: Felsöka program ändrings analys – Azure Monitor
description: Lär dig hur du felsöker problem i program ändrings analys.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 7200978ce31a47f7bd0d023cecf359b10a1c96de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100521185"
---
# <a name="troubleshoot-application-change-analysis-preview"></a>Felsöka program ändrings analys (för hands version)

## <a name="having-trouble-registering-microsoft-change-analysis-resource-provider-from-change-history-tab"></a>Problem med att registrera Microsoft. Ändra Analysis Resource provider från fliken ändrings historik

Om det är första gången du visar ändrings historik efter dess integrering med program ändrings analys, kommer den automatiskt att registrera en resurs leverantör **Microsoft. ChangeAnalysis**. I sällsynta fall kan det Miss kan inträffa av följande orsaker:

- **Du har inte tillräcklig behörighet för att registrera Microsoft. ChangeAnalysis Resource Provider**. Det här fel meddelandet innebär att din roll i den aktuella prenumerationen inte har det definitions område för **Microsoft. support/register/åtgärd** som är associerat med den. Detta kan inträffa om du inte är ägare till en prenumeration och har delade åtkomst behörigheter genom en medarbetare (det vill säga Visa åtkomst till en resurs grupp). Du kan åtgärda detta genom att kontakta Prenumerationens ägare för att registrera **Microsoft. ChangeAnalysis** -resurs leverantören. Detta kan göras i Azure Portal via **prenumerationer | Resurs leverantörer** och Sök efter ```Microsoft.ChangeAnalysis``` och registrera i användar gränssnittet, eller via Azure PowerShell eller Azure CLI.

    Registrera resurs leverantör via PowerShell:
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **Det gick inte att registrera Microsoft. ChangeAnalysis Resource Provider**. Det här meddelandet innebär att något Miss lyckas omedelbart som UI skickat begäran om att registrera resurs leverantören, och det är inte relaterat till behörighets problem. Det kan förmodligen vara ett tillfälligt problem med Internet anslutningen. Försök att uppdatera sidan och kontrol lera din Internet anslutning. Om felet kvarstår kan du kontakta changeanalysishelp@microsoft.com

- **Det tar längre tid än förväntat**. Det här meddelandet innebär att registreringen tar längre tid än två minuter. Detta är ovanligt, men det innebär inte nödvändigt vis något att något har gått fel. Du kan gå till **prenumerationer | Resurs leverantör** för att kontrol lera registrerings statusen för **Microsoft. ChangeAnalysis** Resource Provider. Du kan prova att använda användar gränssnittet för att avregistrera, registrera om eller uppdatera för att se om det hjälper. Kontakta supporten om problemet kvarstår changeanalysishelp@microsoft.com .
    ![Felsök RP-registreringen tar för lång tid](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![Skärm bild av verktyget diagnostisera och lösa problem för en virtuell dator med fel söknings verktyg valt.](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Skärm bild av panelen för att analysera nya ändringar av fel söknings verktyget för en virtuell dator.](./media/change-analysis/analyze-recent-changes.png)

## <a name="azure-lighthouse-subscription-is-not-supported"></a>Azure Lighthouse-prenumeration stöds inte

- **Det gick inte att fråga Microsoft. ChangeAnalysis Resource Provider** med meddelande *Azure Lighthouse-prenumerationen stöds inte. ändringarna är bara tillgängliga i prenumerationens hem klient organisation*. Det finns en begränsning just nu för att kunna registrera Analysis Resource Provider via Azure Lighthouse-prenumerationen för användare som inte finns i hem klient organisationen. Vi arbetar på att lösa den här begränsningen. Om detta är ett blockerings problem finns det en lösning som inbegriper att skapa ett huvud namn för tjänsten och uttryckligen tilldela rollen för att tillåta åtkomst.  Kontakta changeanalysishelp@microsoft.com om du vill veta mer om det.

## <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>Ett fel uppstod vid hämtning av ändringar. Uppdatera den här sidan eller kom tillbaka senare om du vill visa ändringar

Detta är det allmänna fel meddelandet som presenteras av program ändrings analys tjänsten när det inte gick att läsa in ändringar. Några kända orsaker är:

- Internet anslutnings fel från klient enheten
- Change Analysis Service är tillfälligt otillgängligt om du uppdaterar sidan efter några minuter åtgärdar det här problemet vanligt vis. Om felet kvarstår kan du kontakta changeanalysishelp@micorosoft.com

## <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>Du har inte tillräcklig behörighet för att visa vissa ändringar. Kontakta administratören för Azure-prenumerationen

Detta är det allmänna obehöriga fel meddelandet, som förklarar att den aktuella användaren inte har tillräcklig behörighet för att visa ändringen. Åtkomst till minst läsare krävs för resursen för att Visa infrastruktur ändringar som returneras av Azure Resource Graph och Azure Resource Manager. För webb program ändringar i gäst filen och konfigurations ändringar krävs minst deltagar roll.

## <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>Det gick inte att registrera Microsoft. ChangeAnalysis Resource Provider

Det här meddelandet innebär att något Miss lyckas omedelbart som UI skickat begäran om att registrera resurs leverantören, och det är inte relaterat till behörighets problem. Det kan förmodligen vara ett tillfälligt problem med Internet anslutningen. Försök att uppdatera sidan och kontrol lera din Internet anslutning. Om felet kvarstår kan du kontakta changeanalysishelp@microsoft.com

## <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>Du har inte tillräcklig behörighet för att registrera Microsoft. ChangeAnalysis Resource Provider. Kontakta administratören för Azure-prenumerationen

Det här fel meddelandet innebär att din roll i den aktuella prenumerationen inte har det definitions område för **Microsoft. support/register/åtgärd** som är associerat med den. Detta kan inträffa om du inte är ägare till en prenumeration och har delade åtkomst behörigheter genom en medarbetare (det vill säga Visa åtkomst till en resurs grupp). Du kan åtgärda detta genom att kontakta Prenumerationens ägare för att registrera **Microsoft. ChangeAnalysis** -resurs leverantören. Detta kan göras i Azure Portal via **prenumerationer | Resurs leverantörer** och Sök efter ```Microsoft.ChangeAnalysis``` och registrera i användar gränssnittet, eller via Azure PowerShell eller Azure CLI.

Registrera resurs leverantör via PowerShell:

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure Resource Graph](../../governance/resource-graph/overview.md), som hjälper dig att analysera energi ändringar.