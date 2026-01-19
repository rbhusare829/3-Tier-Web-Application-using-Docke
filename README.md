# 🚀 3-Tier Web Application using Docker (NGINX + PHP + MySQL)

This project demonstrates a complete **3-Tier Web Application Architecture** implemented using **Docker** and **Docker Compose**.

A simple **Signup Form** is served via NGINX, processed by PHP-FPM, and user data is stored in a MySQL database.

---

## 🧩 Architecture Overview

```
User (Browser)
     |
     | HTTP :80
     v
NGINX (Web Tier)
     |
     | FastCGI :9000
     v
PHP-FPM (App Tier)
     |
     | MySQL :3306
     v
MySQL (DB Tier)
```

---

## 🛠️ Technologies Used

- Docker  
- Docker Compose  
- NGINX  
- PHP (PHP-FPM)  
- MySQL  
- HTML  
- AWS EC2 (Amazon Linux)

---

## 📁 Project Structure

```
3tier/
├── app/code/submit.php
├── web/code/signup.html
├── web/config/default.conf
├── db/Dockerfile
├── db/init.sql
├── docker-compose.yml
└── README.md
```

---

## ⚙️ docker-compose.yml

```yaml
services:
  web:
    image: nginx
    ports:
      - "80:80"
    volumes:
      - ./web/code/:/usr/share/nginx/html/
      - ./web/config/:/etc/nginx/conf.d/
    networks:
      - webnet
    depends_on:
      - app
      - db

  app:
    image: bitnami/php-fpm
    volumes:
      - ./app/code/:/app
    networks:
      - webnet
      - dbnet
    depends_on:
      - db

  db:
    build: ./db/
    volumes:
      - vol1:/var/lib/mysql/
    networks:
      - dbnet

networks:
  webnet:
  dbnet:

volumes:
  vol1:
```

---

## 🌐 NGINX Configuration (`default.conf`)

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name localhost;

    root /usr/share/nginx/html;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }

    location ~ \.php$ {
        root /app;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME /app$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

---

## 🗄️ MySQL Dockerfile

```dockerfile
FROM mysql

ENV MYSQL_ROOT_PASSWORD=root
ENV MYSQL_DATABASE=FCT

COPY init.sql /docker-entrypoint-initdb.d/

EXPOSE 3306

CMD ["mysqld"]
```

---

## 🗃️ Database Initialization (`init.sql`)

```sql
USE FCT;

CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(20),
    email VARCHAR(100),
    website VARCHAR(255),
    gender VARCHAR(6),
    comment VARCHAR(100)
);
```

---

## 📝 Signup Form (`signup.html`)

```html
<!DOCTYPE html>
<html>
<head>
    <title>Signup Form</title>
</head>
<body>

<h2>Signup Form</h2>

<form action="submit.php" method="post">
    <label>Name:</label><br>
    <input type="text" name="name" required><br><br>

    <label>Email:</label><br>
    <input type="email" name="email" required><br><br>

    <label>Website:</label><br>
    <input type="url" name="website"><br><br>

    <label>Comment:</label><br>
    <textarea name="comment" rows="4" cols="50"></textarea><br><br>

    <label>Gender:</label><br>
    <input type="radio" name="gender" value="female" required> Female<br>
    <input type="radio" name="gender" value="male"> Male<br>
    <input type="radio" name="gender" value="other"> Other<br><br>

    <input type="submit" value="Submit">
</form>

</body>
</html>
```

---

## ⚙️ PHP Backend (`submit.php`)

```php
<?php
error_reporting(E_ALL);
ini_set('display_errors', 1);

$name = $_POST['name'];
$email = $_POST['email'];
$website = $_POST['website'];
$comment = $_POST['comment'];
$gender = $_POST['gender'];

$servername = "db";
$username = "root";
$password = "root";
$dbname = "FCT";

$conn = mysqli_connect($servername, $username, $password, $dbname);

if (!$conn) {
    die("Connection failed: " . mysqli_connect_error());
}

$sql = "INSERT INTO users (name, email, website, comment, gender)
        VALUES ('$name', '$email', '$website', '$comment', '$gender')";
?>

<!DOCTYPE html>
<html>
<head>
    <title>Form Submission Result</title>
</head>
<body>

<?php
if (mysqli_query($conn, $sql)) {
    echo "<h2>New record created successfully!</h2>";
    echo "<h3>Submitted Information:</h3>";
    echo "<ul>";
    echo "<li><strong>Name:</strong> " . htmlspecialchars($name) . "</li>";
    echo "<li><strong>Email:</strong> " . htmlspecialchars($email) . "</li>";
    echo "<li><strong>Website:</strong> " . htmlspecialchars($website) . "</li>";
    echo "<li><strong>Comment:</strong> " . htmlspecialchars($comment) . "</li>";
    echo "<li><strong>Gender:</strong> " . htmlspecialchars($gender) . "</li>";
    echo "</ul>";
} else {
    echo "<h3>Error: " . mysqli_error($conn) . "</h3>";
}

mysqli_close($conn);
?>

</body>
</html>
```

---

## ▶️ How to Run the Project

```bash
docker-compose up -d
```

---

## 🌍 Access the Application

```
http://<EC2_PUBLIC_IP>/signup.html
```

---

## 🔍 Verify Database

```bash
docker exec -it 3tier-db-1 mysql -u root -p
```

```sql
USE FCT;
SELECT * FROM users;
```

---

## ✅ Features

- Complete 3-Tier Docker Architecture  
- NGINX + PHP-FPM integration  
- MySQL database initialization using Docker  
- Persistent storage using Docker volumes  
- Real-world DevOps hands-on project  

---

## 👨‍💻 Author

**Rohit Bhusare**  
DevOps Learner | AWS | Docker | Linux
