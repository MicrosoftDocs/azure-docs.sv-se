---
title: Användar profil och ID för användning med Azure Notebooks för hands version
description: 'Hur du skapar och hanterar din användar profil och ditt användar-ID med Azure Notebooks, som blir en del av URL: en för delade antecknings böcker.'
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: 30d70365fcc0c72df01b4dc059b6e0f4cc607bba
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109512"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>Din profil och ditt användar-ID för Azure Notebooks för hands version

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

I ett kraftfullt, samverkande utrymme i Azure Notebooks visar din användar profil din offentliga avbildning för andra:

[![En Azure Notebooks profil sida](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Ditt användar-ID är en del av de URL: er som du använder för att dela projekt och antecknings böcker. I följande lista beskrivs olika URL-mönster:

- `https://notebooks.azure.com/<user_id>`: Din profil sida.
- `https://notebooks.azure.com/<user_id>/projects`: Dina projekt. Du ser alla projekt. andra användare ser bara dina offentliga projekt.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Projektfiler.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Kloner av ett enskilt projekt.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: HTML-förhands granskningen av en enskild antecknings bok eller fil.

## <a name="your-user-id"></a>Ditt användar-ID

När du loggar in på Azure Notebooks för första gången, tilldelas ditt konto automatiskt ett tillfälligt användar-ID, till exempel "Anon-idr3ca". Så länge som du har ett användar-ID som börjar med "Anon-", kommer Azure Notebooks att be dig ändra den när du loggar in:

![Prompt för att skapa ett användar-ID vid inloggning i Azure Notebooks](media/accounts/create-user-id.png)

Ett **Konfigurera användar-ID-** kommando visas också bredvid det tillfälliga användar namnet:

![Konfigurera användar-ID-kommandot som visas när du använder ett tillfälligt ID](media/accounts/configure-user-id-command.png)

Du kan också ändra ditt användar-ID när du vill på din profil sida.

Ett användar-ID måste bestå av mellan fyra och sexton bokstäver, siffror och bindestreck. Inga andra tecken tillåts och användar-ID: t får inte börja eller sluta med ett bindestreck eller använda flera bindestreck i en rad. Eftersom användar-ID: n är unika för alla Azure Notebooks-konton kan du se meddelandet "användar-ID används redan". (Meddelandet visas också om du försöker använda ett Microsoft-varumärke som användar-ID.) I dessa fall väljer du ett annat användar-ID.

> [!Important]
> Om du ändrar ditt ID ogiltig förklaras alla URL: er som du kanske har delat med ditt tidigare ID. Du kan ändra ditt ID tillbaka till ditt tidigare ID för att omverifiera länkarna. Det är dock möjligt att en annan användare ansöker ett oanvänt ID i tiden.

## <a name="your-profile"></a>Din profil

Din profil består av offentligt synlig information på URL: en `https://notebooks.azure.com/<user_id>` . Profil sidan visar även dina nyligen använda projekt och eventuella stjärnmärkt-projekt.

Om du vill redigera din profil använder du kommandot **Redigera profil information** på din profil sida. Avsnitten i profilen är följande:

| Avsnitt | Innehåll |
| --- | --- |
| Profil foto | En bild som visas på din profil sida. |
| Kontoinformation | Ditt visnings namn, användar-ID och offentliga e-postkonto. E-postkontot här ger andra användare ett medel att kontakta dig och kan skilja sig från det [konto](azure-notebooks-user-account.md) som du använder för att logga in på Azure Notebooks. |
| Profil information | Plats, företag, befattning, webbplats och en kort beskrivning av sig själv. |
| Sociala profiler | Dina GitHub-, Twitter-och Facebook-ID: n, om du vill dela dem. |
| Sekretessinställningar | Innehåller två kommandon:<ul><li>**Exportera min profil**: skapar och laddar ned en *. zip* -fil som innehåller all information som Azure Notebooks sparar i din profil, inklusive ditt fotografi, profil information och säkerhets loggar.</li><li>**Ta bort mitt konto**: all personlig information som lagras i Azure Notebooks tas bort permanent.</li></ul> |
| Aktivera plats funktioner | Gör att du kan styra aspekter av beteendet för Azure Notebooks:<ul><li>**Enhetlig klient del för bärbara datorer**: möjliggör snabbare start av antecknings böcker och bättre beständighet.</li><li>**Kör i JupyterLab som standard**: Azure Notebooks tillhandahåller ett enkelt användar gränssnitt som är lämpligt för de flesta användare. JupyterLab ger ett mer omfattande men mer komplicerat gränssnitt för erfarna användare.</li><li>**VNext webbplats**: aktiverar den moderna Webblayouten som visas i den här dokumentationen.</li></ul> |

## <a name="next-steps"></a>Nästa steg  

> [!div class="nextstepaction"]
> [Snabb start: exportera ett Jupyter Notebook-projekt](quickstart-export-jupyter-notebook-project.md)
