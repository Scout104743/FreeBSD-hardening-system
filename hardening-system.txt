#!/bin/sh
##################################################################
# FreeBSD System Hardening Script
# Updated for FreeBSD 13.2-RELEASE-amd64
# Original by David Childers - 15 February, 2010
# Updated script - (Insert Date)
# IMPORTANT: Test this script in a safe environment before using it in production.
##################################################################

# Syslogd Configuration
# Prevent syslogd from binding to a network socket for local logging only
echo 'syslogd_flags="-ss"' >> /etc/rc.conf

# ICMP Redirects - Ignore ICMP redirects (common attack vector)
echo 'icmp_drop_redirect="YES"' >> /etc/rc.conf

# Sendmail - Disable if not in use
echo 'sendmail_enable="NO"' >> /etc/rc.conf

# Inetd - Disable inetd if these services are not needed
echo 'inetd_enable="NO"' >> /etc/rc.conf

# NFS - Disable if not in use
echo 'nfs_server_enable="NO"' >> /etc/rc.conf
echo 'nfs_client_enable="NO"' >> /etc/rc.conf

# SSHD - Only disable if you do not need SSH access
# echo 'sshd_enable="NO"' >> /etc/rc.conf

# Portmap - Disable if you are not using NFS or NIS
echo 'portmap_enable="NO"' >> /etc/rc.conf

# Update MOTD - Disable automatic update of MOTD
echo 'update_motd="NO"' >> /etc/rc.conf

# Clear /tmp at startup
echo 'clear_tmp_enable="YES"' >> /etc/rc.conf

# Sysctl Configuration
# Security and Network Hardening (Verify each line for your specific requirements)
echo 'security.bsd.see_other_uids=0' >> /etc/sysctl.conf
echo 'net.inet.ip.random_id=1' >> /etc/sysctl.conf
echo 'net.inet.tcp.always_keepalive=1' >> /etc/sysctl.conf
echo 'net.inet.tcp.blackhole=2' >> /etc/sysctl.conf
echo 'net.inet.udp.blackhole=1' >> /etc/sysctl.conf

# Apply immediate sysctl settings (remove if you plan to reboot instead)
sysctl -w net.inet.icmp.bmcastecho=0
sysctl -w net.inet.ip.redirect=0
sysctl -w net.inet.ip6.redirect=0
sysctl -w net.inet.icmp.maskrepl=0
sysctl -w net.inet.ip.sourceroute=0
sysctl -w net.inet.ip.accept_sourceroute=0

# File Permissions - Restrict access to sensitive files
chmod o= /etc/fstab
chmod o= /etc/group
chmod o= /etc/hosts
chmod o= /etc/hosts.allow
chmod o= /etc/ssh/sshd_config
chmod o= /etc/sysctl.conf

# Cron and At Jobs - Restrict to root
echo "root" > /var/cron/allow
echo "root" > /var/at/at.allow
chmod o= /etc/crontab
chmod o= /usr/bin/crontab
chmod o= /usr/bin/at
chmod o= /usr/bin/atq
chmod o= /usr/bin/atrm
chmod o= /usr/bin/batch

# Secure /root directory
chmod 710 /root

# System Log File Directory
chmod o= /var/log

# Merge Temporary Directories
mv /var/tmp/* /tmp/ 2>/dev/null
rm -rf /var/tmp
ln -s /tmp /var/tmp

# NTP Configuration - Ensure accurate timekeeping (manually configure /etc/ntp.conf)
echo 'ntpdate_enable="YES"' >> /etc/rc.conf

echo "End of script."
##################################################################
# Additional manual configurations required for:
# - NTP (edit /etc/ntp.conf)
# - Password encryption method (edit /etc/auth.conf and /etc/login.conf)
# - Single User Mode security (edit /etc/ttys)
##################################################################
