# Student Guide: Install Nginx Web Server on AWS EC2 (RHEL)

Level: Beginner · Platform: AWS EC2 · Operating System: Red Hat Enterprise Linux (RHEL)

In this practical guide, students will learn how to launch a RHEL EC2 instance, connect to the server, install Nginx, host a custom static HTML website, and deploy a website directly from GitHub.

## What you will learn

By the end of this practical, you will be able to:

- Launch and connect to a RHEL EC2 instance.
- Update system packages using `dnf`.
- Install and manage the Nginx web server.
- Understand `systemctl` commands such as `start`, `stop`, `status`, and `enable`.
- Locate website files at `/usr/share/nginx/html`.
- Create and publish a custom HTML website.
- Clone a GitHub repository and deploy its website files.
- Access your website using the EC2 public IP address.

## How the deployment works

AWS EC2 — RHEL Linux Server

Nginx Web Server

Receives HTTP requests on port 80

Website Directory

/usr/share/nginx/html/

Student's Web Browser

[http://YOUR-EC2-PUBLIC-IP](http://YOUR-EC2-PUBLIC-IP)

## Step 1: Launch a RHEL EC2 instance

[Get the most out of Red Hat Enterprise Linux for AWS](https://images.openai.com/static-rsc-4/GlbEOxddfKw7RjfrUbU2fubZusZvarnrEwWEQQXojNo0zNgr6aDXxQAdT0YALSNSEPC7xpUUH4bqQG_Sj9Um6j9Y22mRgZtOX7888Zx5zWzjgCMo2P8cHaJuwnF5UNmAtCeXEDCUGv-OhPOlu1DUDPE4hkChK5bfGhQmkBaHMfQ?purpose=inline)

[redhat.com](https://www.redhat.com/en/blog/get-most-out-red-hat-enterprise-linux-aws)

1. Sign in to the AWS Management Console at [Open EC2 Console](https://console.aws.amazon.com/ec2/).
2. Select EC2 → Instances → Launch instances.
3. Enter an instance name, for example, `rhel-nginx-server`.
4. Under Application and OS Images (AMI), select Red Hat Enterprise Linux (RHEL). Choose an appropriate supported RHEL AMI for your lab.
5. Select an instance type, such as `t3.micro`, if it is available and suitable for your account and selected image.
6. Create or select a key pair. Download the private key file (`.pem`) and keep it secure.
7. Configure the network and security group.

### Configure the security group

Allow these inbound rules:

| Type | Port | Source      | Purpose                            |
| ---- | ---- | ----------- | ---------------------------------- |
| SSH  | 22   | My IP       | Connect to the server              |
| HTTP | 80   | `0.0.0.0/0` | Allow visitors to open the website |

Security note: Restrict SSH to your own IP address. HTTP can be public because the website is intended for visitors. Avoid opening SSH to everyone.

8. Review the configuration and click Launch instance.
9. Wait until the instance is running and its status checks pass.

## Step 2: Copy the public IP address

1. Open EC2 → Instances.
2. Select your RHEL instance.
3. Copy its Public IPv4 address, for example `54.123.45.67`.

This address is how you will connect to the server and later open the website.

The example IP is illustrative. Use the actual IP displayed in your AWS console.

## Step 3: Connect to the RHEL server

There are two beginner-friendly methods.

### Method A: Connect using EC2 Instance Connect

If your selected RHEL AMI and instance configuration support EC2 Instance Connect:

1. Select your instance in the EC2 console.
2. Click Connect.
3. Open the EC2 Instance Connect tab.
4. Use the username `ec2-user` if that is the configured default for your image.
5. Click Connect.

A browser-based terminal will open.

### Method B: Connect using SSH from Windows

Open PowerShell on your Windows computer.

First, go to the folder containing your downloaded `.pem` key. For example:

```
cd $HOME\Downloads
```

Then connect using:

```
ssh -i "my-key.pem" ec2-user@YOUR-PUBLIC-IP
```

Replace `my-key.pem` with your actual key filename and `YOUR-PUBLIC-IP` with your EC2 public IP.

Example:

```
ssh -i "rhel-lab.pem" ec2-user@54.123.45.67
```

If you see a message asking whether you trust the host, type `yes` after verifying that the host is your EC2 instance.

Expected result: Your terminal connects to the Linux server and displays a command prompt.

For example:

```
[ec2-user@ip-172-31-10-25 ~]$
```

This means you are logged in as `ec2-user`.

If SSH reports a permission error for your private key, check that you are using the correct key pair. On Linux or macOS, you can also run:

```
chmod 400 my-key.pem
```

If Windows SSH still rejects the key, check the Windows file permissions for the `.pem` file.

## Step 4: Verify the operating system

Before installing anything, confirm that you are connected to RHEL.

Run:

```
cat /etc/os-release
```

This displays operating system details.

Check the current user:

```
whoami
```

Expected output:

```
ec2-user
```

Check the server's hostname:

```
hostname
```

Check the Linux kernel:

```
uname -r
```

Important: Linux commands are case-sensitive. For example, `hostname` and `Hostname` are not the same command.

## Step 5: Update system packages

RHEL uses the `dnf` package manager to install and update software.

Run:

```
sudo dnf check-update
```

This checks whether package updates are available. An exit code of `100` can mean updates are available; it is not necessarily a failure.

Now update installed packages:

```
sudo dnf upgrade -y
```

Explanation:

- `sudo` — runs the command with administrator privileges.
- `dnf` — the package manager.
- `upgrade` — updates installed packages to available newer versions.
- `-y` — automatically answers yes to confirmation prompts.

Wait for the update to finish. Depending on the instance and available updates, this may take a few minutes.

Check the package manager version:

```
dnf --version
```

## Step 6: Install Nginx

Nginx is a web server that receives HTTP requests and serves website files to browsers.

Run:

```
sudo dnf install nginx -y
```

Wait until the installation completes.

Verify that Nginx is installed:

```
nginx -v
```

Example output:

```
nginx version: nginx/1.x.x
```

The exact version depends on the RHEL image and configured repositories.

You can also inspect the installed package:

```
rpm -q nginx
```

`rpm -q` queries the RPM package database to check whether a package is installed.

## Step 7: Start Nginx and check its status

Installing Nginx does not necessarily mean it is running. Start the service:

```
sudo systemctl start nginx
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

### Understand the service commands

| Command                        | Purpose                                                      |
| ------------------------------ | ------------------------------------------------------------ |
| `sudo systemctl start nginx`   | Starts Nginx now                                             |
| `sudo systemctl stop nginx`    | Stops Nginx                                                  |
| `sudo systemctl restart nginx` | Restarts Nginx                                               |
| `sudo systemctl reload nginx`  | Reloads configuration without a full restart, when supported |
| `sudo systemctl status nginx`  | Shows current service status                                 |
| `sudo systemctl enable nginx`  | Starts Nginx automatically at boot                           |
| `sudo systemctl disable nginx` | Disables automatic startup at boot                           |

Now enable Nginx at boot:

```
sudo systemctl enable nginx
```

Verify the enabled state:

```
sudo systemctl is-enabled nginx
```

Expected output:

```
enabled
```

Confirm that it is running:

```
sudo systemctl is-active nginx
```

Expected output:

```
active
```

Remember the difference: `start` runs the service now, while `enable` configures it to start automatically when the server boots. You usually need both.

## Step 8: Open your website in a browser

On your computer, open:

```
http://YOUR-PUBLIC-IP
```

For example:

```
http://54.123.45.67
```

If Nginx is running, the default Nginx welcome page should appear.

If the page does not load, check these items:

- The EC2 instance is running and has a reachable public IPv4 address.
- The security group allows inbound HTTP traffic on port `80`.
- Nginx is active.
- The subnet and network configuration allow internet traffic.

Do not use `https://` yet. HTTPS requires additional configuration, such as a domain name and TLS certificate.

## Step 9: Locate the Nginx website directory

The default website directory on a typical RHEL Nginx package is:

```
/usr/share/nginx/html/
```

This is the directory from which the default website files are served.

List its contents:

```
ls -l /usr/share/nginx/html/
```

You may see a file such as:

```
index.html
```

View the file:

```
cat /usr/share/nginx/html/index.html
```

The `index.html` file is the default page that Nginx serves when a visitor opens the website root.

### Useful Linux commands for website files

| Command                     | Explanation                                        |
| --------------------------- | -------------------------------------------------- |
| `pwd`                       | Shows the current directory                        |
| `ls`                        | Lists files and directories                        |
| `ls -l`                     | Lists files with permissions, owner, size and date |
| `cd /usr/share/nginx/html/` | Moves into the website directory                   |
| `cat index.html`            | Displays the file contents                         |
| `sudo nano index.html`      | Opens the file in the Nano editor, if installed    |
| `sudo cp file destination`  | Copies a file to another location                  |

## Step 10: Create your own static HTML website

Now replace the default Nginx page with a simple custom website.

We will create a beginner-friendly student learning website using HTML and CSS.

Run the following command to create or replace the website's `index.html` file:

```
sudo tee /usr/share/nginx/html/index.html > /dev/null <<'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RHEL Web Server Lab</title>

    <style>
        * {
            box-sizing: border-box;
        }

        body {
            margin: 0;
            font-family: Arial, sans-serif;
            background: #f1f5f9;
            color: #1e293b;
        }

        header {
            background: #0f172a;
            color: white;
            padding: 22px;
            text-align: center;
        }

        main {
            max-width: 900px;
            margin: 40px auto;
            padding: 20px;
        }

        .card {
            background: white;
            padding: 30px;
            border-radius: 14px;
            box-shadow: 0 5px 20px #00000012;
        }

        h1 {
            margin-top: 0;
            color: #0f766e;
        }

        .status {
            background: #dcfce7;
            color: #166534;
            padding: 12px;
            border-radius: 8px;
            font-weight: bold;
        }

        footer {
            text-align: center;
            padding: 20px;
            color: #64748b;
        }
    </style>
</head>

<body>
    <header>
        <h2>My First AWS Web Server</h2>
        <p>RHEL + Nginx Practical Lab</p>
    </header>

    <main>
        <section class="card">
            <h1>Congratulations, Students!</h1>

            <p>
                You have successfully installed and started
                the Nginx web server on an AWS EC2 instance
                running Red Hat Enterprise Linux.
            </p>

            <p class="status">
                Website Status: Successfully Deployed
            </p>

            <h3>What We Practised</h3>

            <ul>
                <li>Launching an EC2 instance</li>
                <li>Connecting through SSH</li>
                <li>Updating RHEL packages with DNF</li>
                <li>Installing and managing Nginx</li>
                <li>Hosting a custom static website</li>
            </ul>
        </section>
    </main>

    <footer>
        Created for AWS and Linux beginners.
    </footer>
</body>
</html>
EOF
```

### Understand what the command does

- `sudo` — provides administrator privileges to write into the system website directory.
- `tee` — writes the supplied text into a file.
- `/usr/share/nginx/html/index.html` — the destination file.
- `> /dev/null` — hides the copy of the written content from the terminal.
- `<<'EOF' ... EOF` — supplies the multiline HTML content to the command.

This command overwrites the existing `index.html`. That is intentional for this lab.

### Verify your new website

Run:

```
ls -l /usr/share/nginx/html/
```

Then check the beginning of the HTML:

```
head -n 15 /usr/share/nginx/html/index.html
```

Check Nginx:

```
sudo systemctl status nginx
```

Open the EC2 public IP address again in your browser:

```
http://YOUR-PUBLIC-IP
```

Refresh the page. Your custom website should now appear.

Important: You generally do not need to restart Nginx just because you changed a static HTML file. Nginx serves the updated file on the next request.

## Step 11: Alternative method — Deploy a website from GitHub

In the first method, we created the website directly on the server. Now let's learn how to download an existing website from GitHub and host it using Nginx.

The workflow is:

GitHub Repository

Contains HTML, CSS, JavaScript and images

Clone the Repository

Download files using Git

Copy Website Files

Into /usr/share/nginx/html/

Live Website

Accessible through the EC2 public IP

### 11.1 Install Git

Connect to your RHEL server and run:

```
sudo dnf install git -y
```

Verify the installation:

```
git --version
```

Example output:

```
git version 2.x.x
```

The exact version may differ.

### 11.2 Clone a GitHub repository

For this practical, you need a repository containing a ready-to-deploy static website.

You can use your own public repository or create one on GitHub.

Open your repository, click Code, select HTTPS, and copy its repository URL.

For example, if your repository URL is:

```
https://github.com/YOUR-USERNAME/my-website.git
```

Run:

```
cd ~
git clone https://github.com/YOUR-USERNAME/my-website.git
```

Replace the example URL with the real URL of your repository.

What does `git clone` do?

- Downloads the repository to the server.
- Creates a local directory for the repository.
- Downloads the files and Git history.
- Lets you inspect the website before deploying it.

### 11.3 Inspect the downloaded files

Move into your repository:

```
cd ~/my-website
```

Check the current directory:

```
pwd
```

List the files:

```
ls -la
```

For a simple static website, you might see:

```
index.html
style.css
script.js
images/
```

Your repository may contain a different structure.

Check whether the homepage exists:

```
ls -l index.html
```

If this file is missing, inspect the repository to find the correct directory containing the website's `index.html`.

Important: This procedure is for a ready-to-deploy static website. If the repository contains a React, Angular, or other framework project, you may need to build it first and deploy its generated output directory instead.

### 11.4 Back up the current Nginx website

Before replacing the files, create a backup:

```
sudo cp -a /usr/share/nginx/html \
"/usr/share/nginx/html-backup-$(date +%F-%H%M)"
```

This preserves the current website directory under a timestamped backup name.

### 11.5 Copy the GitHub website into the Nginx directory

First, make sure you are in the directory that contains the correct `index.html` file:

```
cd ~/my-website
ls -l index.html
```

Then remove the existing files from the Nginx document root:

```
sudo find /usr/share/nginx/html \
-mindepth 1 -maxdepth 1 -exec rm -rf -- {} +
```

Caution: This command permanently deletes the files inside the specified website directory. Use it only after verifying the path and creating the backup.

Now copy the repository's contents into the Nginx directory:

```
sudo cp -r ~/my-website/. /usr/share/nginx/html/
```

The `/.` at the end means to copy the contents of the source directory, including files and subdirectories, rather than creating an extra `my-website` directory inside the document root.

If your repository has its `index.html` inside a subdirectory, adjust the source path accordingly.

### 11.6 Verify the deployment

List the deployed files:

```
ls -la /usr/share/nginx/html/
```

Check the homepage:

```
head -n 15 /usr/share/nginx/html/index.html
```

Confirm that Nginx is running:

```
sudo systemctl is-active nginx
```

Expected output:

```
active
```

Open the website:

```
http://YOUR-PUBLIC-IP
```

Refresh your browser. You should see the website downloaded from GitHub.

### 11.7 What happens when the GitHub website changes?

Cloning a repository downloads its files at that time. If a developer subsequently pushes new changes to GitHub, your live website does not automatically update.

To deploy later updates, you can pull the latest changes into the repository, then copy the updated static website files into the Nginx document root again. For production environments, use a controlled deployment process and keep a rollback option.

## Step 12: Troubleshooting common problems

Use this table when your website does not work as expected.

| Problem                       | What to check                                                       |
| ----------------------------- | ------------------------------------------------------------------- |
| Website does not open         | Check the EC2 public IP and security group inbound rule for port 80 |
| `systemctl` shows inactive    | Run `sudo systemctl start nginx`                                    |
| Nginx installation fails      | Check repository availability and `dnf` error messages              |
| Default page still appears    | Check that the correct `index.html` was replaced                    |
| Website returns 403 Forbidden | Check file permissions and Nginx error logs                         |
| Website returns 404 Not Found | Verify the file exists at the configured document root              |
| CSS or images are missing     | Check relative paths and ensure assets were copied                  |
| SSH connection fails          | Check the `.pem` key, username, public IP, and port 22 access       |

### Useful debugging commands

Check the service:

```
sudo systemctl status nginx
```

Check whether Nginx responds locally:

```
curl -I http://localhost
```

A successful response commonly includes:

```
HTTP/1.1 200 OK
Server: nginx
```

View recent Nginx error logs:

```
sudo tail -n 50 /var/log/nginx/error.log
```

View service logs:

```
sudo journalctl -u nginx --no-pager -n 50
```

Check the Nginx configuration:

```
sudo nginx -t
```

This checks the configuration syntax. If it reports that the syntax is OK and the test is successful, the configuration is valid.

## Step 13: Understand the important Nginx locations

Website document root

`/usr/share/nginx/html/`

Contains the HTML, CSS, JavaScript and image files served by the default website configuration.

Main configuration

`/etc/nginx/nginx.conf`

Contains the main Nginx configuration.

Additional configuration

`/etc/nginx/conf.d/`

Typically contains additional server configuration files.

Logs

`/var/log/nginx/`

Typically contains access and error logs.

These are the typical paths for the standard RHEL Nginx package. The configured `root` directive determines which directory is actually served, so check the server configuration if the behavior differs.

You can inspect the configured document root with:

```
sudo grep -R "root " /etc/nginx/
```

## Step 14: Student practical assignment

Use the following checklist to verify your understanding.

### Practical completion

0 of 12

Launch a RHEL EC2 instance.

Configure SSH and HTTP security group rules.

Connect to the instance through SSH.

Verify the OS using cat /etc/os-release.

Update packages using dnf.

Install Nginx and verify its version.

Start Nginx and enable it at boot.

Open the default website in a browser.

Create a custom index.html file.

Deploy a static website from GitHub.

Verify the deployed files and check Nginx logs.

Stop and start Nginx to practise service management.

Reset checklist

## Final revision: Commands to remember

| Task               | Command                                |
| ------------------ | -------------------------------------- |
| Update packages    | `sudo dnf upgrade -y`                  |
| Install Nginx      | `sudo dnf install nginx -y`            |
| Start Nginx        | `sudo systemctl start nginx`           |
| Enable at boot     | `sudo systemctl enable nginx`          |
| Check status       | `sudo systemctl status nginx`          |
| Test configuration | `sudo nginx -t`                        |
| List website files | `ls -l /usr/share/nginx/html/`         |
| View homepage      | `cat /usr/share/nginx/html/index.html` |
| Install Git        | `sudo dnf install git -y`              |
| Clone repository   | `git clone YOUR-REPOSITORY-URL`        |

Remember the core concept: Nginx serves the website files from its configured document root. For this practical, you install Nginx on RHEL, put your custom `index.html` and supporting assets into `/usr/share/nginx/html/`, and let visitors access them using the EC2 public IP address.

When the lab is complete, stop or terminate the EC2 instance if you no longer need it. A stopped instance can still incur charges for attached storage, and a public IPv4 address may incur charges depending on the AWS pricing rules that apply to your account.
