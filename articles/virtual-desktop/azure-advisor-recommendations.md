---
title: Azure Advisor Windows Virtual Desktop – genom gång – Azure
description: Så här löser du Azure Advisor rekommendationer för virtuella Windows-datorer.
author: Heidilohr
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 760e0212d2d863e6b869c23c2e523a0e056a28ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90033038"
---
# <a name="how-to-resolve-azure-advisor-recommendations"></a>Så här löser du Azure Advisor rekommendationer

Den här artikeln beskriver hur du kan lösa rekommendationer som visas i Azure Advisor för virtuella Windows-datorer.

## <a name="no-validation-environment-enabled"></a>"Ingen validerings miljö aktive rad"

>[!div class="mx-imgBorder"]
>![En skärm bild av den Azure Advisor operativa kompetens sidan. Rekommendationen "ingen validerings miljö aktive rad" är markerad i rött.](media/no-validation-environment.png)

Den här rekommendationen visas under drift kvalitet. Rekommendationen bör också visa ett varnings meddelande som detta:

"Du har ingen validerings miljö aktive rad i den här prenumerationen. När du har gjort dina värdar valde du **Nej** för "validerings miljö" på fliken Egenskaper. Se till att du har minst en adresspool med en validerings miljö där du kan testa eventuella problem för att säkerställa affärs kontinuiteten genom distributioner av virtuella Windows-datorer.

Du kan få det här varnings meddelandet att gå bort genom att aktivera en validerings miljö i någon av dina värdar.

Så här aktiverar du en validerings miljö:

1. Gå till din Azure Portal start sida och välj den modempool som du vill ändra.

2. Välj sedan den modempool som du vill ändra från en produktions miljö till en validerings miljö.

3. I din värddator väljer du **Egenskaper** i den vänstra kolumnen. Bläddra sedan nedåt tills du ser "validerings miljö". Välj **Ja** och välj sedan **Använd**.

>[!div class="mx-imgBorder"]
>![En skärm bild av menyn egenskaper. "Validerings miljön" är markerad i rött och bubblan "Ja" är markerad.](media/validation-yes.png)

Dessa ändringar gör inte att varningen försvinner omedelbart, men den bör försvinna. Azure Advisor uppdateringar två gånger per dag. Tills dess kan du skjuta upp eller stänga av rekommendationen manuellt. Vi rekommenderar att du låter rekommendationerna gå vidare. På så sätt kan Azure Advisor meddela dig om det kommer över eventuella problem när inställningarna ändras.

## <a name="not-enough-production-non-validation-environments-enabled"></a>"Inte tillräckligt med produktions miljöer (ej verifierade)"

Den här rekommendationen visas under drift kvalitet.

I den här rekommendationen visas varnings meddelandet av någon av följande orsaker:

- Du har för många värdar i validerings miljön.
- Du har inga pooler för produktions värdar.

Vi rekommenderar att användarna har färre än hälften av deras lagringspooler i en validerings miljö.

Så här löser du den här varningen:

1. Gå till din Azure Portal start sida.

2. Välj de lagringspooler som du vill ändra från validering till produktion.

3. I din värddator väljer du fliken **Egenskaper** i kolumnen på höger sida av skärmen. Bläddra sedan nedåt tills du ser "validerings miljö". Välj **Nej** och välj sedan **Använd**.

>[!div class="mx-imgBorder"]
>![En skärm bild av menyn egenskaper. "Validerings miljön" är markerad i rött och bubblan "No" är markerad.](media/validation-no.png)

Dessa ändringar gör inte att varningen försvinner omedelbart, men den bör försvinna. Azure Advisor uppdateringar två gånger per dag. Tills dess kan du skjuta upp eller stänga av rekommendationen manuellt. Vi rekommenderar att du låter rekommendationerna gå vidare. På så sätt kan Azure Advisor meddela dig om det kommer över eventuella problem när inställningarna ändras.

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>"Det finns inte tillräckligt många länkar för att implementera den virtuella datorn"

Den här rekommendationen visas under drift kvalitet.

Du måste avblockera vissa URL: er för att kontrol lera att den virtuella datorn (VM) fungerar korrekt. Du kan se listan i [listan över säkra URL:](safe-url-list.md)er. Om URL: erna inte är avblockerade fungerar inte den virtuella datorn som den ska.

Lös den här rekommendationen genom att avblockera alla URL: er i [listan över säkra URL](safe-url-list.md): er. Du kan använda service tag eller FQDN-taggar för att avblockera URL: er.

## <a name="propose-new-recommendations"></a>Föreslå nya rekommendationer

Du kan hjälpa oss att förbättra Azure Advisor genom att skicka idéer till rekommendationer. Din rekommendation kan hjälpa en annan användare från en tålig plats. Om du vill skicka ett förslag går du till [vårt UserVoice-forum](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations) och fyller i formuläret för överföring. När du fyller i formuläret ser du till att ge oss så mycket information som möjligt.

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer djupgående guider om hur du löser vanliga problem kan du läsa [fel söknings översikt, feedback och support för Windows Virtual Desktop](troubleshoot-set-up-overview.md).
