# -Note-taking-for-Homelab-and-learning
My name is Charlie Fogden I am 21 years old and studying cyber security at The Open University.

I will be updating everything that I have done from learning networking to cybersecurity fundamentals, and then implementing to my real world Home lab. 

To start with I have a raspberry pi which is where the learning will start, I will be doing all the basics on here to get an idea of how to do Linux, understanding security, and other features that will help with my future.


# projects on Home Lab

## ssh key authentication

Date: 02/09/2026
Objective: The first half project I wanted to do on the raspberry pi was enable an ssh key. I had just just what private and public keys were in my module TM112 at The Open University and bringing what I had learnt was really helpful in understanding much more. Also it is a really secure thing to do because if someone wanted to get in they couldn't guess the password as it will only be a private and public key that they will need. 

For this I had to find what IP my raspberry pi is. for this you do `hostname -I`, this gave me 192.168.1.229.
Next I used my windows 11 laptop and typed in the command line: `ssh charlie@192.168.1.229` 

Next on the windows command line I had to secure a authorisation key, this was by typing:`ssh-keygen -t ed25519` 
Now I need to take the public key generated on my Windows laptop and add it to the Raspberry Pi.
I created a file named authorised_keys inside the .ssh directory on the Pi and pasted my single-line public key directly into it:
`nano ~/.ssh/authorized_keys`:
ssh-ed25519 [public key] laptop-to-pi

After that i had to secure with these commands:
* `chmod 700 ~/.ssh` (restricts folder access strictly to my user)
* `chmod 600 ~/.ssh/authorized_keys` (restricts read/write access of the key file strictly to my user)

The final test was then exiting the pi then logging back into it: which then worked!

### Disabling Password Logins
To finish with setting up my ssh, passwords and key authorisation as the first mini project and learning, I edited the SSH daemon configuration on the Pi `sudo nano /etc/ssh/sshd_config` and updated the password rule:
`PasswordAuthentication no`

I then restarted the SSH daemon to load the new config into active memory:
`sudo systemctl restart ssh`

Password access is now fully disabled, enforcing key-only authentication across the system.
