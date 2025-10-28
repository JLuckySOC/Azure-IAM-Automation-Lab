# Azure-IAM-Automation-Lab
1. Access Token Call
2. Query Users
3. Query Members Of
4. Export CSV
5. Export Inactive Users
6. Active vs Inactive Users (piechart)

----------------------------------------
import msal
import requests

client_id = "[YOUR CLIENT ID]"
client_secret = "[YOUR CLIENT SECRET]"
tenant_id = "[YOUR TENANT ID]"

authority = f"https://login.microsoftonline.com/{tenant_id}"
scope = ["https://graph.microsoft.com/.default"]

app = msal.ConfidentialClientApplication(
    client_id=client_id,
    authority=authority,
    client_credential=client_secret
)

result = app.acquire_token_for_client(scopes=scope)

if "access_token" in result:
    print("✅ Access token acquired!")
    access_token = result["access_token"]
else:
    print("❌ Failed to acquire token:", result.get("error_description"))
----------------------------------------
headers = {"Authorization": f"Bearer {access_token}"}
users_url = "https://graph.microsoft.com/v1.0/users"
response = requests.get(users_url, headers=headers)

if response.status_code == 200:
    users = response.json()["value"]
    for user in users:
        print(f"{user['displayName']} - {user['userPrincipalName']}")
else:
    print("Error fetching users:", response.text)
----------------------------------------
for user in users:
    user_id = user["id"]
    groups_url = f"https://graph.microsoft.com/v1.0/users/{user_id}/memberOf"
    group_response = requests.get(groups_url, headers=headers)
    if group_response.status_code == 200:
        groups = group_response.json()["value"]
        print(f"\n{user['displayName']} is a member of:")
        for group in groups:
            print(f" - {group['displayName']}")
----------------------------------------
import msal
import requests
import csv

client_id = "[YOUR CLIENT ID]"
client_secret = "[YOUR CLIENT SECRET]"
tenant_id = "[YOUR TENANT ID]"
authority = f"https://login.microsoftonline.com/{tenant_id}"
scope = ["https://graph.microsoft.com/.default"]

app = msal.ConfidentialClientApplication(
    client_id=client_id,
    authority=authority,
    client_credential=client_secret
)

result = app.acquire_token_for_client(scopes=scope)

if "access_token" in result:
    access_token = result["access_token"]
    headers = {"Authorization": f"Bearer {access_token}"}
    users_url = "https://graph.microsoft.com/v1.0/users"
    response = requests.get(users_url, headers=headers)

    if response.status_code == 200:
        users = response.json()["value"]

        # ✅ Export to CSV
        with open("user_data.csv", "w", newline="") as file:
            writer = csv.writer(file)
            writer.writerow(["Display Name", "User Principal Name"])
            for user in users:
                writer.writerow([user["displayName"], user["userPrincipalName"]])
        print("✅ User data exported to user_data.csv")

    else:
        print("❌ Error fetching users:", response.text)
else:
    print("❌ Failed to acquire token:", result.get("error_description"))
----------------------------------------
import csv

inactive_users = []

with open("user_data.csv", newline="") as file:
    reader = csv.DictReader(file)
    for row in reader:
        if "inactive" in row["Display Name"].lower():
            inactive_users.append(row)

with open("inactive_accounts.csv", "w") as alert_file:
    for user in inactive_users:
        alert_file.write(f"ALERT: {user['Display Name']} may be inactive.\n")
----------------------------------------
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("user_data.csv")
df["Status"] = df["Display Name"].apply(lambda x: "Inactive" if "inactive" in x.lower() else "Active")

status_counts = df["Status"].value_counts()
status_counts.plot.pie(autopct='%1.1f%%', startangle=90)
plt.title("Active vs Inactive Users")
plt.ylabel("")
plt.show()
