# Per authpf binary configuration
This code changes the way `authpf` looks for the configuration file 
`/etc/authpf/authpf.conf`. The change forces authpf to read a different 
configuration file based on the name of the executable. This is change is 
implemented in a way that does not alter the original way of operation.

This allows us to separate certain configuration parameters that we otherwise 
couldn't.

```
anchor=defense/authpf
table=defense_authed
```

Compiled to test/run with:

```
cc -O2 -pipe -Wall -c authpf.c 
cc -o authpf-offense authpf.o
cc -o authpf-defense authpf.o
```

This will create two binaries `authpf-offense` and `authpf-defense` that you'd have to copy to your /usr/sbin folder. 
```
install -m 6555 -o root -g authpf authpf-offense /usr/sbin 
install -m 6555 -o root -g authpf authpf-defense /usr/sbin
```

Activate and Change the user's shell 
```
echo /usr/sbin/authpf-offense >>/etc/shells
usermod -s /usr/sbin/authpf-offense offense
```

In order to activate the feature follow the normal authpf instructions and create the required .conf files under /etc/authpf/
```
echo "anchor=authpf_offense\ntable=offense_authenticated\n">/etc/authpf/authpf-offense.conf
```

# Developer details

Code cloned from OpenBSD -current with the following command:

```
cvs -qd anoncvs@anoncvs.ca.openbsd.org:/cvs get -P src/usr.sbin/authpf
```

The diff is produced with the following way:

```
cd src
cvs diff -u usr.sbin/authpf/authpf.c > ../authpf.patch
```