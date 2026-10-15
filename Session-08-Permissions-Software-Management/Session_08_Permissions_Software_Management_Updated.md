# Session 08 --- Permissions & Software Management

📚 **Program:** 55-Session Multi Cloud + DevOps with AI Program\
📦 **Module:** Linux Administration for DevOps + GCP\
🎯 **Topic:** Permissions & Software Management\
👨‍🏫 **Mentor:** Vikas Ratnawat --- CloudDevOpsHub

------------------------------------------------------------------------

## Table of Contents

-   [1. What Is SSH?](#1-what-is-ssh)
-   [2. What Is an SSH Key?](#2-what-is-an-ssh-key)
-   [3. What Is an SSH Key Pair?](#3-what-is-an-ssh-key-pair)
-   [4. Why Do We Use SSH Key Pairs?](#4-why-do-we-use-ssh-key-pairs)
-   [5. When Do We Use SSH Keys?](#5-when-do-we-use-ssh-keys)
-   [6. What Is ssh-keygen?](#6-what-is-ssh-keygen)
-   [7. How Passwordless SSH Works](#7-how-passwordless-ssh-works)
-   [8. Cloud Shell vs SSH to a VM](#8-cloud-shell-vs-ssh-to-a-vm)
-   [9. Generate an SSH Key Pair](#9-generate-an-ssh-key-pair)
-   [10. Configure and Test SSH
    Access](#10-configure-and-test-ssh-access)
-   [11. Linux File Permissions](#11-linux-file-permissions)
-   [12. Understanding chmod 600](#12-understanding-chmod-600)
-   [13. Understanding chmod 755 and
    644](#13-understanding-chmod-755-and-644)
-   [14. Understanding chown](#14-understanding-chown)
-   [15. Software Management with APT](#15-software-management-with-apt)
-   [16. Common HTTP Status Codes](#16-common-http-status-codes)
-   [17. Key Takeaways](#17-key-takeaways)

------------------------------------------------------------------------

## 1. What Is SSH?

**SSH (Secure Shell)** is a network protocol used to securely connect to
and manage a remote computer or server over a network.

DevOps engineers use SSH to: - Connect to remote Linux servers. -
Troubleshoot application and server issues. - Check logs and system
resources. - Perform administration tasks. - Support deployments and
automation.

Example:

``` bash
ssh username@SERVER_IP
```

Replace `username` and `SERVER_IP` with the correct remote Linux
username and server address.

## 2. What Is an SSH Key?

An SSH key is a cryptographic credential used to authenticate a user or
system when connecting to a remote server.

When public-key authentication is configured correctly, SSH can
authenticate the client without requiring the remote account password
for every login.

## 3. What Is an SSH Key Pair?

An SSH key pair consists of two mathematically related keys:

  -----------------------------------------------------------------------
  Key                                 Purpose
  ----------------------------------- -----------------------------------
  Private key                         Kept secret on the client machine
                                      or in an approved secure key store.
                                      Never share it.

  Public key                          Can be provided to an administrator
                                      or installed/configured for the
                                      relevant user on the remote server
                                      through an approved access method.
  -----------------------------------------------------------------------

The server verifies proof that the client possesses the matching private
key. The private key itself is not sent to the server.

**Important:** A public key authenticates access; it does not make your
application or server publicly accessible to everyone.

## 4. Why Do We Use SSH Key Pairs?

### Security

Key-based authentication avoids relying on a remote account password for
every SSH login. Private keys must be protected carefully, and a
passphrase can add protection.

### Convenience

Once configured, a user can connect without repeatedly entering the
remote account password. If the private key has a passphrase, an SSH
agent can help avoid entering that passphrase for every connection.

### Automation

Approved automation systems and scripts can use SSH keys or other
non-interactive authentication methods to perform administration and
deployment tasks.

## 5. When Do We Use SSH Keys?

SSH keys are commonly used when: - An engineer needs to connect to a
Linux VM. - An automation tool needs SSH access to a server. - A user
needs SSH authentication to a Git repository. - A cloud access setup
uses SSH key-based authentication.

The exact method depends on the organization's security policy and cloud
configuration. SSH keys are common, but they are **not required for
every cloud VM or every access method**.

## 6. What Is `ssh-keygen`?

`ssh-keygen` is a command used to generate and manage SSH key pairs.

Example:

``` bash
ssh-keygen -t ed25519
```

-   `ssh-keygen`: starts SSH key generation.
-   `-t`: specifies the key type.
-   `ed25519`: selects the Ed25519 cryptographic algorithm.

With the default filename, the generated files are usually:

``` text
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

The file without `.pub` is the private key; the `.pub` file is the
public key. Keep the private key secret.

## 7. How Passwordless SSH Works

Passwordless SSH commonly refers to using SSH public-key authentication
without entering the remote account password for each login.

1.  The client has a private key.
2.  The matching public key is authorized for the remote user through
    the appropriate server or cloud access configuration.
3.  The client proves possession of the private key during SSH
    authentication.
4.  The server verifies the proof.
5.  If authentication and access checks succeed, the SSH session opens.

A private-key passphrase may still be required unless an agent or
another approved mechanism handles it. "Passwordless" does not mean the
connection has no authentication.

## 8. Cloud Shell vs SSH to a VM

**Cloud Shell** is a browser-based terminal provided by Google Cloud. It
runs in a Google-managed environment.

When Cloud Shell asks for authorization, it is asking permission to use
Google Cloud credentials for Google Cloud API calls. This is not, by
itself, proof that SSH access to a particular VM has been authorized.

To connect to a VM, establish a separate connection, for example:

``` bash
gcloud compute ssh VM_NAME --zone=ZONE
```

Replace `VM_NAME` and `ZONE` with the actual VM name and zone.

After the SSH connection succeeds, commands run on the remote VM. Before
connecting, commands run in the Cloud Shell environment.

**Remember:** - Cloud Shell = browser-based terminal. - SSH = protocol
used to connect securely to a remote system. - Cloud Shell authorization
= permission to use Google Cloud credentials for API calls. - SSH
authentication = authentication for the remote SSH connection.

## 9. Generate an SSH Key Pair

Example command:

``` bash
ssh-keygen -t ed25519
```

Accept the default path only if it is appropriate for your environment.
You may set a passphrase to protect the private key.

Before generating a new key, check whether an appropriate key pair
already exists. Do not overwrite an existing key unless you understand
the impact.

## 10. Configure and Test SSH Access

The public key must be authorized for the correct remote user through
the applicable method. Depending on the environment, this could involve
`authorized_keys`, cloud VM metadata, OS Login, or another approved
access mechanism.

Example connection:

``` bash
ssh username@SERVER_IP
```

To troubleshoot an authentication failure, check: - Is the username
correct? - Is the server address correct and reachable? - Is the correct
private key being offered? - Is the matching public key authorized for
that remote user? - Are file permissions and ownership correct? - Do
firewall rules and cloud IAM/access policies permit the connection? - Is
the VM configured to allow the authentication method being used?

Never share or upload your private key to troubleshoot an SSH issue.

## 11. Linux File Permissions

Linux permissions control what the owner, group, and other users can do
with a file or directory.

  -----------------------------------------------------------------------
  Symbol                  Meaning                 For a file
  ----------------------- ----------------------- -----------------------
  `r`                     Read                    Read file contents

  `w`                     Write                   Modify file contents

  `x`                     Execute                 Run the file as a
                                                  program/script, subject
                                                  to other requirements
  -----------------------------------------------------------------------

Permissions are represented for: - **Owner** --- the file's owner. -
**Group** --- users in the file's group. - **Others** --- all other
users.

## 12. Understanding `chmod 600`

Example:

``` bash
chmod 600 ~/.ssh/id_ed25519
```

Permission `600` means: - Owner: read and write (`rw-`) - Group: no
permissions (`---`) - Others: no permissions (`---`)

This restricts access to the private key file to its owner, assuming
ownership and parent-directory access are correctly configured.

**Is `chmod 600` mandatory for every SSH key?** Not universally. SSH
clients commonly reject private keys whose permissions are too open.
`600` is a common safe setting for a private key, but the exact
requirement depends on the client and environment. The private key
should not be accessible to other users.

## 13. Understanding `chmod 755` and `chmod 644`

### `chmod 755`

``` bash
chmod 755 script.sh
```

Permission breakdown: - Owner: `rwx` --- read, write, execute. - Group:
`r-x` --- read and execute. - Others: `r-x` --- read and execute.

This is commonly used for executable scripts or directories when those
access levels are appropriate. Use it only when group and other users
should have read/execute access.

### `chmod 644`

``` bash
chmod 644 index.html
```

Permission breakdown: - Owner: `rw-` --- read and write. - Group: `r--`
--- read only. - Others: `r--` --- read only.

This is commonly used for files that users or a web server need to read
but do not need to modify.

Choose permissions according to the file's purpose and security
requirements; do not apply these modes blindly to every file.

## 14. Understanding `chown`

`chown` changes the owner and/or group of a file or directory.

Example:

``` bash
sudo chown www-data:www-data index.html
```

This sets the owner and group to `www-data` on systems where that
account exists. The correct owner/group depends on the operating system
and how the web server is configured.

To inspect ownership and permissions:

``` bash
ls -l
```

## 15. Software Management with APT

On Ubuntu and other Debian-based systems, APT manages software packages.

### Update package lists

``` bash
sudo apt update
```

This downloads updated package metadata. It does not, by itself, upgrade
all installed software.

### Install software

``` bash
sudo apt install nginx
```

This installs the Nginx package and its required dependencies, subject
to the package sources and system configuration.

## 16. Common HTTP Status Codes

  -----------------------------------------------------------------------
  Status code                         Meaning
  ----------------------------------- -----------------------------------
  `403 Forbidden`                     The server understood the request
                                      but refuses to authorize it.

  `404 Not Found`                     The requested resource was not
                                      found.

  `500 Internal Server Error`         A generic server-side error
                                      occurred. It does not always mean
                                      the whole server has crashed.
  -----------------------------------------------------------------------

These status codes indicate different kinds of HTTP responses. To find
the root cause, inspect application and web-server logs and check the
relevant permissions and configuration.

## 17. Key Takeaways

1.  SSH provides secure remote access to systems.
2.  SSH key pairs contain a private key and a public key; the private
    key must remain secret.
3.  `ssh-keygen` generates SSH keys.
4.  Passwordless SSH generally means connecting with an approved
    authentication method without entering the remote account password
    for every login.
5.  Cloud Shell is a browser-based terminal, not the VM itself.
6.  `chmod 600` restricts a private key file to its owner; SSH clients
    may reject overly permissive private-key files.
7.  `chmod 755` grants the owner read/write/execute and group/others
    read/execute.
8.  `chmod 644` grants the owner read/write and group/others read-only
    access.
9.  `chown` changes file ownership.
10. `apt update` refreshes package metadata; `apt install` installs a
    package.
11. HTTP `403`, `404`, and `500` indicate different response conditions
    and require investigation to identify the root cause.

------------------------------------------------------------------------
