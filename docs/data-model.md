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
| Name (Title) | Text | Full name, filled automatically from first + last |
| FirstName | Text | |
| LastName | Text | |
| Email | Text | Contact email (may be personal, so it lasts after graduation) |
| ClassID | Number | ID of the class in BLS Classes |
| Status | Choice | Enrolled / Waitlisted / Cancelled |
| CancelToken | Text | Random GUID; used in the private cancel link |