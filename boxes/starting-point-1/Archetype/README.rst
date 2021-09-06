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

//$TM/backups share
===================
:Note: list smbclient cmds: help <cmd>

.. code-block:: Bash

	# Access backups share
	smbclient -N //$TM/backups
	# List files
	smb: \>ls
		> prod.dtsConfig
	# Read file
	more prod.dtsConfig
	# Get file to local drive
	get prod.dtsConfig

DTSCONFIG file
--------------
:NOTE: DTSCONFIG files often contain sensitive information, therefore, access to the locations of the files should be restricted.
:Prgms that open DTSCONFIG: MS SQL Server 2019
:In prod.DTSCONFIG: Provider=SQLNCLI10.1

DTSCONFIG file
	an XML config file used to apply property values to SQL Server Integration
	Services In (SSIS) packages.  The file contains >1 pkg configs that consist of
	metadata such as the server name, database names, and other connection
	properties to configure SSIS packages.

The DTSCONFIG file is used to update the values of pkg properties and pkg
objects at run time.  The file is designed to provide flexibility when working
with SSIS pkgs and are helpful when deploying pkgs to different environments as
they allow values, such as server and database names, to be easily changed.

The DTSCONFIG file is made up of various elements.  The
`DTSConfigurationHeading` element stores attributes and their values that
define when and how the file is generated.  The DTSCONFIG file also includes a
`Configuration` element for each pkg configuration, which contains attributes
and their values that are necessary to determine which property is being
referenced.  Each `Configuration` element has a `ConfiguredValue` element that
features the property's actual value.

SQL Server
	a relational database mgmt system (RDBMS) application.  SSIS pkgs are used
	to automate extraction, transformation, and loading (ETL) operations to and
	from a database.

Provider=SQLNCLI10
	SQL Native CLIent is used to access MS SQL DB --deprecated.  Use MSOLEDBSQL
	(MS OLE Driver for SQL Server)

Accessing MS SQL from Linux
===========================
:Installed Prgms: python3-pymssql
:Installation: apt install python3-pymssql

python3-pymssql
	Python database access for MS SQL server and Sybase

Running Impacket's mssqlclient
------------------------------
:SYNTAX: impacket-mssqlclient <user>[:<passwd>]@<domain> <optionalArgs>
:Note: optional arg `-windows-auth` is required for this exercise

-windows-auth
	whether or not to use Windows Authentication (default False)

.. code-block:: Bash

	# Login with creds extracted from prod.DTSCONFIG
	impacket-mssqlclient ARCHETYPE/sql_svc:M3g4c0rp123@$TM -windows-auth

	# list available commands
	SQL> help

MS SQL
******
:ref1: https://straightpathsql.com/archives/2009/10/how-to-use-sp_configure-in-sql-server/
:ref2: https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql?view=sql-server-ver15
:ref3: https://www.mssqltips.com/sqlservertip/1020/enabling-xpcmdshell-in-sql-server/

