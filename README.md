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
| David      | Johnson   | djohnson | System Administrator | P@ssword123!       |
| Aisha      | Khan      | akhan    | DevOps Engineer      | P@ssword123!       |
| Noah       | Smith     | nsmith   | Security Analyst     | P@ssword123!       |
| Fatima     | Ahmed     | fahmed   | Data Scientist       | P@ssword123!       |
| Lucas      | Brown     | lbrown   | Network Engineer     | P@ssword123!       |
| Zoe        | Taylor    | ztaylor  | Software Developer   | P@ssword123!       |
| Ethan      | Clarke    | eclarke  | Finance Manager      | P@ssword123!       |
| Emily      | Wright    | ewright  | HR Manager           | P@ssword123!       |
| Omar       | Ali       | oali     | Sales Representative | P@ssword123!       |
| Sofia      | Lopez     | slopez   | Marketing Specialist | P@ssword123!       |

Each user was created using the `useradd` command, ensuring a structured and automated approach to account creation.

```bash
sudo useradd -m -s /bin/bash djohnson
```

Setting the initial password and enforcing security policies were crucial steps. I utilized `passwd` to define passwords and enforce resets on first login.

```bash
echo "P@ssword123!" | sudo passwd --stdin djohnson
sudo chage -d 0 djohnson
```

---

### **Organizing Users into Groups**
With users in place, I structured them into relevant groups based on their job roles. This helps streamline permission management and access control.

```bash
sudo groupadd devops
sudo usermod -aG devops akhan
```

For security and compliance, I assigned administrative privileges only to essential personnel, using the `wheel` group for sudo access.

```bash
sudo usermod -aG wheel djohnson
```

---

### **Configuring Permissions and Security Policies**
A key learning in this project was the importance of the principle of least privilege. I restricted file and directory access by setting appropriate ownership and permissions.

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

---

### **Lessons and Reflections**
This project reinforced my understanding of user and access management within cloud environments. It highlighted the significance of structured role assignment, automation in user creation, and enforcing security best practices.

From a scalability perspective, managing users through groups rather than individual permissions simplifies administration, especially in growing environments. Additionally, enforcing strong password policies and SSH restrictions greatly reduces security risks.

For larger-scale implementations, integrating IAM and automating user provisioning with tools like Terraform and Ansible would be the next step, reinforcing cloud-native security and efficiency.





