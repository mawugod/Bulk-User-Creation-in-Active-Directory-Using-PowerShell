# Bulk-User-Creation-in-Active-Directory-Using-PowerShell

> **Prerequisites**:  
> - Active Directory Domain Services (AD DS)  
> - Administrative access to Domain Controller  
> - PowerShell execution policy set to allow scripts

## 📝 1. Prepare the CSV Template
### Download Template
```
[NewUsersSent.csv](https://www.alitajran.com/wp-content/uploads/spreadsheets/NewUsersSent.csv)
```
## Generate Sample Data
NB: You can manually enter the data into the respective fields but I chose to use ChatGPT to do the task  
  
1. Upload template to ChatGPT/AI tool
2. Use prompt:
 - Autopopulate 15 user records with HR, Sales, and IT as OUs.
3. Save as NewUsersReceived.csv

## Verify Data Format
* Confirm Telephone column uses numeric format (e.g., 1234567890) as I had to change the format for the Telephone column
  
  <img width="938" height="348" alt="image" src="https://github.com/user-attachments/assets/303f07fb-3c0b-4dbe-b517-6c7745709869" />


## 📂 2. Create Organizational Units (OUs)
### Create OUs in ADUC
1. Go to Server Manager > Tools > Active Directory Users and Computers
2. Right-click domain (e.g., practical.local) > New > Organizational Unit
3. Create: HR, Sales, and IT
   <img width="863" height="739" alt="image" src="https://github.com/user-attachments/assets/6d7c1282-6f9a-4a5a-b64e-2eba51fcc93f" />


### Retrieve OU Distinguished Names
1. Go to Views > Advanced Feature
2. For each OU:
 * Right-click > Properties > Attribute Editor
 * Copy distinguishedName value:
   - HR    : OU=HR,DC=practical,DC=local
   - Sales : OU=Sales,DC=practical,DC=local
   - IT    : OU=IT,DC=practical,DC=local
<img width="938" height="611" alt="image" src="https://github.com/user-attachments/assets/64a9074f-cb58-4db9-a11f-b9bb9e376d83" />  
<img width="621" height="441" alt="image" src="https://github.com/user-attachments/assets/16ac4556-59cc-4dff-872b-bc2acd4a7164" />  
<img width="696" height="338" alt="image" src="https://github.com/user-attachments/assets/b21c805e-70b1-46e7-92a1-bf49bd1a8d24" />  



## ✨ 3. Finalize CSV File
### Populate OU Data
1. Add OU column to NewUsersReceived.csv
2. Paste corresponding distinguishedName for each user. Eg. is as seen in the image below
<img width="975" height="441" alt="image" src="https://github.com/user-attachments/assets/c566530e-c354-4a99-b74e-33f03c69cb0c" />

### Save Correct Format
File > Save As > NewUsersFinal.csv
 - Type: CSV UTF-8 (Comma delimited) (*.csv)
<img width="938" height="175" alt="image" src="https://github.com/user-attachments/assets/6110c74f-4d21-436d-b369-106bd0da5a4f" />

### Prepare the Script 
Visit the link:
[Add-NewUsers.ps1](https://www.alitajran.com/wp-content/uploads/scripts/Add-NewUsers.ps1) 
Press the Ctrl + S keys and save the file as **Add-NewUsers.ps1** 
<img width="378" height="89" alt="image" src="https://github.com/user-attachments/assets/8614027c-ba8a-44bd-b49e-1d7abe497d2d" />

## ⚙️ 4. Prepare Environment

### Create directories:
Create a folder as Temp in the C drive of the Domain Controller and copy the NewUsersFinal.csv file in there. You can use the command line.
```
New-Item -Path "C:\Temp" -ItemType Directory
```
Create another folder as Script in the same C drive.
```
New-Item -Path "C:\Script" -ItemType Directory
```
<img width="812" height="367" alt="image" src="https://github.com/user-attachments/assets/184ffd89-83c7-48a0-b426-656ff165b13b" />


### Place files in respective folders:
Wherever the NewUsersFinal.csv file is saved, make sure it is copied into the Temp folder.  
<img width="952" height="460" alt="image" src="https://github.com/user-attachments/assets/da93ee5e-f864-4203-8221-bb6ebfd987bb" />

Also copy the **Add-NewUsers.ps1** script in the Script folder
<img width="1020" height="478" alt="image" src="https://github.com/user-attachments/assets/1ab6748f-4ae5-46f4-85bf-b4a7bf29c9cc" />


## Validate CSV Structure
It is good to know and validate if your csv file reads as a comma or semicolon.  This is help you either to add the `-Delimiter ";"` which is part of the next (5.) instruction.
A means of checking is to open the csv file and confirm if it is using a comma or semicolon.  
 - If it looks like: FirstName;LastName;Username → use -Delimiter ";"
 - If it looks like: FirstName,LastName,Username → omit the delimiter (default is comma)
<img width="1277" height="420" alt="image" src="https://github.com/user-attachments/assets/3cd3995d-9a24-4349-aad6-810103a07f82" />  
  
As seen above, mine is using comma and so there will be no need to use the `-Delimiter ";"`  in *5*.  
OR   
Open Powershell and run the below command from C:\Temp. This is to check if it is outputing the right format  
```
Import-Csv ".\NewUsersFinal.csv" | Format-Table
```
  
OR  
If uses semicolon-delimited, validate the format with:  
```
Import-Csv "C:\Temp\NewUsersFinal.csv" -Delimiter ";" | Format-Table  
```  
The two commands give an output as below
<img width="975" height="855" alt="image" src="https://github.com/user-attachments/assets/269ee56c-ce6b-4533-8c84-316827720bcd" />



## 🚀 5. Configure & Execute Script
### Edit Script (Add-NewUsers.ps1)
Go to the Script folder and double-click the Add-NewUsers.ps1 script to open.
 - In Line 25: Update CSV path if that is not where you stored it.  
  - Also, if you have a comma separating character instead of a semicolon in your CSV file, then Remove "-Delimiter ';'" 
```
$Users = Import-Csv "C:\Temp\NewUsersFinal.csv" 
```
 - Replace the domain name if it is different
```
$UPN = "practical.local" 
```
- In line 25, update CSV path and delimiter. Remove "-Delimiter ';'" if using commas
```
$Users = Import-Csv "C:\Temp\NewUsersFinal.csv"
```
- In line 28, replace your domain with yours
```
$UPN = "@practical.local" 
```
## Run Script
Open PowerShell as Admin in C:\Script and run the following command
```
Set-ExecutionPolicy RemoteSigned -Force
```
```
.\Add-NewUsers.ps1
```
<img width="975" height="745" alt="image" src="https://github.com/user-attachments/assets/54ca35cd-3847-4488-8f41-6045f639aa10" />


## ✅ 6. Verify User Creation
1. Refresh Active Directory Users and Computers
2. Check users in respective OUs:
  *HR  
   <img width="975" height="859" alt="image" src="https://github.com/user-attachments/assets/b5eea1c5-aefc-4093-98ea-6aace3fe34b7" />
  *Sales  
   <img width="975" height="842" alt="image" src="https://github.com/user-attachments/assets/c5d72ec3-845b-4f24-ae17-a2499438eb43" />
   *IT  
   <img width="938" height="805" alt="image" src="https://github.com/user-attachments/assets/828217b5-240b-4c9f-a523-48d884943a83" />
  

   Powershell Credit : [ALI TAJRAN](https://www.alitajran.com/create-active-directory-users-from-csv-with-powershell/)
