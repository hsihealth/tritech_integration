# Tri-Tech Resolve Billing Integration

## Read Only Queries

### Billing Schedules

```SQL
SELECT
  CAST(PKey as BIGINT) AS BillingSchedule_ID,
  ID AS NaturalKey,
  Description AS [Name],
  CASE WHEN Tag = 'A' THEN CAST (0 as bit) ELSE CAST(1 as bit) END AS IsDeleted,
  CASE WHEN Tag = 'I' THEN current_timestamp ELSE null END AS DeletedWhen
FROM
  Code_Schedules WITH(NOLOCK)
  INNER JOIN (
  SELECT
    DISTINCT Code_Pkey
  FROM
    Code_Schedules_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_Pkey = PKey

```

### Billing Events

```SQL
SELECT
  CAST(PKey as BIGINT) AS BillingScheduleEvent_ID,
  ID AS NaturalKey,
  Description AS [Name],
  CASE WHEN Tag = 'A' THEN CAST (0 AS BIT) ELSE CAST(1 AS BIT) END AS IsDeleted,
  CASE WHEN Tag = 'I' THEN CURRENT_TIMESTAMP ELSE null END AS DeletedWhen
FROM
  Code_Events WITH(NOLOCK)
  JOIN (
  SELECT
    DISTINCT Code_Pkey
  FROM
    Code_Events_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_Pkey = PKey

```

### Diagnoses

```SQL
SELECT
  CAST(PKey as BIGINT) AS Diagnosis_ID,
  ID AS NaturalKey,
  CASE WHEN Tag = 'A' THEN CAST(0 as bit) ELSE CAST(1 as bit) END AS IsDeleted,
  CASE WHEN Tag = 'I' THEN current_timestamp ELSE NULL END AS DeletedWhen,
  Description AS [Name],
  Default_ICD9 AS DxCode
FROM
  Code_Reasons WITH(NOLOCK)
  JOIN (
    SELECT
      DISTINCT Code_Pkey
    FROM
      Code_REasons_History WITH(NOLOCK)
    WHERE
      Modified_DateTime >= @SyncStartTime AND
      Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_PKey = PKey
```

### Caller Types

```SQL
SELECT
  CAST(PKey as BIGINT) AS CallerType_ID,
  ID AS NaturalKey,
  Description AS [Name],
  CASE WHEN Tag = 'A' THEN CAST (0 as bit) ELSE CAST(1 as bit) END AS IsDeleted,
  CASE WHEN Tag = 'I' THEN current_timestamp ELSE null END AS DeletedWhen
FROM
  Code_Callers WITH(NOLOCK)
  INNER JOIN (
  SELECT
    DISTINCT Code_Pkey
  FROM
    Code_Callers_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_Pkey = PKey
```

### Urgency Types

```SQL
SELECT
  CAST(PKey as BIGINT) AS UrgencyType_ID,
  ID AS NaturalKey,
  Description AS [Name],
  CASE WHEN Tag = 'A' THEN CAST (0 as bit) ELSE CAST(1 as bit) END AS IsDeleted,
  CASE WHEN Tag = 'I' THEN current_timestamp ELSE null END AS DeletedWhen
FROM
  Code_Urgency WITH(NOLOCK)
  INNER JOIN (
  SELECT
    DISTINCT Code_Pkey
  FROM
    Code_Urgency_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_Pkey = PKey
```

### Physicians

```SQL
SELECT
  CAST(Code_Doctors.PKey as BIGINT) AS Doctor_ID,
  CAST(Code_Doctors.Dir_PKey as BIGINT) AS Provider_ID,
  Code_Doctors.ID AS NaturalKey,
  Title,
  Last_NAME AS [LastName],
  First_NAME AS [FirstName],
  UPIN,
  NPI,
  Code_Categories.Description AS Category,
  CASE WHEN Code_Doctors.Tag = 'A' THEN CAST (0 as bit) ELSE CAST(1 as bit) END AS IsDeleted,
  CASE WHEN Code_Doctors.Tag = 'I' THEN current_timestamp ELSE null END AS DeletedWhen
FROM
  Code_Doctors WITH(NOLOCK)
  INNER JOIN (
  SELECT
    DISTINCT Code_Pkey
  FROM
    Code_Doctors_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_Pkey = PKey
  LEFT JOIN Code_Categories WITH(NOLOCK) ON Code_Categories.PKey = Code_Doctors.Report_Category_PKey

```

### Narratives
```SQL
SELECT
  CAST(PKey as BIGINT) AS Narrative_ID,
  ID AS NaturalKey,
  Description AS [Name],
  CASE WHEN Code_Narratives.Use_As_Call_Billing_Narrative = 'Y' Then CAST(1 AS BIT) ELSE CAST(0 AS BIT) END AS ForCallBilling,
  CASE WHEN Code_Narratives.Use_As_Call_Questionnaire = 'Y' Then CAST(1 AS BIT) ELSE CAST(0 AS BIT) END AS ForCallQuestionnaire,
  CASE WHEN Code_Narratives.Use_As_Call_Collections = 'Y' Then CAST(1 AS BIT) ELSE CAST(0 AS BIT) END AS ForCallCollection,
  CASE WHEN Code_Narratives.Use_As_Call_Charge_Narrative = 'Y' Then CAST(1 AS BIT) ELSE CAST(0 AS BIT) END AS ForCallCharge,
  Narrative_Text AS Description,
  CASE WHEN Tag = 'A' THEN CAST (0 as bit) ELSE CAST(1 as bit) END AS IsDeleted,
  CASE WHEN Tag = 'I' THEN current_timestamp ELSE null END AS DeletedWhen
FROM
  Code_Narratives WITH(NOLOCK)
  INNER JOIN (
  SELECT
    DISTINCT Code_Pkey
  FROM
    Code_Narratives_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_Pkey = PKey

```

### Employers
```SQL
SELECT
  CAST(Code_Employers.PKey as BIGINT) AS Employer_ID,
  Code_Employers.[ID] AS NaturalKey,
  CASE WHEN Code_Employers.Tag = 'A' THEN CAST(0 AS BIT) ELSE CAST(1 AS BIT) END AS IsDeleted,
  CASE WHEN Code_Employers.Tag = 'I' THEN CURRENT_TIMESTAMP ELSE NULL END AS DeletedWhen,
  Code_Employers.Description AS [Name],
  Code_Employers.Address1 AS [Address~Address1],
  Code_Employers.Address2 AS [Address~Address2],
  Code_Cities.Description as [Address~City],
  LEFT(Code_Cities.State_Province,2) as [Address~State],
  CASE WHEN Code_Cities.Zip_Postal IS NOT NULL THEN
    CASE WHEN Zip_Plus4 IS NULL OR LEN(LTRIM(RTRIM(Zip_Plus4))) < 1 THEN Code_Cities.Zip_Postal ELSE Code_Cities.Zip_Postal + '-' + Zip_Plus4 END
  ELSE NULL END AS [Address~Zip],
  Code_Employers.Description AS [Contact~Organization],
  CAST(0 AS BIT) AS [Contact~IsPerson],
  'Phone1=' + Code_Employers.Phone1 + '; Phone2=' + Code_Employers.Phone2 AS [Contact~PhoneNumbers]
FROM
  Code_Employers WITH(NOLOCK)
  JOIN (
  SELECT
    DISTINCT Code_Pkey
  FROM
    Code_Employers_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_Pkey = PKey
  LEFT OUTER JOIN Code_Cities WITH(NOLOCK) ON Code_Cities.PKey = Code_Employers.City_PKey

```

### Forms
```SQL
SELECT
  CAST(PKey as BIGINT) AS Form_ID,
  [ID] AS NaturalKey,
  Description AS [Name],
  System_Global_Pick_Lists.Pick_List_Text AS FormType,
  Form_Type AS FormType_ID,
  CASE WHEN Tag = 'A' THEN CAST (0 as bit) ELSE CAST(1 as bit) END AS IsDeleted,
  CASE WHEN Tag = 'I' THEN current_timestamp ELSE null END AS DeletedWhen
FROM
  Code_Forms WITH(NOLOCK)
  INNER JOIN (
  SELECT
    DISTINCT Code_Pkey
  FROM
    Code_Forms_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_Pkey = PKey
  LEFT JOIN System_Global_Pick_Lists WITH(NOLOCK) ON System_Global_Pick_Lists.Pick_List_ID = 31 AND System_Global_Pick_Lists.Pick_List_Item = Form_Type

```

### Cities
```SQL
SELECT
  CAST(PKey as BIGINT) AS City_ID,
  ID AS NaturalKey,
  Description AS [Name],
  State_province AS State,
  Zip_Postal AS Zip,
  System_Global_Pick_Lists.pick_list_text AS Country,
  CASE WHEN Tag = 'A' THEN CAST (0 as bit) ELSE CAST(1 as bit) END AS IsDeleted,
  CASE WHEN Tag = 'I' THEN current_timestamp ELSE null END AS DeletedWhen
FROM
  Code_Cities WITH(NOLOCK)
  INNER JOIN (
  SELECT
    DISTINCT Code_Pkey
  FROM
    Code_Cities_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_Pkey = PKey
  LEFT JOIN System_Global_Pick_Lists ON Pick_list_id = 16 AND Pick_list_item = Code_Cities.country

```

### Zones
```SQL
SELECT
  CAST(PKey as BIGINT) AS Zone_ID,
  CAST(Dir_PKey as BIGINT) AS Provider_ID,
  [ID] AS NaturalKey,
  Description AS [Name],
  CASE WHEN Tag = 'A' THEN CAST (0 as bit) ELSE CAST(1 as bit) END AS IsDeleted,
  CASE WHEN Tag = 'I' THEN current_timestamp ELSE null END AS DeletedWhen
FROM
  Code_Zones WITH(NOLOCK)
  INNER JOIN (
  SELECT
    DISTINCT Code_Pkey
  FROM
    Code_Zones_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_Pkey = PKey
 ```
 
### Statistics
```SQL
SELECT
  CAST(PKey as BIGINT) AS Statistic_ID,
  [ID] AS NaturalKey,
  CAST(Dir_PKey AS BIGINT) AS Provider_ID,
  Code_Statistics.Description AS [Name],
  System_Global_Statistic_Names.Description AS StatisticType,
  CAST(System_Global_Statistic_Names.Stat_Master_PKey AS BIGINT) AS StatisticType_ID,
  CASE WHEN Code_Statistics.Tag = 'A' THEN CAST (0 as bit) ELSE CAST(1 as bit) END AS IsDeleted,
  CASE WHEN Code_Statistics.Tag = 'I' THEN current_timestamp ELSE null END AS DeletedWhen
FROM
  Code_Statistics WITH(NOLOCK)
  INNER JOIN (
  SELECT
    DISTINCT Code_Pkey
  FROM
    Code_Statistics_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_Pkey = PKey
  LEFT JOIN System_Global_Statistic_Names  WITH(NOLOCK) ON System_Global_Statistic_Names.Stat_Master_PKey = Code_Statistics.Statistic_Master_PKey
```

### Staff
```SQL
SELECT
  CAST(Code_Staff.PKey AS BIGINT) AS Staff_ID,
  [ID] AS NaturalKey,
  CAST(Code_Staff.Dir_PKey AS BIGINT) AS Provider_ID,
  First_Name AS FirstName,
  Last_Name AS LastName,
  System_Global_Pick_Lists.Pick_List_Text AS StaffType,
  CAST(System_Global_Pick_Lists.Pick_List_Item AS BIGINT) AS StaffType_ID,
  Code_Categories.Description AS Category,
  CASE WHEN Tag = 'A' THEN CAST(0 AS BIT) ELSE CAST(1 AS BIT) END AS IsDeleted,
  CASE WHEN Tag = 'I' THEN CURRENT_TIMESTAMP ELSE NULL END AS DeletedWhen
FROM
  Code_Staff WITH(NOLOCK)
JOIN (
  SELECT
    DISTINCT Code_Pkey
  FROM
    Code_Staff_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_Pkey = PKey
LEFT JOIN System_Global_Pick_Lists WITH(NOLOCK) ON System_Global_Pick_Lists.Pick_List_ID = 62 AND System_Global_Pick_Lists.Pick_List_Item = Staff_Type
LEFT JOIN Code_Categories WITH(NOLOCK) ON Code_Categories.PKey = Code_Staff.Report_Category_PKey
WHERE
  User_ID IS NULL OR LEN(USER_ID) = 0  -- Non-blank User_ID indicate Sweet User
 ```
 
### Charge Types
```SQL
SELECT
  CAST(PKey as BIGINT) AS ChargeType_ID,
  ID AS NaturalKey,
  CASE WHEN Tag = 'A' Then CAST(0 as bit) ELSE Cast(1 as bit) END AS IsDeleted,
  CASE WHEN Tag = 'I' Then current_timestamp ELSE null END AS DeletedWhen,
  Description AS [Name],
  CAST(CASE WHEN Description like '%BASE RATE%' then 1
     WHEN Description like '%Miles%' then 2
     WHEN Description like '%NIGHT%' then 4
     ELSE 3
  END AS BIGINT) AS ChargeCategory_ID
FROM
  Code_Charges WITH(NOLOCK)
  JOIN (
  SELECT
    DISTINCT Code_Pkey
  FROM
    Code_Charges_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_PKey = PKey
```

### Credit Types
```SQL
SELECT
  CAST(PKey AS BIGINT) AS CreditType_ID,
  ID AS NaturalKey,
  CAST(ISNULL(CreditSubCatetory.Destination_ID, 0) AS BIGINT) AS CreditSubCategory_ID,
  CASE WHEN Tag = 'A' Then CAST(0 as bit) ELSE Cast(1 as bit) END AS IsDeleted,
  CASE WHEN TAg = 'I' Then current_timestamp ELSE null END AS DeletedWhen,
  Description AS [Name]
FROM
  Code_Credits WITH(NOLOCK)
  JOIN (
  SELECT
    DISTINCT Code_Pkey
  FROM
    Code_Credits_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_PKey = PKey
  LEFT JOIN RpmMapping.dbo.Credit_Sub_Category_Lookup AS CreditSubCatetory ON CreditSubCatetory.Source_id = Code_Credits.PKey

```

### Providers/Customers/Directories
```SQL
SELECT DISTINCT
  CAST(PKey AS BIGINT) AS Provider_ID,
  Directory_Name AS Name
FROM System_Directories
  JOIN System_Directories_History ON System_Directories_History.Directory_PKey = PKey
WHERE
  System_Directories_History.Modified_DateTime >= @SyncStartTime AND
  System_Directories_History.Modified_DateTime <= @SyncEndTime
```

### Profit Centers
```SQL
SELECT
  CAST(Code_Companies.PKey AS BIGINT) AS ProfitCenter_ID,
  Code_Companies.ID AS NaturalKey,
  CAST( CASE
      WHEN System_Directories.PKey = 2 AND Code_Companies.Description LIKE '%SIERRA%' THEN 4 -- Separate Sierra Life Flight from TSCF
      ELSE System_Directories.PKey
      END AS BIGINT) AS Provider_ID,
  CASE WHEN Code_Companies.TAg = 'A' THEN CAST (0 as bit) ELSE CAST (1 as bit) END AS IsDeleted,
  CASE WHEN Code_Companies.Tag = 'I' THEN current_timestamp ELSE null END AS DeletedWhen,
  Code_Companies.Description AS [Name],
  Code_Companies.Default_Provider_Number AS ProviderNumber,
  Code_Companies.Default_Participate_Indicator AS ParticipateIndicator,
  Code_Companies.NPI AS NPI,
  Code_Companies.NPI_Effective_Date AS NpiEffectiveDate,
  Code_Companies.Address1 AS [Address~Address1],
  Code_Companies.Address2 AS [Address~Address2],
  Code_Cities.Description as [Address~City],
  LEFT(Code_Cities.State_Province,2) as [Address~State],
  CASE WHEN Code_Cities.Zip_Postal IS NOT NULL THEN
    CASE WHEN Zip_Plus4 IS NULL OR LEN(LTRIM(RTRIM(Zip_Plus4))) < 1 THEN Code_Cities.Zip_Postal ELSE Code_Cities.Zip_Postal + '-' + Zip_Plus4 END
  ELSE NULL END AS [Address~Zip],
  Code_Companies.Site_Info_Address1 AS [SiteAddress~Address1],
  Code_Companies.Site_Info_Address2 AS [SiteAddress~Address2],
  SiteCity.Description as [SiteAddress~City],
  LEFT(SiteCity.State_Province,2) as [SiteAddress~State],
  CASE WHEN SiteCity.Zip_Postal IS NOT NULL THEN
    CASE WHEN Site_Info_Zip_Plus4 IS NULL OR LEN(LTRIM(RTRIM(Site_Info_Zip_Plus4))) < 1 THEN SiteCity.Zip_Postal ELSE SiteCity.Zip_Postal + '-' + Site_Info_Zip_Plus4 END
  ELSE NULL END AS [SiteAddress~Zip],
  Code_Companies.Description AS [Contact~Organization],
  CAST(0 as bit) AS [Contact~IsPerson],
  'Phone1=' + Code_Companies.Phone1 + '; Phone2=' + Code_Companies.Phone2 + '; Phone3=' + Code_Companies.Phone3 + '; Phone4=' + Code_Companies.Phone4 AS [Contact~PhoneNumbers]
FROM
  Code_Companies WITH(NOLOCK)
  JOIN System_Directories WITH(NOLOCK) ON System_Directories.Pkey = Code_Companies.Dir_PKey
  JOIN (
  SELECT
    DISTINCT Code_Companies_History.Code_Pkey
  FROM
    Code_Companies_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_Pkey = Code_Companies.PKey
  LEFT OUTER JOIN Code_Cities WITH(NOLOCK) ON Code_Cities.PKey = Code_Companies.City_PKey
  LEFT OUTER JOIN Code_Cities AS SiteCity WITH(NOLOCK) ON SiteCity.PKey = Code_Companies.Site_Info_City_PKey

```

### Profit Center Payors
```SQL
SELECT CAST(CAST(Company_PKey AS VARCHAR) + CAST(Payor_PKey AS VARCHAR) AS BIGINT) AS ProfitCenterPayer_ID,
  CAST( CASE
     WHEN System_Directories.PKey = 2 AND Code_Companies.Description LIKE '%SIERRA%' THEN 4 -- Separate Sierra Life Flight from TSCF
     ELSE System_Directories.PKey
     END AS BIGINT) AS Provider_ID,
  CAST(Company_PKey AS BIGINT) AS ProfitCenter_ID,
  CAST(Payor_PKey AS BIGINT) AS Payer_ID,
  Code_Payors.Description AS PayerName,
  Code_Companies_Payors.Provider_Number AS ProviderNumber,
  Code_Companies_Payors.Participating_Indicator AS ParticipateIndicator,
  Code_Companies_Payors.NPI AS NPI,
  Code_Companies_Payors.NPI_Effective_Date AS NpiEffectiveDate
FROM Code_Companies_Payors WITH(NOLOCK)
  JOIN Code_Companies WITH(NOLOCK) ON Code_Companies_Payors.Company_PKey = Code_Companies.PKey
  JOIN System_Directories WITH(NOLOCK) ON System_Directories.Pkey = Code_Companies.Dir_PKey
  JOIN Code_Payors WITH(NOLOCK) ON Code_Companies_Payors.Payor_PKey = Code_Payors.PKey
  JOIN (
    SELECT
      DISTINCT Code_Companies_History.Code_Pkey
    FROM
      Code_Companies_History WITH(NOLOCK)
    WHERE
      Modified_DateTime >= @SyncStartTime AND
      Modified_DateTime <= @SyncEndTime
   ) AS Modified ON Modified.Code_Pkey = Code_Companies.PKey

```

### Facilities
```SQL
SELECT
  CAST(Code_Locations.PKey as BIGINT) AS Facility_ID,
  Code_Locations.[ID] AS Location_ID,
  Code_Locations.[ID] AS NaturalKey,
  CASE WHEN Code_Locations.Tag = 'A' THEN CAST(0 AS BIT) ELSE CAST(1 AS BIT) END AS IsDeleted,
  CASE WHEN Code_Locations.Tag = 'I' THEN CURRENT_TIMESTAMP ELSE NULL END AS DeletedWhen,
  Code_Locations.Description AS [Name],
  Code_Locations.Address1 AS [Address~Address1],
  Code_Locations.Address2 AS [Address~Address2],
  Code_Cities.Description as [Address~City],
  Code_Zones.Description as [Address~County],
  LEFT(Code_Cities.State_Province,2) as [Address~State],
  CASE WHEN Code_Cities.Zip_Postal IS NOT NULL THEN
    CASE WHEN Zip_Plus4 IS NULL OR LEN(LTRIM(RTRIM(Zip_Plus4))) < 1 THEN Code_Cities.Zip_Postal ELSE Code_Cities.Zip_Postal + '-' + Zip_Plus4 END
  ELSE NULL END AS [Address~Zip],
  CountryList.Pick_list_text AS [Address~Country],
  Code_Locations.Description AS [Contact~Organization],
  CAST(0 AS BIT) AS [Contact~IsPerson],
  'Phone1=' + Code_Locations.Phone1 + '; Phone2=' + Code_Locations.Phone2 AS [Contact~PhoneNumbers],
  Code_Zones.ID as Zone_ID
FROM
  Code_Locations WITH(NOLOCK)
  LEFT OUTER JOIN Code_Cities WITH(NOLOCK) ON Code_Cities.PKey = Code_Locations.City_PKey
  LEFT OUTER JOIN Code_Zones WITH(NOLOCK) ON Code_Zones.PKey = Code_Locations.Zone_PKey
  LEFT OUTER JOIN System_Global_Pick_Lists AS CountryList ON CountryList.pick_list_id = 16  AND CountryList.pick_list_item = Code_Cities.country
WHERE
  Code_Locations.PKey IN (
    SELECT DISTINCT Code_Pkey FROM Code_Locations_History WITH(NOLOCK)
    WHERE Modified_DateTime >= @SyncStartTime AND Modified_DateTime <= @SyncEndTime
  )
```

### Payors
```SQL
SELECT
  CAST(Code_Payors.PKey as BIGINT) AS Payer_ID,
  Code_Payors.ID AS NaturalKey,
  CAST(Code_Payors.Payor_Type as BIGINT) AS PayerType_ID,
  Code_Categories.Description AS ReportCategory,
  CAST(Code_Categories.Pkey AS BIGINT) AS ReportCategory_ID,
  CASE WHEN Code_Payors.Tag = 'A' Then CAST(0 AS BIT) ELSE CAST(1 AS BIT) END AS IsDeleted,
  CASE WHEN Code_Payors.Tag = 'I' THEN CURRENT_TIMESTAMP ELSE NULL END AS DeletedWhen,
  Code_Payors.Description AS [Name],
  Code_Payors.Address1 AS [Address~Address1],
  Code_Payors.Address2 AS [Address~Address2],
  Code_Cities.Description AS [Address~City],
  LEFT(Code_Cities.State_Province,2) AS [Address~State],
  CASE WHEN Code_Cities.Zip_Postal IS NOT NULL THEN
    CASE WHEN Zip_Plus4 IS NULL OR LEN(LTRIM(RTRIM(Zip_Plus4))) < 1 THEN Code_Cities.Zip_Postal ELSE Code_Cities.Zip_Postal + '-' + Zip_Plus4 END
  ELSE NULL END AS [Address~Zip],
  Code_Payors.Description AS [Contact~Organization],
  CAST(0 AS BIT) AS [Contact~IsPerson],
  'Phone1=' + Code_Payors.Phone1 + '; Phone2=' + Code_Payors.Phone2 AS [Contact~PhoneNumbers]
FROM
  Code_Payors WITH(NOLOCK)
INNER JOIN (
  SELECT
    DISTINCT Code_Pkey
  FROM
    Code_Payors_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_Pkey = PKey
LEFT JOIN Code_Cities WITH(NOLOCK) ON Code_Cities.PKey = Code_Payors.City_PKey
LEFT JOIN Code_Categories ON Code_Categories.Code_type = 'PA' AND Code_Payors.Report_Category_PKey = Code_Categories.Pkey
```

### Payor Family
```SQL
SELECT
  CAST(Code_Payors.PKey as BIGINT) AS Payer_ID,
  CAST(Code_Categories.PKey AS BIGINT) AS PayerFamily_ID,
  Code_Categories.Description AS FamilyName
FROM
  Code_Payors WITH(NOLOCK)
INNER JOIN Code_Common_DECategories WITH(NOLOCK) ON Code_Payors.PKey = Code_Common_DECategories.Code_Pkey AND Code_Common_DECategories.Code_type = 'PA'
LEFT JOIN Code_Categories WITH(NOLOCK) ON Code_Categories.PKey = Code_Common_DECategories.Category_PKey AND Code_Categories.Code_type = 'PA'
INNER JOIN (
  SELECT
    DISTINCT Code_Pkey
  FROM
    Code_Payors_History WITH(NOLOCK)
  WHERE
    Modified_DateTime >= @SyncStartTime AND
    Modified_DateTime <= @SyncEndTime
  ) AS Modified ON Modified.Code_Pkey = Code_Payors.PKey
```

### Patients
```SQL
SELECT
  CAST(Patient.PKey AS BIGINT) AS Patient_ID,
  CASE WHEN Patient.Tag = 'A' THEN CAST(0 as bit) ELSE CAST(1 AS BIT) END AS IsDeleted,
  CASE WHEN Patient.Tag = 'I' THEN CURRENT_TIMESTAMP ELSE null END AS DeletedWhen,
  Patient.Patient_Number AS PatientNumber,
  Patient.Last_Name AS [Contact~LastName],
  Patient.First_Name AS [Contact~FirstName],
  Patient.Middle_Initial,
  Generation.Pick_List_Text AS Generation,
  Patient.Date_Of_Birth AS DateOfBirth,
  CAST(Patient.Sex as BIGINT) AS Gender,
  CAST(Patient.SSN AS VARCHAR) AS SocialSecurityNumber,
  Patient.Last_Modified_Date AS SourceRecordUpdatedWhen,
  Patient.Address1 AS [Contact~Address~Address1],
  Patient.Address2 AS [Contact~Address~Address2],
  Code_Cities.Description AS [Contact~Address~City],
  LEFT(Code_Cities.State_Province,2) AS [Contact~Address~State],
  Code_Zones.Description as [Contact~Address~County],
  CASE WHEN Code_Cities.Zip_Postal IS NOT NULL THEN
    CASE WHEN Zip_Plus4 IS NULL OR LEN(LTRIM(RTRIM(Zip_Plus4))) < 1 THEN Code_Cities.Zip_Postal ELSE Code_Cities.Zip_Postal + '-' + Zip_Plus4 END
  ELSE NULL END AS [Contact~Address~Zip],
  CountryList.pick_list_text AS [Contact~Address~Country],
  CAST(1 AS BIT) AS [Contact~IsPerson],
  'Phone1=' + Patient.Phone1 + '; Phone2=' + Patient.Phone2 AS [Contact~PhoneNumbers],
  Patient.E_Mail_Address AS [Contact~EmailAddress],
  Patient.Medicare_Number AS MedicareNumber,
  Patient.Medicaid_Number AS MedicaidNumber,
  CASE WHEN Patient.Patient_Deceased = 'Y' THEN CAST(1 AS BIT) ELSE CAST(0 AS BIT) END AS IsDeceased,
  Patient.Marital AS MaritalStatus_ID,
  Patient.Language AS Language_ID,
  CASE WHEN Patient.LifeTime_signature = 'Y' THEN CAST(1 AS BIT) ELSE CAST(0 AS BIT) END AS LifeTimeSignature,
  CAST(Patient.Signature_Source AS BIGINT) AS SignatureSourceType,
  Patient.Signature_Effective_Date AS SignatureEffectiveDate,
  Patient.sign_source_payor_medicare AS SignSourceMedicare,
  Patient.sign_source_payor_medicaid AS SignSourceMedicaid,
  Patient.sign_source_payor_insurance AS SignSourceInsurance,
  Patient.Member_Effective_Date AS MemberEffectiveDate,
  Patient.Member_Expiration_Date AS MemberExpirationDate
FROM
  Patient WITH(NOLOCK)
  LEFT OUTER JOIN Code_Cities WITH(NOLOCK) ON Code_Cities.PKey = Patient.City_PKey
  LEFT OUTER JOIN Code_Zones WITH(NOLOCK) ON Code_Zones.PKey = Patient.Zone_PKey
  LEFT OUTER JOIN System_Global_Pick_Lists AS Generation ON Generation.Pick_List_Item = Patient.Generation AND Pick_List_ID = 35
  LEFT OUTER JOIN System_Global_Pick_Lists AS CountryList ON CountryList.pick_list_id = 16  AND CountryList.pick_list_item = Code_Cities.country
WHERE
  (@BillingRecordID = 0 AND Last_Modified_Date >= @SyncStartTime AND Last_Modified_Date <= @SyncEndTime )
  OR ( @BillingRecordID > 0 AND Patient.PKey IN (SELECT DISTINCT Patient_PKey FROM Call WITH(NOLOCK) WHERE PKEY = @BillingRecordID) )

```

### Patient Payor
```SQL
SELECT
  CAST(CAST(Patient_PKey AS VARCHAR) + CAST(Sequence AS VARCHAR) AS BIGINT) AS PatientPayer_ID,
  CASE WHEN Patient_Payors.Tag = 'A' THEN CAST(0 AS BIT) ELSE CAST(0 AS BIT) END AS IsDeleted,
  CASE WHEN Patient_Payors.Tag = 'I' THEN CURRENT_TIMESTAMP ELSE NULL END AS DeletedWhen,
  CAST(Patient_PKey AS BIGINT) AS Patient_ID,
  CAST(Patient_Payors.Payor_Pkey AS BIGINT) AS Payer_ID,
  CAST(Coverage AS BIGINT) AS Coverage_ID,
  CAST(Relationship AS BIGINT) AS Relationship_ID,
  Policy_Number AS PolicyNumber,
  Group_Number AS GroupNumber,
  Group_Name AS GroupName,
  CAST(Payment_Source AS BIGINT) AS PaymentSource_ID,
  CAST(Eligibility_Status AS BIGINT) AS EligibilityStatus_ID,
  CAST(Guarantor_PKey AS BIGINT) AS Guarantor_ID,
  Sequence AS OrderIndex
FROM
  Patient_Payors WITH(NOLOCK)
  INNER JOIN Patient WITH(NOLOCK) ON Patient_Payors.Patient_PKey = Patient.PKey
  INNER JOIN (
    SELECT DISTINCT
      Primary_Payor_PKey AS Payor_PKey
    FROM
      Call WITH(NOLOCK)
    WHERE
      @BillingRecordID = 0 OR (@BillingRecordID > 0 AND Call.PKey = @BillingRecordID)
    UNION
    SELECT DISTINCT
      Current_Payor_PKey AS Payor_PKey
    FROM
      Call WITH(NOLOCK)
    WHERE
      @BillingRecordID = 0 OR (@BillingRecordID > 0 AND Call.PKey = @BillingRecordID)
  ) AS EffectitvePayors ON EffectitvePayors.Payor_PKey = Patient_Payors.Payor_PKey
WHERE
  @BillingRecordID > 0 OR
  (@BillingRecordID = 0 AND Patient.Last_Modified_Date >= @SyncStartTime AND Patient.Last_Modified_Date <= @SyncEndTime)
```

### CMS Signatures
```SQL
SELECT
  CAST(Patient_PKey AS BIGINT) AS Patient_ID,
  CAST(Patient_HIPAA.Patient_PKey AS NVARCHAR(20)) + '-' + CAST(Patient_HIPAA.Series AS NVARCHAR(36)) AS CmsSignatureHistory_ID,
  NULL AS SignatureSourceType,
  Signed_Requested_By AS Relationship_ID, -- TODO: find out what Signed_Requested_By values mean
  Signed_Requested_By_Name AS SignedByName,
  Effective_Date AS EffectiveDate,
  CASE WHEN Patient_HIPAA.Tag = 'A' THEN CAST(0 AS BIT) ELSE CAST(0 AS BIT) END AS IsDeleted
FROM Patient_HIPAA WITH(NOLOCK)
  INNER JOIN Patient WITH(NOLOCK) ON Patient.PKey = Patient_HIPAA.Patient_PKey
  INNER JOIN (
    SELECT DISTINCT
      Patient_PKey AS Effective_Patient_PKey
    FROM
      Call WITH(NOLOCK)
    WHERE
      Patient_PKey IS NOT NULL AND
      (@BillingRecordID = 0 OR (@BillingRecordID > 0 AND Call.PKey = @BillingRecordID))
  ) AS EffectivePatients ON EffectivePatients.Effective_Patient_PKey = Patient_PKey
WHERE
  @BillingRecordID > 0 OR
    (@BillingRecordID = 0 AND Last_Modified_Date >= @SyncStartTime AND Last_Modified_Date <= @SyncEndTime )
```

### Patient PCS
```SQL
SELECT
  CAST(Patient_PCS.Patient_PKey AS BIGINT) AS Patient_ID,
  Patient_PCS.Effective_Date as EffectiveDate,
  Patient_PCS.Expires_Date as ExpiresDate
FROM
  Patient_PCS WITH(NOLOCK)
  INNER JOIN Patient WITH(NOLOCK) ON Patient.PKey = Patient_PCS.Patient_PKey
  INNER JOIN (
    SELECT DISTINCT
      Patient_PKey
    FROM
      Call WITH(NOLOCK)
    WHERE
      Patient_PKey IS NOT NULL AND
      (@BillingRecordID = 0 OR (@BillingRecordID > 0 AND Call.PKey = @BillingRecordID))
  ) AS EffectivePatients ON EffectivePatients.Patient_PKey = Patient.PKey
WHERE
  Narrative_Text NOT LIKE '%invalid%' AND
  @BillingRecordID > 0 OR
  (@BillingRecordID = 0 AND Patient.Last_Modified_Date BETWEEN @SyncStartTime AND @SyncEndTime)

```

### Claims
```SQL
SELECT
   CAST(Call.PKey AS BIGINT) AS BillingRecord_ID,
   CASE WHEN Call.Tag = 'A' THEN CAST(0 AS BIT) ELSE CAST(1 AS BIT) END AS IsDeleted,
   CASE WHEN Call.Tag = 'I' THEN CURRENT_TIMESTAMP ELSE null END AS DeletedWhen,
   Call.Call_Number AS SourceSystemRecordNumber,
   Call.Call_Date AS DateOfService,
   CAll.Log_Date,
   CAST(Call.Patient_PKey AS BIGINT) AS Patient_ID,
   CAST(Call.Incident_Number_Numeric AS BIGINT) AS CadRecordID,
   TypeOfService.TypeOfService_ID,
   CAST(Call.Company_PKey AS BIGINT) AS ProfitCenter_ID,
   CAST(Call.Current_Payor_Pkey AS BIGINT) AS CurrentPayer_ID,
   CAST(Call.Primary_Payor_PKey AS BIGINT) AS PrimaryPayer_ID,
   CAST(CAll.Schedule_Pkey AS BIGINT) AS CurrentSchedule_ID,
   CAST(CAll.Event_Pkey AS BIGINT) AS CurrentEvent_ID,
   (Call.Miles_Loaded / 10.0) as LoadedMiles,
   (Call.Miles_Total / 10.0) as TotalMiles,
   CAST(Total_Credits / 100.0 AS DECIMAL(19,2)) AS TotalCredits,
   CAST(Total_Charges / 100.0 AS DECIMAL(19,2)) AS TotalCharges,
   CAST(Balance / 100.0 AS DECIMAL(19,2)) AS Balance,
   CASE WHEN Call.Accident_Indicator = 1 THEN 'Auto Accident'
        WHEN Call.Accident_Indicator = 2 THEN 'Other Accident'
        WHEN Call.Accident_Indicator = 3 THEN 'Not an Accident'
        ELSE 'Unspecified'
   END AS AccidentIndicator,
   CAST(ISNULL(LevelOfCare.Destination_ID,0) AS BIGINT) AS LevelOfCare_ID,
   Code_Levels_of_Care.ID AS LevelOfCareNaturalKey,
   CAST(ISNULL(Call.From_Location_PKey,0) AS BIGINT) AS PickupFacility_ID,
   CAST(Call.To_Location_PKey AS BIGINT) AS ReceivingFacility_ID,
   CASE WHEN TypeOfService.TypeOfService_ID = 20 AND From_Location_Detail_Exist <> 'Y' THEN NULL
     WHEN FromDetail.Address1 IS NULL OR LEN(LTRIM(RTRIM(FromDetail.Address1))) < 1 THEN FromLoc.Address1
       ELSE FromDetail.Address1
       END AS [PickupLocation~Address1],
   CASE WHEN TypeOfService.TypeOfService_ID = 20 AND From_Location_Detail_Exist <> 'Y' THEN NULL
       WHEN FromDetail.Address2 IS NULL OR LEN(LTRIM(RTRIM(FromDetail.Address2))) < 1 THEN FromLoc.Address2
       ELSE FromDetail.Address2
       END AS [PickupLocation~Address2],
   CASE WHEN TypeOfService.TypeOfService_ID = 20 AND From_Location_Detail_Exist <> 'Y' THEN NULL
       WHEN FromDetail.City IS NULL THEN FromLoc.City
       ELSE FromDetail.City
       END AS [PickupLocation~City],
   CASE WHEN TypeOfService.TypeOfService_ID = 20 THEN NULL
     WHEN FromZone.Description IS NULL THEN FromLoc.County
     ELSE FromZone.Description
     END AS [PickupLocation~County],
   CASE WHEN TypeOfService.TypeOfService_ID = 20 AND From_Location_Detail_Exist <> 'Y' THEN NULL
       WHEN FromDetail.State IS NULL THEN FromLoc.State
     ELSE FromDetail.State
       END AS [PickupLocation~State],
   CASE WHEN TypeOfService.TypeOfService_ID = 20 AND From_Location_Detail_Exist <> 'Y' THEN NULL
       WHEN FromDetail.Zip IS NULL OR LEN(LTRIM(RTRIM(FromDetail.Zip))) < 5 THEN FromLoc.Zip
       ELSE FromDetail.Zip
       END AS [PickupLocation~Zip],
   CASE WHEN ToDetail.Address1 IS NULL OR LEN(LTRIM(RTRIM(ToDetail.Address1))) < 1
       THEN ToLoc.Address1
       ELSE ToDetail.Address1
       END AS [DropoffLocation~Address1],
   CASE WHEN ToDetail.Address2 IS NULL OR LEN(LTRIM(RTRIM(ToDetail.Address2))) < 1
       THEN ToLoc.Address2
       ELSE ToDetail.Address2
       END AS [DropoffLocation~Address2],
   CASE WHEN ToDetail.City IS NULL THEN ToLoc.City
       ELSE ToDetail.City
       END AS [DropoffLocation~City],
   CASE WHEN ToZone.Description IS NULL THEN ToLoc.County
     ELSE ToZone.Description
     END AS [DropoffLocation~County],
   CASE WHEN ToDetail.State IS NULL THEN ToLoc.State
       ELSE ToDetail.State
       END AS [DropoffLocation~State],
   CASE WHEN ToDetail.Zip IS NULL OR LEN(LTRIM(RTRIM(ToDetail.Zip))) < 5 THEN ToLoc.Zip
       ELSE ToDetail.Zip
       END AS [DropoffLocation~Zip],
  Call.Last_Modified_User_Date AS SourceRecordUpdatedWhen,
  CASE
    WHEN Call.Sign_Source_Date IS NOT NULL AND Call.Sign_Source > 0
    THEN CAST(1 AS BIT)
    ELSE CAST(0 AS BIT)
  END AS IsConsentFormOnFile,
  CASE WHEN Call.Bill_as_Emergency = 1 THEN CAST(1 AS BIT) ELSE CAST(0 AS BIT) END As BillAsEmergency,
  Call.Pcs_Status AS PcsStatus_ID,
  Call_Billing_Narratives.Billing_Narrative_1 AS Narrative,
  Call.Sign_Source_Date AS SignatureDate,
  Call.Sign_Source AS SignatureSourceType,
  Call.sign_source_payor_medicare AS SignSourceMedicare,
  Call.sign_source_payor_medicaid AS SignSourceMedicaid,
  Call.sign_source_payor_insurance AS SignSourceInsurance,
  CAST(Unit.PKey AS BIGINT) AS Unit_ID,
  Unit.ID AS UnitNaturalKey,
  Unit.Description AS Unit_Name
FROM Call WITH(NOLOCK)
   JOIN (
     SELECT PKey,
        CAST(CASE WHEN ID = '911' THEN 10
         WHEN ID IN ('INTF','02') THEN 20
         WHEN ID = 'TNT' THEN 30
         ELSE 40
        END AS BIGINT) AS TypeOfService_ID
     FROM Types_Call
   ) AS TypeOfService ON TypeOfService.PKey = Call.Call_Type_PKey
   JOIN (
    SELECT DISTINCT Call_PKey FROM (
    SELECT DISTINCT Call_PKey FROM Call_Charges WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Credits WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Event_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Date_Time >= @SyncStartTime AND Date_Time <= @SyncEndTime
    UNION
    SELECT PKey AS Call_PKey FROM Call WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Last_Modified_User_Date >= @SyncStartTime AND Last_Modified_User_Date <= @SyncEndTime
        UNION
    SELECT DISTINCT Call_PKey FROM Call_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Modified_DateTime >= @SyncStartTime AND Modified_DateTime <= @SyncEndTime
    UNION
      SELECT ISNULL(@BillingRecordID,0) AS Call_PKey
   ) AS Temp ) AS ChangedCalls ON ChangedCalls.Call_PKey = Call.PKey
   LEFT JOIN (
      SELECT
        Code_Locations.PKey,
        Address1,
        Address2,
        Code_Cities.Description AS City,
        Code_Zones.Description AS County,
        LEFT(Code_Cities.State_Province,2) AS State,
        Code_Cities.Zip_Postal AS Zip
      FROM Code_Locations WITH(NOLOCK)
        LEFT JOIN Code_Cities WITH(NOLOCK) ON Code_Cities.PKey = Code_Locations.City_PKey
        LEFT JOIN Code_Zones WITH(NOLOCK) ON Code_Zones.PKey = Code_Locations.Zone_PKey
   ) AS FromLoc ON FromLoc.PKey = Call.From_Location_PKey
   LEFT JOIN (
      SELECT
        Code_Locations.PKey,
        Address1,
        Address2,
        Code_Cities.Description AS City,
        Code_Zones.Description AS County,
        LEFT(Code_Cities.State_Province,2) AS State,
        Code_Cities.Zip_Postal AS Zip
      FROM Code_Locations WITH(NOLOCK)
        LEFT JOIN Code_Cities WITH(NOLOCK) ON Code_Cities.PKey = Code_Locations.City_PKey
        LEFT JOIN Code_Zones WITH(NOLOCK) ON Code_Zones.PKey = Code_Locations.Zone_PKey
   ) AS ToLoc ON ToLoc.PKey = Call.To_Location_PKey
   LEFT JOIN (
    SELECT Call_PKey,
      Location_address_1 AS Address1,
      Location_address_2 AS Address2,
      Code_Cities.Description AS City,
      LEFT(Code_Cities.State_Province,2) AS State,
      Zip_Postal AS Zip,
      Type
    FROM Call_Locations_Detail WITH(NOLOCK)
      LEFT JOIN Code_Cities WITH(NOLOCK) ON Code_Cities.PKey = Location_City_PKey
   ) AS FromDetail ON FromDetail.Call_PKey = Call.PKey AND FromDetail.[Type]='P'
   LEFT JOIN (
    SELECT Call_PKey,
      Location_address_1 AS Address1,
      Location_address_2 AS Address2,
      Code_Cities.Description AS City,
      LEFT(Code_Cities.State_Province,2) AS State,
      Zip_Postal AS Zip,
      Type
    FROM Call_Locations_Detail WITH(NOLOCK)
      LEFT JOIN Code_Cities WITH(NOLOCK) ON Code_Cities.PKey = Location_City_PKey
   ) AS ToDetail ON ToDetail.Call_PKey = Call.PKey AND ToDetail.[Type]='D'
   LEFT JOIN Code_Levels_of_Care WITH(NOLOCK) ON Code_Levels_of_Care.PKey = Call.Level_of_care_pkey
   LEFT JOIN RpmMapping.dbo.Level_Of_Care_Lookup AS LevelOfCare ON LevelOfCare.Source_ID = Call.Level_of_care_pkey
   LEFT JOIN Code_Zones AS FromZone WITH(NOLOCK) ON FromZone.PKey = Call.From_Zone_PKey
   LEFT JOIN Code_Zones AS ToZone WITH(NOLOCK) ON ToZone.PKey = Call.To_Zone_PKey
   LEFT JOIN Code_Units AS Unit WITH(NOLOCK) ON Unit.PKey = Call.Unit_PKey
   LEFT JOIN Call_Billing_Narratives WITH(NOLOCK) ON Call_Billing_Narratives.Call_PKey = Call.PKey
```

### Charges
```SQL
SELECT
  CAST(CAST(Call_Charges.Call_PKey AS VARCHAR) + CAST(Sequence AS VARCHAR) AS BIGINT) AS Charge_ID,
  CAST(Call_Charges.Call_PKey AS BIGINT) AS BillingRecord_ID,
  CAST(Call_Charges.Charge_PKey AS BIGINT) AS ChargeType_ID,
  CASE WHEN Call_Charges.Tag = 'A' THEN Cast(0 AS BIT) ELSE Cast(1 AS BIT) END AS IsDeleted,
  CASE WHEN Call_Charges.Tag = 'I' THEN CURRENT_TIMESTAMP ELSE NULL END AS DeletedWhen,
  Call_Charges.Log_Date AS ChargeEnteredDate,
  Call_Charges.Charge_Date AS EffectiveDate,
  (CAST(PPU AS BIGINT) * CAST(Qty AS BIGINT)) / 10000.0 AS Amount,
  PPU /100.0 AS UnitCost,
  Qty/100.0 AS Quantity,
  ISNULL(UserLookup.Destination_ID, CAST (Call_Charges.User_PKey AS BIGINT)) AS [User_ID],
  CASE WHEN Code_Charges_Payors.HCPCS IS NOT NULL AND Code_Charges_Payors.HCPCS <> '' THEN
      Code_Charges_Payors.HCPCS + ISNULL(FromLoc.Default_Modifier,'') + ISNULL(ToLoc.Default_Modifier,'')
      ELSE Code_Charges.HCPCS
  END AS HCPCS,
  CAST(Code_Charges.PKey AS BIGINT) AS ChargeCode_ID
FROM
  Call_Charges WITH(NOLOCK)
  JOIN (
    SELECT DISTINCT Call_PKey FROM (
    SELECT DISTINCT Call_PKey FROM Call_Charges WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Credits WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Event_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Date_Time >= @SyncStartTime AND Date_Time <= @SyncEndTime
    UNION
    SELECT PKey AS Call_PKey FROM Call WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Last_Modified_User_Date >= @SyncStartTime AND Last_Modified_User_Date <= @SyncEndTime
        UNION
    SELECT DISTINCT Call_PKey FROM Call_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Modified_DateTime >= @SyncStartTime AND Modified_DateTime <= @SyncEndTime
    UNION
      SELECT ISNULL(@BillingRecordID,0) AS Call_PKey
    ) AS Temp ) AS ChangedCalls ON ChangedCalls.Call_PKey = Call_Charges.Call_PKey
    JOIN Call WITH(NOLOCK) ON Call.PKey = Call_Charges.Call_PKey
    LEFT JOIN Code_Locations AS FromLoc WITH(NOLOCK) ON FromLoc.PKey = Call.From_Location_PKey
    LEFT JOIN Code_Locations AS ToLoc WITH(NOLOCK) ON ToLoc.PKey = Call.To_Location_PKey
    LEFT JOIN Code_Charges_Payors WITH(NOLOCK) ON Code_Charges_Payors.Charge_PKey = Call_Charges.Charge_PKey AND Code_Charges_Payors.Payor_PKey = Call.Current_Payor_PKey
    LEFT OUTER JOIN RpmMapping.dbo.User_Lookup as UserLookup on
    UserLookup.Source_ID = CAST(Call_Charges.User_PKey AS BIGINT)
  LEFT JOIN Code_Charges WITH(NOLOCK) ON Code_Charges.PKey = Call_Charges.Charge_PKey
ORDER BY Call_Charges.Sequence
```

### Credits
```SQL
SELECT
  (CAST(Call_Credits.Call_PKey as BIGINT) * 10000 + Call_Credits.Sequence) as Credit_ID,
  CAST(Call_Credits.Call_PKey AS BIGINT) AS BillingRecord_ID,
  CASE WHEN Call_Credits.Tag = 'A' THEN CAST(0 AS BIT) ELSE CAST(1 AS BIT) END AS IsDeleted,
  CASE WHEN Call_Credits.Tag = 'I' THEN CURRENT_TIMESTAMP ELSE NULL END AS DeletedWhen,
  CAST(Call_Credits.Credit_PKey AS BIGINT) AS CreditType_ID,
  Call_Credits.Amount / 100.0 AS Amount,
  Call_Credits.Received_Date CreditReceivedDate,
  Call_Credits.Log_Date AS CreditEnteredDate,
  Call_Credits.REceived_Date AS EffectiveDate,
  CAST(Call_Credits.Payor_PKey AS BIGINT) AS Payer_ID,
  ISNULL(UserLookup.Destination_ID, CAST(Call_Credits.User_PKey AS BIGINT)) AS [User_ID],
  Code_Staff.[ID] AS UserNaturalKey,
  CAST(Code_Credits.PKey AS BIGINT) AS CreditCode_ID
FROM
  Call_Credits WITH(NOLOCK)
  JOIN (
    SELECT DISTINCT Call_PKey FROM (
    SELECT DISTINCT Call_PKey FROM Call_Charges WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Credits WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Event_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Date_Time >= @SyncStartTime AND Date_Time <= @SyncEndTime
    UNION
    SELECT PKey AS Call_PKey FROM Call WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Last_Modified_User_Date >= @SyncStartTime AND Last_Modified_User_Date <= @SyncEndTime
        UNION
    SELECT DISTINCT Call_PKey FROM Call_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Modified_DateTime >= @SyncStartTime AND Modified_DateTime <= @SyncEndTime
    UNION
      SELECT ISNULL(@BillingRecordID,0) AS Call_PKey
   ) AS Temp ) AS ChangedCalls ON ChangedCalls.Call_PKey = Call_Credits.Call_PKey
  LEFT OUTER JOIN RpmMapping.dbo.User_Lookup as UserLookup on UserLookup.Source_ID = CAST(Call_Credits.User_PKey AS BIGINT)
  LEFT JOIN Code_Credits WITH(NOLOCK) ON Code_Credits.PKey = Call_Credits.Credit_PKey
  LEFT JOIN Code_Staff WITH(NOLOCK) ON Code_Staff.PKey = Call_Credits.User_PKey
ORDER BY Call_Credits.Sequence
```

### Claim Diagnoses / Problems / Reasons
```SQL
SELECT
  CAST(CAST(Call_Reasons.Call_PKey AS VARCHAR) + CAST(Call_Reasons.Sequence AS VARCHAR) AS BIGINT) AS BillingRecordDiagnosis_ID,
  CAST(Call_Reasons.Call_PKey AS BIGINT) AS BillingRecord_ID,
  CAST(Call_Reasons.Reason_PKey AS BIGINT) AS Diagnosis_ID,
  cast(Call_Reasons.Sequence as INT) AS OrderIndex
FROM
  Call_Reasons WITH(NOLOCK)
  JOIN (
    SELECT DISTINCT Call_PKey FROM (
    SELECT DISTINCT Call_PKey FROM Call_Charges WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Credits WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Event_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Date_Time >= @SyncStartTime AND Date_Time <= @SyncEndTime
    UNION
    SELECT PKey AS Call_PKey FROM Call WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Last_Modified_User_Date >= @SyncStartTime AND Last_Modified_User_Date <= @SyncEndTime
        UNION
    SELECT DISTINCT Call_PKey FROM Call_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Modified_DateTime >= @SyncStartTime AND Modified_DateTime <= @SyncEndTime
    UNION
      SELECT ISNULL(@BillingRecordID,0) AS Call_PKey
   ) AS Temp ) AS ChangedCalls ON ChangedCalls.Call_PKey = Call_Reasons.Call_PKey
WHERE
   Call_Reasons.TYPE IS NULL OR Call_Reasons.TYPE = 1

```

### Claim Notes
```SQL
SELECT
  CAST(CAST(Call_Collections.Call_Pkey AS VARCHAR) + CAST(Series AS VARCHAR) AS BIGINT) AS SourceSystemRecordID,
  CAST(CAST(Call_Collections.Call_Pkey AS VARCHAR) + CAST(Series AS VARCHAR) AS BIGINT) AS BillingRecordNote_ID,
  CAST (Call_Collections.Call_PKey AS BIGINT) AS BillingRecord_ID,
  Entered_Date AS NoteTime,
  CASE WHEN Call_Collections.Tag = 'A' THEN CAST(0 AS BIT) ELSE CAST (1 AS BIT) END AS IsDeleted,
  CASE WHEN Call_Collections.Tag = 'I' THEN CURRENT_TIMESTAMP ELSE NULL END AS DeletedWhen,
  CASE WHEN Code_Narratives.ID IS NULL THEN Comment
     ELSE Code_Narratives.ID + ': ' + Comment
  END AS NoteText,
  ISNULL(UserLookup.Destination_ID, CAST (User_PKey AS BIGINT)) AS Author_ID
FROM
  Call_Collections WITH(NOLOCK)
  JOIN (
    SELECT DISTINCT Call_PKey FROM (
    SELECT DISTINCT Call_PKey FROM Call_Charges WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Credits WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Event_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Date_Time >= @SyncStartTime AND Date_Time <= @SyncEndTime
    UNION
    SELECT PKey AS Call_PKey FROM Call WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Last_Modified_User_Date >= @SyncStartTime AND Last_Modified_User_Date <= @SyncEndTime
        UNION
    SELECT DISTINCT Call_PKey FROM Call_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Modified_DateTime >= @SyncStartTime AND Modified_DateTime <= @SyncEndTime
    UNION
      SELECT ISNULL(@BillingRecordID,0) AS Call_PKey
   ) AS Temp ) AS ChangedCalls ON ChangedCalls.Call_PKey = Call_Collections.Call_PKey
  LEFT OUTER JOIN RpmMapping.dbo.User_Lookup as UserLookup on
    UserLookup.Source_ID = CAST(User_PKey AS BIGINT)
  LEFT OUTER JOIN Code_Narratives WITH(NOLOCK) ON Code_Narratives.PKey = Call_Collections.Narrative_PKey
```

### Claim History
```SQL
SELECT
  Call_Event_History.Event_History_PKey as BillingRecordHistory_ID,
  CAST (Call_Event_History.Call_PKey AS BIGINT) AS BillingRecord_ID,
  Call_Event_History.Event_History_PKey as SourceSystemRecordID,
  Date_Time as [Time],
  ISNULL(UserLookup.Destination_ID, CAST (Call_Event_History.Creating_User_PKey AS BIGINT)) as [User_ID],
  System_Global_Pick_Lists.Pick_List_Text as [Action],
  Call_Event_History.Current_Payor_PKey as Payer_ID,
  CAST(Call_Event_History.Schedule_Pkey AS BIGINT) AS Schedule_ID,
  Call_Event_History.Event_PKey as Event_ID,
  Code_Forms.Description as Form
FROM
  Call_Event_History WITH(NOLOCK)
  INNER JOIN (
    SELECT DISTINCT Call_PKey FROM (
    SELECT DISTINCT Call_PKey FROM Call_Charges WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Event_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Date_Time >= @SyncStartTime AND Date_Time <= @SyncEndTime
    UNION
    SELECT PKey AS Call_PKey FROM Call WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Last_Modified_User_Date >= @SyncStartTime AND Last_Modified_User_Date <= @SyncEndTime
        UNION
    SELECT DISTINCT Call_PKey FROM Call_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Modified_DateTime >= @SyncStartTime AND Modified_DateTime <= @SyncEndTime
    UNION
      SELECT ISNULL(@BillingRecordID,0) AS Call_PKey
  ) AS Temp ) AS ChangedCalls ON ChangedCalls.Call_PKey = Call_Event_History.Call_PKey
  LEFT OUTER JOIN Code_Forms on
    Code_Forms.PKey = Call_Event_History.Form_PKey
  LEFT OUTER JOIN RpmMapping.dbo.User_Lookup as UserLookup on
    UserLookup.Source_ID = CAST(Call_Event_History.Creating_User_PKey AS BIGINT)
  LEFT OUTER JOIN System_Global_Pick_Lists on
    System_Global_Pick_Lists.Pick_List_Item = Call_Event_History.History_Type and
    System_Global_Pick_Lists.Pick_List_ID = 107
```

### Claim Questionnaire
```SQL
SELECT
  CAST(ChangedCalls.Call_PKey AS BIGINT) AS BillingRecord_ID,
  CAST(Call_Questions.Question_PKey AS BIGINT) AS Questionnaire_ID,
  System_Global_Questions.Description AS Question,
  CASE
      WHEN Call_Questions_Text.Text_Answer IS NOT NULL THEN Call_Questions_Text.Text_Answer
      WHEN System_Global_Questions_Lists.Answer_Description IS NOT NULL THEN System_Global_Questions_Lists.Answer_Description
    ELSE System_Global_Questions_Lists.List_Description
  END AS Answer,
  System_Global_Questions_Lists.List_ID AS AnwserType,
  System_Global_Questions_Lists.Answer_Item AS Answer_ID
FROM Call_Questions WITH(NOLOCK)
  JOIN (
    SELECT DISTINCT Call_PKey FROM (
    SELECT DISTINCT Call_PKey FROM Call_Charges WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Credits WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Event_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Date_Time >= @SyncStartTime AND Date_Time <= @SyncEndTime
    UNION
    SELECT PKey AS Call_PKey FROM Call WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Last_Modified_User_Date >= @SyncStartTime AND Last_Modified_User_Date <= @SyncEndTime
        UNION
    SELECT DISTINCT Call_PKey FROM Call_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Modified_DateTime >= @SyncStartTime AND Modified_DateTime <= @SyncEndTime
    UNION
      SELECT ISNULL(@BillingRecordID,0) AS Call_PKey
   ) AS Temp ) AS ChangedCalls ON ChangedCalls.Call_PKey = Call_Questions.Call_PKey
  LEFT JOIN Call_Questions_Text WITH(NOLOCK) ON Call_Questions_Text.Call_Pkey = Call_Questions.Call_PKey AND Call_Questions_Text.Question_PKey = Call_Questions.Question_PKey
  LEFT JOIN System_Global_Questions ON System_Global_Questions.Question_PKey = Call_Questions.Question_PKey
  LEFT JOIN System_Global_Questions_Lists ON System_Global_Questions_Lists.List_ID = System_Global_Questions.List_ID
    AND System_Global_Questions_Lists.Answer_Item = Call_Questions.Answer_Item
WHERE
  Call_Questions.Question_PKey IN (1,2,3,4,6,7,8,9,12,13,39,40,584)
ORDER BY ChangedCalls.Call_PKey
```

### Dispatch Time Stamps
```
SELECT
  CAST(Call_Times.Call_PKey AS BIGINT) AS BillingRecord_ID,
  CAST(System_Global_Time_Types.Time_system_ID AS BIGINT) AS DispatchTimeStamp_ID,
  Call_Times.DateTime AS TimeStamp
FROM Call_Times WITH(NOLOCK)
  JOIN (
    SELECT DISTINCT Call_PKey FROM (
    SELECT DISTINCT Call_PKey FROM Call_Charges WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Credits WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Event_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Date_Time >= @SyncStartTime AND Date_Time <= @SyncEndTime
    UNION
    SELECT PKey AS Call_PKey FROM Call WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Last_Modified_User_Date >= @SyncStartTime AND Last_Modified_User_Date <= @SyncEndTime
        UNION
    SELECT DISTINCT Call_PKey FROM Call_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Modified_DateTime >= @SyncStartTime AND Modified_DateTime <= @SyncEndTime
    UNION
      SELECT ISNULL(@BillingRecordID,0) AS Call_PKey
   ) AS Temp ) AS ChangedCalls ON ChangedCalls.Call_PKey = Call_Times.Call_PKey
  LEFT JOIN System_Global_Time_Types ON System_Global_Time_Types.Time_master_PKey = Call_Times.Time_master_PKey
WHERE
  System_Global_Time_Types.Time_system_ID IN (2,3,4,6,7,8)
ORDER BY Call_Times.Call_PKey
```

### Claim Crew Members
```SQL
SELECT
  CAST(Call_Crews.Call_PKey AS BIGINT) AS BillingRecord_ID,
  CAST(Call_Crews.Call_PKey AS VARCHAR) + '-' + CAST(Call_Crews.Staff_PKey AS VARCHAR) AS CrewMember_ID,
  CASE WHEN Call_Crews.Crew_Type_PKey = 1100003 THEN 1
    WHEN Call_Crews.Crew_Type_PKey = 1100002 THEN 2
    WHEN Call_Crews.Crew_Type_PKey = 1100001 THEN 3
    WHEN Call_Crews.Crew_Type_PKey BETWEEN 1100004 AND 1100010 THEN 4
    ELSE 0
  END AS CrewType_ID,
  Code_Staff.Last_Name + ISNULL(', ' + Code_Staff.First_Name,'') AS CrewName,
  Code_Staff.ID AS CrewNaturalKey,
  CAST(Code_Staff.PKey AS BIGINT) AS Crew_ID
FROM Call_Crews WITH(NOLOCK)
  JOIN (
    SELECT DISTINCT Call_PKey FROM (
    SELECT DISTINCT Call_PKey FROM Call_Charges WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Credits WITH(NOLOCK)
        WHERE ISNULL(@BillingRecordID,0) = 0 AND Log_date >= @SyncStartTime AND Log_date <= @SyncEndTime
    UNION
    SELECT DISTINCT Call_PKey FROM Call_Event_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Date_Time >= @SyncStartTime AND Date_Time <= @SyncEndTime
    UNION
    SELECT PKey AS Call_PKey FROM Call WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Last_Modified_User_Date >= @SyncStartTime AND Last_Modified_User_Date <= @SyncEndTime
        UNION
    SELECT DISTINCT Call_PKey FROM Call_History WITH(NOLOCK)
      WHERE ISNULL(@BillingRecordID,0) = 0 AND Modified_DateTime >= @SyncStartTime AND Modified_DateTime <= @SyncEndTime
    UNION
      SELECT ISNULL(@BillingRecordID,0) AS Call_PKey
   ) AS Temp ) AS ChangedCalls ON ChangedCalls.Call_PKey = Call_Crews.Call_PKey
  LEFT JOIN Code_Staff WITH(NOLOCK) ON Code_Staff.PKey = Call_Crews.Staff_PKey
```

