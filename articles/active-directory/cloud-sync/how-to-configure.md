---
title: Azure AD Connect molnets synkronisering ny agent konfiguration
description: Den här artikeln beskriver hur du installerar Cloud Sync.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffdd27b67a122f82cc5fdb5568f11c85387955e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98660804"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-sync"></a>Skapa en ny konfiguration för Azure AD Connect Cloud Sync

När du har installerat Azure AD Connect etablerings agenten måste du logga in på Azure Portal och konfigurera den. Följ dessa steg om du vill aktivera agenten.

## <a name="configure-provisioning"></a>Konfigurera etablering
Följ dessa steg om du vill konfigurera etableringen.

 1. I Azure Portal väljer du **Azure Active Directory**
 2. Välj **Azure AD Connect**.
 3. Välj **hantera synkronisering av moln**.

 ![Hantera etablering](media/how-to-install/install-6.png)
 
 4. Välj **ny konfiguration**.
 5. På sidan konfiguration väljer du din domän och anger om du vill aktivera hash-synkronisering av lösen ord.  Klicka på **skapa**.  
 
 ![Skapa ny konfiguration](media/how-to-configure/configure-1.png)


 6.  Konfigurations skärmen för att redigera etablering öppnas.

   ![Redigera konfiguration](media/how-to-configure/con-1.png)

 7. Ange ett **e-postmeddelande för avisering**. Det här e-postmeddelandet kommer att meddelas när etableringen inte är felfritt.  Vi rekommenderar att du **förhindrar att oavsiktlig borttagning** är aktiverat och anger **tröskelvärdet för oavsiktlig borttagning** till ett tal som du vill bli aviserad om.  Mer information finns i [oavsiktliga borttagningar](#accidental-deletions) nedan.
 8. Flytta väljaren för att aktivera och välj Spara.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Omfattnings etablering för vissa användare och grupper
Du kan begränsa agenten till att synkronisera vissa användare och grupper genom att använda lokala Active Directory grupper eller organisationsenheter. Du kan inte konfigurera grupper och organisationsenheter i en konfiguration. 

 1.  Välj **Azure Active Directory** i Azure Portal.
 2. Välj **Azure AD Connect**.
 3. Välj **hantera synkronisering av moln**.
 4. Under **konfiguration** väljer du din konfiguration.

 ![Konfigurations avsnitt](media/how-to-configure/scope-1.png)
 
 5. Under **Konfigurera** väljer du **Redigera omfångs filter** för att ändra omfånget för konfigurations regeln.
 ![Redigera omfattning](media/how-to-configure/scope-3.png)
 7. Till höger kan du ändra omfattningen.  Klicka på **klart**  och **Spara** när du är färdig.
 8. När du har ändrat omfattning bör du [starta om etableringen](#restart-provisioning) för att initiera en omedelbar synkronisering av ändringarna.

## <a name="attribute-mapping"></a>Attributmappning
Med Azure AD Connect Cloud Sync kan du enkelt mappa attribut mellan dina lokala användar-och grupp objekt och objekt i Azure AD.  Du kan anpassa standardattributen – mappningar efter dina affärs behov. Så du kan ändra eller ta bort befintliga attribut-mappningar eller skapa nya attribut-mappningar.  Mer information finns i [attribut mappning](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>Etablering på begäran
Med Azure AD Connect Cloud Sync kan du testa konfigurations ändringar genom att tillämpa dessa ändringar på en enskild användare eller grupp.  Du kan använda detta för att verifiera och kontrol lera att ändringarna som har gjorts i konfigurationen har tillämpats korrekt och synkroniseras korrekt med Azure AD.  Mer information finns i [etablering på begäran](how-to-on-demand-provision.md).

## <a name="accidental-deletions"></a>Oavsiktliga borttagningar
Funktionen för oavsiktlig borttagning är utformad för att skydda dig från oavsiktliga konfigurations ändringar och ändringar i din lokala katalog som påverkar många användare och grupper.  Med den här funktionen kan du:

- Konfigurera möjligheten att förhindra oavsiktliga borttagningar automatiskt. 
- Ange antalet objekt (tröskel) utöver vilka konfigurationen börjar gälla 
- Konfigurera en e-postadress för avisering så att de kan få ett e-postmeddelande när synkroniseringen i fråga placeras i karantän för det här scenariot 

Mer information finns i [oavsiktliga borttagningar](how-to-accidental-deletes.md)

## <a name="quarantines"></a>Placerar
Cloud Sync övervakar hälso tillståndet för din konfiguration och placerar Felaktiga objekt i ett karantäns tillstånd. Om de flesta eller alla anrop som görs mot mål systemet inte fungerar konsekvent på grund av ett fel, till exempel ogiltiga administratörsautentiseringsuppgifter, markeras synkroniseringsschemat som i karantän.  Mer information finns i fel söknings avsnittet i [karantän](how-to-troubleshoot.md#provisioning-quarantined-problems).

## <a name="restart-provisioning"></a>Starta om etablering 
Om du inte vill vänta på nästa schemalagda körning utlöser du etablerings körningen med hjälp av knappen **starta om etablering** . 
 1.  Välj **Azure Active Directory** i Azure Portal.
 2. Välj **Azure AD Connect**.
 3.  Välj **hantera synkronisering av moln**.
 4. Under **konfiguration** väljer du din konfiguration.

   ![Konfigurations val för att starta om etableringen](media/how-to-configure/scope-1.png)

 5. Välj **starta om etablering** längst upp.

## <a name="remove-a-configuration"></a>Ta bort en konfiguration
Följ dessa steg om du vill ta bort en konfiguration.

 1.  Välj **Azure Active Directory** i Azure Portal.
 2. Välj **Azure AD Connect**.
 3. Välj **hantera synkronisering av moln**.
 4. Under **konfiguration** väljer du din konfiguration.
   
   ![Konfigurations val för att ta bort konfiguration](media/how-to-configure/scope-1.png)

 5. Klicka på **ta bort** överst på konfigurations skärmen.

>[!IMPORTANT]
>Det finns ingen bekräftelse innan du tar bort en konfiguration. Kontrol lera att detta är den åtgärd du vill vidta innan du väljer **ta bort**.


## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
