# Managing-Users-and-Groups-on-an-Amazon-Linux-EC2-Instance

In this project, I took on the challenge of managing users and groups on an Amazon Linux EC2 instance, applying best practices in Linux system administration while considering security and scalability.

Below is a detailed breakdown of the process, showcasing the steps I took, the reasoning behind them, and key takeaways from this hands-on experience.

---

### **Setting Up the Environment**
Launching an EC2 instance is always the foundation. I started by provisioning an Amazon Linux 2023 instance, ensuring it had the necessary security group configurations for SSH access. Connecting to the instance via SSH, I verified system details and performed initial housekeeping, such as updating system packages for security and performance enhancements.

```bash
sudo yum update -y
```

With a fresh and updated system, I was ready to define user roles and access controls.

---

### **Creating Users and Assigning Roles**
To maintain a structured approach to user management, I created user accounts based on predefined job roles. This ensures a clear distinction of access levels, reducing the risk of unauthorized actions.

The following table outlines the users I created along with their roles and initial credentials:

| First Name | Last Name | User ID  | Job Role             | Starting Password  |
|------------|-----------|----------|----------------------|--------------------|
| David      | Johnson   | djohnson | System Administrator | Welcome@2025       |
| Aisha      | Khan      | akhan    | DevOps Engineer      | Welcome@2025       |
| Noah       | Smith     | nsmith   | Security Analyst     | Welcome@2025       |
| Fatima     | Ahmed     | fahmed   | Data Scientist       | Welcome@2025       |
| Lucas      | Brown     | lbrown   | Network Engineer     | Welcome@2025       |
| Zoe        | Taylor    | ztaylor  | Software Developer   | Welcome@2025       |
| Ethan      | Clarke    | eclarke  | Finance Manager      | Welcome@2025       |
| Emily      | Wright    | ewright  | HR Manager           | Welcome@2025       |
| Omar       | Ali       | oali     | Sales Representative | Welcome@2025       |
| Sofia      | Lopez     | slopez   | Marketing Specialist | Welcome@2025       |

Before proceeding, I verified my working directory using:

```bash
pwd
```

Each user was created using the `useradd` command, ensuring a structured and automated approach to account creation.

```bash
sudo useradd djohnson
```

Setting the initial password and enforcing security policies were crucial steps. I utilized `passwd` to define passwords and enforce resets on first login.

```bash
echo "Welcome@2025" | sudo passwd --stdin djohnson
sudo chage -d 0 djohnson
```

To verify that the users were successfully created, I ran:

```bash
sudo cat /etc/passwd | cut -d: -f1
```
---

### **Organizing Users into Groups**
With users in place, I organized them into relevant groups based on their job roles. This helps streamline permission management and access control.

```bash
sudo groupadd devops
sudo usermod -aG devops akhan
```
To verify that the group was added, I checked:

```bash
cat /etc/group
```

For security and compliance, I assigned administrative privileges only to essential personnel, using the `wheel` group for sudo access.

```bash
sudo usermod -aG wheel djohnson
```

To check group memberships, I ran:
```bash
sudo cat /etc/group
```

---

### **Configuring Permissions and Security Policies**
I restricted file and directory access by setting appropriate ownership and permissions based on principle of least privilege.

```bash
sudo chown root:devops /opt/devops-tools
sudo chmod 770 /opt/devops-tools
```

Additionally, I configured SSH settings to prevent root login and enforce key-based authentication.

```bash
sudo nano /etc/ssh/sshd_config
PermitRootLogin no
PasswordAuthentication no
```

Restarting the SSH service ensured that changes took effect.

```bash
sudo systemctl restart sshd
```


### **Logging in as a New User**

To verify access control, I attempted to log in as `djohnson` and perform basic actions:

```bash
su djohnson
pwd
touch myFile.txt
```

This action failed because djohnson did not have sudo privileges. To check system logs for authentication attempts, I switched to a privileged user and ran:

```bash
sudo cat /var/log/secure
```
---

### **Lessons and Reflections**
This project reinforced my understanding of user and access management within cloud environments. It highlighted the significance of structured role assignment, automation in user creation, and enforcing security best practices.

From a scalability perspective, managing users through groups rather than individual permissions simplifies administration, especially in growing environments. Additionally, enforcing strong password policies and SSH restrictions greatly reduces security risks.

For larger-scale implementations, integrating IAM and automating user provisioning with tools like Terraform and Ansible would be the next step, reinforcing cloud-native security and efficiency.





