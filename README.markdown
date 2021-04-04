# Docker RSyslog local forwarder with RELP module support (https://github.com/gynter/docker-rsyslog-local-forwarder)

A generic Docker image for [RSyslog](https://www.rsyslog.com/) with [RELP](https://en.wikipedia.org/wiki/Reliable_Event_Logging_Protocol)
module support . This image is designed to run securely on non-systemd (i.e Alpine) containers as unprivileged user (UID/GID 1000)

See https://github.com/gynter/rsyslog-docker-compose-example for deployment example using Docker Compose.

## Important files and directories

- `/etc/rsyslog.conf` - Main rsyslogd configuration file, can be overwritten by a bind mount;
- `/etc/rsyslog.d/` - Extra rsyslogd configuration files, can be mounted as a volume. Files must have `*.conf` extension
  to be loaded by rsyslogd;
- `/srv/rsyslog/` - rsyslogd working directory, PID file will be also created there. It's recommended to mount this
  directory as a volume if there's a need to persist working files (i.e when using queue files to store data on file
  system);
- `/logs/` - A directory for storing log files. Should be mounted as a volume.
- `/tmp/log.sock` - Socket for local system logging;
- `/etc/pki/rsyslog/` - PKI files (CA certificate, server certificate and key) for rsyslogd. Must be mounted as a
   volume or bind mount. File names are defined in `90-remote-log-all.conf` as follows:
     - `ca.pem` - PEM format x509 root or intermediate CA from which TLS certs are issued from;
     - `cert.pem` - PEM format x509 certificate for client for mTLS;
     - `key.pem` - Private key corresponding to server certificate.

## Environment variables

- `RSYSLOG_RELP_TARGET` - RSyslog server hostname or IP address;
- `RSYSLOG_RELP_TARGET_PORT` - RSyslog server port;
- `RSYSLOG_TLS_PERMITTED_PEER` - List of permitted peer's as defined in [TLS.PermittedPeer](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imrelp.html#tls-permittedpeer).
  Domain names must correspond to x509 certificate SAN extension DNS Name value.

## Known issues

It's not possible to use list format `["peer1_name", "peer2_name"]` for `RSYSLOG_TLS_PERMITTED_PEER`. It might be
due to rsyslogd-s failure to expand environment variables correctly if those contain special characters. This needs
further analysis.

## License

Parts of RSyslog official documentation were used in configuration file comments, therefore the license is same as
RSyslog documentation, Apache License Version 2.0. The copy of the license can be found in file `LICENSE`.
