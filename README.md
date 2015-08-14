# Udacity Server Configuration Project
This file outlines the steps needed to configure an Ubuntu server to serve a Flask application with a Python database. These steps are specific to Project 5 of Udacity's Fullstack Web Developer Nanodegree.

## Accessing the Server
IP Address: `52.26.243.255`
SSH Port: `2200`

## Viewing the Web Application
URL: `http://52.26.243.255`

## Steps to Configure Server and Install Software

### Setup User
1. Initially ssh in as root.
2. Create a new user: `adduser <USERNAME>`
3. Add new user to the sudo group: `gpasswd -a <USERNAME> sudo`
4. Add public key to user's account so we can ssh into server as the user
  - in user's home, run: `mkdir .ssh`
  - copy `.ssh/authorized_keys` from root's home to user's home
  - change owner and group of the new authorized_keys file to user
  - change folder permissions of .ssh folder: `chmod 700 .ssh`
  - change file permission on authorized_keys: `chmod 644 authorized_keys`
