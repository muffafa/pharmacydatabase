# Pharmacy database

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
 PatientSSN VARCHAR(11) ,
 DoctorSSN VARCHAR(11) ,
 Date DATE,
 BarCode VARCHAR(50),
 UsageDescription VARCHAR(100),
);
```

## Consider the database schema given above, write the following queries in relational algebra

- Find the SSN, name, and address of patients whose family doctor’s years of experience
is less than 5 years.
- Find the SSN, name, and age of patients who have obtained prescriptions from all
doctors.
- Find the SSN and name of the patients who live in Adana and have prescribed drug
with formula equal to aspirin by their family doctors.
- Find the SSN, name and specialty of the doctors who are assigned as family doctors
and have prescribed drug with formula aspirin but not prescribed drug with formula
antibiotics.
- Find the barcode and name of the drugs which have not been prescribed by any doctor.

## Consider the database schema given above, write the below queries in standard SQL

- Find the SSN and name of doctors who have prescribed all drugs.
- Find the SSN and name of patients who have obtained highest number of prescriptions
(you should count number of occurrences of each patient in the prescription table and
find the patient who have appeared max times in the prescription table).
- Find the SSN, name and address of the patients who have obtained prescriptions from
any doctor but not obtained any prescription from their family doctor.
- Find the barcode and name of drugs which have been prescribed more than 100 times
to patients live in Adana.
- Find the barcode and name of drugs which have prescribed by doctors who are not
family doctor.
