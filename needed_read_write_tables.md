# Tritech Required Read/Write Capabilities

## Tables

Required Transactional Tables
```
* Patient						   
* Patient_Payors				
* Patient_Comments
* Call
* Call_Crews
* Call_Times
* Call_Reasons
* Call_Charges
* Call_Billing_Narratives
* Call_Statistics
* Call_Procedures
* Call_Medications
* Call_Assessments
* Call_Assessments_Secondary
* Call_Comments
* Call_Credits
* Call_Collections
* Call_Event_History
* Call_Questions
* Patient_HIPAA
* Patient_PCS
```

Required Reference Tables / Data Dictionaries
```
* Code_Doctors (affects Code_Doctors_History)
* Code_Employers (affects Code_Employers_History)
* Code_Cities (affects Code_Cities_History)
* Code_Locations
* Code_Payors (affects Code_Payors_History)
```

Desired Reference Tables / Data Dictionaries
```
* Zones (affects Code_Zones_History)
* Code_Staff (affects Code_Staff_History)
* Code_Charge_Types (affects Code_Charge_Types_History)
* Code_Credits (affects Code_Credits_History)
* Code_Companies (affects Code_Companies_History)
* Code_Companies_Payors
````
