# Muhammed Mustafa Savar 2020555403

Consider the following relations where doctors prescribe drugs for patients. A doctor could
prescribe one or more drugs for several patients, and a patient could obtain prescriptions from
several doctors. Every patient has a family doctor, and every doctor has at least one patient.

Database schema:

> DOCTORS (***DoctorSSN***, Name, Specialty, YearsOfExperience)

``` sql
 CREATE TABLE DOCTORS(
 DoctorSSN VARCHAR(11) PRIMARY KEY,
 Name VARCHAR(50) NOT NULL,
 Specialty VARCHAR(50) NOT NULL,
 YearsOfExperience INT NOT NULL
);
```

> DRUGS (***BarCode***, Name, Formula)

```sql
 CREATE TABLE DRUGS(
 BarCode VARCHAR(50) PRIMARY KEY,
 Name VARCHAR(50) NOT NULL,
 Formula VARCHAR(50)
 );
```

> PATIENTS (***PatientSSN***, Name, Age, Address, City, FamilyDoctorSSN)

```sql
CREATE TABLE PATIENTS(
 PatientSSN VARCHAR(11) PRIMARY KEY,
 Name VARCHAR(50) NOT NULL,
 Age INT NOT NULL,
 Address VARCHAR(50) NOT NULL,
 City VARCHAR(50) NOT NULL,
 FamilyDoctorSSN VARCHAR(11) REFERENCES DOCTORS(DoctorSSN)
);
```

> PRESCRIPTION (***PatientSSN, DoctorSSN, Date, BarCode,*** UsageDescription)

```sql
CREATE TABLE PRESCRIPTION(
 PatientSSN VARCHAR(11) REFERENCES PATIENTS(PatientSSN),
 DoctorSSN VARCHAR(11)  REFERENCES DOCTORS(DoctorSSN),
 Date DATE NOT NULL,
 BarCode VARCHAR(50) REFERENCES DRUGS(BarCode),
 UsageDescription VARCHAR(100)
);
```

## Consider the database schema given above, write the following queries in relational algebra

- Find the SSN, name, and address of patients whose family doctor’s years of experience
is less than 5 years.
  - Answer: π (PatientSSN, Name, Address) (σ YearsOfExperience < 5 (DOCTORS ⋈[DoctorSSN = FamilyDoctorSSN] PATIENTS))

- Find the SSN, name, and age of patients who have obtained prescriptions from all
doctors.
  - Answer: π (PatientSSN, Name, Age) ((π DoctorSSN,PatientSSN (PRESCRIPTION) / π DoctorSSN (DOCTORS)) ⋈ PATIENTS)

- Find the SSN and name of the patients who live in Adana and have prescribed drug
with formula equal to aspirin by their family doctors.
  - Answer: π(PatientSSN,Name) (σ Formula = 'aspirin'(σ  City = 'Adana'(PATIENTS ⨝ PRESCRIPTION ⨝[FamilyDoctorSSN = DoctorSSN] DOCTORS)))

- Find the SSN, name and specialty of the doctors who are assigned as family doctors
and have prescribed drug with formula aspirin but not prescribed drug with formula
antibiotics.
  - Answer: π (DoctorSSN, Name, Specialty) ( (σ Formula = 'aspirin' ((PATIENTS ⨝ PRESCRIPTION ⨝[FamilyDoctorSSN = DoctorSSN] DOCTORS) ⨝ DRUGS)) - (σ Formula = 'antibiotics' ((PATIENTS ⨝ PRESCRIPTION ⨝[FamilyDoctorSSN = DoctorSSN] DOCTORS) ⨝ DRUGS)))

- Find the barcode and name of the drugs which have not been prescribed by any doctor.
  - Answer: (π(Barcode,Name) (DRUGS)) – (π(Barcode,Name)(PRESCRIPTION ⨝ DRUGS))

## Consider the database schema given above, write the below queries in standard SQL

- Find the SSN and name of doctors who have prescribed all drugs.
  - Answer:
  
  ```sql
  SELECT DoctorSSN, Name
  FROM DOCTORS
  WHERE DoctorSSN IN (
    SELECT DoctorSSN
    FROM PRESCRIPTION
    GROUP BY DoctorSSN
    HAVING COUNT(DISTINCT BarCode) = (
        SELECT COUNT(*)
        FROM DRUGS
    )
  );
  ```

- Find the SSN and name of patients who have obtained highest number of prescriptions
(you should count number of occurrences of each patient in the prescription table and
find the patient who have appeared max times in the prescription table).
  - Answer:
  
  ```sql
  SELECT PatientSSN, Name
  FROM PATIENTS
  WHERE PatientSSN IN (
    SELECT PatientSSN
    FROM PRESCRIPTION
    GROUP BY PatientSSN
    ORDER BY COUNT(*) DESC
    LIMIT 1
  );
  ```

- Find the SSN, name and address of the patients who have obtained prescriptions from
any doctor but not obtained any prescription from their family doctor.
  - Answer:
  
  ```sql
  SELECT PatientSSN, Name, Address
  FROM PATIENTS
  WHERE PatientSSN NOT IN (
      SELECT PatientSSN
      FROM PRESCRIPTION
      WHERE DoctorSSN = FamilyDoctorSSN
  );
  ```

- Find the barcode and name of drugs which have been prescribed more than 100 times
to patients live in Adana.
  - Answer:

  ```sql
  SELECT BarCode, Name
  FROM DRUGS
  WHERE BarCode IN (
    SELECT BarCode
    FROM PRESCRIPTION
    JOIN PATIENTS ON PRESCRIPTION.PatientSSN = PATIENTS.PatientSSN
    WHERE PATIENTS.City = 'Adana'
    GROUP BY BarCode
    HAVING COUNT(*) > 100
  );
  ```

- Find the barcode and name of drugs which have prescribed by doctors who are not
family doctor.
  - Answer:

  ```sql
  SELECT DISTINCT DRUGS.BarCode, DRUGS.Name
  FROM PRESCRIPTION
  JOIN DRUGS ON PRESCRIPTION.BarCode = DRUGS.BarCode
  JOIN DOCTORS ON PRESCRIPTION.DoctorSSN = DOCTORS.DoctorSSN
  WHERE DOCTORS.DoctorSSN NOT IN (
    SELECT FamilyDoctorSSN 
    FROM PATIENTS
    JOIN DOCTORS ON PATIENTS.FamilyDoctorSSN = DOCTORS.DoctorSSN
  );
  ```
