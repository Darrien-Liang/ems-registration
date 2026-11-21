<u>**Note: Due to the order in which functions were implemented for the website, all flow names start with "BLS". However, the logic used for these flows work for all classes, including BLS, Heartsaver, etc.**</u>

<u>**DO NOT RENAME THESE FLOWS.**</u>

# BLSRegister flow (Power Automate)

Called by the app's Submit button:
`BLSRegister.Run(LastName, Email, ClassID, FirstName, Affiliation)`

1. **Get class**: read the class's capacity and date.
2. **Check existing**: find active (not cancelled) signups by this email for classes from today onward.
   If any exist, respond `Duplicate` and stop. Prevents double enrollment or malicious spamming.

   Rule : Only 1 active registration per person across all courses.

    In other words, "BLS" XOR "HeartSaver"
3. **Create registration**: save the signup as `Enrolled`, with ClassDate copied from the class
   and a random `CancelToken` (`guid()`). When CancelToken is added to the end of the website URL, a custom link is made that allows class cancellation of ONLY the user associated with that CancelToken. 
4. **Enrolled up to me**: count `Enrolled` signups for this class with ID ≤ this signup's ID.
5. **If class is full** (count > capacity): set this signup to `Waitlisted`.
   Because SharePoint IDs increase in creation order, the earliest signup always keeps the last seat. Prevents
    double-booking when two people submit at once.
6. **Respond** to the app with `result` (Enrolled / Waitlisted / Duplicate) and `canceltoken`.

The flow runs with the owner's permissions, so students never write to the Lists directly.

7. **App link** (Compose): the published app's web link.
8. **Cancel link** (Compose): app link + `&cancel=` + the registration's CancelToken.
9. **Class date** (Compose): `formatDateTime(ClassDate, 'dddd, MMMM d')`.
10. **Payment section** (Compose): only for *enrolled* students in *AHA BLS Provider* classes.
    Shows the course fee (from Price, formatted as currency) and either the class's PaymentLink
    or "payment instructions will be sent separately" if no link is set. Empty for Heartsaver
    and waitlisted students.
11. **Email body** (Compose): HTML email with class details, the payment section, and the private cancel link.
12. **Send an email (V2)**: sends the confirmation ("Registration confirmed" or "Waitlisted") to the student.

Steps 7–12 run *after* the response to the app, so students aren't kept waiting while the email sends.

# BLSCancel flow (Power Automate)

Called by CancelScreen: `BLSCancel.Run(Token, DoCancel)`
- `DoCancel = false` → look up only (to show "Cancel registration for …?")
- `DoCancel = true` → cancel the registration

1. **Find registration**: find the active (not cancelled) registration whose CancelToken matches the token.
2. **If found** (a match exists AND the token isn't blank):
   - No → respond `NotFound` and stop.
   - Yes → continue:
3. **Reg**: take the single matching row.
4. **Get class**: read the class's course, date, time, and location.
5. **If DoCancel**: set the registration's Status to `Cancelled`.
6. **Respond** with `result` (Found / Cancelled), the student's `name`, and class `details`.

The app never reads or edits the Registrations list directly for cancellations.
The app skips the flow entirely when the link has no token.


# BLSRecount flow (Power Automate)

Called whenever a row in the Registrations spreadsheet is created (person registered) or modified (cancellation, fields are edited by admin, etc)
1. **Enrolled for class**: Looks at all registrants enrolled in a given class.
2. **Update item**: Updates the value of EnrolledCount, in the BLS Classes list, to equal the number of registrants with that ClassID and are enrolled. Later used to determine available slots.

Note: When a person registers, their entry immediately shows up on Registrations. However, the website and EnrolledCount on the BLS Classes list
refreshes every minute. As such, after someone registers, the number of available slots won't immediately drop by 1. 

This is a visual feature - overbooking for a class is still prevented.