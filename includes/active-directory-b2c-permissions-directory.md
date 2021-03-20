---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: f8c972bdb9195008c2983d3993e8d9369749b284
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85200163"
---
#### <a name="app-registrations"></a>[Appregistreringar](#tab/app-reg-ga/) 

1. Under **Hantera**, Välj **API-behörigheter**.
1. Under **konfigurerade behörigheter** väljer du **Lägg till en behörighet**.
1. Välj fliken **Microsoft API: er** och välj sedan **Microsoft Graph**.
1. Välj **Programbehörigheter**.
1. Expandera rätt behörighets grupp och markera kryss rutan för behörighet att bevilja ditt hanterings program. Exempel:
    * **AuditLog**  >  **AuditLog. Read. all**: för att läsa katalogens gransknings loggar.
    * **Katalog**  >  **Directory. readwrite. all**: för användar migrering eller användar hanterings scenarier.
    * **Princip**  >  **Princip. readwrite. TrustFramework**: för scenarier med kontinuerlig integrering/kontinuerlig leverans (CI/CD). Till exempel anpassad princip distribution med Azure-pipelines.
1. Välj **Lägg till behörigheter**. Vänta några minuter innan du fortsätter till nästa steg.
1. Välj **bevilja administrativt godkännande för (ditt klient namn)**.
1. Om du inte är inloggad med ett globalt administratörs konto loggar du in med ett konto i din Azure AD B2C-klient som har tilldelats minst rollen som *administratör för moln program* och väljer sedan **bevilja administrativt medgivande för (ditt klient namn)**.
1. Välj **Uppdatera** och verifiera sedan att "beviljat..." visas under **status**. Det kan ta några minuter innan behörigheterna har spridits.

#### <a name="applications-legacy"></a>[Program (bakåtkompatibelt)](#tab/applications-legacy/)

1. På sidan **registrerad app** -översikt väljer du **Inställningar**.
1. Under **API-åtkomst** väljer du **nödvändiga behörigheter**.
1. Välj **Microsoft Graph**.
1. Under **program behörigheter** markerar du kryss rutan för behörigheten att bevilja ditt hanterings program. Exempel:
    * **Läs alla Gransknings logg data**: Välj den här behörigheten för att läsa katalogens gransknings loggar.
    * **Läsa och skriva katalog data**: Välj den här behörigheten för användar migrering eller användar hanterings scenarier.
    * **Läs och skriv organisationens förtroende Ramverks principer**: Välj den här behörigheten för scenarier för kontinuerlig integrering/kontinuerlig leverans (CI/CD). Till exempel anpassad princip distribution med Azure-pipelines.
1. Välj **Spara**.
1. Välj **bevilja behörigheter** och välj sedan **Ja**. Det kan ta några minuter innan behörigheterna är fullständigt spridda.
