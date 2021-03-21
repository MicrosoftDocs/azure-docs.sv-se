---
title: Referens för Azure Active Directory-och Workday-integrering
description: Teknisk djup inblick i Workday – HR driven etablering
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 02/09/2021
ms.author: chmutali
ms.openlocfilehash: 2b1a43ee6b13d32c0eaed92538cf9c25405e061b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100104339"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>Hur Azure Active Directory etablering integreras med Workday

[Azure Active Directory användar etablerings tjänsten](../app-provisioning/user-provisioning.md) integreras med [Workday-HCM](https://www.workday.com) för att hantera användarens identitets livs cykel. Azure Active Directory erbjuder tre färdiga integreringar: 

* [Arbets dag till lokal Active Directory användar etablering](../saas-apps/workday-inbound-tutorial.md)
* [Arbets dag för att Azure Active Directory användar etablering](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Workday – tillbakaskrivning](../saas-apps/workday-writeback-tutorial.md)

Den här artikeln förklarar hur integreringen fungerar och hur du kan anpassa etablerings beteendet för olika HR-scenarier. 

## <a name="establishing-connectivity"></a>Upprätta anslutning 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>Begränsa Workday API-åtkomst till Azure AD-slutpunkter
Azure AD Provisioning-tjänsten använder grundläggande autentisering för att ansluta till API-slutpunkter för Workday-webbtjänster.  

För att ytterligare skydda anslutningen mellan Azure AD Provisioning-tjänsten och Workday kan du begränsa åtkomsten så att den utsedda system användaren endast får åtkomst till Workday-API: erna från tillåtna Azure AD IP-intervall. Engagera din Workday-administratör för att slutföra följande konfiguration i din Workday-klient. 

1. Hämta de [senaste IP-intervallen](https://www.microsoft.com/download/details.aspx?id=56519) för det offentliga Azure-molnet. 
1. Öppna filen och Sök efter taggen **AzureActiveDirectory** 

   >[!div class="mx-imgBorder"] 
   >![IP-intervall för Azure AD](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Kopiera alla IP-adressintervall som anges i elementets *addressPrefixes* och Använd intervallet för att bygga IP-adress listan.
1. Logga in på arbets dag administrations portalen. 
1. Öppna aktiviteten **Underhåll IP-intervall** för att skapa ett nytt IP-intervall för Azure-datacenter. Ange IP-intervall (med CIDR-notering) som en kommaavgränsad lista.  
1. Skapa en ny autentiseringsprincip genom att öppna aktiviteten **Hantera autentiseringsprinciper** . I autentiseringsprincip använder du listan över tillåtna autentiseringar för att ange IP-intervallet för Azure AD och säkerhets gruppen som kommer att få åtkomst från det här IP-intervallet. Spara ändringarna. 
1. Öppna aktiviteten **Aktivera alla väntande autentiserings principer ändringar** för att bekräfta ändringar.

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>Begränsa åtkomsten till arbetarnas data i arbets dagen med begränsade säkerhets grupper

Standard stegen för att [Konfigurera användare av arbets platsens integrations system](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday) beviljar åtkomst för att hämta alla användare i din Workday-klient. I vissa integrerings scenarier kanske du vill begränsa åtkomsten, så att användare som bara tillhör vissa organisationer returneras av Get_Workers API-anropet och bearbetas av en arbets dag Azure AD-anslutning. 

Du kan uppfylla det här kravet genom att arbeta med din arbets plats administratör och konfigurera begränsade integrerings system säkerhets grupper. Mer information om hur det här görs finns i [den här](https://community.workday.com/forums/customer-questions/620393) artikeln om Workday-communityn (*arbets dag community inloggning autentiseringsuppgifter krävs för att komma åt den här artikeln*)

Den här strategin för att begränsa åtkomst med begränsade ISSG (säkerhets grupper för integrerings systemet) är särskilt användbart i följande scenarier: 
* **Scenario** för stegvis distribution: du har en stor arbets dag klient och planerar att utföra en stegvis distribution av workday till automatisk etablering i Azure AD. I det här scenariot, i stället för att utesluta användare som inte omfattas av den aktuella fasen med Azure AD-omfångs filter, rekommenderar vi att du konfigurerar begränsade ISSG så att endast omfångs arbetare visas i Azure AD.
* **Scenario för flera etablerings jobb**: du har en stor arbets dag klient och flera AD-domäner som har stöd för olika affär senheter/Division/företag. Om du vill ha stöd för den här topologin vill du köra flera arbets dagar till Azure AD-etablerings jobb med varje jobb för etablering av en särskild uppsättning arbetare. I det här scenariot, i stället för att använda omfångs filter i Azure AD för att utesluta arbetarnas data, rekommenderar vi att du konfigurerar begränsade ISSG så att endast relevanta Worker-data visas för Azure AD. 

### <a name="workday-test-connection-query"></a>Arbets dag test anslutnings fråga

För att testa anslutningen till arbets dagen skickar Azure AD följande *Get_Workers* begäran om webb tjänster för arbets dagar. 

```XML
<!-- Test connection query tries to retrieve one record from the first page -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to the test connection event -->
<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:28:50.1491022Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:28:50.1491022Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
    <p1:Exclude_Employees>true</p1:Exclude_Employees>
    <p1:Exclude_Contingent_Workers>true</p1:Exclude_Contingent_Workers>
    <p1:Exclude_Inactive_Workers>true</p1:Exclude_Inactive_Workers>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:28:50.1491022Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:28:50.1491022Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>1</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="how-full-sync-works"></a>Hur fullständig synkronisering fungerar

**Fullständig synkronisering** i samband med Workday-driven etablering avser processen för att hämta alla identiteter från Workday och fastställa vilka etablerings regler som ska gälla för varje arbets objekt. Fullständig synkronisering sker när du aktiverar etablering för första gången och även när du *startar om etableringen* antingen från Azure Portal eller använder Graph API: er. 

Azure AD skickar följande *Get_Workers* Workday-webbtjänsters förfrågan om att hämta arbets uppgifter. Frågan söker efter tids periods transaktions loggen för alla effektiva arbets poster från den tid som motsvarar den fullständiga synkroniseringen. 

```XML
<!-- Workday full sync query -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to full sync run -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Type_References>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Hire Employee</p1:ID>
        </p1:Transaction_Type_Reference>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Contract Contingent Worker</p1:ID>
        </p1:Transaction_Type_Reference>
      </p1:Transaction_Type_References>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Transaction_Log_Data>1</p1:Include_Transaction_Log_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```
Noden *Response_Group* används för att ange vilka arbetsattribut som ska hämtas från arbets dagen. En beskrivning av varje flagga i *Response_Group* -noden finns i dokumentationen för WORKDAY [Get_Workers API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType). 

Vissa flagg värden som anges i *Response_Group* -noden beräknas baserat på de attribut som kon figurer ATS i arbets dagen för Azure AD-etablering. Referera till avsnittet om *entiteter som stöds* för de kriterier som används för att ange flagg värden. 

*Get_Workers* svaret från Workday för frågan ovan inkluderar antalet arbets poster och antal sidor.

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
För att hämta nästa sida i resultat uppsättningen anger nästa *Get_Workers* fråga sid numret som en parameter i *Response_Filter*.

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
Azure AD Provisioning-tjänsten bearbetar varje sida och itererar igenom alla effektiva arbetare under fullständig synkronisering. För varje arbets post som importer ATS från Workday:
* [XPath-uttrycket](workday-attribute-reference.md) används för att hämta attributvärden från Workday.
* Mappningen av attribut och matchnings regler tillämpas och 
* Tjänsten fastställer vilken åtgärd som ska utföras i målet (Azure AD/AD). 

När bearbetningen är klar sparar den tidsstämpeln som är kopplad till starten av fullständig synkronisering som en vattenstämpel. Den här vattenstämpeln fungerar som start punkt för den stegvisa synkroniseringen. 

## <a name="how-incremental-sync-works"></a>Så här fungerar stegvis synkronisering

Efter fullständig synkronisering underhåller Azure AD Provisioning-tjänsten `LastExecutionTimestamp` och använder den för att skapa delta frågor för att hämta stegvisa ändringar. Under den stegvisa synkroniseringen skickar Azure AD följande typer av frågor till Workday: 

* [Fråga efter manuella uppdateringar](#query-for-manual-updates)
* [Fråga efter effektiva uppdateringar och uppsägningar](#query-for-effective-dated-updates-and-terminations)
* [Fråga för framtida anställda](#query-for-future-dated-hires)

### <a name="query-for-manual-updates"></a>Fråga efter manuella uppdateringar

Följande *Get_Workers* begär frågor om manuella uppdateringar som har inträffat mellan den senaste körningen och den aktuella körnings tiden. 

```xml
<!-- Workday incremental sync query for manual updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:29:16.0094202Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:49:06.290136Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-effective-dated-updates-and-terminations"></a>Fråga efter effektiva uppdateringar och uppsägningar

Följande *Get_Workers* begär frågor för effektiva uppdateringar som har inträffat mellan den senaste körningen och den aktuella körnings tiden. 

```xml
<!-- Workday incremental sync query for effective-dated updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Effective_From>2021-01-19T02:29:16.0094202Z</p1:Effective_From>
        <p1:Effective_Through>2021-01-19T02:49:06.290136Z</p1:Effective_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-future-dated-hires"></a>Fråga för framtida anställda

Om någon av ovanstående frågor returnerar en kommande anställnings anställd används följande *Get_Workers* begäran för att hämta information om en kommande nyanställd. *WID* -attributet för den nya anställningen används för att genomföra sökningen och det effektiva datumet anges till datum och tid för anställningen. 

```xml
<!-- Workday incremental sync query to get new hire data effective as on hire date/first day of work -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below hire date/first day of work -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_References>
    <p1:Worker_Reference>
      <p1:ID p1:type="WID">7bf6322f1ea101fd0b4433077f09cb04</p1:ID>
    </p1:Worker_Reference>
  </p1:Request_References>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-02-01T08:00:00+00:00</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-02-01T08:00:00+00:00</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="retrieving-worker-data-attributes"></a>Hämtar dataattribut för arbets data

*Get_Workers* -API: et kan returnera olika data uppsättningar som är associerade med en arbetare. Beroende på vilka [XPath API-uttryck](workday-attribute-reference.md) som kon figurer ATS i etablerings schemat, avgör Azure AD Provisioning-tjänsten vilka data uppsättningar som hämtas från arbets dagen. Därför anges *Response_Group* flaggor i *Get_Workers* begäran. 

Tabellen nedan innehåller vägledning om hur du mappar konfigurationen som ska användas för att hämta en speciell data uppsättning. 

| \# | Entiteten Workday                       | Ingår som standard | XPATH-mönster som ska anges i mappningen för att hämta entiteter som inte är standard             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | Person uppgifter                        | Ja                 | WD: Worker \_ -data/WD: person \_ uppgifter                                             |
| 2  | Anställnings data                      | Ja                 | WD: Worker \_ -data/WD: anställnings \_ data                                           |
| 3  | Ytterligare jobb data                  | Ja                 | WD: Worker \_ -data/WD: anställnings \_ data/WD \_ : \_ \[ @wd:Primary \_ jobb data jobb = 0\]|
| 4  | Organisations data                    | Ja                 | WD: Worker \_ -data/WD: organisations \_ data                                         |
| 5  | Hanterings kedje data                | Ja                 | WD: Worker \_ -data/WD: hanterings \_ kedje \_ data                                    |
| 6  | Övervaknings organisation             | Ja                 | TILLSYNSMYNDIGHETER                                                                 |
| 7  | Företag                              | Ja                 | Firm                                                                     |
| 8  | Affärsenhet                        | Inga                  | "affär \_ senhet"                                                              |
| 9  | Hierarki för affär senhet              | Inga                  | "affär \_ senhets \_ hierarki"                                                   |
| 10 | Organisationshierarki                    | Inga                  | ' företags \_ hierarki '                                                          |
| 11 | Cost Center                          | Nej                  | "kostnads \_ ställe"                                                                |
| 12 | Hierarki för kostnads ställe                | Inga                  | "kostnads \_ Center- \_ hierarki"                                                     |
| 13 | Strukturfonderna                                 | Inga                  | STRUKTURFONDERNA                                                                        |
| 14 | Fund-hierarki                       | Inga                  | ' FUND- \_ hierarki '                                                             |
| 15 | Gift                                 | Inga                  | GIFT                                                                        |
| 16 | Present hierarki                       | Inga                  | ' present \_ hierarki '                                                             |
| 17 | Bevilja                                | Inga                  | ÖMSESIDIGT                                                                       |
| 18 | Grant-hierarki                      | Inga                  | GRANT- \_ hierarki                                                            |
| 19 | Hierarki för affärs platser              | Inga                  | ' hierarki för företags \_ webbplats \_ '                                                   |
| 20 | Mat ris organisation                  | Inga                  | Matrix                                                                      |
| 21 | Löne grupp                            | Inga                  | ' LÖNE \_ grupp '                                                                  |
| 22 | Program                             | Inga                  | TILLÄGGS                                                                    |
| 23 | Programhierarki                    | Inga                  | ' \_ programhierarki '                                                          |
| 24 | Region                               | Inga                  | REGION- \_ hierarki                                                           |
| 25 | Platshierarki                   | Inga                  | ' LOCATION \_ HIERARCHY '                                                         |
| 26 | Konto etablerings data            | Inga                  | WD: Worker \_ -data/WD: konto \_ etablerings \_ data                                |
| 27 | Bakgrunds kontroll data                | Inga                  | WD: Worker \_ -data/WD: bakgrunds \_ kontroll \_ data                                    |
| 28 | Data för förmånsberättigande             | Inga                  | WD: Worker \_ data/WD: förmåns \_ berättigande \_ data                                 |
| 29 | Förmåns registrerings data              | Inga                  | WD: Worker \_ -data/WD: förmåns \_ registrerings \_ data                                  |
| 30 | Karriär data                          | Inga                  | WD: Worker \_ -data/WD: karriär \_ data                                               |
| 31 | Kompensations data                    | Inga                  | WD: Worker \_ -data/WD: kompensations \_ data                                         |
| 32 | Information om variabla skatte myndigheter | Inga                  | WD: Worker \_ data/WD: typ av anslags \_ \_ skatte \_ myndighet \_ \_ \_       |
| 33 | Data för utvecklings objekt                | Inga                  | WD: Worker \_ -data/WD: utvecklings \_ objekt \_ data                                    |
| 34 | Data för anställdas kontrakt              | Inga                  | WD: Worker \_ data/WD: anställnings \_ kontrakt \_ data                                  |
| 35 | Gransknings data för anställda                 | Inga                  | WD: Worker \_ data/WD: personal \_ gransknings \_ data                                     |
| 36 | Mottagen feedback-data               | Inga                  | WD: Worker \_ -data/WD: feedback \_ mottagen \_ data                                   |
| 37 | Mål data för arbetare                     | Inga                  | WD: Worker \_ data/WD: Worker- \_ mål \_ data                                         |
| 38 | Foto data                           | Inga                  | WD: Worker \_ -data/WD: foto \_ data                                                |
| 39 | Kvalificerings data                   | Inga                  | WD: Worker \_ -data/WD: kvalificerings \_ data                                        |
| 40 | Relaterade person uppgifter                 | Inga                  | WD: Worker \_ data/WD: relaterade \_ person \_ uppgifter                                     |
| 41 | Roll data                            | Inga                  | WD: Worker \_ -data/WD: roll \_ data                                                 |
| 42 | Kunskaps data                           | Inga                  | WD: Worker \_ -data/WD: kunskaps \_ data                                                |
| 43 | Profil data för efterföljande              | Inga                  | WD: Worker \_ -data/WD: \_ data för lyckad profil \_                                  |
| 44 | Personal utvärderings data               | Inga                  | WD: Worker \_ -data/WD: personal \_ utvärderings \_ data                                   |
| 45 | Användar konto data                    | Inga                  | WD: Worker \_ -data/WD: användar \_ konto \_ data                                        |
| 46 | Dokument data för arbetare                 | Inga                  | WD: Worker \_ -data/WD: arbetar- \_ dokument \_ data                                     |

>[!NOTE]
>Varje Workday-enhet som anges i tabellen skyddas av en **säkerhets princip** för en domän i Workday. Om du inte kan hämta några attribut som är kopplade till entiteten efter att ha angett rätt XPATH, kontrollerar du med din Workday-administratör för att säkerställa att rätt domän säkerhets princip har kon figurer ATS för den integrations system användare som är associerad med etablerings appen. För att till exempel hämta *kunskaps data* *krävs åtkomst till* arbets dag för domän *Worker-data: kunskaper och erfarenhet*. 

Här följer några exempel på hur du kan utöka Workday-integreringen för att uppfylla särskilda krav. 

**Exempel 1**

Anta att du vill hämta följande data uppsättningar från Workday och använda dem i dina etablerings regler:

* Kostnadsställe
* Hierarki för kostnads ställe
* Löne grupp

Ovanstående data uppsättningar ingår inte som standard. Hämta dessa data uppsättningar:
1. Logga in på Azure Portal och öppna din arbets dag till AD/Azure AD User Provisioning-appen. 
1. På bladet etablering redigerar du mappningarna och öppnar attribut listan för dagar från avsnittet Avancerat. 
1. Lägg till följande attribut definitioner och markera dem som "obligatoriska". Attributen kommer inte att mappas till något attribut i AD eller Azure AD. De fungerar bara som signaler till anslutningen för att hämta kostnads ställe, hierarki för kostnads ställe och löne grupps information. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Attributnamn | XPATH API-uttryck |
     >|---|---|
     >| CostCenterHierarchyFlag  |  WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER_HIERARCHY ']/wd:Organization_Reference/@wd:Descriptor |
     >| CostCenterFlag  |  WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER ']/WD: Organization_Data/WD: Organization_Code/text () |
     >| PayGroupFlag  |  WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' PAY_GROUP ']/WD: Organization_Data/WD: Organization_Reference_ID/text () |

1. När data uppsättningen kostnads ställe och löne grupp är tillgänglig i *Get_Workers* svar kan du hämta kostnads ställets namn, kostnads ställe kod och löne grupp med hjälp av följande XPath-värden. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Attributnamn | XPATH API-uttryck |
     >|---|---|
     >| CostCenterName  | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' cost center ']/WD: ORGANIZATION_NAME/text () |
     >| CostCenterCode | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' cost center ']/WD: Organization_Code/text () |
     >| PayGroup | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' löne grupp ']/WD: ORGANIZATION_NAME/text () |

**Exempel 2**

Anta att du vill hämta certifieringar som är associerade med en användare. Den här informationen är tillgänglig som en del av *kvalificerings data* uppsättningen. Använd följande XPATH för att hämta den här data uppsättningen som en del av *Get_Workers* svaret: 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

**Exempel 3**

Anta att du vill hämta *etablerings grupper* som har tilldelats en arbets tagare. Den här informationen är tillgänglig som en del av data uppsättningen för *konto etablering* . Använd följande XPATH för att hämta den här data uppsättningen som en del av *Get_Workers* svaret: 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="handling-different-hr-scenarios"></a>Hantera olika HR-scenarier

### <a name="retrieving-international-job-assignments-and-secondary-job-details"></a>Hämtar internationella jobb tilldelningar och information om sekundära jobb

Som standard hämtar anslutnings programmet attribut som är associerade med arbetarens primära jobb. Anslutningen har även stöd för att hämta *ytterligare jobb data* som är associerade med internationella jobb tilldelningar eller sekundära jobb. 

Använd stegen nedan för att hämta attribut som är associerade med internationella jobb tilldelningar: 

1. Ange arbets dag för anslutnings-URL: en med hjälp av webb tjänsten för arbets dagar, version 30,0 eller senare. Ange därför [rätt XPath-värden](workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) i din arbets Plans etablerings app. 
1. Använd väljaren `@wd:Primary_Job=0` på `Worker_Job_Data` noden för att hämta rätt attribut. 
   * **Exempel 1:** För att få `SecondaryBusinessTitle` använda XPath `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Worker_Job_Data[@wd:Primary_Job=0]/wd:Position_Data/wd:Business_Title/text()`
   * **Exempel 2:** För att få `SecondaryBusinessLocation` använda XPath `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Worker_Job_Data[@wd:Primary_Job=0]/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Location_Reference/@wd:Descriptor`

 

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du konfigurerar arbets dagar för att Active Directory etablering](../saas-apps/workday-inbound-tutorial.md)
* [Lär dig hur du konfigurerar Skriv tillbaka till Workday](../saas-apps/workday-writeback-tutorial.md)
* [Läs mer om de Workday-attribut som stöds för inkommande etablering](workday-attribute-reference.md)

