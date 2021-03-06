=======================
openvpn3-service-netcfg
=======================

----------------------
OpenVPN 3 Linux client
----------------------

:Manual section: 8
:Manual group: OpenVPN 3 Linux

SYNOPSIS
========
| ``openvpn3-service-netcfg`` ``[OPTIONS]``
| ``openvpn3-service-netcfg`` ``-h`` | ``--help``


DESCRIPTION
===========
The ``openvpn3-service-netcfg`` process is a backend service for OpenVPN 3
Linux, responsible for creating and managing virtual network interfaces, routing
and DNS configuration provided by VPN connections.  This service will be started
automatically when the ``openvpn3-service-client`` starts and begins to
configure a virtual network interface for its session.

Only one network configuration service can run on the system.

This service is normally started automatically when needed, via the
*net.openvpn.v3.netcfg.service* auto-start configuration by the
``dbus-daemon``\(1), but can also be started manually on the command line.  This
service must start as *root*, but as soon as it has requested the needed
privileges for its operations, it will drop the rest of the privileges and
switch to the *openvpn* user.  Beware that changing this to another user
account also requires updating the D-Bus policy as well.


OPTIONS
=======

-h, --help      Print  usage and help details to the terminal

--version       Prints the version of the program and exists

--log-level LEVEL
                Sets the default log verbosity for log events generated by
                this service.  The default is ``4``.  Valid values are ``0``
                to ``6``.  Higher log levels results in more verbose logs
                and log level ``6`` will contain all debug log events.

--log-file LOG_DESTINATION
                By default, logging will go via the ``openvpn3-service-logger``
                service.  By providing this argument, logging will also be sent
                to *LOG_DESTINATION*, which can be either a filename or ``stdout:``
                where the latter one sends log data to the console.

--colour
                This will add colours to log events when logging to file
                or terminal.  Log events will be coloured based on the log
                level of the event.

--signal-broadcast
                Normally, the ``openvpn3-service-netcfg`` will attach a
                log stream to the ``openvpn3-service-logger`` service.  By
                providing this option, it will instead broadcast all log events
                to all listeners on the system.  This is more useful for
                debugging when the standard logging does not provide any clues.
                This is not recommended for production.

--idle-exit MINUTES
                The ``openvpn3-service-netcfg`` service will exit
                automatically if it is being idle for *MINUTES* minutes.  By
                being idle, it means no active virtual network interfaces is
                being managed by this service.

--resolv-conf RESOLV-CONF-FILE
                This enables DNS configuration, where it will take ownership
                of *RESOLV-CONF-FILE* and update it as needed, with both DNS
                servers and search domains.  It will also preserve the contents
                of it as well as having a backup file which will be restored
                when no DNS settings from VPN tunnels are needed.

--redirect-method METHOD
                This defines how to handle route configurations which involves
                changing the default gateway.  This primarily controls how
                the network traffic routing is done from the VPN client process
                to the remote server.  Valid *METHOD* arguments:

                * *host-route* (default)

                  This will add an explicit direct host route for the VPN
                  server's IP address to go via the default gateway on the
                  system.  This is quite similar to how OpenVPN 2.x behaves.

                * *bind-device*

                  This will bind the device using the *SO_BINDDEV* socket option
                  for the UDP/TCP socket used to connect to the remote side.

                * *none*

                  This will disable any specific routing arrangement for the
                  remote host access.  If the default gateway is modified, this
                  will most certainly make the VPN connection useless; thus
                  this is not recommended for production.

--set-somark MARK
                This will add a *SO_MARK* based reference to the packets sent
                to the remote server.  This can be used by *netfilters* like
                the ``--mark`` feature in ``iptables``.  This can be used
                by itself or in combination with any ``--redirect-method``
                settings, unless ``openvpn3-service-client`` is started with
                ``--disable-protect-socket``.


SEE ALSO
========

``dbus-daemon``\(1)
``openvpn3``\(1)
``openvpn3-service-client``\(8)

