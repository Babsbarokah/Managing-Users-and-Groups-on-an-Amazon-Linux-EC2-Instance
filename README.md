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

To create the CSV file, I used nano with the following steps:

```bash
nano users.csv
```

I then entered the user data in the format above and saved the file as users.csv.

With the CSV file ready, I created users based on the information provided. Here is an example for creating one user:

### *** Manual User Creation ***
The first user was created manually using the `useradd` command, ensuring a structured and automated approach to account creation.

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

<img width="1440" alt="Screenshot 2025-03-27 at 01 03 14" src="https://github.com/user-attachments/assets/e41fdb12-094c-4e9e-a6c3-54a5fe2d3d4d" />

---

### **Automating User Creation**
After manually creating the initial user, I transitioned to an automated approach for creating the rest of the users based on the information in my CSV file. I used a bash script to read the CSV file, create users, set their passwords, and enforce password changes on first login.

```bash
#!/bin/bash

while IFS=',' read -r first_name last_name user_id job_role password
do
    if [[ "$user_id" != "User ID" ]]; then
        sudo useradd "$user_id"
        echo "$password" | sudo passwd --stdin "$user_id"
        sudo chage -d 0 "$user_id"
    fi
done < users.csv
```

This script reads the `users.csv` file and creates users in bulk, ensuring they have the appropriate password and policies set.

---

### **Organizing Users into Groups (Automated)**
With users in place, I organized them into relevant groups based on their job roles. This helps streamline permission management and access control.

```bash
#!/bin/bash

while IFS=',' read -r first_name last_name user_id job_role password
do
    if [[ "$user_id" != "User ID" ]]; then
        sudo usermod -aG "$job_role" "$user_id"
    fi
done < users.csv
```

--- 
In this script, the users are automatically assigned to groups matching their job role, such as `admin_group`, `devops`, `security`, etc.

To verify that the group was added, I checked:

```bash
cat /etc/group
```


---

### **Configuring Permissions and Security Policies (Automated)**
Similarly, I automated configuring file permissions based on the user roles defined in the CSV file. This ensures that each group has the appropriate access to the resources they need.
I restricted file and directory access by setting appropriate ownership and permissions based on principle of least privilege.


```bash
#!/bin/bash

while IFS=',' read -r first_name last_name user_id job_role password
do
    if [[ "$user_id" != "User ID" ]]; then
        case "$job_role" in
            "System Administrator")
                sudo chmod 770 /opt/admin-tools
                sudo chown root:admin_group /opt/admin-tools
                ;;
            "DevOps Engineer")
                sudo chmod 770 /opt/devops-tools
                sudo chown root:devops /opt/devops-tools
                ;;
            "Security Analyst")
                sudo chmod 770 /opt/security-tools
                sudo chown root:security /opt/security-tools
                ;;
            *)
                echo "No permissions defined for $job_role"
                ;;
        esac
    fi
done < users.csv
```

This script sets permissions for different directories based on the job role of each user, ensuring a secure and structured environment.

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

### ***Challenges** 

Initially, automating user creation and role assignment presented some formatting errors in the CSV file, leading to troubleshooting and refinements in the script logic. Another challenge was enforcing security policies consistently across different users and roles, but testing with different accounts helped validate the setup.


<img width="1298" alt="Screenshot 2025-03-27 at 01 11 23" src="https://github.com/user-attachments/assets/8ca0b1c5-dc44-4b35-bdeb-18c52df5278c" />

---

<img width="1429" alt="Screenshot 2025-03-27 at 02 52 15" src="https://github.com/user-attachments/assets/340b6428-a4bc-4410-a287-6ff43ba77e6f" />


### **Lessons and Reflections**
This project reinforced my understanding of user and access management within cloud environments. It highlighted how managing permissions across multiple job roles requires careful planning to ensure security and efficiency.

From a scalability perspective, managing users through groups rather than individual permissions simplifies administration, especially in growing environments. Additionally, enforcing strong password policies and SSH restrictions greatly reduces security risks.

For larger-scale implementations, integrating IAM and automating user provisioning with tools like Terraform and Ansible would be the next step, reinforcing cloud-native security and efficiency.





