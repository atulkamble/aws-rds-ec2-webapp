# 🚀 **AWS RDS Full Project - EC2 + RDS + Web App**

---

## **Project Components**

1. **RDS MySQL Database**
2. **EC2 Instance (Web Server)**
3. **Security Groups Configuration**
4. **Simple Web Application (PHP/Python)**
5. **Connectivity and Data Operations**

---

## **Step-by-Step Guide**

---

## **1. Create RDS MySQL Instance**

### CLI Command:

```bash
aws rds create-db-instance \
    --db-instance-identifier mydbinstance \
    --db-instance-class db.t3.micro \
    --engine mysql \
    --allocated-storage 20 \
    --master-username admin \
    --master-user-password YourStrongPassword123 \
    --vpc-security-group-ids sg-xxxxxxxx \
    --db-subnet-group-name default \
    --backup-retention-period 7 \
    --multi-az \
    --publicly-accessible \
    --region us-east-1
```

### Verify:

```bash
aws rds describe-db-instances --db-instance-identifier mydbinstance
```

---

## **2. Create EC2 Instance (Web Server)**

```bash
aws ec2 run-instances \
    --image-id ami-0c02fb55956c7d316 \
    --instance-type t2.micro \
    --key-name my-keypair \
    --security-group-ids sg-xxxxxxxx \
    --subnet-id subnet-xxxxxxxx \
    --associate-public-ip-address \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=WebServer}]'
```

---

## **3. Security Groups Setup**

* **RDS Security Group**: Allow inbound MySQL/Aurora port **3306** from EC2 Security Group.
* **EC2 Security Group**: Allow inbound **HTTP (80)**, **SSH (22)**, and outbound to RDS.

---

## **4. Web App Setup on EC2 (PHP + MySQL Example)**

### SSH into EC2:

```bash
ssh -i my-keypair.pem ec2-user@<EC2_PUBLIC_IP>
```

### Install Apache, PHP, MySQL Client:

```bash
sudo yum update -y
sudo yum install -y httpd php php-mysqlnd mysql
sudo systemctl start httpd
sudo systemctl enable httpd
```

---

## **5. Web App Code (PHP Example)**

Create a file `/var/www/html/index.php`:

```php
<?php
$servername = "<RDS-ENDPOINT>";
$username = "admin";
$password = "YourStrongPassword123";
$dbname = "mydatabase";

$conn = new mysqli($servername, $username, $password, $dbname);
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
echo "Connected successfully to RDS MySQL";
?>
```

---

## **6. Access Web App**

* Open browser → `http://<EC2_PUBLIC_IP>`
* You should see: **"Connected successfully to RDS MySQL"**

---

## **7. (Optional) Create a Database & Table on RDS**

SSH into EC2 and connect to RDS:

```bash
mysql -h <RDS-ENDPOINT> -u admin -p
```

Run SQL:

```sql
CREATE DATABASE mydatabase;
USE mydatabase;
CREATE TABLE users (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(50), email VARCHAR(50));
INSERT INTO users (name, email) VALUES ('Atul', 'atul@example.com');
SELECT * FROM users;
```

---

## **8. Terraform (Optional - Infra as Code)**

Want me to provide **Terraform code for RDS + EC2 + SG Setup (Full IaC Project)?**

---

## **Project Repo Structure**

```
aws-rds-ec2-webapp/
│
├── rds-setup.sh         # CLI commands for RDS
├── ec2-setup.sh         # CLI commands for EC2
├── webapp/
│   └── index.php        # PHP App connecting to RDS
├── terraform/            # (Optional) Full Infra as Code
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
└── README.md             # Project Documentation
```

---
