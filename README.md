# supernets

Given a list of IP networks, supernets will produce the smallest list<br>
of contiguous supernets that aggregate all of those networks.

##### Usage: #####
Supply the names of one of more files used for input.<br>
If no files are supplied, supernets will process standard input, <br>
allowing you to pipe input from another programs output.<br>
Each network must be on its own line and in CIDR format.<br>
supernets works with both IPv4 and IPv6 addresses.<br>

-h, --help      displays help.<br>
-v, --verbose   provides a display of how each decision is made.


This is implemented in Python and is intended to be run from the command line.<br>
It is writtent to be compatible with both Python 2.6+ and Python 3+.<br>
This is accomplished by using future imports.<br>
Additionally, the ipaddress module expects unicode input.<br>
This presented a challenge: <br>
Python 3 defaults to unicode, so the code needed no modification to work.<br>
Python 2 defaults to a non-unicode string type, requiring a .decode() method.<br>
This method breaks Python 3 since it is already decoded.<br>
The solution is to encode to bytes and then decode to unicode.<br>
    line = line.strip().encode().decode()  # Python 2/3 dual support.<br>

Another intersting trick was to get the final output sorted.<br>
The problem is that IPv4 and IPv6 networks are of different types<br>
that cannot be directly compared, so I needed to create a lambda<br>
function to get the network address and represent it in byte format,<br>
allowing the two IP address formats to be directly comparable.<br>
    for network in sorted(networks, key=lambda ip: ip.network_address.packed):<br>


Program Logic - This is how we do:<br>

A global networks dictionary is created and all networks are added to it.<br>
Using a dictionary prevents duplicate networks.<br>
Networks are stored as type ipaddress.ip_network, <br>
provided by the ipaddress module.<br>

For each network, we recursively decrement the prefix length,<br>
checking for existing networks of an exact match.  Finding one<br>
indicates that the current network is a subnet of an existing supernet<br>
and therefore we discard the subnetwork.<br>

All non-duplicate networks are added to a prefix dictionary.<br>
Each prefix entry in the dictionary is a list of networks <br>
of the same prefix length.  The list of networks of the same<br>
prefix length is sorted and each network can then be compared<br>
to the next network, to test is they are both contiguous and <br>
can be aggregated together.  This test is done by decrementing <br>
the prefix length of each network and comparing the new values.<br>
If the two resulting networks are the same network, <br>
then we discard the original two networks and store <br>
the new aggregate in the two dictionaries.<br>
