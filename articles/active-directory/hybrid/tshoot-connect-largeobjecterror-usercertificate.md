---
title: Azure AD Connect-LargeObject fel som orsakas av attributet userCertificate | Microsoft Docs
description: Det här avsnittet innehåller reparations stegen för LargeObject-fel som orsakas av userCertificate-attributet.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: d33b419e0f24201d661ad0f5f1373022ea6e9e9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861756"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Azure AD Connect synkronisering: hantera LargeObject-fel som orsakas av userCertificate-attribut

Azure AD tvingar fram max gränsen på **15** certifikat värden i attributet **userCertificate** . Om Azure AD Connect exporterar ett objekt med fler än 15 värden till Azure AD returnerar Azure AD ett **LargeObject** -fel med meddelandet:

>*"Det etablerade objektet är för stort. Rensa antalet attributvärden för det här objektet. Ett nytt försök kommer att göras vid nästa synkronisering... "*

LargeObject-felet kan bero på andra AD-attribut. För att bekräfta att det orsakas av userCertificate-attributet måste du verifiera mot objektet antingen i lokalt AD eller i [Synchronization Service Manager metaversum-sökningen](./how-to-connect-sync-service-manager-ui-mvsearch.md).

Använd någon av följande metoder för att hämta listan över objekt i din klient organisation med LargeObject-fel:

 * Om din klient organisation är aktive rad för Azure AD Connect Health för synkronisering kan du referera till [rapporten om synkroniseringsfel](./how-to-connect-health-sync.md) .
 
 * E-postmeddelandet för katalog-synkroniseringsfel som skickas i slutet av varje synkronisering har en lista över objekt med LargeObject-fel. 
 * På [fliken Synchronization Service Manager åtgärder](./how-to-connect-sync-service-manager-ui-operations.md) visas listan med objekt med LargeObject-fel om du klickar på den senaste exporten till Azure AD-åtgärd.
 
## <a name="mitigation-options"></a>Alternativ för minskning
Det går inte att exportera andra attributändringar till samma objekt till Azure AD förrän LargeObject-felet har åtgärd ATS. Du kan lösa problemet genom att överväga följande alternativ:

 * Uppgradera Azure AD Connect för att bygga 1.1.524.0 eller efter. I Azure AD Connect build-1.1.524.0 har de färdiga reglerna för synkronisering har uppdaterats till att inte exportera attributen userCertificate och userSMIMECertificate om attributen har fler än 15 värden. Mer information om hur du uppgraderar Azure AD Connect finns i artikeln [Azure AD Connect: uppgradera från en tidigare version till den senaste](./how-to-upgrade-previous-version.md).

 * Implementera en **regel för utgående synkronisering** i Azure AD Connect som exporterar ett **Null-värde i stället för de faktiska värdena för objekt med fler än 15 certifikat värden**. Det här alternativet är lämpligt om du inte behöver något av de certifikat värden som ska exporteras till Azure AD för objekt med fler än 15 värden. Mer information om hur du implementerar den här synkroniseringsregeln finns i nästa avsnitt [implementera Synkroniseringsregel för att begränsa export av userCertificate-attribut](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Minska antalet certifikat värden för det lokala AD-objektet (högst 15) genom att ta bort värden som inte längre används av din organisation. Detta är lämpligt om attributet överdriven storlek orsakas av certifikat som har förfallit eller inte används. Du kan använda [PowerShell-skriptet som är tillgängligt här](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) för att hitta, säkerhetskopiera och ta bort utgångna certifikat i din lokala AD. Innan du tar bort certifikaten rekommenderar vi att du verifierar med administratörer för offentliga nycklar i organisationen.

 * Konfigurera Azure AD Connect att undanta attributet userCertificate från att exporteras till Azure AD. I allmänhet rekommenderar vi inte det här alternativet eftersom attributet kan användas av Microsoft Online Services för att aktivera vissa scenarier. Framför allt:
    * Attributet userCertificate i användarobjektet används av Exchange Online-och Outlook-klienter för meddelande signering och kryptering. Mer information om den här funktionen finns i artikeln [S/MIME for Message signing and Encryption](/microsoft-365/security/office-365-security/s-mime-for-message-signing-and-encryption).

    * Attributet userCertificate i datorobjektet används av Azure AD för att tillåta att Windows 10 lokala domänanslutna enheter ansluter till Azure AD. Mer information om den här funktionen finns i artikeln [Anslut domänanslutna enheter till Azure AD för Windows 10-upplevelser](../devices/hybrid-azuread-join-plan.md).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementerar Synkroniseringsregel för att begränsa exporten av attributet userCertificate
För att lösa det LargeObject-fel som orsakas av userCertificate-attributet kan du implementera en regel för utgående trafik i Azure AD Connect som exporterar ett **Null-värde i stället för de faktiska värdena för objekt med fler än 15 certifikat värden**. I det här avsnittet beskrivs de steg som krävs för att implementera synkroniseringsregeln för **användar** objekt. Stegen kan anpassas för **kontakt** -och **dator** objekt.

> [!IMPORTANT]
> Exportera null-värde tar bort certifikat värden som tidigare har exporter ATS till Azure AD.

Stegen kan sammanfattas som:

1. Inaktivera Sync Scheduler och kontrol lera att ingen synkronisering pågår.
3. Hitta den befintliga regeln för utgående synkronisering för userCertificate-attributet.
4. Skapa regeln för utgående synkronisering krävs.
5. Verifiera den nya synkroniseringsregeln för ett befintligt objekt med LargeObject-fel.
6. Använd den nya synkroniseringsregeln för kvarvarande objekt med LargeObject-fel.
7. Kontrol lera att det inte finns några oväntade ändringar som väntar på att exporteras till Azure AD.
8. Exportera ändringarna till Azure AD.
9. Återaktivera synkronisering av Schemaläggaren.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Steg 1. Inaktivera Sync Scheduler och kontrol lera att ingen synkronisering pågår
Se till att ingen synkronisering sker medan du är i mitten av implementeringen av en ny Synkroniseringsregel för att undvika oönskade ändringar som exporteras till Azure AD. Så här inaktiverar du den inbyggda Sync Scheduler:
1. Starta PowerShell-sessionen på Azure AD Connect servern.

2. Inaktivera schemalagd synkronisering genom att köra cmdleten: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Föregående steg gäller endast för nyare versioner (1,1. xxx. x) av Azure AD Connect med den inbyggda schemaläggaren. Om du använder äldre versioner (1.0. xxx. x) av Azure AD Connect som använder Schemaläggaren i Windows eller om du använder en egen anpassad Scheduler (inte common) för att utlösa regelbunden synkronisering måste du inaktivera dem.

1. Starta **Synchronization Service Manager** genom att gå till Start → synkroniseringstjänst.

1. Gå till fliken **åtgärder** och bekräfta att det inte finns någon åtgärd vars status är *"pågår".*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Steg 2. Hitta den befintliga regeln för utgående synkronisering för attributet userCertificate
Det bör finnas en befintlig Synkroniseringsregel som är aktive rad och konfigurerad för att exportera userCertificate-attribut för användar objekt till Azure AD. Leta upp den här synkroniseringsregeln för att ta reda på dess **prioritet** och **omfattnings filter** konfiguration:

1. Starta **Redigeraren för regler för synkronisering** genom att gå till Start → redigerare för Synkroniseringsregel.

2. Konfigurera Sök filtren med följande värden:

    | Attribut | Värde |
    | --- | --- |
    | Riktning |**Utgående** |
    | Typ av MV-objekt |**Person** |
    | Anslutning |*namn på din Azure AD-anslutning* |
    | Kopplings objekt typ |**användarvänlig** |
    | MV-attribut |**userCertificate** |

3. Om du använder userCertficiate för OOB (out-of-Box) till Azure AD Connector för att exportera-attribut för användar objekt, bör du gå tillbaka till regeln *"ut till AAD – User ExchangeOnline"* .
4. Anteckna **prioritet** svärdet för den här synkroniseringsregeln.
5. Välj Synkroniseringsregel och klicka på **Redigera**.
6. I popup-dialog rutan *"redigera reserverad regel bekräftelse"* klickar du på **Nej**. (Oroa dig inte, vi kommer inte att göra några ändringar i den här synkroniseringsregeln).
7. På sidan Redigera väljer du fliken **omfångs filter** .
8. Anteckna konfigurationen för omfångs filtret. Om du använder en OOB-Synkroniseringsregel bör det finnas exakt **en omfattnings filter grupp som innehåller två satser**, inklusive:

    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | sourceObjectType | SKEPPNINGSKVANTITETEN | User |
    | cloudMastered | NOTEQUAL | Sant |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Steg 3. Skapa regeln för utgående synkronisering krävs
Den nya synkroniseringsregeln måste ha samma **omfattnings filter** och **högre prioritet** än den befintliga synkroniseringsregeln. Detta säkerställer att den nya synkroniseringsregeln gäller för samma uppsättning objekt som den befintliga synkroniseringsregeln och åsidosätter den befintliga synkroniseringsregeln för attributet userCertificate. Så här skapar du en Synkroniseringsregel:
1. I redigeraren för regler för synkronisering klickar du på knappen **Lägg till ny regel** .
2. Ange följande konfiguration på **fliken Beskrivning**:

    | Attribut | Värde | Information |
    | --- | --- | --- |
    | Name | *Ange ett namn* | T. ex. *"ut till AAD – anpassad åsidosättning för userCertificate"* |
    | Beskrivning | *Ange en beskrivning* | T. ex. *"If userCertificate-attribut har fler än 15 värden, exportera null".* |
    | Anslutet system | *Välj Azure AD-anslutning* |
    | Ansluten system objekt typ | **användarvänlig** | |
    | Metaversum objekt typ | **sända** | |
    | Länktyp | **Join** | |
    | Prioritet | *Välj ett tal mellan 1-99* | Antalet som väljs får inte användas av någon befintlig Synkroniseringsregel och har ett lägre värde (och därför högre prioritet) än den befintliga synkroniseringsregeln. |

3. Gå till fliken **omfångs filter** och implementera samma omfångs filter som den befintliga synkroniseringsregeln använder.
4. Hoppa över fliken **kopplings regler** .
5. Gå till fliken **omvandlingar** om du vill lägga till en ny omvandling med följande konfiguration:

    | Attribut | Värde |
    | --- | --- |
    | Flödestyp |**Uttryck** |
    | Target-attribut |**userCertificate** |
    | Källattribut |*Använd följande uttryck*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Klicka på knappen **Lägg till** för att skapa synkroniseringsregeln.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Steg 4. Verifiera den nya synkroniseringsregeln för ett befintligt objekt med LargeObject-fel
Detta är för att kontrol lera att synkroniseringsregeln som skapats fungerar korrekt på ett befintligt AD-objekt med LargeObject-fel innan du tillämpar det på andra objekt:
1. Gå till fliken **åtgärder** i Synchronization Service Manager.
2. Välj den senaste exporten till Azure AD-åtgärden och klicka på ett av objekten med LargeObject-fel.
3.  I popup-fönstret Egenskaper för kopplings utrymmes objekt klickar du på knappen **Förhandsgranska** .
4. I popup-fönstret för förhands granskning väljer du **fullständig synkronisering** och klickar på **Förhandsgranskning av incheckning**.
5. Stäng förhands gransknings skärmen och objekt fönstret Egenskaper för anslutnings utrymme.
6. Gå till fliken **anslutningar** i Synchronization Service Manager.
7. Högerklicka på **Azure AD** -anslutaren och välj **Kör...**
8. I popup-fönstret kör koppling väljer du **Exportera** steg och klickar på **OK**.
9. Vänta tills exporten till Azure AD har slutförts och bekräfta att det inte finns några fler LargeObject-fel på det här objektet.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Steg 5. Använd den nya synkroniseringsregeln för kvarvarande objekt med LargeObject-fel
När du har lagt till synkroniseringsregeln måste du köra ett steg för fullständig synkronisering på AD-anslutningen:
1. Gå till fliken **anslutningar** i Synchronization Service Manager.
2. Högerklicka på **AD** -anslutningen och välj **Kör...**
3. I popup-fönstret kör koppling väljer du steget **fullständig synkronisering** och klickar på **OK**.
4. Vänta tills steget fullständig synkronisering har slutförts.
5. Upprepa stegen ovan för de återstående AD-anslutningarna om du har fler än en AD-anslutning. Det krävs vanligt vis flera anslutningar om du har flera lokala kataloger.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Steg 6. Kontrol lera att det inte finns några oväntade ändringar som väntar på att exporteras till Azure AD
1. Gå till fliken **anslutningar** i Synchronization Service Manager.
2. Högerklicka på **Azure AD** Connector och välj **search Connector-utrymme**.
3. I popup-fönstret för Sök kopplings utrymmen:
    1. Ange omfång till **väntande export**.
    2. Markera alla tre kryss rutor, inklusive **Lägg till**, **ändra** och **ta bort**.
    3. Klicka på knappen **Sök** för att returnera alla objekt med ändringar som väntar på att exporteras till Azure AD.
    4. Kontrol lera att det inte finns några oväntade ändringar. Om du vill granska ändringarna för ett angivet objekt dubbelklickar du på objektet.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Steg 7. Exportera ändringarna till Azure AD
Exportera ändringarna till Azure AD:
1. Gå till fliken **anslutningar** i Synchronization Service Manager.
2. Högerklicka på **Azure AD** -anslutaren och välj **Kör...**
4. I popup-fönstret kör koppling väljer du **Exportera** steg och klickar på **OK**.
5. Vänta tills exporten till Azure AD har slutförts och bekräfta att det inte finns några fler LargeObject-fel.

### <a name="step-8-re-enable-sync-scheduler"></a>Steg 8. Återaktivera synkronisering av Schemaläggaren
Nu när problemet är löst aktiverar du den inbyggda synkroniseringen på nytt:
1. Starta PowerShell-session.
2. Återaktivera schemalagd synkronisering genom att köra cmdleten: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Föregående steg gäller endast för nyare versioner (1,1. xxx. x) av Azure AD Connect med den inbyggda schemaläggaren. Om du använder äldre versioner (1.0. xxx. x) av Azure AD Connect som använder Schemaläggaren i Windows eller om du använder en egen anpassad Scheduler (inte common) för att utlösa regelbunden synkronisering måste du inaktivera dem.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
