## **Azure Sentinel SIEM Home Lab**

### **Project Overview**

This project details the process of building a fully functional, cloud-based Security Information and Event Management (SIEM) system using Microsoft Azure. By setting up a vulnerable Windows virtual machine, we will capture, forward, and analyze security logs to detect and visualize real-time cybersecurity threats. We will forward these logs to a **Log Analytics Workspace**, connect it to **Microsoft Sentinel** (our SIEM), and use a custom **Watchlist** to enrich the log data with geolocation information, ultimately creating a live attack map.  
<img width="512" height="279" alt="image" src="https://github.com/user-attachments/assets/2b61668d-cb06-4dbc-93fa-ac2ee0b481dc" />


This hands-on lab is perfect for anyone looking to gain practical experience in cloud security, log analysis, and threat detection using industry-standard tools.

* * *

### **Step 1: Activate Your Azure Free Subscription** ⚙️

The foundation of this lab is Microsoft's Azure cloud platform. The first step is to create an Azure free account, which provides access to the necessary services without initial cost.

**What is an Azure Free Subscription?** An Azure free account gives you a collection of popular services for free for 12 months, a **$200 credit** to explore any Azure service for the first 30 days, and access to over 25 services that are always free. This is more than enough to complete this project.

**Action: Create Your Account**

1.  Navigate to the official [Azure Free Account](https://azure.microsoft.com/en-us/free/) page.
    
2.  Click on the **"Start free"** button.
    
3.  You will need to sign in with a Microsoft account and provide a phone number, email address, and a credit card for identity verification.
    

> **⚠️ Important Note on Billing:** Your credit card is used **only for identity verification** and will **not be charged** unless you manually upgrade your account or your 30-day period with the $200 credit expires and you continue to use paid services. To avoid any potential costs, it is crucial to **delete all created resources** (like the virtual machine and Log Analytics Workspace) and **cancel the subscription** after you have completed the project. Set a calendar reminder to ensure you don't forget!

<img width="809" height="451" alt="image" src="https://github.com/user-attachments/assets/0a9b5587-0103-41f4-ac04-f21edfb8d9dd" />

* * *

### **Step 2: Build the Lab Infrastructure 🏗️**

With our subscription ready, we will now build the foundational container and network for our lab. This involves creating a **Resource Group** to keep our project organized and a **Virtual Network (VNet)** to provide a private, secure space for our virtual machine.

> **What is Cloud Infrastructure?** 📦 The infrastructure for this lab has two key components:
> 
> 1.  A **Resource Group** is a logical folder in Azure. It holds all the related pieces of our project (like virtual machines, networks, and storage) in one place, making them easy to manage and delete together when the project is finished.
>     
> 2.  A **Virtual Network (VNet)** is your own private and isolated network in the cloud. It allows the resources you create, like your virtual machine, to communicate securely with each other and the internet, just like a physical office network.
>     

**Create the Resources**

1.  First, navigate to the "Resource groups" service in the Azure portal and create a new resource group, giving it a unique name and selecting a region.
 <img width="897" height="521" alt="image" src="https://github.com/user-attachments/assets/dae296af-00e4-46e0-aa86-efefffa63d5c" />
   
2.  Next, search for the "Virtual networks" service and create a new VNet. Ensure you place it inside the resource group you just made and use the same region. The default network and IP address settings are sufficient for this lab!

<img width="813" height="661" alt="image" src="https://github.com/user-attachments/assets/4e0ea330-54e5-4233-975a-3597698ed6db" />

<img width="584" height="690" alt="image" src="https://github.com/user-attachments/assets/256b13fd-48f8-45c1-8b3d-fd2b16d73665" />

<img width="911" height="562" alt="image" src="https://github.com/user-attachments/assets/d5acd326-1fa0-4b08-959a-78265c74838b" />

> **⚠️ Important Note on Consistency:** It is crucial that you create both the **Resource Group** and the **Virtual Network** in the same Azure **Region** (e.g., East US). Mismatching regions can cause deployment failures and prevent the resources from working together correctly.

* * *

### **Step 3: Deploy a Vulnerable Virtual Machine 🎯**

This step involves creating the centerpiece of our lab: a Windows virtual machine. We will intentionally configure it as a "honeypot"—an attractive and insecure target—to generate the real-world attack logs that our SIEM will later analyze.

<img width="949" height="523" alt="image" src="https://github.com/user-attachments/assets/c6e697f1-08c2-4ffd-ab60-456d61fea0c9" />

> **What is a Honeypot VM?** 💻 A **Virtual Machine (VM)** is a complete, software-based computer that runs in the cloud. For our lab, we turn it into a **honeypot** by introducing two key vulnerabilities:
> 
> 1.  **Exposed RDP Port:** We configure its network settings to allow **Remote Desktop Protocol (RDP)** connections on port 3389 from anywhere on the internet. This is a common target for automated brute-force attacks.
>     
> 2.  **Disabled Firewall:** We turn off the internal **Windows Defender Firewall**, removing a critical layer of defense from the operating system itself. This setup makes the VM an easy and visible target, ensuring it will attract malicious traffic.
>     

**Create and Weaken the VM**

In the Azure portal, create a new **Windows 10 Virtual Machine** within your existing resource group. During the setup process, on the **Networking** tab, ensure you **allow public inbound traffic for RDP (port 3389)**.

<img width="789" height="603" alt="image" src="https://github.com/user-attachments/assets/6ad5e765-5958-4abf-92fe-fdde5ba0023d" />


<img width="1312" height="646" alt="image" src="https://github.com/user-attachments/assets/3df467ea-14b5-485a-ab5a-d485f74c427b" />


<img width="1422" height="684" alt="image" src="https://github.com/user-attachments/assets/a332e229-4ebf-4fff-8d4d-9a97033da1b2" />


<img width="1420" height="617" alt="image" src="https://github.com/user-attachments/assets/87a81d04-dfe1-408c-b0bc-f231a095ccee" />


Once the VM is deployed and running, connect to it using its public IP address and the **Remote Desktop Connection** application.

<img width="1275" height="584" alt="image" src="https://github.com/user-attachments/assets/06a7d6c6-f471-44c8-a143-2d341fa4824a" />


After logging into the VM, search for and open the Windows Firewall settings, and completely **disable the firewall** for all network profiles (Domain, Private, and Public).

<img width="1263" height="579" alt="image" src="https://github.com/user-attachments/assets/71699ad9-70e0-4761-8966-b1379f214553" />

<img width="570" height="590" alt="image" src="https://github.com/user-attachments/assets/060053b1-0dc7-4ee0-b51d-c6c5aecc497b" />

<img width="552" height="464" alt="image" src="https://github.com/user-attachments/assets/1c310666-2be5-42f3-8b26-18082d1b0e85" />

After just a few minutes, you can witness the results of this exposure directly on the VM. Open the **Event Viewer**, navigate to **Windows Logs > Security**, and you will begin to see a flood of "Audit Failure" events. These are the automated attacks in action, with **Event ID 4625** specifically indicating an unsuccessful logon attempt.

<img width="817" height="481" alt="image" src="https://github.com/user-attachments/assets/29321ea7-10c4-4f0a-ac2a-2babf4fbe2ac" />

> **⚠️ SECURITY WARNING:** This virtual machine is **intentionally and critically insecure**. It will be actively attacked by bots and scanners on the internet. Do NOT store any personal or sensitive information on this VM. It should only be used for the purposes of this lab and must be deleted upon completion.

* * *

### Step 4: Set Up Log Analytics Workspace (LAW)

With our vulnerable VM deployed and generating logs, we'll now create a central hub to collect and analyze them. We'll set up a Log Analytics Workspace (LAW) and configure the VM to forward its security logs to it.

**What is a Log Analytics Workspace?** 🧠 A Log Analytics Workspace (LAW) is a dedicated storage and analysis service in Azure that acts as a central repository for all our log data. It's a secure environment where logs from our VM will be stored, allowing us to query, search, and analyze them in one place. Think of it as a cloud-based database specifically designed for log data.

**What is the Azure Monitor Agent (AMA)?** 🦾 The Azure Monitor Agent (AMA) is a lightweight agent we'll install on our VM. Its primary job is to collect log data from the operating system, based on rules we define, and forward it to our LAW. It's the essential bridge between our VM and our centralized log repository.

**Deploy the LAW and Connect the VM**

- **Create the LAW:** In the Azure portal, search for "Log Analytics workspaces" and create a new workspace. Make sure to place it in the same resource group and region as your VM and Virtual Network.
    
- **Install the AMA:** Once the LAW is created, you will connect it to your VM. Azure will automatically handle the installation of the Azure Monitor Agent on your VM and set up a data collection rule.
    
- **Configure Data Collection:** During the connection process, you will be prompted to select which data you want to collect. Choose to collect "Windows security events," as this is where our failed logon attempts are logged. This configuration tells the AMA to forward only the security logs to the LAW.
    

<img width="765" height="675" alt="image" src="https://github.com/user-attachments/assets/102e4756-61de-4bf8-aaed-c7423947a6aa" />


> ⚠️ **Important Note on Data:** After you have successfully connected the VM to the LAW, it may take a few minutes for the initial log data to begin flowing. You can confirm the connection is working by checking the "Tables" section within the LAW's menu and looking for the "SecurityEvent" table.

* * *

### Step 6: Query Logs and Monitor Unauthorized Access Attempts

Now that our logs are being ingested into the Log Analytics Workspace, we can begin to analyze them for malicious activity. We'll use **Kusto Query Language (KQL)** to find specific events that indicate unauthorized access attempts to our vulnerable VM.

<img width="1429" height="671" alt="image" src="https://github.com/user-attachments/assets/ff9781bb-0b9d-4cd8-be7e-5dbedfac44b1" />

**What is KQL?** 🔍 **Kusto Query Language (KQL)** is a powerful query language used to explore, analyze, and visualize data in Azure's log services. It's similar to SQL but is optimized for the structure of log data. With KQL, we can easily filter logs, count events, and perform complex searches to find exactly what we're looking for. The basic syntax is simple: start with the table name (e.g., `SecurityEvent`) and then add filters using the `where` operator.

<img width="1068" height="644" alt="image" src="https://github.com/user-attachments/assets/db27339f-8f9c-43cf-92f0-b55c0ae56cab" />


## **Why is Event ID 4625 Important?** 🚨 In Windows, every security event is assigned a unique Event ID. **Event ID 4625** specifically indicates a "failed logon attempt." This is the key we'll use to identify all the unauthorized login attempts on our honeypot VM. By querying for this specific ID, we can quickly filter out all other security events and focus on the brute-force attacks happening on our exposed RDP port.

<img width="1103" height="694" alt="image" src="https://github.com/user-attachments/assets/d77867eb-3431-4c34-9d19-cba3b98142a7" />


**Query the Logs**

- **Access the Logs:** In the Azure portal, navigate to your **Log Analytics Workspace** and select "**Logs**" from the menu. This will open the query editor.
    
- **Write and Run the Query:** In the query editor, type the following KQL query: `SecurityEvent | where EventID == 4625`.
    
- **Analyze the Results:** Click "**Run**" to execute the query. The results table will display all the failed login attempts on your VM. You will see columns for **TimeGenerated**, **Account**, and **Computer**, which will show you when the attempts happened, the account names being targeted, and the source of the event (your VM, `SG-CORP-NET`).
    
- **Further Analysis:** You can refine the query to get more specific results, for example, by adding `| distinct Account` to see a list of unique usernames that attackers are trying to use. The query editor is a powerful tool for exploring your data.
    

> ⚠️ **Important Note on Live Data:** The logs you are viewing are from live attacks. The data you see is not a simulation; it's a real-time record of bots and attackers from around the globe attempting to compromise your intentionally vulnerable VM. This is the raw data that a security analyst would work with in a real-world scenario.

* * *

### Step 7: Create and Upload Watchlist with Geolocation Data

Querying logs is effective, but to truly visualize and understand the global scale of the attacks, we need to enrich our data. We will achieve this by creating a **Watchlist** in Microsoft Sentinel and uploading a dataset that maps IP addresses to geographic locations.

**What is a Watchlist?** 📝 A **Watchlist** is a collection of data that we can use to enrich log data in Microsoft Sentinel. It’s essentially a lookup table that allows us to join external, static data with our live log streams. In this step, we will use a GeoIP dataset as our Watchlist, which contains mappings of IP address ranges to specific countries, cities, latitudes, and longitudes. This allows us to take a raw log that shows an attacker's IP and instantly add its geographic location.

<img width="1024" height="632" alt="image" src="https://github.com/user-attachments/assets/31e0f704-2eb4-4c94-a86a-01995317ccb8" />


**Why Use a Watchlist for Geo-enrichment?** 🗺️ Our `SecurityEvent` logs contain the source IP addresses of the attackers, but they don't natively include geographic information. By joining our log data with a GeoIP Watchlist, we can transform simple IP addresses into valuable context. This enrichment is crucial for creating a visual attack map and gaining a global perspective on the threat landscape.
<img width="1031" height="699" alt="image" src="https://github.com/user-attachments/assets/aaf806af-9f76-4065-95ca-bf11dbdecea7" />


**Create and Upload the Watchlist**

- **Find a GeoIP Database:** First, you need a GeoIP database in a CSV format. Free, non-commercial versions are available from providers like MaxMind. You should select a file that contains columns for `network`, `latitude`, `longitude`, `cityname`, and `countryname`.
    
- **Navigate to Watchlists:** In the Microsoft Sentinel menu, under **Configuration**, select "**Watchlist**."
    
- **Create New Watchlist:** Click "**Create new watchlist**" and provide a name (e.g., `geoip`). Follow the wizard, selecting "**Local file**" as the source type and uploading your CSV file.
    
<img width="1363" height="648" alt="image" src="https://github.com/user-attachments/assets/f5891495-c290-4d6c-a98f-32ee53fe88aa" />

<img width="1027" height="654" alt="image" src="https://github.com/user-attachments/assets/3fc85972-a976-446a-9942-a127fde84865" />

- **Define the SearchKey:** Set the `SearchKey` to the column name that contains the IP address information (e.g., `network`). This tells Sentinel which column to use for joining with our log data.

> ⚠️ **Important Note on Data:** The GeoIP data is not real-time and may have some inaccuracies. However, for the purpose of this lab, it is more than sufficient for demonstrating the power of data enrichment and visualization. Once the Watchlist is created, it will be available for use in your KQL queries and workbooks.

### Step 8: Build the Attack Map Workbook

Now that our logs are enriched with geolocation data, we can create a visual representation of the attacks. We'll use a **Microsoft Sentinel Workbook** to build an interactive map that displays the source of the brute-force attacks in real-time.

**What is a Workbook?** 📊 A **Workbook** is a customizable dashboard within Microsoft Sentinel that allows you to create rich visual reports. It's a blank canvas where you can combine text, queries, and various charts to tell a story with your data. Our goal is to use a workbook to display the number of failed login attempts on a world map, with each point corresponding to the location of an attacker.

<img width="1129" height="581" alt="image" src="https://github.com/user-attachments/assets/19720116-8649-4ebf-bff9-6121b7108edb" />

**Why Create a Custom Workbook?** 🎨 While Sentinel has pre-built workbooks, creating our own allows for full control. By building a custom workbook, we can create a unique visualization for `Event ID 4625` and use our custom `geoip` watchlist to enrich the data, which demonstrates a key security analyst skill: taking raw data, adding context, and presenting it clearly.

**Build the Workbook**

- **Create a New Workbook:** In the Microsoft Sentinel menu, navigate to **Workbooks** and click "**Add workbook**" to open a new, blank workbook.

<img width="696" height="542" alt="image" src="https://github.com/user-attachments/assets/872d2181-4fa0-476f-8f98-942881af1632" />

- **Add a Query:** Click "**Add**" and select "**Add query**." You'll write a KQL query that joins your `SecurityEvent` logs with your `geoip` watchlist. This query will extract the source IP address and find the corresponding latitude and longitude.

<img width="884" height="705" alt="image" src="https://github.com/user-attachments/assets/3edc7edc-1f0d-4b4e-a88d-3b68d969512e" />


- **Create the Map Visualization:** Once the query runs, change the visualization type to "**Map**." Configure the map to use the latitude and longitude columns and display the count of failed attempts for each location. You can customize the colors and sizing of the points to make the map more impactful.
    
- **Save the Workbook:** When you're happy with your attack map, click "**Done Editing**" and save your new workbook.
    
<img width="795" height="640" alt="image" src="https://github.com/user-attachments/assets/9300f2c4-1a2c-419c-9a28-22c3f01f1803" />


> ⚠️ **Important Note on the Query:** The KQL query is crucial for this step. It must correctly join the `SecurityEvent` table with the `geoip` watchlist using the IP address as the common key. A sample query would be: `SecurityEvent | where EventID == 4625 | extend IpAddress = tostring(SourceIP) | join kind=leftouter (geoip) on $left.IpAddress == $right.network | summarize FailureCount = count() by cityname, countryname, latitude, longitude`. This query counts the failures by location, preparing the data for the map.

* * *

### Why This Project?

This project is a perfect hands-on learning experience for several key reasons:

- **Practical Cloud Security Skills:** It provides direct, hands-on experience with core cloud security concepts, including log management, threat detection, and security monitoring using Microsoft Azure.
    
- **Accessible Learning:** By leveraging Azure's free-tier services, this project is designed to be accessible to everyone, from students to seasoned professionals, without incurring initial costs.
    
- **Real-World Application:** You will simulate and analyze real-world attack data, moving beyond theoretical knowledge to practical application in threat intelligence and log analysis with Kusto Query Language (KQL).
