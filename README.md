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

## Methodology

The guest sync engine utilises a combination of the following Microsoft components:

| ![image](https://user-images.githubusercontent.com/51473494/214437454-5c52eab0-f57b-45e1-a86f-8ccb16b6c967.png)
 | ![image](https://user-images.githubusercontent.com/51473494/214437479-a084519f-1a8b-4ed5-9b9d-57964fed4087.png)
 | ![image](https://user-images.githubusercontent.com/51473494/214437505-4b73586d-7d5b-42c9-b9c4-da3152a34c61.png) | ![image](https://user-images.githubusercontent.com/51473494/214437526-5928b1e7-2328-4eff-a2c8-b4afea234406.png) |
|-----------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|-------------------------------------------------|---------------------------------------------------------------------|
| **Azure Active Directory**                                                              | **SharePoint**                                                                          | **Power Automate**                              | **Microsoft Graph**                                                 |
| App Registration Users & Guests Security group membership                               | Storage of guest synchronisation status and profile information                         | Scheduling and performing of actions            | Access to users and groups Creation of guests Updating of profiles  |


## Key sections
- Architecture reference for host tenant
- Onboarding a new subsidiary tenant
