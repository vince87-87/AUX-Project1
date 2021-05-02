#!/bin/bash

#Create variable
devgroup="developers"
file="names.csv"
pw="myShe!!P@ssw0rd"


if grep -q ${devgroup} /etc/group # check if developers exists
then
  echo "developers group exists"
else
  groupadd $devgroup
  echo "developers group successfully created!"
fi

# Check if users exists
#getent passwd | grep -f ${file}
  if grep -qf ${file} /etc/passwd # if command return exit 0, return users exists else create users
  then
    echo "Users Exists"
  else
    for user in `more ${file}`
    do
      useradd -m $user
      usermod -a -G ${devgroup} $user
      echo ${pw} | passwd --stdin $user
      passwd -e $user
      if [ -d "/home/.ssh" ] # check if .ssh directory exist
      then
        echo ".ssh directory exists"
      else
        mkdir /home/$user/.ssh
        chmod 755 /home/$user/.ssh
        cp /home/ec2-user/.ssh/authorized_keys /home/$user/.ssh/authorized_keys
        chmod 755 /home/$user/.ssh/authorized_keys
        echo $user
      fi
    done
  fi
