# Data model (SharePoint Lists)

## BLS Classes
Holds every scheduled class for all courses (name kept for now; may be renamed "Classes").

| Column | Type | Notes |
|---|---|---|
| ID | Number (auto) | Referenced by Registrations.ClassID |
| Title | Text | Course name |
| Course | Choice | AHA BLS Provider / AHA Heartsaver CPR AED |
| ClassDate | Date | |
| TimeText | Text | e.g. "1:00 PM – 4:00 PM" |
| Location | Text | |
| Capacity | Number | Max enrolled students |
| Price | Number | 0 for free courses |
| PaymentLink | Text | Included in confirmation email when not blank (BLS) |

## Registrations
| Column | Type | Notes |
|---|---|---|
| ID | Number (auto) | Creation order; breaks ties for the last seat |
| Name (Title) | Text | Full name, filled automatically from first + last |
| FirstName | Text | |
| LastName | Text | |
| Email | Text | Contact email (may be personal, so it lasts after graduation) |
| Affiliation | Choice | UW Student / UW Faculty / UW Staff / Other |
| ClassID | Number | ID of the class in BLS Classes |
| ClassDate | Date | Copied from the class at signup; used for the one-upcoming-class rule |
| Status | Choice | Enrolled / Waitlisted / Cancelled |
| CancelToken | Text | Random GUID; used in the private cancel link |