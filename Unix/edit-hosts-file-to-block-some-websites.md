# Edit hosts file to block some websites

Say I want to stop checking Twitter so often. One radical way to do it is by editing `/etc/hosts` file to add some entries. For instance mine had this content:

```
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1	localhost
255.255.255.255	broadcasthost
::1             localhost
```

And I've added these lines (using `sudo vi hosts` as I needed write permission):

```
127.0.0.1       www.twitter.com
127.0.0.1       twitter.com 
127.0.0.1       m.twitter.com
127.0.0.1       mobile.twitter.com
```

After this, I've typed `sudo killall -HUP mDNSResponder` to flush my Mac's DNS cache, so it doesn't get confused by the changes I've made to the `hosts` file.
