# Microsoft 365 Azure Active Directory Guest Sync Engine
Organisations sometimes have a number of subsidiary organisations, which may continue to grow in number. To assist with sharing information, the parent organisation (host) would like all of the users at each subsidiary to access the intranet – however the native security design of Microsoft 365 (M365) makes this slightly challenging.

Normal behaviour of M365 would predicate that every external user (within the subsidiary) is individually invited to the parent/host environment. This requires both administrative effort, as well as effort by the end user to accept the invitation from the email sent by the process.

The M365 Tenant Guest Sync Solution automates the process of:
- Inviting users from external organisation M365 tenants
- Reporting on whether external users have successfully accessed the EBOS tenant
- Keeping guest profile properties current
- Removing guests if they are no longer required

## Terminology used

| Term              | Meaning                                                                                                                                                                                                           |
|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Invite/Invitation | In the context of guests, an invitation refers to creation of a guest account object within Azure Active Directory. The invitation email itself does not necessarily need to be sent to the person being invited. |
| User              | Member account within Azure Active Directory                                                                                                                                                                      |
| Guest             | External account within Azure Active Directory – source is an external tenant                                                                                                                                     |
| Redeem/Redemption | The process of accepting an “invitation” by a guest, by which they consent that the inviting organisation can see basic information about their profile.                                                          |
| App Registration  | A service within Azure Active Directory that provides authentication and access to Microsoft 365 services and data.                                                                                               |
| Application       | Related to App Registration, also known as the Client ID.                                                                                                                                                         |
| Secret            | A code used to authenticate against the App Registration.                                                                                                                                                         |
| Tenant            | Organisation within Microsoft 365 (including Azure Active Directory)                                                                                                                                              |
| ID                | Unique identification string for an object in Azure Active Directory such as a user, group, guest, etc.                                                                                                           |
| Subsidiary        | Any external organisation which has its users synchronised to the EBOS tenant as guests                                                                                                                           |
| UPN               | User Principal Name – the login account of users (e.g. user@domain.com)                                                                                                                                           |

## Components

The guest sync engine utilises a combination of the following Microsoft components:

<img src="https://user-images.githubusercontent.com/51473494/214437736-36725cd6-0751-46a5-8a6d-19338031e3e5.png">


## Key sections
- Operational workflow reference
- [Architecture reference for host tenant](Architecture reference for host tenant)
- Onboarding a new subsidiary tenant
