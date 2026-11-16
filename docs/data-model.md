# Data model (SharePoint Lists)

## BLSClasses
| Column | Type | Notes |
|---|---|---|
| ID | Number (auto) | Referenced by Registrations.ClassID |
| Title | Text | Course name |
| ClassDate | Date | |
| TimeText | Text | e.g. "1:00 PM – 4:00 PM" |
| Location | Text | |
| Capacity | Number | |

## Registrations
| Column | Type | Notes |
|---|---|---|
| Title | Text | Student name |
| Email | Text | |
| ClassID | Number | ID of the class in BLSClasses |
| Status | Choice | enrolled / waitlisted / cancelled |