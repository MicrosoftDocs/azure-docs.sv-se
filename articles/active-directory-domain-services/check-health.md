---
title: Kontrol lera hälso tillståndet för Azure Active Directory Domain Services | Microsoft Docs
description: Lär dig hur du kontrollerar hälsan för en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän och förstå status meddelanden med hjälp av Azure Portal.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 92cc6157c590d3e05b4002e0f071c08e4ec23a16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620332"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Kontrol lera hälso tillståndet för en Azure Active Directory Domain Services hanterad domän

Azure Active Directory Domain Services (Azure AD DS) kör vissa bakgrunds aktiviteter för att hålla den hanterade domänen felfri och uppdaterad. I dessa uppgifter ingår säkerhets kopiering, säkerhets uppdateringar och synkronisering av data från Azure AD. Om det finns problem med den hanterade domänen i Azure AD DS kan de här uppgifterna inte slutföras. Om du vill granska och lösa eventuella problem kan du kontrol lera hälso statusen för en hanterad domän med hjälp av Azure Portal.

Den här artikeln visar hur du visar hälso status för Azure AD DS och förstår den information eller de aviseringar som visas.

## <a name="view-the-health-status"></a>Visa hälso status

Hälso status för en hanterad domän visas med hjälp av Azure Portal. Information om den senaste säkerhets kopierings tiden och synkroniseringen med Azure AD kan ses, tillsammans med eventuella aviseringar som indikerar ett problem med den hanterade domänens hälsa. Utför följande steg för att Visa hälso status för en hanterad domän:

1. I Azure Portal söker du efter och väljer **Azure AD Domain Services**.
1. Välj din hanterade domän, till exempel *aaddscontoso.com*.
1. På vänster sida av fönstret Azure AD DS-resurs väljer du **hälso tillstånd**. Skärm bilden i följande exempel visar en felhanterad domän och status för den senaste säkerhets kopieringen och Azure AD-synkronisering:

    ![Översikt över hälso sidan i Azure Portal som visar Azure Active Directory Domain Services status](./media/check-health/health-page.png)

Den *senaste utvärderade* tidsstämpeln för hälso sidan visar när den hanterade domänen senast kontrollerades. Hälso tillståndet för en hanterad domän utvärderas varje timme. Om du gör några ändringar i en hanterad domän väntar du tills nästa utvärderings cykel visar den uppdaterade hälso statusen.

Statusen längst upp till höger visar den övergripande hälsan för den hanterade domänen. Status faktorerna är alla befintliga aviseringar på din domän. Följande tabell innehåller information om tillgängliga status indikatorer:

| Status | Ikon | Förklaring |
| --- | :----: | --- |
| Körs | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Den hanterade domänen körs på rätt sätt och har inte några kritiska eller varnings aviseringar. Domänen kan innehålla informations aviseringar. |
| Kräver åtgärd (varning) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Det finns inga kritiska aviseringar på den hanterade domänen, men det finns en eller flera varnings aviseringar som ska åtgärdas. |
| Behöver åtgärdas (kritisk) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Det finns en eller flera kritiska aviseringar för den hanterade domän som måste åtgärdas. Du kan också ha varnings-och/eller informations aviseringar. |
| Deploy | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Den hanterade domänen distribueras. |

## <a name="understand-monitors-and-alerts"></a>Förstå övervakare och aviseringar

Hälso tillståndet för en hanterad domän visar två typer av information – *övervakare* och *aviseringar*. Övervakare visar den tid som grundläggande bakgrunds aktiviteter har slutförts. Aviseringar ger information eller förslag för att förbättra stabiliteten i den hanterade domänen.

### <a name="monitors"></a>Övervakare

Övervakare är områden i en hanterad domän som kontrol leras regelbundet. Om det finns aktiva aviseringar för den hanterade domänen kan det leda till att en av övervakarna rapporterar ett problem. Azure AD DS har för närvarande Övervakare för följande områden:

* Backup
* Synkronisering med Azure AD

#### <a name="backup-monitor"></a>Övervakare för säkerhets kopiering

Säkerhets kopierings övervakaren kontrollerar att automatiserade regelbundna säkerhets kopieringar av den hanterade domänen har körts. Följande tabell innehåller information om den tillgängliga statusen för säkerhets kopierings övervakaren:

| Detalj värde | Förklaring |
| --- | --- |
| Aldrig säkerhets kopie rad | Det här läget är normalt för nya hanterade domäner. Den första säkerhets kopieringen ska skapas 24 timmar efter att den hanterade domänen har distribuerats. Om den här statusen kvarstår [öppnar du en support förfrågan för Azure][azure-support]. |
| Den senaste säkerhets kopieringen tog 1 till 14 dagar sedan | Det här tidsintervallet är förväntad status för säkerhets kopierings övervakaren. Automatiserade regelbundna säkerhets kopieringar ska ske under den här perioden. |
| Den senaste säkerhets kopieringen vidtogs över 14 dagar sedan. | Ett TimeSpan som är längre än två veckor indikerar att det är problem med de automatiserade regelbundna säkerhets kopieringarna. Aktiva kritiska aviseringar kan förhindra att den hanterade domänen säkerhets kopie ras. Lös eventuella aktiva aviseringar för den hanterade domänen. Om säkerhets kopierings övervakaren inte uppdaterar statusen för att rapportera en ny säkerhets kopia [öppnar du en support förfrågan för Azure][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Synkronisering med Azure AD Monitor

En hanterad domän synkroniseras regelbundet med Azure Active Directory. Antalet användare och grupp objekt och antalet ändringar som gjorts i Azure AD-katalogen sedan den senaste synkroniseringen, påverkar hur lång tid det tar att synkronisera. Om den hanterade domänen senast synkroniserades över tre dagar sedan, kontrollerar du och löser eventuella aktiva aviseringar. Om övervakaren för synkronisering inte uppdaterar statusen för att visa en senaste synkronisering när du har besvarat alla aktiva aviseringar [öppnar du en support förfrågan för Azure][azure-support].

### <a name="alerts"></a>Aviseringar

Aviseringar genereras för problem i en hanterad domän som måste åtgärdas för att tjänsten ska kunna köras korrekt. Varje avisering förklarar problemet och ger en URL som beskriver specifika steg för att lösa problemet. Mer information om möjliga aviseringar och deras resolutioner finns i [fel söknings aviseringar](troubleshoot-alerts.md).

Aviseringar om hälso status kategoriseras i följande nivåer av allvarlighets grad:

 * **Kritiska varningar** är problem som kraftigt påverkar den hanterade domänen. Dessa aviseringar bör åtgärdas omedelbart. Azure-plattformen kan inte övervaka, hantera, korrigera och synkronisera den hanterade domänen förrän problemen är lösta.
 * **Varnings aviseringar** meddelar dig om problem som kan påverka de hanterade domän åtgärderna om problemet kvarstår. De här aviseringarna ger även rekommendationer för att skydda den hanterade domänen.
 * **Informations aviseringar** är aviseringar som inte påverkar den hanterade domänen negativt. Informations aviseringar ger viss insikt om vad som händer i den hanterade domänen.

## <a name="next-steps"></a>Nästa steg

Mer information om aviseringar som visas på sidan hälso status finns i [lösa aviseringar på din hanterade domän][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
