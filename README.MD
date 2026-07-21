# linux_helper

Various Linux deployments

## Requirements

* Ansible 3.0.0

## Example configuration

```yaml
---
linux_helper:
# Set hostname or not
  - hostname:
      - set_hostname: "true"
        hostname: "{{ inventory_hostname }}"
    modprobe:
      - set_modprobe: "{{ linux_helper_set_modprobe | default('true') }}"
        drop_exists: 'true'
        modprobes:
          - file_name: 'intel-mei'
            actions:
        # Create alias for 'mei', 'mei-me', 'mei-txe'
          - action: 'alias'
            wildcard: 'intel-mei'
            module: 'mei*'
        # Blacklist this modules
          - action: 'blacklist'
            module: 'intel-mei'
        # Set sequence options for module
          - file_name: 'intel-snd'
            actions:
          - action: 'options'
            module: 'snd_hda_intel'
            options:
              - 'power_save=1'
              - 'power_save_controller=N'
        # Set one option for module, as string
          - file_name: 'intel-iwl'
             actions:
          - action: 'options'
            module: 'iwlwifi'
            options: '11n_disable=8'
# Set system locales. Order is important for LANG environmental, i.e. only last
# record is your locale. Example:
    locale:
# Generate locales for ru_RU and en_US locales, if you want to set LANG env.
# This sets your env LANG to 'en_US.UTF-8'
      - set_langs: 'true'
        locales:
          - locale: 'ru_RU'
            charset: 'UTF-8'
          - locale: 'en_US'
            charset: 'UTF-8'
# IMPORTANT for RedHat systems, you should add `lang` field, that match with your locale package. This required for package manager:
        locales:
          - lang: 'ru'
            locale: 'ru_RU'
            charset: 'UTF-8'
          - lang: 'en'
            locale: 'en_US'
            charset: 'UTF-8'
    krb5:
      - libdefaults:
          - default_realm: 'EXAMPLE.COM'
            dns_lookup_realm: 'false'
            dns_lookup_kdc: 'true'
            udp_preference_limit: '0'
# Static table lookup for hostnames
    hosts:
      - set_hosts: "{{ linux_helper_set_hosts | default('true') }}"
        hosts:
        # IP address
          - address: '192.168.0.193'
        # List of aliases
            aliases:
              - 'home'
              - 'mirror.archlinux.org'
# /etc/sudoers entries
    sudoers:
      - 'root ALL=(ALL) ALL'
      - '%wheel ALL=(ALL) NOPASSWD: ALL'
# netconsole deployment with systemd service
    netconsole:
      - set_netconsole: 'true'
        netconsole:
          - local_port: '6666'
            remote_ipaddr: '10.0.1.100'
            remote_port: '555'
            remote_maddr: '52:8b:7d:b8:8e:7d'
            device: 'bond0'
    krb5:
      - libdefaults:
# If this flag is set to false, then weak encryption types will be filtered out
# of the lists 'default_tgs_enctypes', 'default_tkt_enctypes, and
# 'permitted_enctypes'. The default value for this tag is 'false'
        - allow_weak_crypto: 'false'
# If this flag is set to 'true', initial ticket requests to the KDC will
# request canonicalization of the client principal name, and answers with
# different client principals than the requested principal will be accepted.
# The default value is 'false'
          canonicalize: 'false'
# This parameter determines the format of credential cache types created by
# kinit(1) or other programs. The default value is '4', which represents the
# most current format. Smaller values can be used for compatibility with very
# old implementations of Kerberos which interact with credential caches on the
# same host
          ccache_type: '4'
# Sets the maximum allowable amount of clockskew in seconds that the library
# will tolerate before assuming that a Kerberos message is invalid. The default
# value is 300 seconds, or five minutes. The clockskew setting is also used
# when evaluating ticket start and expiration times. For example, tickets that
# have reached their expiration time can still be used (and renewed if they are
# renewable tickets) if they have been expired for a shorter duration than the
# clockskew setting.
          clockskew: ''
# This relation specifies the name of the default credential cache. The default
# is "/tmp/krb5cc_%{uid}"
          default_ccache_name: ''
# This relation specifies the name of the default keytab for obtaining client
# credentials. The default is FILE:/var/lib/krb5/user/%{euid}/client.keytab.
          default_client_keytab_name: ''
# This relation specifies the default keytab name to be used by application
# servers such as sshd. The default is "/etc/krb5.keytab". This relation is
# subject to parameter expansion (see below).
          default_keytab_name: ''
# This relation specifies the name of the default replay cache. The default is
# "dfl:".
          default_rcache_name: ''
# Identifies the default Kerberos realm for the client. Set its value to your
# Kerberos realm. If this value is not set, then a realm must be specified with
# every Kerberos principal when invoking programs such as kinit
          default_realm: ''
# Identifies the supported list of session key encryption types that the client
# should request when making a TGS-REQ, in order of preference from highest to
# lowest. The list may be delimited with commas or whitespace
          default_tgs_enctypes: ''
# Identifies the supported list of session key encryption types that the client
# should request when making an AS-REQ, in order of preference from highest to
# lowest. The format is the same as for 'default_tgs_enctypes'. Do not set this
# unless required for specific backward compatibility purposes, stale values of
# this setting can prevent clients from taking advantage of new stronger
# enctypes when the libraries are upgraded
          default_tkt_enctypes: ''
# Indicate whether name lookups will be used to canonicalize hostnames for use
# in service principal names. Setting this flag to false can improve security by
# reducing reliance on DNS, but means that short hostnames will not be
# canonicalized to fully-qualified hostnames. If this option is set to fallback,
# DNS canonicalization will only be performed the server hostname is not found
# with the original name when requesting credentials. The default value is true.
          dns_canonicalize_hostname: ''
# Indicate whether DNS SRV records should be used to locate the KDCs and other
# servers for a realm, if they are not listed in the krb5.conf information for
# the realm. Note that the 'admin_server' entry must be in the krb5.conf realm
# information in order to contact kadmind, because the DNS implementation for
# kadmin is incomplete. Enabling this option does open up a type of
# denial-of-service attack, if someone spoofs the DNS records and redirects you
# to another server. However, it's no worse than a denial of service, because
# that fake KDC will be unable to decode anything you send it (besides the
# initial ticket request, which has no encrypted data), and anything the fake
# KDC sends will not be trusted without verification using some secret that it
# won't know.
          dns_lookup_kdc: ''
# Indicate whether DNS URI records should be used to locate the KDCs and other
# servers for a realm, if they are not listed in the krb5.conf information for
# the realm. SRV records are used as a fallback if no URI records were found.
# The default value is 'true'
          dns_uri_lookup: ''
# If this flag to true, GSSAPI credential delegation will be disabled when the
# ok-as-delegate flag is not set in the service ticket. If this flag is 'false',
# the ok-as-delegate ticket flag is only enforced when an application
# specifically requests enforcement. The default value is 'false'
          enforce_ok_as_delegate: ''
# This relation allows for custom error message formatting. If a value is set,
# error messages will be formatted by substituting a normal error message for
# '%M' and an error code for '%C' in the value
          err_fmt: ''
# This allows a computer to use multiple local addresses, in order to allow
# Kerberos to work in a network that uses NATs while still using
# address-restricted tickets. The addresses should be in a comma-separated list.
# This option has no effect if 'noaddresses' is 'true'
          extra_addresses: ''
# If this flag is 'true', initial tickets will be forwardable by default, if
# allowed by the KDC. The default value is 'false'.
          forwardable: ''
# When accepting GSSAPI or krb5 security contexts for host-based service
# principals, ignore any hostname passed by the calling application, and allow
# clients to authenticate to any service principal in the keytab matching the
# service name and realm name (if given). This option can improve the
# administrative flexibility of server applications on multihomed hosts, but
# could compromise the security of virtual hosting environments. The default
# value is 'false'
          ignore_acceptor_hostname: ''
# If this flag is 'true', principals must be listed in a local user's k5login
# file to be granted login access, if a .k5login(5) file exists. If this flag is
# 'false', a principal may still be granted login access through other
# mechanisms even if a k5login file exists but does not list the principal. The
# default value is 'true'
          k5login_authoritative: ''
# If set, the library will look for a local user's k5login file within the
# named directory, with a filename corresponding to the local username. If not
# set, the library will look for k5login files in the user's home directory,
# with the filename .k5login. For security reasons, .k5login files must be
# owned by the local user or by root
          k5login_directory: ''
# On macOS only, determines the name of the bootstrap service used to contact
# the KCM daemon for the KCM credential cache type. If the value is '-', Mach
# RPC will not be used to contact the KCM daemon. The default value is
# 'org.h5l.kcm'
          kcm_mach_service: ''
# Determines the path to the Unix domain socket used to access the KCM daemon
# for the KCM credential cache type. If the value is '-', Unix domain sockets
# will not be used to contact the KCM daemon. The default value is
# '/var/run/.heim_org.h5l.kcm-socket'
          kcm_socket: ''
# Default KDC options (Xored for multiple values) when requesting initial
# tickets. By default it is set to '0x00000010' (KDC_OPT_RENEWABLE_OK)
          kdc_default_options: ''
# Accepted values for this relation are 1 or 0. If it is nonzero, client
# machines will compute the difference between their time and the time returned
# by the KDC in the timestamps in the tickets and use this value to correct for
# an inaccurate system clock when requesting service tickets or authenticating
# to services. This corrective factor is only used by the Kerberos library, it
# is not used to change the system clock. The default value is '1'
          kdc_timesync: ''
# If this flag is true, requests for initial tickets will not be made with
# address restrictions set, allowing the tickets to be used across NATs. The
# default value is 'true'
          noaddresses: ''
# Identifies the encryption types that servers will permit for session keys and
# for ticket and authenticator encryption, ordered by preference from highest to
# lowest
          permitted_enctypes: ''
# If set, determines the base directory where krb5 plugins are located. The
# default value is the krb5/plugins subdirectory of the krb5 library directory
          plugin_base_dir: ''
# This allows you to set the preferred preauthentication types which the client
# will attempt before others which may be advertised by a KDC. The default value
# for this setting is '17, 16, 15, 14', which forces libkrb5 to attempt to use
# PKINIT if it is supported
          preferred_preauth_types: ''
# If this flag is true, initial tickets will be proxiable by default, if
# allowed by the KDC. The default value is 'false'
          proxiable: ''
# If this string is set, it determines the domain suffix for single-component
# hostnames when DNS canonicalization is not used (either because
# dns_canonicalize_hostname is false or because forward canonicalization
# failed). The default value is the first search domain of the system's DNS
# configuration
          qualify_shortname: ''
# If this flag is 'true', reverse name lookup will be used in addition to
# forward name lookup to canonicalizing hostnames for use in service principal
# names. If 'dns_canonicalize_hostname' is set to 'false', this flag has no
# effect. The default value is 'true'
          rdns: ''
# Indicate whether a host's domain components should be used to determine the
# Kerberos realm of the host. The value of this variable is an integer: '-1'
# means not to search, '0' means to try the host's domain itself, '1' means to
# also try the domain's immediate parent, and so forth. The library's usual
# mechanism for locating Kerberos realms is used to determine whether a domain
# is a valid realm, which may involve consulting DNS if 'dns_lookup_kdc' is set.
# The default is not to search domain components
          realm_try_domains: ''
# Sets the default renewable lifetime for initial ticket requests.
# The default value is '0'
          renew_lifetime: ''
# A whitespace or comma-separated list of words which specifies the groups
# allowed for SPAKE preauthentication
          spake_preauth_groups: ''
# Sets the default lifetime for initial ticket requests. The default value is
# '1' day
          ticket_lifetime: ''
# When sending a message to the KDC, the library will try using TCP before UDP
# if the size of the message is above udp_preference_limit. If the message is
# smaller than udp_preference_limit, then UDP will be tried before TCP.
# Regardless of the size, both protocols will be tried if the first attempt
# fails
          udp_preference_limit: ''
# If this flag is 'true', then an attempt to verify initial credentials will
# fail if the client machine does not have a keytab. The default value is
# 'false'
          verify_ap_req_nofail: ''
# If this flag is 'true', then all application protocol authentication
# requests will be flagged to indicate that the application supports channel
# bindings when operating over a secure channel. The default value is false
          client_aware_channel_bindings: ''
      realms:
        - name: 'ATHENA.MIT.EDU'
# Identifies the host where the administration server is running. Typically,
# this is the primary Kerberos server. This tag must be given a value in order
# to communicate with the kadmind server for the realm.
          admin_server: 'kerberos.mit.edu'
# This tag allows you to set a general rule for mapping principal names to
# local user names. It will be used if there is not an explicit mapping for the
# principal name that is being translated
          auth_to_local:
            - 'RULE:[2:$1](johndoe)s/^.*$/guest/'
            - 'RULE:[2:$1;$2](^.*;admin$)s/;admin$//'
            - 'RULE:[2:$2](^.*;root)s/^.*$/root/'
            - 'DEFAULT'
# This subsection allows you to set explicit mappings from principal names to
# local user names. The tag is the mapping name, and the value is the
# corresponding local user name
          auth_to_local_names: ''
# This tag specifies the domain used to expand hostnames when translating
# Kerberos 4 service principals to Kerberos 5 principals (for example, when
# converting rcmd.hostname to host/hostname.domain)
          default_domain: ''
# If this flag is true, the client will not perform encrypted timestamp
# preauthentication if requested by the KDC. Setting this flag can help to
# prevent dictionary attacks by active attackers, if the realm's KDCs support
# SPAKE preauthentication or if initial authentication always uses another
# mechanism or always uses FAST. This flag persists across client referrals
# during initial authentication. This flag does not prevent the KDC from
# offering encrypted timestamp
          disable_encrypted_timestamp: ''
# When KDCs and kpasswd servers are accessed through HTTPS proxies, this tag can
# be used to specify the location of the CA certificate which should be trusted
# to issue the certificate for a proxy server. If left unspecified, the
# system-wide default set of CA certificates is used
          http_anchors: ''
# The name or address of a host running a KDC for that realm. An optional port
# number, separated from the hostname by a colon, may be included. If the name
# or address contains colons (for example, if it is an IPv6 address), enclose
# it in square brackets to distinguish the colon from a port separator. For
# your computer to be able to communicate with the KDC for each realm, this tag
# must be given a value in each realm subsection in the configuration file, or
# there must be DNS SRV records specifying the KDCs
          kdc: ''
# Points to the server where all the password changes are performed. If there is
# no such entry, DNS will be queried (unless forbidden by dns_lookup_kdc)
          kpasswd_server: ''
# The name for 'primary_kdc' prior to release 1.19. Its value is used as a
# fallback if 'primary_kdc' is not specified
          master_kdc: ''
# Identifies the primary KDC. Currently, this tag is used in only one case: if
# an attempt to get credentials fails because of an invalid password, the client
# software will attempt to contact the primary KDC, in case the user's password
# has just been changed, and the updated database has not been propagated to the
# replica servers yet
          primary_kdc: ''
# This subsection allows the administrator to configure exceptions to the
# default_domain mapping rule. It contains V4 instances (the tag name) which
# should be translated to some specific hostname (the tag value) as the second
# component in a Kerberos V5 principal name
          v4_instance_convert: ''
# This relation is used by the krb524 library routines when converting a V5
# principal name to a V4 principal name. It is used when the V4 realm name and
# the V5 realm name are not the same, but still share the same principal names
# and passwords. The tag value is the Kerberos V4 realm name
          v4_realm: ''
# This content will be placed to /etc/profile.d
    profile: |
      #!/bin/bash

      source /etc/os-release
      echo -e "Hello, I'm ${PRETTY_NAME} $(uname -r)\n"
```
