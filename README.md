# SQL Server — Windows Authentication Login (Portfolio Project)

## 📌 Description
Step-by-step guide to create and verify a Windows Authentication login in Microsoft SQL Server.  
Includes **SSMS GUI steps**, **T-SQL examples**, **verification queries**, **screenshots**, and **security best practices**.

---

## 📑 Table of Contents
1. Project Overview  
2. Prerequisites  
3. Quick Summary  
4. Step-by-Step Instructions (GUI + T-SQL)  
   - Connect to SSMS  
   - Open Security → Logins  
   - Create Windows Authentication login (GUI)  
   - Assign server roles (least privilege)  
   - Map the login to a database user & assign DB roles  
   - Test login & verify  
5. Audit & Disable Services (proof screenshots)  
6. Verification Queries & T-SQL Scripts  
7. Screenshots — Filenames & Guidance  
8. Repository Structure & Upload Instructions  
9. Security Best Practices Checklist  
10. License & Contact  

---

## 1. Project Overview
This project documents how to provision a **Windows Authentication login** in SQL Server and validate least-privilege mapping into a database (e.g., `AdventureWorks2022`).  
It’s intended as a professional portfolio project to demonstrate practical DBA skills.

---

## 2. Prerequisites
- Microsoft SQL Server 2019/2022 (or later)  
- SQL Server Management Studio (SSMS)  
- A Windows user or AD group (example: `MAH-CYNTHIA\Mah Cynthia`)  
- An account with `sysadmin` (or equivalent) to create logins  
- A `screenshots/` folder in your repo  

---

## 3. Quick Summary
At the end of this project you will have:  
- ✅ A Windows Authentication login created  
- ✅ Least privilege roles mapped (`db_datareader`, `db_datawriter`)  
- ✅ Verified login works with queries  
- ✅ Screenshots of every key step  

---

## 4. Step-by-Step Instructions

### A. Connect to SQL Server
1. Open **SQL Server Management Studio**  
2. Select **Windows Authentication** and connect  
3.  
![SSMS Connect](screenshots/ssms_connect.png)

---

### B. Open Security → Logins
1. In **Object Explorer** → expand server → **Security** → **Logins**  
2. Right-click **Logins** → **New Login…**  
3.  
![Security Logins](screenshots/security_logins.png)

---

### C. Create Windows Authentication Login
1. In **Login – New** dialog:  
   - Click **Search…**, choose Windows user/group (e.g., `MAH-CYNTHIA\Mah Cynthia`)  
   - Ensure **Windows Authentication** is selected  
   - Leave password blank  
2.  
![New Login](screenshots/new_login_window.png)

---

### D. Assign Server Roles (least privilege)
1. Select **Server Roles** tab  
2. Avoid `sysadmin` — keep roles minimal  
3.  
![Server Roles](screenshots/server_roles.png)

---

### E. Map Login to Database User & Roles
1. Open **User Mapping** tab  
2. Check database (e.g., `AdventureWorks2022`)  
3. Assign `db_datareader` + `db_datawriter`  
4.  
![User Mapping](screenshots/user_mapping.png)

---

### F. Test Login & Verify
1. Connect with Windows user account  
2. Run:  
   ```sql
   SELECT SUSER_SNAME() AS ServerPrincipal, USER_NAME() AS DatabaseUser;
   ```  
3. Should return Windows login + mapped DB user  
4.  
![Test Login](screenshots/test_login.png)

---

## 5. Audit & Disable Services
- **Audit logs**:  
![Audit Logs](screenshots/audit_logs.png)

- **Disabled unused services** (e.g., SQL Browser):  
![Disabled Services](screenshots/step6_disable_unused_services.png)

---

## 6. Verification Queries & T-SQL Scripts

### Create Login
```sql
IF NOT EXISTS (SELECT 1 FROM sys.server_principals WHERE name = N'MAH-CYNTHIA\Mah Cynthia')
  CREATE LOGIN [MAH-CYNTHIA\Mah Cynthia] FROM WINDOWS;
GO
```

### Map Login to DB User
```sql
USE AdventureWorks2022;
GO
IF NOT EXISTS (SELECT 1 FROM sys.database_principals WHERE name = N'MAH-CYNTHIA\Mah Cynthia')
  CREATE USER [MAH-CYNTHIA\Mah Cynthia] FOR LOGIN [MAH-CYNTHIA\Mah Cynthia];
GO
```

### Optional: Custom Role
```sql
CREATE ROLE app_readonly;
GRANT SELECT ON SCHEMA::Person TO app_readonly;
ALTER ROLE app_readonly ADD MEMBER [MAH-CYNTHIA\Mah Cynthia];
```

### Verify
```sql
SELECT name, type_desc FROM sys.server_principals WHERE name = N'MAH-CYNTHIA\Mah Cynthia';
SELECT name, type_desc FROM sys.database_principals WHERE name = N'MAH-CYNTHIA\Mah Cynthia';
SELECT SUSER_SNAME() AS ServerPrincipal, USER_NAME() AS DatabaseUser;
```

---

## 7. Screenshots — Filenames & Guidance
| Step | Filename |
|------|-----------|
| SSMS Connect | `ssms_connect.png` |
| Security → Logins | `security_logins.png` |
| New Login dialog | `new_login_window.png` |
| Server Roles | `server_roles.png` |
| User Mapping | `user_mapping.png` |
| Test Login Result | `test_login.png` |
| Audit Logs | `audit_logs.png` |
| Disabled Services | `step6_disable_unused_services.png` |

---

## 8. Repository Structure
```
/sqlserver-windows-auth-login
│── README.md
│── LICENSE
│── /screenshots
│     ├── ssms_connect.png
│     ├── security_logins.png
│     ├── new_login_window.png
│     ├── server_roles.png
│     ├── user_mapping.png
│     ├── test_login.png
│     ├── audit_logs.png
│     └── step6_disable_unused_services.png
```

### Upload to GitHub
```bash
cd path/to/sqlserver-windows-auth-login
git init
git add .
git commit -m "Initial commit — Windows Auth login guide with screenshots"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/sqlserver-windows-auth-login.git
git push -u origin main
```

---

## 9. Security Best Practices Checklist
- ✅ Use Windows Authentication for integrated security  
- ✅ Apply least privilege principle  
- ✅ Enforce strong Windows passwords & AD policies  
- ✅ Patch & update SQL Server regularly (`SELECT @@VERSION;`)  
- ✅ Encrypt data at rest (TDE) & in transit (TLS)  
- ✅ Audit user activity & store logs securely  
- ✅ Disable unused features/services (e.g., Browser, xp_cmdshell)  
- ✅ Backup encryption certificates/keys offsite  

---

## 10. License & Contact
**License:** MIT (recommended)  
**Author:** *Your Name* — Add email / LinkedIn / portfolio link here  
