<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1: Deploy Virtual Machines in Azure
- Step 2: Use server managaer to configure Active Directory
- Step 3: Create a user and assign permissions
- Step 4: Manage security protocols using Group Policy

<h2>Deployment and Configuration Steps</h2>

<p>
<img src="https://i.imgur.com/02RJ4aH.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
To configure an Active Directory within Microsoft Azure, we are going to need two virtual machines. One will act as the domain controller, and one will be a standard client computer located on the same network. The domain controller VM will need to be made with the Windows Server edition, and the other VM can be made with Windows 10. In this project, we will be using Remote Desktop to view both virtual machines on the cloud, so make sure to keep track of which virtual machine we will be using in the steps below.
</p>
<br />

<p>
<img src="https://i.imgur.com/17NuXNi.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Firstly, let's use Remote Desktop to view our domain controller and turn off the firewall because we need Client-1 to be able to access the DC-1 (domain controller) DNS server. After doing this, we need to change the DNS settings of Client-1 within Azure so that the virtual machine relies on the domain controller for DNS. In Microsoft Azure, we need to locate the private IP address of the domain controller, and then we go to the custom DNS server setting for Client-1 and change it to the private IP address we obtained. Now, we can log in and Remote Desktop into Client-1 and open PowerShell from the Start menu. From there, we can ping the domain controller’s private IP and see if a connection is able to be established. After pinging DC-1, if we see that the packets were able to be sent and received, then we can determine that a stable connection was established. Lastly, we can type ipconfig in the command window, and it should show DC-1’s private IP address under DNS server.
</p>
<br />

<p>
<img src="https://i.imgur.com/86PEsvh.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now that we've completed setting up the domain controller and the client computer in Azure, we can log into DC-1 and open the Server Manager through the Start menu. On the Server Manager Dashboard, you will now want to click on Add Roles and Features and just follow the on-screen prompts. This will give you all the necessary features to operate an Active Directory in your domain. Next, we need to promote the server to a domain controller by hitting the flag in the top left corner, and on the configuration page, click on Add a New Forest and name it mydomain.com. Next, head over to the Windows Start menu and type in Active Directory Users and Computers and click on Open. From here, you should see the domain we created (mydomain.com), and after right-clicking on it, you should see an option to create an Organizational Unit. Create an OU titled "_Employees" and one named "_Admins". From here, we can create a new user in the _Admins OU. In this example, I will name the user Jake Lowen and give him a username and password so we can use that account to log into our Client-1 virtual machine.
</p>
<br />

<p>
<img src="https://i.imgur.com/6endEFM.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Making a user in the _Admins OU doesn’t automatically give the user admin permissions, so we need to right-click on the user and head to the Properties tab. From within Properties, we click on "A Member Of", and we will type in Domain Admin in the text box. Now, log into Client-1 as Jake Lowen and go to Settings so that we can rename the PC and add it to the list of objects being used within the domain; doing so will require you to sign into the admin account we created earlier. After doing this, we should see Client-1 in the Computers folder when we check the Active Directory. Just to keep ourselves organized, let's drag the computer into a new OU called Client-1.
</p>
<br />

<p>
<img src="https://i.imgur.com/n1dZxUT.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In the next part of the tutorial, we will go over how to add users into the Employees Organizational Unit using a script that we input into PowerShell. Let's log back into Client-1 as Jake and go to Remote Desktop Settings in Windows. From there, we can click on the link that says "Select users that can remotely access this PC", and we will allow Domain Users to log into Client-1. Next, let's log back into DC-1 and open PowerShell as an administrator. From here, copy and paste the script in the Resource section of this project, and you should see the _Employees OU begin to fill with random users. All these users will have random usernames, but the passwords will all be "Password1". Test any account you want by typing in their username into Client-1 and using Password1.
</p>
<br />

<p>
<img src="https://i.imgur.com/1wjsw3i.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In order for any of our users in the domain to be locked out of their accounts, the Account Lockout Policy needs to be configured. Let's go to the Start Menu, type in Group Policy Management, and hit Open. From there, we can right-click the Default Policy and hit Edit, then we can go to Windows Settings and from there, click on Security Settings/Account Lockout. The two parameters you can edit are Duration and Threshold. Duration is how long before login attempts can be made after a lockout, and Threshold is the number of login attempts it takes before an account is locked. For now, let's set the Duration to 30 minutes and the Threshold to 5 attempts. Now, we can head back into our Active Directory and look into the Employees tab for an account to log into Client-1 with. Now, let's use that account (ac1) and purposely fail the login 6 times. At this point, you should see a message that states "You've been locked out of your account," which is exactly what we are looking for.
</p>
<br />

<p>
<img src="https://i.imgur.com/iKxA8Td.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next, you need to log back into DC-1, open the Active Directory, and right-click on our domain and click Find. From there, we can type in our user's name, and it's as simple as right-clicking the user's profile, going to Properties, and checking the Unlock Account box. On this page, you can also reset the password of the account if needed. As a side note, accounts can also be disabled and enabled by right-clicking on the user.
</p>
<br />
