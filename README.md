# Azure IAM Automation Lab

This lab simulates an identity audit pipeline using Microsoft Graph API and Python. It authenticates securely, queries live user and group data, detects inactive accounts, and visualizes identity hygiene — all on the Microsoft Entra Free tier.

## 🔧 Features

- 🔐 Secure token acquisition via MSAL
- 👥 Live user and group queries from Azure AD
- 📁 CSV export of structured identity data
- 🚨 Inactive account detection and alert logging
- 📊 Pandas + Matplotlib visualization of user activity

## 📸 Screenshots

> Email addresses and domain names have been redacted to protect personal information.

- [Token Acquired](images/Access Token Call.png)  
- [User Query](images/Query Users.png)  
- [Group Memberships](images/Query Members Of.png)  
- [CSV Export](images/Export CSV.png)  
- [Inactive Alerts](images/Export Innactive Users.png)  
- [Pie Chart](images/Active vs Inactive Users.png)

## 🧠 Notes

- Built entirely on the Microsoft Entra Free tier
- Ideal for IAM audits, identity hygiene, and operational alerting
- Future enhancements may include MFA flagging and role-based access analysis
