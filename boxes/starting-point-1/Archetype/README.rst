Walkthrough Notes
#################
:Author: David Boyd
:Date: 2021-08-28

Steps
*****

1. Enumeration
2. Foothold
3. Privilige Escalation

1. Enumeration
**************

.. code-block:: Bash

	# Map box
	nmap -T5 -A $TARGET
	# Check if annonymous access has been permitted and list services
	smbclient -N -L $TARGET
	# Access a share
	smbclient -N -L \\\\$TARGET\\backups

Ports 445, 1433 are open, which are associated with SMB (file sharing) and SQL
Server. Check SMB using `smbclient` to check if annonymous access is permitted.

smbclient
=========
:Note: Order matters!  Use `-N` proceeding `smbclient`
:Note: Must use an extra backslash to escape terminal backslash

smbclient <prgm>
	command-line SMB/CIFS clients for Unix;
	ftp-like client to access SMB/CIFS resources on servers.

-N|--no-pass
	If specified, this parameter suppresses the normal
	password prompt from the client to the user. This is
	useful when accessing a service that does not
	require a password.

-L|--list	// list services
	This option allows you to look at what services are
	available on a server. You use it as smbclient -L
	host and a list should appear. The -I option may be
	useful if your NetBIOS names don't match your TCP/IP
	DNS host names or if you are trying to reach a host
	on another network.




