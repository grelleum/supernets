# supernets

Given a list of IP networks, supernets will produce the smallest list
of contiguous supernets that aggregate all of those networks.


Usage:
Supply the names of one of more files used for input.
If no files are supplied, supernets will process standard input, 
allowing you to pipe input from another programs output.
Each network must be on its own line and in CIDR format.
supernets works with both IPv4 and IPv6 addresses.

-h, --help      displays help.
-v, --verbose   provides a display of how each decision is made.


This is implemented in Python and is intended to be run from the command line.
It is writtent to be compatible with both Python 2.6+ and Python 3+.
This is accomplished by using future imports.
Additionally, the ipaddress module expects unicode input.
This presented a challenge: 
Python 3 defaults to unicode, so the code needed no modification to work.
Python 2 defaults to a non-unicode string type, requiring a .decode() method.
This method breaks Python 3 since it is already decoded.
The solution is to encode to bytes and then decode to unicode.
    line = line.strip().encode().decode()  # Python 2/3 dual support.


Another intersting trick was to get the final output sorted.
The problem is that IPv4 and IPv6 networks are of different types
that cannot be directly compared, so I needed to create a lambda
function to get the network address and represent it in byte format,
allowing the two IP address formats to be directly comparable.
    for network in sorted(networks, key=lambda ip: ip.network_address.packed):


Program Logic - This is how we do:

A global networks dictionary is created and all networks are added to it.
Using a dictionary prevents duplicate networks.
Networks are stored as type ipaddress.ip_network, 
provided by the ipaddress module.

For each network, we recursively decrement the prefix length,
checking for existing networks of an exact match.  Finding one
indicates that the current network is a subnet of an existing supernet
and therefore we discard the subnetwork.

All non-duplicate networks are added to a prefix dictionary.
Each prefix entry in the dictionary is a list of networks 
of the same prefix length.  The list of networks of the same
prefix length is sorted and each network can then be compared
to the next network, to test is they are both contiguous and 
can be aggregated together.  This test is done by decrementing 
the prefix length of each network and comparing the new values.
If the two resulting networks are the same network, 
then we discard the original two networks and store 
the new aggregate in the two dictionaries.

