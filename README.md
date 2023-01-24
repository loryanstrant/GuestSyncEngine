# Microsoft 365 Azure Active Directory Guest Sync Engine
Organisations sometimes have a number of subsidiary organisations, which may continue to grow in number. To assist with sharing information, the parent organisation (host) would like all of the users at each subsidiary to access the intranet – however the native security design of Microsoft 365 (M365) makes this slightly challenging.
Normal behaviour of M365 would predicate that every external user (within the subsidiary) is individually invited to the parent/host environment. This requires both administrative effort, as well as effort by the end user to accept the invitation from the email sent by the process.
The M365 Tenant Guest Sync Solution automates the process of:
- Inviting users from external organisation M365 tenants
- Reporting on whether external users have successfully accessed the EBOS tenant
- Keeping guest profile properties current
- Removing guests if they are no longer required

## Key sections
- Architecture reference for host tenant
- Onboarding a new subsidiary tenant
