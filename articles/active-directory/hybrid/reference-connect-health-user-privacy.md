---
title: Azure AD Connect Health-och användar sekretess | Microsoft Docs
description: Det här dokumentet beskriver användar sekretess med Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ecdfefa79716bcc0a69063293d5a1067693a78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89182368"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Användar sekretess och Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Den här artikeln handlar om Azure AD Connect Health-och användar sekretess.  Information om Azure AD Connect och användar sekretess finns [här](reference-connect-user-privacy.md).

## <a name="user-privacy-classification"></a>Sekretess klassificering för användare
Azure AD Connect Health hamnar i **data processor** kategorin för GDPR-klassificeringen. Som data behandlings pipeline tillhandahåller tjänsten data bearbetnings tjänster för viktiga partner och slut konsumenter. Azure AD Connect Health genererar inte användar data och har ingen oberoende kontroll över vilka personliga data som samlas in och hur de används. Data hämtning, sammansättning, analys och rapportering i Azure AD Connect Health baseras på befintliga lokala data. 

## <a name="data-retention-policy"></a>Datakvarhållningsprincip
Azure AD Connect Health genererar inte rapporter, utför analyser eller ger insikter mer än 30 dagar. Därför lagrar Azure AD Connect Health inte, bearbetar eller behåller data mer än 30 dagar. Den här designen är kompatibel med reglerna för GDPR, Microsofts sekretess regler för efterlevnad och Azure AD-datakvarhållning. 

Servrar med aktiva **hälso tjänst data är inte uppdaterade** **fel** aviseringar i mer än 30 dagar i följd, föreslå att inga data har uppnått Connect Health under den tids perioden. Dessa servrar kommer att inaktive ras och visas inte i Connect Health-portalen. Om du vill återaktivera servrarna måste du avinstallera och [installera om hälso agenten](how-to-connect-health-agent-install.md). Observera att detta inte gäller för **varningar** med samma aviserings typ. Varningar anger att partiella data saknas på den server som du är aviserad om. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Inaktivera insamling och övervakning av data i Azure AD Connect Health
Med Azure AD Connect Health kan du stoppa data insamling för varje övervakad Server eller en instans av en övervakad tjänst. Du kan till exempel stoppa data insamling för enskilda ADFS-servrar (Active Directory Federation Services (AD FS)) som övervakas med hjälp av Azure AD Connect Health. Du kan också stoppa data insamlingen för hela ADFS-instansen som övervakas med hjälp av Azure AD Connect Health. När du väljer att göra det tas motsvarande servrar bort från Azure AD Connect Health Portal efter att data insamlingen stoppats. 

>[!IMPORTANT]
> Du behöver antingen global administratörs behörighet för Azure AD eller deltagar rollen i Azure RBAC för att ta bort övervakade servrar från Azure AD Connect Health.
>
> Det går inte att ångra en åtgärd för att ta bort en server eller tjänst instans från Azure AD Connect Health. 

### <a name="what-to-expect"></a>Vad ska du vänta på?
Observera följande om du stoppar data insamling och övervakning för en enskild övervakad Server eller en instans av en övervakad tjänst:

- När du tar bort en instans av en övervakad tjänst tas instansen bort från listan Azure AD Connect Health övervaknings tjänster i portalen. 
- När du tar bort en övervakad Server eller en instans av en övervakad tjänst avinstalleras inte hälso agenten eller tas bort från dina servrar. Hälso agenten har kon figurer ATS för att inte skicka data till Azure AD Connect Health. Du måste avinstallera hälso agenten manuellt på tidigare övervakade servrar.
- Om du inte har avinstallerat hälso agenten innan du utför det här steget kan du se fel händelser på de servrar som är relaterade till hälso agenten.
- Alla data som hör till instansen av den övervakade tjänsten tas bort enligt principen Microsoft Azure Data bevarande.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Inaktivera insamling och övervakning av data för en instans av en övervakad tjänst
Se [hur du tar bort en tjänst instans från Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Inaktivera insamling och övervakning av data för en övervakad Server
Se [så här tar du bort en server från Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Inaktivera insamling och övervakning av data för alla övervakade tjänster i Azure AD Connect Health
Azure AD Connect Health ger också möjlighet att stoppa data insamling av **alla** registrerade tjänster i klienten. Vi rekommenderar att du noga överväger och fullständig bekräftelse på alla globala administratörer innan du vidtar åtgärden. När processen har börjat slutar Connect Health Service att ta emot, bearbeta och rapportera alla data för alla dina tjänster. Befintliga data i Connect Health Service kommer att behållas i högst 30 dagar.
Om du vill stoppa data insamlingen av en speciell Server följer du stegen vid borttagning av vissa servrar. Om du vill stoppa insamling av klient data följer du stegen nedan för att stoppa data insamling och ta bort alla tjänster för klienten.

1. Klicka på **allmänna inställningar** under konfiguration på huvud bladet. 
2. Klicka på knappen **stoppa data insamling** överst på bladet. De andra alternativen för klient konfigurations inställningar kommer att inaktive ras när processen startar.  
 
   ![Stoppa data insamling](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Kontrol lera listan över onboarded Services som påverkas av att data samlingar stoppas. 
4. Ange det exakta klient organisations namnet för att aktivera knappen **ta bort** åtgärd
5. Klicka på **ta bort** för att utlösa borttagningen av alla tjänster. Connect Health upphör att ta emot, bearbeta, rapportera data som skickas från dina registrerade tjänster. Hela processen kan ta upp till 24 timmar. Observera att det här steget inte kan ångras. 
6. När processen har slutförts visas inga registrerade tjänster i Connect Health längre. 

   ![När data insamlingen stoppades](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Återaktivera insamling och övervakning av data i Azure AD Connect Health
Om du vill återaktivera övervakning i Azure AD Connect Health för en tidigare borttagen övervakad tjänst måste du avinstallera och [installera om hälso agenten](how-to-connect-health-agent-install.md) på alla servrar.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Återaktivera insamling och övervakning av data för alla övervakade tjänster

Insamling av klient data kan återupptas i Azure AD Connect Health. Vi rekommenderar att du noga överväger och fullständig bekräftelse på alla globala administratörer innan du vidtar åtgärden.

>[!IMPORTANT]
> Följande steg är tillgängliga efter 24 timmars inaktive rings åtgärder.
> När du har aktiverat data insamling visar de visade insikter och övervaknings data i Connect Health inte några tidigare insamlade data innan. 

1. Klicka på **allmänna inställningar** under konfiguration på huvud bladet. 
2. Klicka på knappen **Aktivera data insamling** överst på bladet. 
 
   ![Aktivera datainsamling](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Ange det exakta klient organisations namnet för att aktivera knappen **Aktivera** .
4. Klicka på knappen **Aktivera** om du vill bevilja behörighet för data insamling i Connect Health Service. Ändringen kommer snart att gälla. 
5. Följ [installations processen](how-to-connect-health-agent-install.md) för att installera om agenten på de servrar som ska övervakas och tjänsterna kommer att finnas i portalen.  


## <a name="next-steps"></a>Nästa steg
* [Läs sekretess policyn för Microsoft på säkerhets Center](https://www.microsoft.com/trustcenter)
* [Azure AD Connect-och användar sekretess](reference-connect-user-privacy.md)

