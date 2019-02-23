# duplicity-s3-backup-scripts
A collection of scripts used to automate backups on Linux using duplicity and Amazon S3

## About
This repository contains a collection of scripts I've developed/collected to backup important files on my Manjaro Linux desktop to Amazon S3 storage using the duplicity backup tool. Inspiration for these scripts came from a guide provided by Digital Ocean located at https://www.digitalocean.com/community/tutorials/how-to-use-duplicity-with-gpg-to-securely-automate-backups-on-ubuntu. 

## Setup
### Generate GPG Keys
You need to generate a gpg key pair to encrypt and sign your backups. Generate a RSA key that never expires and give it a good name like "Duplicity Backup".

`
gpg --full-gen-key
`

### Import GPG Secret Keys for Root
We need to export the secret key from your user so we can use it in the cron jobs.

`
gpg --export-secret-keys [key id] > dup_secret_key.asc
sudo -i 
gpg ---pinentry-mode loopback --import /path/to/dup_secret_key.asc  
gpg --edit-key [key id] trust quit
pick option 5
`

### Create an AWS S3 Bucket
Create a new AWS identity and give it S3 permissions. Generate the necessary ACCESS KEY ID and SECRET ACCESS KEY. Record these, because you will need them for the scripts.

### Create .passphrase File 
Create a .passphrase file in the root of your home directory and root's home folder (normally /root). Substitute your gpg passphrase and the aws access key id and secret key.

`
PASSPHRASE=[gpg passphrase]
AWS_ACCESS_KEY_ID=[AWS access key id]
AWS_SECRET_ACCESS_KEY=[AWS access key]
`

Change the access on the file so only the owner can read/write/execute it.

`
chmod 700 ~/.passphrase
chmod 700 /root/.passphrase
`

### Copy cron jobs
Copy the duplicity-inc file to /etc/cron.daily. Edit the file to include your encryption and signature key ids and your bucket name. Also update include and excludes as appropriate.

Copy the duplicity-full file to the /etc/cron.monthly. Edit the file like you did for the duplicity-inc file.

Make both file executable by root.

`
chmod 750 /etc/cron.daily/duplicity-inc
chmod 750 /etc/cron.monthly/duplicity-full
`

### Copy other script files.
Copy the other script files to a place of your chosing. Edit them as necessary and make them executable. You can place them in your PATH if you wish.
