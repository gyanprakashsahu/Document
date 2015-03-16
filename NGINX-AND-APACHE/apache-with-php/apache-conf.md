```
#The ServerRoot directive specifies the top-level directory containing website content. 
By default, ServerRoot is set to "/etc/httpd" for both secure and non-secure servers.

ServerRoot "/etc/httpd" 

#PidFile names the file where the server records its process ID (PID). By default the 
PID is listed in /var/run/httpd.pid.

PidFile run/httpd.pid

#Timeout defines, in seconds, the amount of time that the server waits for receipts and transmissions during communications.

Timeout 180

#KeepAlive sets whether the server allows more than one request per connection and can be used 
to prevent any one client from consuming too much of the server's resources.

KeepAlive On

# This directive sets the maximum number of requests allowed per persistent connection. The Apache Project 
recommends a high setting, which improves the server's performance

MaxKeepAliveRequests 100

# KeepAliveTimeout sets the number of seconds the server waits after a request has been served before 
it closes the connection. Once the server receives a request, the Timeout directive applies instead.

KeepAliveTimeout 20

# <IfModule> and </IfModule> tags create a conditional container which are only activated if the specified
 module is loaded. Directives within the IfModule container are processed under one of two conditions. 
The directives are processed if the module contained within the starting <IfModule> tag is loaded. Or, 
if an exclamation point [!] appears before the module name, the directives are processed only if the 
module specified in the <IfModule> tag is not loaded.

<IfModule prefork.c>
    StartServers               4      ## I will define these tags further.
    MinSpareServers            2
    MaxSpareServers            2
    ServerLimit              150
    MaxClients               150
    MaxRequestsPerChild     1000
    MaxConnectionsPerChild 20000
</IfModule>

# StartServers sets how many server processes are created upon startup. Since the Web server dynamically 
kills and creates server processes based on traffic load, it is not necessary to change this parameter. 
The Web server is set to start 8 server processes at startup for the prefork MPM and 2 for the worker MPM.

# MaxRequestsPerChild sets the total number of requests each child server process serves before the child
 dies. The main reason for setting MaxRequestsPerChild is to avoid long-lived process induced memory leaks. 
The default MaxRequestsPerChild for the prefork MPM is 1000 and for the worker MPM is 0.

# MaxClients sets a limit on the total number of server processes, or simultaneously connected clients, 
that can run at one time. The main purpose of this directive is to keep a runaway Apache HTTP Server from 
crashing the operating system. For busy servers this value should be set to a high value. The server's 
default is set to 150 regardless of the MPM in use. However, it is not recommended that the value for 
MaxClients exceeds 256 when using the prefork MPM.

#The server checks the number of servers waiting for a request and kills some if there are more MaxSpareServers 
or creates some if the number of servers is less than MinSpareServers. These values are only used with the prefork MPM.

## The server checks the number of server threads waiting for a request and kills some if there are more than 
MaxSpareThreads or creates some if the number of servers is less than MinSpareThreads. These values are only 
used with the worker MPM. 

## ThreadsPerChild is only used with the worker MPM. It sets the number of threads within each child process.

NOTE: To use the worker MPM, create the file /etc/sysconfig/httpd and add the following directive:

HTTPD=/usr/sbin/httpd.worker

# directive controls whether Server response header field which is sent back to clients includes a 
description of the generic OS-type of the server as well as information about compiled-in modules.
Default: ServerTokens Full. Syntax: ServerTokens Major|Minor|Min[imal]|Prod[uctOnly]|OS|Full

ServerTokens Min

# ServerLimit Directive is the Upper limit on configurable number of processes.For the prefork MPM, 
this directive sets the maximum configured value for MaxClients for the lifetime of the Apache process.
For the worker MPM, this directive in combination with ThreadLimit sets the maximum configured value for 
MaxClients for the lifetime of the Apache process. Any attempts to change thisdirective during a restart 
will be ignored, but MaxClients can be modified during a restart. Special care must be taken when using 
this directive. If ServerLimit is set to a value much higher than necessary, extra, unused shared memory 
will be allocated. If both ServerLimit and MaxClients are set to values higherthan the system can handle, 
Apache may not start or the system may become unstable. With the prefork MPM, use this directive only if 
you need to set MaxClients higher than 256 (default). Do not set the value of this directive any higher 
than what you might want to set MaxClients to. With worker use this directive only if your MaxClients and 
ThreadsPerChild settings require more than 16 server processes (default). Do not set the value of this 
directive any higher than the number of server processes required by what you may want for MaxClients 
and ThreadsPerChild.

# The Listen command identifies the ports on which the Web server accepts incoming requests.
If the Apache HTTP Server is configured to listen to a port under 1024, only the root user can start it. 
For port 1024 and above, httpd can be started as a regular user.The Listen directive can also be used to 
specify particular IP addresses over which the server accepts connections.

Listen 80

#Include allows other configuration files to be included at runtime. For the server to use individually 
packaged modules, such as mod_ssl, mod_perl, and php, the following directive must be included in 
Section 1: Global Environment of httpd.conf.

# LoadModule is used to load in Dynamic Shared Object (DSO) modules. If creating a new module, first 
install the httpd-devel package as it contains the include files, the header files, as well as the 
APache eXtenSion (/usr/sbin/apxs) application, which uses the include files and the header files to compile DSOs.

LoadModule auth_basic_module modules/mod_auth_basic.so

#The ExtendedStatus directive controls whether Apache generates basic (off) or detailed server status 
information (on), when the server-status handler is called. 

# The User directive sets the user name of the server process and determines what files the server is 
allowed to access. Any files inaccessible to this user are also inaccessible to clients connecting to the 
Apache HTTP Server. For security reasons, the Apache HTTP Server refuses to run as the root user.

User apache

# Specifies the group name of the Apache HTTP Server processes.

Group apache

# Sets the ServerAdmin directive to the email address of the Web server administrator. This email address 
shows up in error messages on server-generated Web pages, so users can report a problem by sending email 
to the server administrator.

ServerAdmin webmaster@example.com

# ServerName specifies a hostname and port number (matching the Listen directive) for the server. 
The ServerName does not need to match the machine's actual hostname.The value specified in ServerName 
must be a valid Domain Name Service (DNS) name that can be resolved by the system — do not make something up.

ServerName www.example.com:80

# UseCanonicalName When set to on, this directive configures the Apache HTTP Server to reference itself using the value 
specified in the ServerName 
and Port directives. When UseCanonicalName is set to off, the server instead uses the value used by the 
requesting client when referring to itself.

# The DocumentRoot is the directory which contains most of the HTML files which are served in response to requests.

DocumentRoot "/var/www/html"

# <Directory /path/to/directory> and </Directory> tags create a container used to enclose a group of 
configuration directives which apply only to a specific directory and its subdirectories. Any directive 
which is applicable to a directory may be used within Directory tags. 
By default, very restrictive parameters are applied to the root directory (/), using the Options 
and AllowOverride directives. Under this configuration, any directory on the system which needs more 
permissive settings has to be explicitly given those settings. In the default configuration, another 
Directory container is configured for the DocumentRoot which assigns less rigid parameters to the 
directory tree so that the Apache HTTP Server can access the files residing there.
EXAMPLE:

<Directory "/var/www/html">
    Options +ExecCGI +Indexes -FollowSymLinks
    AllowOverride None
    Order Allow,Deny
    Allow From All
</Directory>

# The Options directive controls which server features are available in a particular directory. 
For example, under the restrictive parameters specified for the root directory, Options is set to 
only FollowSymLinks. No features are enabled, except that the server is allowed to follow symbolic 
links in the root directory.

# The AllowOverride directive sets whether any Options can be overridden by the declarations in an 
.htaccess file. By default, both the root directory and the DocumentRoot are set to allow no .htaccess overrides.

# The Order directive controls the order in which allow and deny directives are evaluated. The server 
is configured to evaluate the Allow directives before the Deny directives for the DocumentRoot directory.

# Allow specifies which client can access a given directory. The client can be all, a domain name, 
an IP address, a partial IP address, a network/netmask pair, and so on. The DocumentRoot directory is 
configured to Allow requests from all, meaning everyone has access.

# Deny works similar to Allow, except it specifies who is denied access. The DocumentRoot is not 
configured to Deny requests from anyone by default.

#On systems with multiple users, each user can be permitted to have a web site in their home directory 
using the UserDir directive. Visitors to a URL http://example.com/~username/ will get content out of the 
home directory of the user "username", out of the subdirectory specified by the UserDir directive.
Note that, by default, access to these directories is not enabled. You can enable access when using 
UserDir by uncommenting the line : #Include conf/extra/httpd-userdir.conf UserDir is the subdirectory 
within each user's home directory where they should place personal HTML files which are served by the 
Web server.The name for the subdirectory is set to public_html in the default configuration.Make sure 
that the permissions on the users' home directories are set correctly. Users' home directories must be 
set to 0711. The read (r) and execute (x) bits must be set on the users' public_html directories (0755 also works). 
Files that are served in a users' public_html directories mustbe set to at least 0644. 

UserDir		URL					translated File Path
public_html	 http://example.com/~rbowen/file.html	 /home/rbowen/public_html/file.html
/var/html	 http://example.com/~rbowen/file.html	/var/html/rbowen/file.html
UserDir /var/www/*/docs	http://example.com/~rbowen/file.html	/var/www/rbowen/docs/file.html
public_html /var/html	http://example.com/~rbowen/file.html	/var/html/rbowen/file.html

Ex. UserDir /var/html

The UserDir directive can be used to redirect user directory request to external URLs
EX.    UserDir http://example.org/users/*/

you can restrict what users are permitted to use this functionality:
EX.    UserDir disabled root jro fish

You can disable the feature for all but a few users by using a configuration like the following:

UserDir disabled
UserDir enabled rbowen krietz

In order to give each user their own cgi-bin directory, you can use a <Directory> directive to make a 
particular subdirectory of a user's home directory cgi-enabled.

<Directory /home/*/public_html/cgi-bin/>
Options ExecCGI
SetHandler cgi-script
</Directory>

presuming that UserDir is set to public_html, a cgi program example.cgi could be loaded from that directory as:

http://example.com/~rbowen/cgi-bin/example.cgi

If you want to allows users to modify the server configuration in their web space, they will need to use 
.htaccess files to make these changes. Ensure that you have set AllowOverride to a value sufficient for 
the directives that you want to permit the users to modify.

# The DirectoryIndex is the default page served by the server when a user requests an index of a directory by specifying 
a forward slash (/) at the end of the directory name. When a user requests the page http://example/this_directory/, they 
get either the DirectoryIndex page if it exists or a server-generated directory list. The default for DirectoryIndex is 
index.html and the index.html.var type map. The server tries to find either of these files and returns the first one it 
finds. If it does not find one of these files and Options Indexes is set for that directory, the server generates and 
returns a listing, in HTML format, of the subdirectories and files within the directory, unless the directory listing 
feature is turned off.

EX. DirectoryIndex	index.html index.php

# AccessFileName names the file which the server should use for access control information in each directory. 
The default is .htaccess. Immediately after the AccessFileName directive, a set of Files tags apply access control 
to any file beginning with a .ht. These directives deny Web access to any .htaccess files (or other files which 
begin with .ht) for security reasons.

AccessFileName 		.noaccess

# By default, the Web server asks proxy servers not to cache any documents which were negotiated on the basis of 
content (that is, they may change over time or because of the input from the requester). If CacheNegotiatedDocs is 
set to on, this function is disabled and proxy servers are allowed to such cache documents.

# TypesConfig names the file which sets the default list of MIME type mappings (file name extensions to content types). 
The default TypesConfig file is /etc/mime.types. Instead of editing /etc/mime.types, the recommended way to add MIME 
type mappings is to use the AddType directive.
EX. 	TypesConfig /etc/mime.types
	DefaultType text/plain
	AddType application/x-tar .tgz

# DefaultType sets a default content type for the Web server to use for documents whose MIME types cannot be determined. 
The default is text/plain.

# Use the AddType directive to define or override a default MIME type and file extension pairs. The following example 
directive tells the Apache HTTP Server to recognize the .tgz file extension:

# HostnameLookups can be set to on, off or double. If HostnameLookups is set to on, the server automatically resolves 
the IP address for each connection. Resolving the IP address means that the server makes one or more connections to a 
DNS server, adding processing overhead. If HostnameLookups is set to double, the server performs a double-reverse DNS 
look up adding even more processing overhead.To conserve resources on the server, HostnameLookups is set to off by default.

#ErrorLog specifies the file where server errors are logged. By default, this directive is set to /var/log/httpd/error_log.

#LogLevel sets how verbose the error messages in the error logs are. LogLevel can be set (from least verbose to most 
verbose) to emerg, alert, crit, error, warn, notice, info or debug. The default LogLevel is warn.

# The LogFormat directive configures the format of the various Web server log files. The actual LogFormat used depends 
on the settings given in the CustomLog directive.The following are the format options if the CustomLog directive is set to combined:

%h : Lists the remote IP address of the requesting client. If HostnameLookups is set to on, the client hostname is 
recorded unless it is not available from DNS.

%l : Not used. A hyphen [-] appears in the log file for this field.

%u : If authentication was required, lists the user name of the user is recorded. Usually, this is not used, so a 
hyphen [-] appears in the log file for this field.

%t : Lists the date and time of the request.

%s : Lists the HTTP status code which was returned to the client host.

%b : Lists the size of the document.

%\"%{Referer}i\" (referrer) : Lists the URL of the webpage which referred the client host to Web server.

%\"%{User-Agent}i\" (user-agent) : Lists the type of Web browser making the request.

# CustomLog identifies the log file and the log file format. By default, the log is recorded to the /var/log/httpd/access_log file.

The default CustomLog format is combined.

CustomLog logs/access_log combined

# The ServerSignature directive adds a line containing the Apache HTTP Server server version and the ServerName to any 
server-generated documents, such as error messages sent back to clients. ServerSignature is set to on by default.
It can also be set to off or to EMail. EMail, adds a mailto:ServerAdmin HTML tag to the signature line of auto-generated responses.

# The Alias setting allows directories outside the DocumentRoot directory to be accessible. Any URL ending in the 
alias automatically resolves to the alias' path. By default, one alias for an icons/ directory is already set up. 
An icons/ directory can be accessed by the Web server, but the directory is not in the DocumentRoot.

# The ScriptAlias directive defines where CGI scripts are located. Generally, it is not good practice to leave 
CGI scripts within the DocumentRoot, where they can potentially be viewed as text documents. For this reason, a 
special directory outside of the DocumentRoot directory containing server-side executables and scripts is designated 
by the ScriptAlias directive. This directory is known as a cgi-bin and set to /var/www/cgi-bin/ by default.

# When a webpage is moved, Redirect can be used to map the file location to a new URL. The format is as follows:
Redirect /<old-path>/<file-name> http://<current-domain>/<current-path>/<file-name>

# IndexOptions controls the appearance of server generated directing listings, by adding icons, file descriptions, 
and so on. If Options Indexes is set (refer to Section 10.5.22 Options), the Web server generates a directory listing 
when the Web server receives an HTTP request for a directory without an index. The AddDescription option, when used 
in conjunction with FancyIndexing, presents a short description for the file in server generated directory listings. 
IndexOptions has a number of other parameters which can be set to control the appearance of server generated directories. 
Parameters include IconHeight and IconWidth, to make the server include HTML HEIGHT and WIDTH tags for the icons in 
server generated webpages; IconsAreLinks, for making the icons act as part of the HTML link anchor along with the 
filename and others.

# AddIconByEncoding directive names icons which are displayed by files with MIME encoding in server generated directory 
listings. For example, by default, the Web server shows the compressed.gif icon next to MIME encoded x-compress and x-gzip 
files in server generated directory listings.

# AddIconByType directive names icons which are displayed next to files with MIME types in server generated directory 
listings. For example, the server shows the icon text.gif next to files with a mime-type oftext, in server generated directory listings.

# AddIcon specifies which icon to show in server generated directory listings for files with certain extensions. 
For example, the Web server is set to show the icon binary.gif for files with .bin or .exe extensions.

# DefaultIcon specifies the icon displayed in server generated directory listings for files which have no other icon 
specified. The unknown.gif image file is the default.

# AddDescription When using FancyIndexing as an IndexOptions parameter, the AddDescription directive can be used to 
display user-specified descriptions for certain files or file types in a server generated directory listing. The 
AddDescription directive supports listing specific files, wildcard expressions, or file extensions.

# ReadmeName names the file which, if it exists in the directory, is appended to the end of server generated directory 
listings. The Web server first tries to include the file as an HTML document and then try to include it as plain text. 
By default, ReadmeName is set to README.html.

#HeaderName names the file which, if it exists in the directory, is prepended to the start of server generated directory 
listings. Like ReadmeName, the server tries to include it as an HTML document if possible or in plain text if not.

# IndexIgnore  lists file extensions, partial file names, wildcard expressions or full filenames. The Web server does not 
include any files which match any of those parameters in server generated directory listings.

# AddEncoding names filename extensions which should specify a particular encoding type. AddEncoding can also be used to 
instruct some browsers to uncompress certain files as they are downloaded.

# AddLanguage associates file name extensions with specific languages. This directive is useful for Apache HTTP Servers 
which serve content in multiple languages based on the client Web browser's language settings.

# LanguagePriority sets precedence for different languages in case the client Web browser has no language preference set.

# AddHandler maps file extensions to specific handlers. For example, the cgi-script handlercan be matched with the 
extension .cgi to automatically treat a file ending with .cgi as a CGI script. The following is a sample AddHandler 
directive for the .cgi extension. 

# Action specifies a MIME content type and CGI script pair, so that whenever a file of that media type is requested, a particular CGI script is executed.

# The ErrorDocument directive associates an HTTP response code with a message or a URL to be sent back to the client. 
By default, the Web server outputs a simple and usually cryptic error message when an error occurs. The ErrorDocument 
directive forces the Web server to instead output a customized message or page.To be valid, the message must be enclosed 
in a pair of double quotes ["].

#The BrowserMatch directive allows the server to define environment variables and take appropriate actions based on 
the User-Agent HTTP header field — which identifies the client's Web browser type. By default, the Web server uses 
BrowserMatch to deny connections to specific browsers with known problems and also to disable keepalives and HTTP 
header flushes for browsers that are known to have problems with those actions.

Ex. BrowserMatch "Mozilla/2" nokeepalive

#The <Location> and </Location> tags create a container in which access control based on URL can be specified.

For instance, to allow people connecting from within the server's domain to see status reports, use the following directives:

<Location /server-status>
    SetHandler server-status/server-info
    Order deny,allow
    Deny from all 
    Allow from <.example.com>
</Location>
======================================================================================
## Apache can be configured in both a forward and reverse proxy (also known as gateway) mode.
An ordinary forward proxy is an intermediate server that sits between the client and the origin server. In order to 
get content from the origin server, the client sends a request to the proxy naming the origin server as the target 
and the proxy then requests the content from the origin server and returns it to the client. The client must be 
specially configured to use the forward proxy to access other sites.

A typical usage of a forward proxy is to provide Internet access to internal clients that are otherwise restricted
 by a firewall. The forward proxy can also use caching (as provided by mod_cache) to reduce network usage.

The forward proxy is activated using the ProxyRequests directive. Because forward proxies allow clients to access 
arbitrary sites through your server and to hide their true origin, it is essential that you secure your server so 
that only authorized clients can access the proxy before activating a forward proxy.

A reverse proxy (or gateway), by contrast, appears to the client just like an ordinary web server. No special configuration 
on the client is necessary. The client makes ordinary requests for content in the name-space of the reverse proxy. The 
reverse proxy then decides where to send those requests, and returns the content as if it was itself the origin.

A typical usage of a reverse proxy is to provide Internet users access to a server that is behind a firewall. Reverse 
proxies can also be used to balance load among several back-end servers, or to provide caching for a slower back-end 
server. In addition, reverse proxies can be used simply to bring several servers into the same URL space.

A reverse proxy is activated using the ProxyPass directive or the [P] flag to the RewriteRule directive. It is not 
necessary to turn ProxyRequests on in order to configure a reverse proxy.

#Reverse Proxy

ProxyPass /foo http://foo.example.com/bar
ProxyPassReverse /foo http://foo.example.com/bar

#Forward Proxy

ProxyRequests On
ProxyVia On

<Proxy *>
Order deny,allow
Deny from all
Allow from internal.example.com
</Proxy>
#ProxyRequests:  To configure the Apache HTTP Server to function as a proxy server, remove the hash mark (#) from the beginning of 
the <IfModule mod_proxy.c> line, the ProxyRequests, and each line in the <Proxy> stanza. Set the ProxyRequests 
directive to On, and set which domains are allowed access to the server in the Allow from directive of the <Proxy> stanza.

#Proxy: Proxy *> and </Proxy> tags create a container which encloses a group of configuration directives meant to 
apply only to the proxy server. Many directives which are allowed within a <Directory> container may also be used 
within <Proxy> container.

# The ProxyVia command controls whether or not an HTTP Via: header line is sent along with requests or replies which 
go through the Apache proxy server. The Via: header shows the hostname if ProxyVia is set to On, shows the hostname 
and the Apache HTTP Server version for Full, passes along any Via: lines unchanged for Off, and Via: lines are removed for Block.

====================================================================================

# A number of commented cache directives are supplied by the default Apache HTTP Server configuration file. In most cases, 
uncommenting these lines by removing the hash mark (#) from the beginning of the line is sufficient. The following, however, 
is a list of some of the more important cache-related directives.

CacheEnable — Specifies whether the cache is a disk, memory, or file descriptor cache.
CacheRoot — Specifies the name of the directory containing cached files.
CacheSize — Specifies how much space the cache can use in kilobytes.
CacheMaxExpire — Specifies how long HTML documents are retained (without a reload from the originating Web server) in the cache. 
The default is 24 hours (86400 seconds).
CacheLastModifiedFactor — Specifies the creation of an expiry (expiration) date for a document which did not come from 
its originating server with its own expiry set. The default CacheLastModifiedFactor is set to 0.1, meaning that the 
expiry date for such documents equals one-tenth of the amount of time since the document was last modified.
CacheDefaultExpire — Specifies the expiry time in hours for a document that was received using a protocol that does 
not support expiry times. The default is set to 1 hour (3600 seconds).
NoProxy — Specifies a space-separated list of subnets, IP addresses, domains, or hosts whose content is not cached. This 
setting is most useful for Intranet sites.

#The NameVirtualHost directive associates an IP address and port number, if necessary, for any name-based virtual hosts. 
Name-based virtual hosting allows one Apache HTTP Server to serve different domains without using multiple IP addresses.
Name-based virtual hosts only work with non-secure HTTP connections. If using virtual hosts with a secure server, use 
IP address-based virtual hosts instead.

##NameVirtualHost *
#
#<VirtualHost  *>
#    ServerAdmin webmaster@dummy-host.example.com
#    DocumentRoot /www/docs/dummy-host.example.com
#    ServerName dummy-host.example.com
#    ErrorLog logs/dummy-host.example.com-error_log
#    CustomLog logs/dummy-host.example.com-access_log common
#</VirtualHost>

# <VirtualHost> and </VirtualHost> tags create a container outlining the characteristics of a virtual host. The VirtualHost 
container accepts most configuration directives.
A commented VirtualHost container is provided in httpd.conf, which illustrates the minimum set of configuration directives 
necessary for each virtual host.

# SetEnvIf sets environment variables based on the headers of incoming connections. It is not solely an SSL directive, 
though it is present in the supplied /etc/httpd/conf.d/ssl.conf file. It's purpose in this context is to disable HTTP 
keepalive and to allow SSL to close the connection without a close notify alert from the client browser. This setting 
is necessary for certain browsers that do not reliably shut down the SSL connection.

```
