---
title: Hanterings verktyg för virtuella Windows-datorer (klassiskt) – Azure
description: Fel sökning av problem med hanterings verktyget för virtuella Windows-datorer (klassisk).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6f03284103b4a2aa6900bf1ba5c50a4688c50b0e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88005490"
---
# <a name="troubleshoot-the-windows-virtual-desktop-classic-management-tool"></a>Felsöka hanterings verktyget för Windows Virtual Desktop (klassiskt)

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows.

Den här artikeln beskriver problem som kan uppstå när du distribuerar Windows hanterings verktyg för virtuella skriv bord och hur du åtgärdar dem.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Fel: hanterings verktygets tjänster har kon figurer ATS men automatisk installation Miss lyckas

När du har konfigurerat tjänster för hanterings verktyget men automatisk installation Miss lyckas visas följande fel meddelande:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Det innebär vanligt vis ett av följande två saker:

- Användaren har ägar behörigheter för prenumerationen och den globala administratören på klient nivå, men de kan inte logga in på Azure.
- Användar konto inställningarna har Multi-Factor Authentication aktiverat.

Så här löser du detta:

1. Se till att användaren som du skapade för Azure Active Directory användarens huvud namn har prenumerations nivån "deltagare".
2. Logga in på <portal.azure.com> med UPN-kontot för att kontrol lera konto inställningarna och se till att Multi-Factor Authentication inte är aktiverat. Om den är aktive rad kan du stänga av den.
3. Besök sidan för godkännande av Windows-datorer och kontrol lera att servern och klientens appar har tillstånd.
4. Gå igenom självstudien [distribuera ett hanterings verktyg](manage-resources-using-ui.md) om problemet kvarstår och distribuera verktyget igen.

## <a name="error-job-with-specified-id-already-exists"></a>Fel: det finns redan ett jobb med angivet ID

Om användaren ser fel meddelandet "det finns redan ett jobb med angivet ID", beror det på att de inte angav ett unikt namn i parametern "program namn" när du distribuerar mallen.

Du kan åtgärda detta genom att distribuera om hanterings verktyget med parametern "program namn" ifylld.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Uppskjuten medgivande-prompt vid öppnande av hanterings verktyg

När du distribuerar hanterings verktyget kanske inte medgivande meddelandet öppnas direkt. Det innebär att Azure Web App-tjänsten tar längre tid än vanligt att läsa in. Frågan bör visas när Azure Web har lästs in.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>Användaren kan inte distribuera hanterings verktyget i regionen USA, östra

Om en kund sätter region till USA, östra, kan de inte distribuera hanterings verktyget.

Du kan åtgärda detta genom att distribuera hanterings verktyget i en annan region. Att omdistribuera verktyget i en annan region bör inte påverka användar upplevelsen.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om eskalerade spår i [fel söknings översikt, feedback och support](troubleshoot-set-up-overview-2019.md).
- Lär dig hur du rapporterar problem med Windows-verktyg för virtuella skriv bord i [arm-mallar för Fjärrskrivbordstjänster](https://github.com/Azure/RDS-Templates/blob/master/README.md).
- Information om hur du distribuerar hanterings verktyget finns i [distribuera ett hanterings verktyg](manage-resources-using-ui.md).