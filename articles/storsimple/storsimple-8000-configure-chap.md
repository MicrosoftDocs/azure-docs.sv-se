---
title: Konfigurera CHAP för enhet med StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du konfigurerar Challenge Handshake Authentication Protocol (CHAP) på en StorSimple-enhet.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 694d63dacedd8ffcd075e73bb91b5ab0a089b58c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91766501"
---
# <a name="configure-chap-for-your-storsimple-device"></a>Konfigurera CHAP för din StorSimple-enhet

I den här självstudien beskrivs hur du konfigurerar CHAP för din StorSimple-enhet. Proceduren som beskrivs i den här artikeln gäller StorSimple 8000-seriens enheter.

CHAP står för Challenge Handshake Authentication Protocol. Det är ett autentiseringsschema som används av-servrar för att verifiera identiteten för fjärrklienter. Verifieringen baseras på ett delat lösen ord eller en hemlighet. CHAP kan vara ett enkelriktat (enkelriktat) eller ömsesidigt (dubbelriktat). Ett sätt CHAP är när målet autentiserar en initierare. I ömsesidig eller omvänd CHAP autentiserar målet initieraren och sedan autentiserar initieraren målet. Autentisering av initierare kan implementeras utan mål-autentisering. Target Authentication kan dock endast implementeras om autentisering av initierare också implementeras.

Som bästa praxis rekommenderar vi att du använder CHAP för att förbättra iSCSI-säkerheten.

> [!NOTE]
> Tänk på att IPSEC inte stöds för närvarande på StorSimple-enheter.

CHAP-inställningarna på StorSimple-enheten kan konfigureras på följande sätt:

* Enkelriktad eller enkelriktad autentisering
* Dubbelriktad eller ömsesidig eller omvänd autentisering

I vart och ett av dessa fall måste portalen för enheten och serverns iSCSI-initierarkort vara konfigurerad. De detaljerade stegen för den här konfigurationen beskrivs i följande självstudie.

## <a name="unidirectional-or-one-way-authentication"></a>Enkelriktad eller enkelriktad autentisering

I enkelriktad autentisering autentiserar målet initieraren. Den här autentiseringen kräver att du konfigurerar inställningarna för CHAP-initieraren på StorSimple-enheten och iSCSI-initierarens program vara på värden. Detaljerade procedurer för StorSimple-enheten och Windows-värden beskrivs härnäst.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Konfigurera enheten för enkelriktad autentisering

1. I Azure Portal går du till din StorSimple Enhetshanteraren-tjänst. Klicka på **enheter** och markera och klicka på en enhet som du vill konfigurera CHAP för. Gå till **enhets inställningar > säkerhet**. Klicka på **CHAP** i bladet **säkerhets inställningar** .
   
    ![CHAP-initierare](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. I **CHAP** -bladet och i avsnittet **CHAP Initiator** :
   
   1. Ange ett användar namn för din CHAP-initierare.
   2. Ange ett lösen ord för din CHAP-initierare.
      
      > [!IMPORTANT]
      > CHAP-användarnamnet måste innehålla färre än 233 tecken. CHAP-lösenordet måste vara mellan 12 och 16 tecken. Ett längre användar namn eller lösen ord resulterar i att ett autentiseringsfel uppstår på Windows-värden.
   
   3. Bekräfta lösenordet.

       ![CHAP-initierare 2](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Klicka på **Spara**. Ett bekräftelse meddelande visas. Spara ändringarna genom att klicka på **OK**.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Konfigurera enkelriktad autentisering på Windows-värd servern
1. Starta iSCSI-initieraren på Windows-värd servern.
2. I fönstret **Egenskaper för iSCSI-initierare** , utför följande steg:
   
   1. Klicka på fliken **identifiering** .
      
       ![iSCSI-initieraregenskaper](./media/storsimple-configure-chap/IC740944.png)
   2. Klicka på **identifiera Portal**.
3. I dialog rutan **identifiera mål Portal** :
   
   1. Ange enhetens IP-adress.
   2. Klicka på **Avancerat**.
      
       ![Identifiera mål Portal](./media/storsimple-configure-chap/IC740945.png)
4. I dialog rutan **Avancerade inställningar** :
   
   1. Markera kryss rutan **Aktivera CHAP-inloggning** .
   2. I fältet **namn** anger du det användar namn som du angav för CHAP-initieraren i Azure Portal.
   3. I fältet **hemligt mål** anger du det lösen ord som du angav för CHAP-initieraren i Azure Portal.
   4. Klicka på **OK**.
      
       ![Avancerade inställningar allmänt](./media/storsimple-configure-chap/IC740946.png)
5. På fliken **mål** i fönstret **Egenskaper för iSCSI-initierare** ska enhetens status visas som **ansluten**. Om du använder en StorSimple 1200-enhet monteras varje volym som ett iSCSI-mål. Därför måste steg 3-4 upprepas för varje volym.
   
    ![Volymer monterade som separata mål](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Om du ändrar iSCSI-namnet används det nya namnet för nya iSCSI-sessioner. Nya inställningar används inte för befintliga sessioner förrän du loggar ut och loggar in igen.

Mer information om hur du konfigurerar CHAP på Windows-värd servern finns i [ytterligare överväganden](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Dubbelriktad eller ömsesidig autentisering

Vid dubbelriktad autentisering autentiserar målet initieraren och sedan autentiserar initieraren målet. Den här proceduren kräver att användaren konfigurerar inställningarna för CHAP-initierare, omvänt CHAP-inställningar på enheten och iSCSI-initierarens program vara på värden. Följande procedurer beskriver stegen för att konfigurera ömsesidig autentisering på enheten och på Windows-värden.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Så här konfigurerar du enheten för ömsesidig autentisering

1. I Azure Portal går du till din StorSimple Enhetshanteraren-tjänst. Klicka på **enheter** och markera och klicka på en enhet som du vill konfigurera CHAP för. Gå till **enhets inställningar > säkerhet**. Klicka på **CHAP** i bladet **säkerhets inställningar** .
   
    ![CHAP-mål](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Rulla nedåt på den här sidan och i avsnittet **CHAP-mål** :
   
   1. Ange ett **användar namn för omvänt CHAP** för enheten.
   2. Ange ett **lösen ord för omvänt CHAP** för enheten.
   3. Bekräfta lösenordet.
3. I avsnittet **CHAP-initierare** :
   
   1. Ange ett **användar namn** för din enhet.
   2. Ange ett **lösen ord** för enheten.
   3. Bekräfta lösenordet.

       ![CHAP-initierare 3](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Klicka på **Spara**. Ett bekräftelse meddelande visas. Spara ändringarna genom att klicka på **OK**.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Så här konfigurerar du dubbelriktad autentisering på Windows-värd servern

1. Starta iSCSI-initieraren på Windows-värd servern.
2. I fönstret **Egenskaper för iSCSI-initierare** klickar du på fliken **konfiguration** .
3. Klicka på **CHAP**.
4. I dialog rutan **ömsesidig CHAP-hemlighet i iSCSI-initieraren** :
   
   1. Ange det **omvänt CHAP-lösenordet** som du konfigurerade i Azure Portal.
   2. Klicka på **OK**.
      
       ![iSCSI-initierare ömsesidig CHAP-hemlighet](./media/storsimple-configure-chap/IC740949.png)
5. Klicka på fliken **mål** .
6. Klicka på knappen **Anslut**. 
7. I dialog rutan **Anslut till mål** klickar du på **Avancerat**.
8. I dialog rutan **avancerade egenskaper** :
   
   1. Markera kryss rutan **Aktivera CHAP-inloggning** .
   2. I fältet **namn** anger du det användar namn som du angav för CHAP-initieraren i Azure Portal.
   3. I fältet **hemligt mål** anger du det lösen ord som du angav för CHAP-initieraren i Azure Portal.
   4. Markera kryss rutan **utför ömsesidig autentisering** .
      
       ![Ömsesidig autentisering med avancerade inställningar](./media/storsimple-configure-chap/IC740950.png)
   5. Slutför CHAP-konfigurationen genom att klicka på **OK**

Mer information om hur du konfigurerar CHAP på Windows-värd servern finns i [ytterligare överväganden](#additional-considerations).

## <a name="additional-considerations"></a>Annat som är bra att tänka på

Funktionen för **snabb anslutning** stöder inte anslutningar med CHAP aktiverat. När CHAP är aktiverat, se till att du använder knappen **Anslut** som finns på fliken **mål** för att ansluta till ett mål.

![Anslut till mål](./media/storsimple-configure-chap/IC740947.png)

I dialog rutan **Anslut till mål** som visas markerar du kryss rutan **Lägg till den här anslutningen i listan över favorit mål** . Det här valet ser till att varje gång datorn startas om görs ett försök att återställa anslutningen till iSCSI-favorit målen.

## <a name="errors-during-configuration"></a>Fel under konfigurationen

Om CHAP-konfigurationen är felaktig visas ett fel meddelande om **autentiseringsfel** .

## <a name="verification-of-chap-configuration"></a>Verifiering av CHAP-konfiguration

Du kan kontrol lera att CHAP används genom att slutföra följande steg.

#### <a name="to-verify-your-chap-configuration"></a>Verifiera din CHAP-konfiguration
1. Klicka på **favorit mål**.
2. Välj det mål som du har aktiverat autentisering för.
3. Klicka på **information**.
   
    ![egenskaper för iSCSI-initierare favorit mål](./media/storsimple-configure-chap/IC740951.png)
4. I dialog rutan **favorit mål information** noterar du posten i fältet **Authentication** . Om konfigurationen lyckades bör det stå **CHAP**.
   
    ![Information om favorit mål](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [StorSimple-säkerhet](storsimple-8000-security.md).
* Lär dig mer om [att använda tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

