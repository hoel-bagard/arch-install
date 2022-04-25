If someone fails to enter their passwords too many times and gets locked out:
```
faillock --user <username> --reset
```

To see who is taking all the space on a server:
```
sudo du -shcx /home/* | sort -hr
```

### [ACL](https://wiki.archlinux.org/title/Access_Control_Lists)
The following command gives userA read access to userB's home directory:
(Use `-R` for recursive)
```
sudo setfacl -m u:userA:r /home/userB
```
And to remove userA's access:
```
sudo setfacl -x u:userA /home/userB
```
