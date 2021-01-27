---
title: Konfigurera e-postaviseringar för Azure Security Center aviseringar
description: Lär dig hur du finjusterar de typer av e-postmeddelanden som skickas ut av Azure Security Center för säkerhets aviseringar.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2020
ms.author: memildin
ms.openlocfilehash: 72ded01b141aafb7fd3e4d761882a10eaf0c4b33
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920417"
---
# <a name="configure-email-notifications-for-security-alerts"></a>Konfigurera e-postaviseringar för säkerhets aviseringar 

Säkerhets aviseringar måste kontakta rätt personer i din organisation. Som standard skickar Security Center e-post till prenumerations ägare varje gång en avisering med hög allvarlighets grad utlöses för prenumerationen. På den här sidan förklaras hur du anpassar de här meddelandena.

Om du vill definiera egna inställningar för e-postmeddelanden Azure Security Center kan du med hjälp av sidan Inställningar för **e-postavisering** välja:

- **_vem som_ ska meddelas** – e-postmeddelanden kan skickas till utvalda individer eller till någon med en angiven Azure-roll för en prenumeration. 
- **_vad_ de ska meddelas om** – ändra allvarlighets graderna som Security Center ska skicka ut meddelanden till.

För att undvika aviserings utmattning begränsar Security Center volymen utgående e-post. Security Center skickar för varje prenumeration:

- högst ett e-postmeddelande per **6 timmar** (4 e-postmeddelanden per dag) för aviseringar med **hög allvarlighets grad**
- högst ett e-postmeddelande per **12 timmar** (2 e-postmeddelanden per dag) för aviseringar med **medelhög allvarlighets grad**
- högst ett e-postmeddelande per **24 timmar** för aviseringar med **låg allvarlighets grad**

:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="Konfigurera information om den kontakt som får e-postmeddelanden om säkerhets aviseringar." :::
 
## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|Kostnadsfri|
|Nödvändiga roller och behörigheter:|**Säkerhetsadministratör**<br>**Prenumerations ägare** |
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||


## <a name="customize-the-security-alerts-email-notifications"></a>Anpassa e-postaviseringar om säkerhets varningar<a name="email"></a>

Du kan skicka e-postmeddelanden till enskilda användare eller till alla användare med vissa Azure-roller.

1. Välj den relevanta prenumerationen från Security Center **pris & inställningar** och välj **e-postaviseringar**.

1. Definiera mottagarna för dina meddelanden med ett eller båda av dessa alternativ:

    - Välj från de tillgängliga rollerna i list rutan.
    - Ange vissa e-postadresser avgränsade med kommatecken. Det finns ingen gräns för antalet e-postadresser som du kan ange.

1. Om du vill använda säkerhets kontakt information för din prenumeration väljer du **Spara**.


## <a name="see-also"></a>Se även
Mer information om säkerhets aviseringar finns i följande sidor:

- [Säkerhets aviseringar – en referens guide](alerts-reference.md)– lär dig mer om säkerhets aviseringar som du kan se i Azure Security Center skydds modul för hot
- [Hantera och åtgärda säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)– lär dig hantera och reagera på säkerhets aviseringar
- [Arbets flödes automatisering](workflow-automation.md)– automatisera svar på aviseringar med anpassad meddelande logik