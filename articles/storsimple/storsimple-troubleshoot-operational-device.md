---
title: Felsöka en distribuerad StorSimple-enhet | Microsoft Docs
description: Beskriver hur du diagnostiserar och åtgärdar fel som uppstår på en StorSimple enhet som för närvarande har distribuerats och fungerar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 6cd1d981737db1e7c852931ecc2449e0afc03530
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956797"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Felsöka en fungerande StorSimple-enhet
> [!NOTE]
> Den klassiska portalen för StorSimple är inaktuell. Dina StorSimple-enhetshanterare flyttas automatiskt till nya Azure Portal enligt utfasningsschemat. Du kommer att få ett e-postmeddelande och ett portalmeddelande om flytten. Det här dokumentet kommer också att dras tillbaka snart. Om du har frågor om flytten kan du läsa [Vanliga frågor och svar: Flytta till Azure Portal](./index.yml).

## <a name="overview"></a>Översikt
Den här artikeln innehåller användbara fel söknings anvisningar för att lösa konfigurations problem som kan uppstå efter att din StorSimple-enhet har distribuerats och fungerar. Det beskriver vanliga problem, möjliga orsaker och rekommenderade steg som hjälper dig att lösa problem som kan uppstå när du kör Microsoft Azure StorSimple. Den här informationen gäller både för den lokala fysiska enheten StorSimple och den virtuella StorSimple-enheten.

I slutet av den här artikeln hittar du en lista över felkoder som du kan stöta på under Microsoft Azure StorSimple åtgärd, samt de steg som du kan vidta för att lösa felen. 

## <a name="setup-wizard-process-for-operational-devices"></a>Installations guide process för operativa enheter
Du kan använda installations guiden ([Invoke-HcsSetupWizard][1]) för att kontrol lera enhets konfigurationen och vidta lämpliga åtgärder om det behövs.

När du kör installations guiden på en tidigare konfigurerad och fungerande enhet är process flödet annorlunda. Du kan bara ändra följande poster:

* IP-adress, nätmask och gateway
* Primär DNS-Server
* Primär NTP-server
* Valfri webbproxy-konfiguration

Installations guiden utför inte åtgärder som rör insamling av lösen ord och enhets registrering.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Fel som inträffar vid efterföljande körningar av installations guiden
I följande tabell beskrivs de fel som du kan stöta på när du kör installations guiden på en fungerande enhet, möjliga orsaker till felen och rekommenderade åtgärder för att lösa dem. 

| Nej. | Fel meddelande eller villkor | Möjliga orsaker | Rekommenderad åtgärd |
|:--- |:--- |:--- |:--- |
| 1 |Fel 350032: enheten har redan inaktiverats. |Det här felet visas om du kör installations guiden på en enhet som är inaktive rad. |[Kontakta Microsoft Support](./storsimple-8000-contact-microsoft-support.md) för nästa steg. En inaktive rad enhet kan inte placeras i tjänsten. Det kan krävas en fabriks återställning innan enheten kan aktive ras igen. |
| 2 |Invoke-HcsSetupWizard: ERROR_INVALID_FUNCTION (undantag från HRESULT: 0x80070001) |Uppdateringen av DNS-servern fungerar inte. DNS-inställningar är globala inställningar och tillämpas på alla aktiverade nätverks gränssnitt. |Aktivera gränssnittet och tillämpa DNS-inställningarna igen. Detta kan störa nätverket för andra aktiverade gränssnitt eftersom dessa inställningar är globala. |
| 3 |Enheten verkar vara online i StorSimple Manager Service Portal, men när du försöker slutföra den lägsta konfigurationen och spara konfigurationen, Miss lyckas åtgärden. |Under den inledande installationen konfigurerades inte webbproxyn, även om det fanns en faktisk proxyserver på plats. |Använd [cmdleten test-HcsmConnection][2] för att hitta felet. [Kontakta Microsoft Support](./storsimple-8000-contact-microsoft-support.md) om du inte kan rätta till problemet. |
| 4 |Invoke-HcsSetupWizard: värdet ligger inte inom det förväntade intervallet. |En felaktig nätmask genererar det här felet. Möjliga orsaker är: <ul><li> Nät masken saknas eller är tom.</li><li>IPv6-prefixets format är felaktigt.</li><li>Gränssnittet är moln aktiverat, men gatewayen saknas eller är felaktig.</li></ul>Observera att DATA 0 automatiskt är Cloud-aktiverade om de konfigureras via installations guiden. |Identifiera problemet genom att använda Subnet 0.0.0.0 eller 256.256.256.256 och sedan titta på utdata. Ange korrekta värden för under näts mask, gateway och IPv6-prefix efter behov. |

## <a name="error-codes"></a>Felkoder
Fel visas i numerisk ordning.

| Fel nummer | Fel text eller beskrivning | Rekommenderad användar åtgärd |
|:--- |:--- |:--- |
| 10502 |Ett fel uppstod vid åtkomst till ditt lagrings konto. |Vänta några minuter och försök sedan igen. Om felet kvarstår kontaktar du Microsoft Support för nästa steg. |
| 40017 |Säkerhets kopierings åtgärden misslyckades eftersom det inte gick att hitta en volym som angetts i säkerhets kopierings principen på enheten. |Försök att säkerhetskopiera igen. kontakta Microsoft Support om felet kvarstår. för nästa steg. |
| 40018 |Säkerhets kopierings åtgärden misslyckades eftersom ingen av de volymer som angavs i säkerhets kopierings principen hittades på enheten. |Försök att säkerhetskopiera igen. kontakta Microsoft Support om felet kvarstår. för nästa steg. |
| 390061 |Systemet är upptaget eller inte tillgängligt. |Vänta några minuter och försök sedan igen. Om felet kvarstår kontaktar du Microsoft Support för nästa steg. |
| 390143 |Ett fel har uppstått med felkoden 390143. (Okänt fel.) |Om felet kvarstår kontaktar du Microsoft Support för nästa steg. |

## <a name="next-steps"></a>Nästa steg
Om du inte kan lösa problemet [kontaktar du Microsoft Support](./storsimple-8000-contact-microsoft-support.md) för att få hjälp. 

[1]: /previous-versions/windows/powershell-scripting/dn688135(v=wps.630)
[2]: /previous-versions/windows/powershell-scripting/dn715782(v=wps.630)