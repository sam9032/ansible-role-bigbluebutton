# BigBlueButton

> Ansible role for a BigBlueButton installation

This role is following the documentation on <https://docs.bigbluebutton.org/2.2/install.html>

Also check [Before you install](https://docs.bigbluebutton.org/2.2/install.html#before-you-install) and [Minimum server requirements](https://docs.bigbluebutton.org/2.2/install.html#minimum-server-requirements) from the official documentation as they also apply here.

## Role Variables

> ⚠️ **WATCH OUT FOR _REQUIRED_ VARIABLES!** ⚠️

> ⚠️ **IF NOT SET THIS ROLE WILL FAIL!** ⚠️

| Variable Name | Function | Default value | Comment |
| ------------- | -------- | ------------- | ------- |
| `bbb_hostname` | Hostname for this BigBlueButton instance _(required)_ | `{{ ansible_fqdn }}` |
| `bbb_state` | Install BigBlueButton to state | `present` | for updating BigBlueButton with this role use `latest` |
| `bbb_apt_mirror` | apt repo server for BigBlueButton packages | `https://ubuntu.bigbluebutton.org` | other value would be e.g. `https://packages-eu.bigbluebutton.org` |
| `bbb_letsencrypt_enable` | Enable letsencrypt/HTTPS | `yes` |
| `bbb_letsencrypt_email` | E-mail for use with letsencrypt | | _(required when letsencrypt is enabled)_ |
| `bbb_nginx_privacy` | only log errors not access | `yes` |
| `bbb_nginx_listen_https` | nginx: use https | `yes` | This is useful for a reverse proxy configuration where the BBB server is behind a load balancing server like haproxy that does SSL termination |
| `bbb_nginx_root` | Default nginx www path of BigBlueButton | `/var/www/bigbluebutton-default` | Set the default nginx `www` path of BigBlueButton |
| bbb_ssl_cert | Define the ssl cert location/name | `"/etc/letsencrypt/live/{{ bbb_hostname }}/fullchain.pem"` | |
| bbb_ssl_key | Define the ssl key location/name | `"/etc/letsencrypt/live/{{ bbb_hostname }}/privkey.pem"` | |
| bbb_own_cert | Define the name of the cert file which shall be used | `undefined` | |
| bbb_own_key | Define the name of the key file which shall be used | `undefined` | |
| `bbb_default_welcome_message` | Welcome Message in the client | Welcome to <b>%%CONFNAME%%</b>!<br><br>For help on using BigBlueButton see these (short) <a href="https://www.bigbluebutton.org/html5"><u>tutorial videos</u></a>.<br><br>To join the audio bridge click the phone button.  Use a headset to avoid causing background noise for others. | Needs to be encoded with `native2ascii -encoding UTF8`! |
| `bbb_default_welcome_message_footer` | Footer of the welcome message | This server is running <a href="https://docs.bigbluebutton.org/" target="_blank"><u>BigBlueButton</u></a>. | Encoded as the welcome message |
| `bbb_default_presentation` | Location of default presentation | `${bigbluebutton.web.serverURL}/default.pdf` |
| `bbb_custom_presentation` | Overwrite the default.pdf | `None` | Location of a custom presentation will be renamed to `default.pdf` if `bbb_custom_presentation_name` is not defined - see [Ansible search paths](https://docs.ansible.com/ansible/latest/user_guide/playbook_pathing.html) for where to place your custom pdf - Example `playbooks/files/default.pdf` |
| `bbb_custom_presentation_name` | Set a custom presentation name | `None` | Instead of overwriting the `default.pdf` setting the name will add for example the `customer.pdf` |
| `bbb_coturn_enable` | enable installation of the TURN-server | `yes` |
| `bbb_coturn_server` | server name on coturn (realm) | `{{ bbb_hostname }}` |
| `bbb_coturn_port` | the port for the TURN-Server to use | `3443` |
| `bbb_coturn_port_tls` | the port for tls for the TURN-Server to use | `3443` |
| `bbb_coturn_secret` | Secret for the TURN-Server  _(required)_ | | can be generated with `openssl rand -hex 16` |
| `bbb_coturn_min_port` | Lower bound of the UDP relay endpoints | `49152` | |
| `bbb_coturn_max_port` | Upper bound of the UDP relay endpoints | `65535` | |
| `bbb_turn_enable` | enable the use uf TURN in general | `yes` | |
| `bbb_stun_servers` | a list of STUN-Server to use | `{{ bbb_hostname }}` | an array with key `server` - take a look in defaults/main.yml |
| `bbb_ice_servers` | a list of RemoteIceCandidate for STUN | `[]` | in array with key `server` |
| `bbb_turn_servers` | a list of TURN-Server to use | `{{ bbb_hostname }}` with `{{ bbb_coturn_secret }}` | take a look in defaults/main.yml |
| `bbb_greenlight_enable` | enable installation of the greenlight client | `yes` | |
| `bbb_greenlight_hosts` | the hostname that greenlight is accessible from | `{{ bbb_hostname }}` | |
| `bbb_greenlight_secret` | Secret for greenlight _(required when using greenlight)_ |  | can be generated with `openssl rand -hex 64` |
| `bbb_greenlight_db_password` | Password for greenlight's database  _(required when using greenlight)_ | | can be generated with `openssl rand -hex 16` |
| `bbb_greenlight_default_registration` | Registration option open(default), invite or approval | `open` | |
| `bbb_greenlight_users` | Greenlight users' list to create. No email notification will be triggered. As it contains passwords, recommend to put in ansible-vault. for more details see defaults/main.yml | `[]` |
| `bbb_allow_mail_notifications`  | Set this to true if you want GreenLight to send verification emails upon the creation of a new account | `true` |
| `bbb_disable_recordings` | Disable options in gui to have recordings | `no` | [Recordings are running constantly in background](https://github.com/bigbluebutton/bigbluebutton/issues/9202) which is relevant as privacy relevant user data is stored |
| `bbb_api_demos_enable` | enable installation of the api demos | `no` | |
| `bbb_mute_on_start:` | start with muted mic on join | `no` | |
| `bbb_app_log_level:` | set bigbluebutton log level | `DEBUG` | |
| `bbb_meteor:` | overwrite settings in meteor | `{}` | |
| `bbb_nodejs_version` | version of nodejs to be installed | `12.x` | |
| `bbb_system_locale` | the system locale to use | `en_US.UTF-8` | |
| `bbb_secret` | define the secret for bbb | `none` | `set this if you want to define the bbb-conf -secret. Otherwise the secret is generated by bbb`
| `bbb_freeswitch_ipv6` | Enable IPv6 support in FreeSWITCH | `true` | Disable to fix [FreeSWITCH IPv6 error][bbb_freeswitch_ipv6] |
| `bbb_freeswitch_ip_address` | Set IP address for FreeSWITCH's wss-binding | `{{ ansible_default_ipv4.address }}` | Can be used when port 7443 is already in use on `{{ ansible_default_ipv4.address }}` or in IPv6-only setups. |
| `bbb_freeswitch_external_ip` | Set stun server for sip and rtp on FreeSWITCH | `stun:{{ (bbb_stun_servers \| first).server }}` | WARNING: the value of the default freeswitch installation is `stun:stun.freeswitch.org` |
| `bbb_dialplan_quality` | Set quality of dailplan for FreeSWITCH | `cdquality` | |
| `bbb_dialplan_energy_level` | Set energy level of dailplan for FreeSWITCH | `100` | only for selected profile `bbb_dialplan_quality` |
| `bbb_dialplan_comfort_noise` | Set comfort noise of dailplan for FreeSWITCH | `1400` | only for selected profile `bbb_dialplan_quality` |
| `bbb_webhooks_enable` | install bbb-webhooks | `no` | |
| `bbb_monitoring_all_in_one_enable` | deploy [all in one monitoring stack](https://bigbluebutton-exporter.greenstatic.dev/installation/all_in_one_monitoring_stack/) (docker) | `no` |
| `bbb_monitoring_all_in_one_version` | Version of the `greenstatic/bigbluebutton-exporter` docker image | `latest` | |
| `bbb_monitoring_all_in_one_directory` | Directory for the docker compose files | `/root/bbb-monitoring` | |
| `bbb_monitoring_all_in_one_port` | Internal Port for the monitoring werbservice | `3001` | |
| `bbb_monitoring_all_in_one_grafana` | Enable(true)/Disable(false) the Grafana container | `true` | |
| `bbb_monitoring_all_in_one_prometheus` | Enable(true)/Disable(false) the prometheus container | `true` | |
| `bbb_monitoring_all_in_one_external` | Enable exposure to nginx | `false` | Can be reached under `/mon/bbb` and `/mon/node` - requires `htpasswd` and `htpasswd_user` |
| `bbb_monitoring_all_in_one_htpasswd_user` | The user for the htpasswd - _(required)_ if external | `Undefined` | |
| `bbb_monitoring_all_in_one_htpasswd` | The password for the htpasswd - _(required)_ if external | `Undefined` | |
| `bbb_monitoring_recordings_from_disk` | Collect recordings metrics by querying the disk instead of the API. See [this](https://bigbluebutton-exporter.greenstatic.dev/exporter-user-guide/#optimizations) for details. | `true` |
| `bbb_dialin_enabled` | enable phone dial-in, will also remove any previous dial-in configuration if set to `false`  | `false` | |
| `bbb_dialin_provider_proxy` | IP or Domain of your SIP provider, also known as registrar | `sip.example.net` | |
| `bbb_dialin_provider_username` | Username for authentication on the SIP-server | `provider-account` | |
| `bbb_dialin_provider_password` | Password for authentication on the SIP-server | `provider-password` | |
| `bbb_dialin_provider_extension` | Extension of your SIP account | `6135551234` | |
| `bbb_dialin_default_number` | Number to present to users for dial-in. Enable `bbb_dialin_overwrite_footer` or use `%%DIALNUM%%` and `%%CONFNUM%%` in you footer (see `bbb_default_welcome_message_footer`) | `6135551234` | |
| `bbb_dialin_mask_caller` | Mask caller-number in the BBB web-interface for privacy reasons (`01711233121` → `xxx-xxx-3121`) | |
| `bbb_dialin_overwrite_footer` | Set the default dial-in footer instead of `bbb_default_welcome_message_footer` | `false` | |
| `bbb_dialin_footer` | The default dial-in notice, if you want to customize it, it is recommended to change `bbb_default_welcome_message_footer` instead | `<br><br>To join this meeting by phone, dial:<br>  %%DIALNUM%%<br>Then enter %%CONFNUM%% as the conference PIN number.` | |
| `bbb_guestpolicy` | How guest can access | `ALWAYS_ACCEPT` | acceptable options: ALWAYS_ACCEPT, ALWAYS_DENY, ASK_MODERATOR |
| `bbb_ntp_cron` | Disable automatic time synchronisation and instead configure a cronjob | `false` |
| `bbb_ntp_cron_day` | Day of the month the time-sync job should run | `*` |
| `bbb_ntp_cron_hour` | Hour when the time-sync job should run | `5` |
| `bbb_ntp_cron_minute` | Minute when the time-sync job should run | `0` |
| `bbb_html5_node_options` | Allow to set extra options for node for the html5-webclient | unset | Could be used for example with <https://github.com/bigbluebutton/bigbluebutton/issues/11183> ; `--max-old-space-size=4096 --max_semi_space_size=128` |
| `bbb_meeting_inactivity_timeout_minutes` | set the default timeout in minutes | `10` | TBD |
| `bbb_freeswitch_socket_password` | set password for freeswitch _(required)_ |  | Can be generated with `pwgen -s 16 1` |
| `bbb_html5_backend_processes` | amount of html5 backend processes | 1 | min = 1; max = 4 |
| `bbb_html5_frontend_processes` | amount of html5 frontend processes | 1 | min = 1; max = 4; or 0 to let the same process do front- and backend (2.2 behavior) |
| `bbb_container_compat` | Compatibility with unprivileged containers | `false` | Enabling this option allows to deploy BBB into a unprivileged container |

### Extra options for Greenlight

The Web-Frontend has some extra configuration options, listed below:

#### SMTP

The notifications are sent using sendmail, unless the `bbb_greenlight_smtp.server` variable is set.
In that case, make sure the rest of the variables are properly set.

The default value for `bbb_greenlight_smtp.sender` is `bbb@{{ bbb_hostname }}`

Example Setup:

```yaml
bbb_greenlight_smtp:
  server: smtp.gmail.com
  port: 587
  domain: gmail.com
  username: youremail@gmail.com
  password: yourpassword
  auth: plain
  starttls_auto: true
  sender: youremail@gmail.com
```

#### LDAP

You can enable LDAP authentication by providing values for the variables below.
Configuring LDAP authentication will take precedence over all other providers.
For information about setting up LDAP, see: <https://docs.bigbluebutton.org/greenlight/gl-config.html#ldap-auth>

Example Setup:

```yaml
bbb_greenlight_ldap:
  server: ldap.example.com
  port: 389
  method: plain
  uid: uid
  base: dc=example,dc=com
  bind_dn: cn=admin,dc=example,dc=com
  password: password
  role_field: ou
```

#### GOOGLE_OAUTH2

For in-depth steps on setting up a Google Login Provider, see:  <https://docs.bigbluebutton.org/greenlight/gl-config.html#google-oauth2>
The `bbb_greenlight_google_oauth2.hd` variable is used to limit sign-ins to a particular set of Google Apps hosted domains. This can be a string with separating commas such as, 'domain.com, example.com' or a string that specifies a single domain restriction such as, 'domain.com'. If left blank, GreenLight will allow sign-in from all Google Apps hosted domains.

```yaml
bbb_greenlight_google_oauth2:
  id:
  secret:
  hd:
```

#### OFFICE365

For in-depth steps on setting up a Office 365 Login Provider, see: <https://docs.bigbluebutton.org/greenlight/gl-config.html#office365-oauth2>

```yaml
bbb_greenlight_office365:
    id:
    secret:
    hd:
```

#### In Application Authentication

By default, the ability for anyone to create a Greenlight account is enabled. To disable this, use `false`.
For more information see: <https://docs.bigbluebutton.org/greenlight/gl-config.html#in-application-greenlight>

```yaml
bbb_greenlight_accounts: false
```

#### RECAPTCHA

To enable reCaptcha on the user sign up, define these 2 keys.
You can obtain these keys by registering your domain using the following url: <https://www.google.com/recaptcha/admin>

```yaml
bbb_greenlight_recaptcha:
  site_key:
  secret_key:
```

#### METEOR

With settings `bbb_meteor` it is possible to overwrite / change settings of meteor.

The following example is from [infra.run](https://gitlab.com/infra.run/public/ansible-bigbluebutton-tiny/-/blob/bbb-2.3/defaults/main.yml)

```yaml
bbb_meteor:
  public:
    note:
      url: "https://{{ inventory_hostname }}/pad"
    app:
      skipCheck: false
      mirrorOwnWebcam: true
      enableMultipleCameras: true
      enableNetworkInformation: true
      breakoutRoomLimit: 16
    chat:
      bufferChatInsertsMs: 100
      typingIndicator:
        enabled: false
    media:
      sipjsHackViaWs: true
    kurento:
      wsUrl: "wss://{{ inventory_hostname }}/bbb-webrtc-sfu"
      cameraProfiles:
      - id: low-u30
        name: low-u30
        bitrate: 30
        hidden: true
        constraints:
          frameRate: 3
      - id: low-u25
        name: low-u25
        bitrate: 40
        hidden: true
        constraints:
          frameRate: 3
      - id: low-u20
        name: low-u20
        bitrate: 50
        hidden: true
        constraints:
          frameRate: 5
      - id: low-u15
        name: low-u15
        bitrate: 70
        hidden: true
        constraints:
          frameRate: 8
      - id: low-u12
        name: low-u12
        bitrate: 90
        hidden: true
        constraints:
          frameRate: 10
      - id: low-u8
        name: low-u8
        bitrate: 100
        hidden: true
        constraints:
          frameRate: 10
      - id: low
        name: Low quality
        default: false
        bitrate: 50
      - id: medium
        name: Medium quality
        default: true
        bitrate: 200
      - id: high
        name: High quality
        default: false
        bitrate: 500
      - id: hd
        name: High definition
        default: false
        bitrate: 1200
      cameraQualityThresholds:
        enabled: true
        thresholds:
          - threshold: 8
            profile: low-u8
          - threshold: 12
            profile: low-u12
          - threshold: 15
            profile: low-u15
          - threshold: 20
            profile: low-u20
          - threshold: 25
            profile: low-u25
          - threshold: 30
            profile: low-u30
      cameraTimeouts:
        baseTimeout: 30000
      pagination:
        enabled: true
        pageChangeDebounceTime: 2500
        desktopPageSizes:
          moderator: 16
          viewer: 16
        mobilePageSizes:
          moderator: 8
          viewer: 8
```

### LXD/LXC compatibility

To run BigBlueButton in unprivileged LXD/LXC containers, you have to set `bbb_container_compat` to `true`.

### Phone dial-in

Example configuration using [sipgate](https://sipgate.de) for dial-in. Be sure to check with your provider if this usage is permitted.

```yaml
bbb_dialin_enabled: true
bbb_dialin_provider_proxy: 'sipgate.de'
bbb_dialin_provider_username: '158d43584d'
bbb_dialin_provider_password: 'xxxx-secret-xxxx'
bbb_dialin_provider_extension: '133713374223'
bbb_dialin_default_number: '0133 713-337-4223'
bbb_dialin_mask_caller: true
bbb_dialin_overwrite_footer: true
```

## Dependencies

- [geerlingguy.nodejs](https://github.com/geerlingguy/ansible-role-nodejs)
- [geerlingguy.docker](https://github.com/geerlingguy/ansible-role-docker)

## Example Playbook

This is an example, of how to use this role. Warning: the value of the Variables should be changed!

```yaml
    - hosts: servers
      roles:
         - { role: n0emis.bigbluebutton, bbb_turn_secret: ee8d093109a9b273, bbb_greenlight_secret: 107308d54ff4a5f, bbb_greenlight_db_password: 2585c27c785e8895ec, bbb_letsencrypt_email: mail@example.com }
```

## License

MIT

[bbb_freeswitch_ipv6]: https://docs.bigbluebutton.org/2.2/troubleshooting.html#freeswitch-fails-to-bind-to-port-8021
