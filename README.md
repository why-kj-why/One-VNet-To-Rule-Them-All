# One VNet To Rule Them All

In this Instruction Manual, you shall learn how to configure a Virtual Network within an Azure Resource Group such that all other resources can only interact with each other if they are placed in the same Virtual Network.


## Introduction

Before we begin, it is important for you to be well-acquainted with the resources you shall be creating in this Manual. Each heading serves as an anchor link, pressing which will directly take you to the steps to create that resource.

* [**Azure Resource Group**](#resource-group)
A Resource Group is a logical container in Azure that holds related resources like VMs, networks, and databases. It helps organize, manage, and control access to these resources as a unit, simplifying deployment, monitoring, and billing.

* [**Azure Virtual Network (VNet)**](#virtual-network)
An Azure Virtual Network is a private, isolated network that securely connects Azure resources. It enables communication between VMs, services, and on-premises networks, and supports subnets, private IPs, network security groups, and peering.

* [**Azure Virtual Machine (VM)**](#virtual-machine-sql-server)
Azure Virtual Machines are scalable, on-demand computing resources that run Windows or Linux. They offer full control over the OS and environment, ideal for hosting applications, databases, or custom workloads.

* [**Azure Container Registry (ACR)**](#container-registry)
ACR is a managed Docker container registry in Azure. It allows you to build, store, manage, and deploy container images securely and at scale, integrated with services like Azure Kubernetes Service and Function Apps.

* [**Azure Function App**](#function-app)
An Azure Function App lets you run serverless code triggered by events (like HTTP requests or timers). It abstracts infrastructure management, enabling quick deployment of lightweight, scalable functions with pay-per-use pricing.


## Assembling Our Heroes

Now that you have attained a surface-level understanding of how each Azure resource works, let us string all of these pieces together to achieve our objective.

#### Resource Group

1. Sign-in to the Azure Portal and press the **+Create a Resource** button on the top-left of your Dashboard
2. Type **Resource group** in the search bar and press **Create**
3. In the **Basics** tab, select the desired **Subscription**, enter a suitable **Name** for your Azure Resource Group and select its **Region** of operation ![Screenshot](https://github.com/why-kj-why/One-VNet-To-Rule-Them-All/blob/main/manual-assets/resource-group-3.png)

4. Add any **Tags** you may deem necessary and press **Review+Create**
5. Azure will run a quick validation before allowing you to create your Resource Group


#### Virtual Network

1. Navigate to the Azure Resource Group created earlier and press the **+Create** button on the top-left of the Overview tab
2. Type **Virtual Network** in the search bar and press **Create**
3. In the **Basics** tab, confirm the **Subscription**, **Resource Group** and the **Region** of operation before entering a suitable **Name** for your Virtual Network ![Screenshot](https://hackmd.io/_uploads/HkL_UoRR1l.png)

4. In the **Security** tab, leave all checkboxes unchecked and proceed
5. In the **IP addresses** tab, you will notice that an **address space** has already been created with a **default subnet** present
6. Press the **+Add a subnet** button and enter a suitable **Name** for this subnet in the window that opens on the right-hand side of the window
7. For a general-purpose subnet (like the one dedicated to an SQL Server) the **Size** should be set to **/24** ![Screenshot](https://hackmd.io/_uploads/rJRewj0CJg.png)

8. **Create** and name a new Network Security Group for this subnet and select the **Network Security Groups** option in the **Private endpoint network policy** dropdown before pressing **Add** ![Screenshot](https://hackmd.io/_uploads/rJoZwj0R1e.png)

9. Repeat step 6 to step 8 for a subnet dedicated to Azure Function Apps, but with **Size** set to **/27**
10. Add any **Tags** you may deem necessary and press **Review+Create**
11. Azure will run a quick validation before allowing you to create your Virtual Network
12. Once the Virtual Network resource has been registered, navigate to its **Subnets** tab under the **Settings** section of the left-hand side toolbar
13. Press the **+Subnet** button to add a new subnet dedicated to Private Endpoints, with **Size** set to **/24** and **Private endpoint network policy** set to **Disabled** (since a new Network Security Group is not necessary)
14. The final subnet configuration of your Virtual Network should look like this: ![Screenshot](https://hackmd.io/_uploads/HklE_iACJx.png)


#### Virtual Machine (SQL Server)

1. Navigate to the Azure Resource Group created earlier and press the **+Create** button on the top-left of the Overview tab
2. Type **Virtual Machine** in the search bar and press **Create**
3. In the **Basics** tab, confirm the **Subscription**, **Resource Group** and the **Region** of operation before entering a suitable **Name** for your Virtual Machine and setting the Availability options as **Availability Zones** ![Screenshot](https://hackmd.io/_uploads/rkU8toA0kx.png)

4. When selecting the **Image**, click on **See all images**, type **SQL Server 2019** in the search bar, select the **Free SQL Server License: SQL 2019 Developer on Windows Server 2019 - x64 Gen 2** image and set the **Size** to **Standard_B2s** ![Screenshot](https://hackmd.io/_uploads/SkvSisA0kl.png)

5. Configure the **Username** and **Password** for your Virtual Machine and select **SSH (22)** and **RDP (3389**) in the **Select inbound ports** setting ![Screenshot](https://hackmd.io/_uploads/HJe8oi0C1e.png)

6. In the **Disks** tab, select **Image default (127 GiB)** as the **OS disk size**, **Premium SSD (locally-redundant storage)** as the **OS disk type** and make sure the **Delete with VM** checkbox is checked
7. In the **Networking** tab, select the **Virtual Network** created earlier, then select the **default subnet** as the **Subnet** before pressing **Create new** in the **Public IP** setting and keeping **None** as the **Load balancing option**
8. The final Networking configuration of your Virtual Machine should look like this: ![Screenshot](https://hackmd.io/_uploads/B1rT6o00Jx.png)
 
9. In the **Management** tab, make sure that all of the checkboxes are left unchecked, before proceeding to the **Monitoring** tab, where the default settings can be used
10. Since you do not need any **Extensions** or **Applications**, you can leave the **Advanced** tab unchanged
11. In the **SQL Server settings** tab, make sure the **SQL connectivity** setting is set to **Private (within Virtual Network)** and the **Port** is set to **1433**, before selecting **Enable** on the **SQL Authentication** setting, which will automatically choose the Username and Password you assigned to this Virtual Machine
12. Add any **Tags** you may deem necessary and press **Review+Create**
13. Azure will run a quick validation before allowing you to create your Windows Virtual Machine
14. Once the Virtual Machine resource has been registered, you can easily RDP into it using the downloadable RDP file in your Native RDP software with the Username and Password you had set


#### Virtual Machine (Neo4j)

1. Navigate to the Azure Resource Group created earlier and press the **+Create** button on the top-left of the Overview tab
2. Type **Virtual Machine** in the search bar and press **Create**
3. In the **Basics** tab, confirm the **Subscription**, **Resource Group** and the **Region** of operation before entering a suitable **Name** for your Virtual Machine and setting the Availability options as **Availability Zones**
4. When selecting the **Image**, click on **See all images**, type **Ubuntu 24.04 LTS** in the search bar, select the **Ubuntu Server 24.04 LTS - x64 Gen2** image provided by Canonical and set the **Size** to **Standard_DS1_v2**
5. Select **Password** as the **Authentication type** and set the **Username** and **Password** for your Linux Virtual Machine, before selecting **HTTP (80)**, **HTTPS (443)** and **SSH (22)** as the permitted **Inbound ports** for this resource ![Screenshot](https://hackmd.io/_uploads/BkujGuykgg.png)

6. In the **Disks** tab, select **Image default (30 GiB)** as the **OS disk size**, **Premium SSD (locally-redundant storage)** as the **OS disk type** and make sure the **Delete with VM** checkbox is checked
7. In the **Networking** tab, select the **Virtual Network** created earlier, then select the **default subnet** as the **Subnet** before pressing **Create new** in the **Public IP** setting and keeping **None** as the **Load balancing option**
8. The final Networking configuration of your Virtual Machine should look like this: ![Screenshot](https://hackmd.io/_uploads/HJU3EO1yxx.png)

9. In the **Management** tab, make sure that all of the checkboxes are left unchecked, before proceeding to the **Monitoring** tab, where the default settings can be used
10. Since you do not need any **Extensions** or **Applications**, you can leave the **Advanced** tab unchanged
11. Add any **Tags** you may deem necessary and press **Review+Create**
12. Azure will run a quick validation before allowing you to create your Windows Virtual Machine
13. Once your Virtual Machine resource has been registered, navigate to its **Connect** tab and select the **SSH using Azure CLI** option, which will open up a **Cloud Shell** within your Browser window after a brief validation check, where you would be asked to type **Yes** to continue connecting to your Linux machine via SSH
14. Type the following command into your Cloud Shell instance to update your Linux machine's package list with the latest versions of the available software:
```
$ sudo apt update
```

15. Type the following command into your Cloud Shell instance to install Java, which is a Neo4j dependency:
```
$ sudo apt install openjdk-17-jdk -y
```

16. Type the following command into your Cloud Shell instance to add the Neo4j APT repo:
```

$ wget -O - https://debian.neo4j.com/neotechnology.gpg.key | \
sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/neo4j.gpg && \
echo 'deb https://debian.neo4j.com stable 5' | \
sudo tee /etc/apt/sources.list.d/neo4j.list && \
sudo apt update
```

17. Type the following command into your Cloud Shell instance to update your Linux machine's package list with the latest versions of the available software once again:
```
$ sudo apt update
```

18. Type the following command into your Cloud Shell instance to install the latest version of Neo4j into your Linux machine:
```
$ sudo apt install neo4j -y
```

19. Once Neo4j has been successfully installed, type the following commands into your Cloud Shell instance to first enable and then start the Neo4j service:
```
$ sudo systemctl enable neo4j
$ sudo systemctl start neo4j
```

20. Type the following command into your Cloud Shell instance to check the status of your Neo4j service:
```
$ sudo systemctl status neo4j
```

21. Type the following command into your Cloud Shell instance to open the Neo4j configuration file:
```
$ sudo nano /etc/neo4j/neo4j.conf
```

22. Type the following command into your Cloud Shell instance to allow traffic on port 7474:
```
$ sudo ufw allow 7474/tcp
```

23. Press **Ctrl+W**, search for **#server.default_listen_address** and uncomment the line to allow Neo4j to accept non-local connections, then press **Ctrl+S** and type the following command into your Cloud Shell instance to verify if your Linux machine is listening on all network interfaces:
```
$ sudo ss -tuln | grep 7474
```

24. Minimise the Cloud Shell and navigate to the **Network settings** tab under the **Networking** section of your Linux Virtual Machine, before scrolling down to the **Rules** section and pressing the **+Create port rule** button on the right-hand side of the window and selecting **Inbound port rule**
25. Select **Neo4j** in the **Service** dropdown menu, confirm that the **Destination port** is set as **7474** and press **Add**
26. Repeat this process by selecting **Custom** in the **Service** dropdown menu and entering the desired ports, such that your Linux Virtual Machine's final Inbound port rules look like this: ![Screenshot](https://hackmd.io/_uploads/HJgD5q1Jex.png)
27. Search for **http://virtual-machine-IP-address:7474** in your Browser to open the Neo4j web interface and enter neo4j as both the **Username** and the **Password**, before setting up a new password for your Neo4j instance
28. The Neo4j web interface should look like either one of the following images: ![Screenshot](https://hackmd.io/_uploads/S1rQ351klx.png)
![Screenshot](https://hackmd.io/_uploads/rk6Q2cyyge.png)


#### Container Registry

1. Navigate to the Azure Resource Group created earlier and press the **+Create** button on the top-left of the Overview tab
2. Type **Container Registry** in the search bar and press **Create**
3. In the **Basics** tab, confirm the **Subscription**, **Resource Group** and **Location** before giving the resource a suitable **Name** and selecting **Basic** as the **Pricing plan**
4. With the **Basic** pricing plan, you will be unable to make any changes in the **Networking** and **Encryption** tabs
5. Add any **Tags** you may deem necessary and press **Review+Create**
6. Azure will run a quick validation before allowing you to create your Azure Container Registry
7. Once the resource has been registered, navigate to the **Access keys** tab in its **Settings** section and check the **Admin user** checkbox to automatically assign it an access key


#### Function App

1. Navigate to the Azure Resource Group created earlier and press the **+Create** button on the top-left of the Overview tab
2. Type **Function App** in the search bar and press **Create**
3. Select App Service as the Hosting Option, and confirm the **Subscription** and **Resource Group** before giving the resource a suitable **Name** in the **Basics** tab
4. Select the **Region** of operation that has been used for all the other resources in your resource group and create a new **App Service Plan** with its **Pricing plan** set as **Premium V3 P1V3** (Function Apps created in the future can also choose this App Service Plan) ![Screenshot](https://hackmd.io/_uploads/HyZMLhACJg.png)
 
5. In the **Storage** tab, create a new **Storage account** if this is the first Function App you are registering in your resource group, otherwise simply use the one you have already made since the Storage account you create will only be accessible by resources in the same Virtual Network
6. In the **Networking** tab, **Disable (select Off) public access** and **Enable (select On) virtual network integration**, select the **Virtual Network created earlier** and **Disable (select Off) private networks** under the **Inbound access** section ![Screenshot](https://hackmd.io/_uploads/H1d7dhCA1x.png)

7. Under the **Outbound access** section, **Enable (select On) VNet integration** and select the **subnet dedicated to Azure Function Apps** as the **Outbound subnet**, such that your final configuration should look like so: ![Screenshot](https://hackmd.io/_uploads/rJeAdnRRkx.png)

8. In the **Monitoring** tab, **Enable (select Yes) Application Insights** and create a new **Application Insights** resource in the same **Region**, since it would help you in debugging the Function App
9. Leave the default setting in the **Durable Functions** tab, add any **Tags** you may deem necessary and press **Review+Create**
10. Azure will run a quick validation before allowing you to create your Azure Function App
11. Once your Function App has been registered, navigate to the **Configration** tab in its **Settings** section, select **General settings** and press **On** on the **SCM Basic Auth Publishing Credentials** setting
12. Proceed to the **Networking** tab in your Function App's **Settings** section and click on the **Private endpoints** hyperlink under the **Inbound traffic configuration** section
13. Press the **+Add** button on the top-left of the **Private Endpoint connections** window and then select **Express**
14. Give a suitable **Name** to the private endpoint, confirm your **Subscription**, select the **Virtual Network** created earlier, select the **subnet dedicated to Private Endpoints** as the **Subnet** and choose **Yes** to **Integrate with private DNS Zone** before pressing **OK** ![Screenshot](https://hackmd.io/_uploads/B1IbahRAyx.png)

15. After these changes, the Networking configuration of your Azure Function App should like this: ![Screenshot](https://hackmd.io/_uploads/Hke2phCRJe.png)


## Conclusion

If the steps laid out above were followed correctly, you should have the following resources in your Resource Group (among other dependencies):

1. A Virtual Network with Subnets dedicated to SQL Server databases, Azure Function Apps and Private Endpoints
2. A Windows Virtual Machine which hosts your SQL Server
3. A Linux Virtual Machine which hosts your Neo4j instance
4. A Container Registry to handle all of your docker deployments
5. A Function App designed to establish a connection to the SQL Server database using MS ODBC, over the Virtual Network you have created
6. A Function App designed to retrieve files from a Storage Account and establish a connection to the Neo4j instance, over the Virtual Network you have created

Once the entire set-up has been approved, you can push your Python programmes using docker and upload the AdventureWorks2019 database on to your SQL Server using the SSMS software that comes pre-loaded in your Windows machine.
