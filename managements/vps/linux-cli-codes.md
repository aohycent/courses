# Linux Command Line Interface Operation Codes

The list of command line operations needed for server management

## connecting to a server via SSH.
- command `ssh root@IP -pPORT`
- example
```bash
ssh root@1.2.3.4 -p22
```
- IP: is the IP of the server you are connecting to
- PORT: connection port - 22 for a VPS/Dedicated server by default

## updating the server
- command `dnf -y update`
- example
```bash
dnf -y update
```
- dnf: `Dandified Yum` a package manager for Red Hat-based Linux distributions and derivatives. DNF was introduced in Fedora 18 in 2013 as a replacement for yum.
- -y:
  
## installing package
- command `dnf -y install ${PKG} ${dependencies}`
- example
```bash
dnf -y install bind bind-utils
```

## remove package
- command: `dnf remove --nodeps ${PKG}`
- example
```bash
dnf remove --nodeps bind
```

## Update software database
- command: `dnf check-update`

## Delete orphans and config 
- command: ` 	dnf erase ${PKG}`
- example
```bash
 	dnf erase ${PKG}
```

## Show orphans
-command: `package-cleanup -q --leaves --exclude-bin`
(-q is shorthand for --quiet.) 

## Remove package (and orphans)
- command: `dnf remove ${PKG}`
