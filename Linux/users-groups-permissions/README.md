# Linux Users, Groups & Permissions

Today I practised Linux users, groups and permissions on my Raspberry Pi 5 running Debian 13.

The aim was to create different users and groups, give them access to specific folders, and test what they could and couldn't access.

## 1. Check my current user

    whoami
    id
    groups

This showed that I was logged in as `charlie` and showed my groups.

## 2. Create a test user

    sudo adduser analyst

Then:

    id analyst

## 3. Create a security group

    sudo groupadd security
    getent group security

Add `analyst` to the group:

    sudo usermod -aG security analyst

Check:

    id analyst

## 4. Create a test file

    mkdir ~/security-lab
    touch ~/security-lab/report.txt
    ls -l ~/security-lab/report.txt

Change the group ownership:

    sudo chown charlie:security ~/security-lab/report.txt

Change the permissions:

    chmod 660 ~/security-lab/report.txt

## 5. Test the permissions

Log in as `analyst`:

    su - analyst

Try to access the file:

    cat /home/charlie/security-lab/report.txt

I got:

    Permission denied

The reason was that `analyst` could not access `/home/charlie`.

This taught me that permissions on the file aren't the only thing that matter. The user also needs permission to travel through the directories leading to the file.

## 6. Create a shared security folder

    sudo mkdir /home/security-lab
    sudo chown charlie:security /home/security-lab
    sudo chmod 770 /home/security-lab

Move the report:

    sudo mv /home/charlie/security-lab/report.txt /home/security-lab/report.txt

I then tested the file again as `analyst`.

This time `analyst` could read and write to the file because they were a member of the `security` group.

I tested writing to it:

    echo "security test from analyst" > /home/security-lab/report.txt

Then:

    cat /home/security-lab/report.txt

## 7. Create a web team

    sudo groupadd webteam

Create two users:

    sudo adduser developer
    sudo adduser auditor

Add them to the group:

    sudo usermod -aG webteam developer
    sudo usermod -aG webteam auditor

## 8. Create the web lab

    sudo mkdir /home/web-lab
    sudo chown charlie:webteam /home/web-lab
    sudo chmod 770 /home/web-lab

Check it:

    ls -ld /home/web-lab

The directory permissions were:

    drwxrwx---

This means `charlie` and members of `webteam` can read, write and enter the directory, while everyone else has no access.

## 9. Create the webpage

    sudo touch /home/web-lab/index.html
    sudo chown charlie:webteam /home/web-lab/index.html
    sudo chmod 660 /home/web-lab/index.html

Check it:

    ls -l /home/web-lab/index.html

The permissions were:

    -rw-rw----

This means the owner and `webteam` can read and write the file, while everyone else has no access.

## 10. Test as developer

    su - developer
    cd /home/web-lab
    ls

I could see:

    index.html

I then tested writing to the webpage:

    echo "i am testing the website permission" > index.html

Then:

    cat index.html

The result was:

    i am testing the website permission

This proved that `developer` could modify the file because they were a member of `webteam`.

## 11. Test an unauthorised user

I checked the `intern` account:

    id intern

I found that `intern` had accidentally been added to both groups.

I removed `intern` from `security`:

    sudo gpasswd -d intern security

Then removed `intern` from `webteam`:

    sudo gpasswd -d intern webteam

I logged in as `intern`:

    su - intern

Then tried:

    cd /home/security-lab

The result was:

    Permission denied

This confirmed that `intern` no longer had access.

## What I learned

The main thing I learned today is that Linux permissions are based on:

Owner → Group → Others

I also learned:

- How to create users
- How to create groups
- How to add users to groups
- How to remove users from groups
- How to change file ownership with `chown`
- How to change permissions with `chmod`
- The difference between file and directory permissions
- Directory traversal
- How to test permissions using different users
- Least privilege
- Group-based access control

This practical helped me understand how Linux administrators can give specific users access to resources while keeping other users out.

