# Practical: Install Nginx Web Server on AWS EC2 (RHEL)

Beginner-friendly step-by-step guide for students

Objective: Launch a RHEL EC2 instance, install Nginx, and host a simple custom HTML website.

## Step 1: Launch an EC2 instance

[Get the most out of Red Hat Enterprise Linux for AWS](https://images.openai.com/static-rsc-4/GlbEOxddfKw7RjfrUbU2fubZusZvarnrEwWEQQXojNo0zNgr6aDXxQAdT0YALSNSEPC7xpUUH4bqQG_Sj9Um6j9Y22mRgZtOX7888Zx5zWzjgCMo2P8cHaJuwnF5UNmAtCeXEDCUGv-OhPOlu1DUDPE4hkChK5bfGhQmkBaHMfQ?purpose=inline)

[redhat.com](https://www.redhat.com/en/blog/get-most-out-red-hat-enterprise-linux-aws)

1. Open the [AWS EC2 Console](https://console.aws.amazon.com/ec2/).
2. Click Launch instance.
3. Enter the name: `rhel-nginx-server`.
4. Select Red Hat Enterprise Linux (RHEL) as the operating system.
5. Select an instance type, such as `t3.micro`, if available and suitable for your lab.
6. Create or select a key pair and download the `.pem` file.
7. Under Network settings, allow these inbound rules:

| Type | Port | Source                      |
| ---- | ---- | --------------------------- |
| SSH  | 22   | My IP                       |
| HTTP | 80   | Anywhere IPv4 (`0.0.0.0/0`) |

8\. Click Launch instance and wait until it is running.

## Step 2: Connect to the server

1. Select your instance.
2. Click Connect → EC2 Instance Connect → Connect, if supported by your instance.

Alternatively, open PowerShell on Windows and run:

```
ssh -i "my-key.pem" ec2-user@YOUR-PUBLIC-IP
```

Replace the key filename and public IP with your actual values.

Once connected, verify the operating system:

```
cat /etc/os-release
```

## Step 3: Update the system

Run:

```
sudo dnf upgrade -y
```

This updates the installed software packages on your RHEL server.

## Step 4: Install Nginx

Run:

```
sudo dnf install nginx -y
```

Check the installation:

```
nginx -v
```

## Step 5: Start and enable Nginx

Start the web server:

```
sudo systemctl start nginx
```

Enable it to start automatically when the server boots:

```
sudo systemctl enable nginx
```

Check its status:

```
sudo systemctl status nginx
```

Look for:

```
Active: active (running)
```

Press `q` to exit the status screen.

## Step 6: Open the default website

Copy the Public IPv4 address from your EC2 console and open this URL in your browser:

```
http://YOUR-PUBLIC-IP
```

You should see the default Nginx welcome page.

## Step 7: Locate the website files

The default website directory is:

```
/usr/share/nginx/html/
```

List its files:

```
ls -l /usr/share/nginx/html/
```

The main webpage is usually:

```
/usr/share/nginx/html/index.html
```

## Step 8: Create your own website

Copy and run this complete command in the server terminal. It creates a simple HTML website.

```
sudo tee /usr/share/nginx/html/index.html > /dev/null <<'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My First Website</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: #f1f5f9;
            text-align: center;
            padding: 60px 20px;
        }
        .card {
            background: white;
            padding: 30px;
            border-radius: 12px;
            max-width: 600px;
            margin: auto;
        }
        h1 { color: #0f766e; }
    </style>
</head>
<body>
    <div class="card">
        <h1>Welcome to My Website!</h1>
        <p>This website is hosted on AWS EC2.</p>
        <p>Operating System: Red Hat Enterprise Linux</p>
        <p>Web Server: Nginx</p>
        <h3>My First Linux Web Server Practical</h3>
    </div>
</body>
</html>
EOF
```

This replaces the default homepage with your custom website.

## Step 9: Verify your website

Open your public IP address again:

```
http://YOUR-PUBLIC-IP
```

Refresh the browser. Your custom website should now appear.

You do not need to restart Nginx after changing a static HTML file.

## Quick revision

| Task                 | Command                        |
| -------------------- | ------------------------------ |
| Update system        | `sudo dnf upgrade -y`          |
| Install Nginx        | `sudo dnf install nginx -y`    |
| Start Nginx          | `sudo systemctl start nginx`   |
| Enable at boot       | `sudo systemctl enable nginx`  |
| Check status         | `sudo systemctl status nginx`  |
| Locate website files | `ls -l /usr/share/nginx/html/` |

Practical complete! Students have launched a RHEL EC2 instance, installed Nginx, and hosted their first custom static website.
