icinga2 Cookbook
==================

[![Build Status](https://travis-ci.org/vkhatri/chef-icinga2.svg?branch=master)](https://travis-ci.org/vkhatri/chef-icinga2)

This is a [Chef] cookbook to manage [Icinga2] using LWRP.

>> **WARNING** Cookbook has been tested for existing functionality, however this WARNING is added to notify users that this cookbook is still in development phase and few functionalities are yet to be added.

More features and attributes will be added over time, **feel free to contribute**
what you find missing!


## Repository

https://github.com/vkhatri/chef-icinga2


## Recipes

- `icinga2::default`     	- does not do anything, used for LWRP usage

- `icinga2::server`  		- install & configure icinga2 server with default incinga Objects

- `icinga2::server_apache`		- manages apache and icinga2 classic ui / web / web2 vhost using `apache2` cookbook

- `icinga2::server_install`   		- install icinga2 core/ido server packages

- `icinga2::server_core`   			- configures icinga2 core configuration files & directories

- `icinga2::server_features`   		- enable/disable icinga2 features

- `icinga2::server_classic_ui`   		- configures icinga2 classic ui

- `icinga2::server_ido`   		- configures icinga2 ido db

- `icinga2::server_objects`   		- manages icinga2 default objects/templates objects if `node['icinga2']['disable_conf_d']` is set in which case `conf.d` objects config is not included in `icinga2.conf` and objects are created using LWRP

- `icinga2::server_object_host`   	- creates icinga2 `generic-host` `Host` template

- `icinga2::server_object_notificationcommand`   - creates icinga2 default `NotificationCommand` objects

- `icinga2::server_object_service`   		- creates icinga2 default`generic-service` `Service`  templates

- `icinga2::server_object_timeperiod`   	- creates icinga2 default `TimePeriod` objects

- `icinga2::server_object_user`   			- creates icinga2 `generic-user` `User` template and `icingaadmin` `User` object

- `icinga2::server_object_usergroup`   	- creates icinga2 `icingaadmins` `UserGroup` object


## Attributes

- `icinga2::default` 			- icinga2 server default attributes file

- `icinga2::repo`				- icinga2 yum/apt repositories file for packages install

- `icinga2::server_classic_ui` - icinga2 server classic ui default attributes file

- `icinga2::server_constants`	- icinga2 server constants parameters file

- `icinga2::server_objects`		- icinga2 objects default attributes file

- `icinga2::server_ido`		- icinga2 ido db default attributes file

- `icinga2::server_web2`		- icinga2 web2 ui default attributes file



## Supported Icinga Version

This cookbook is being developed for Icinga2 - v2.2.x primarily on Amazon Platform (EPEL Release 6 Package).


## Supported Icinga2 Install Types

Currently Icinga2 installation is supported **ONLY** via Repository Packages.


## Icinga2 Server Setup

To setup icinga2 server on a node, add recipe `icinga2::server` which will install necessary packages and configuration files.



## Icinga2 Classic UI

Icinga2 Classic UI is enabled by default and set as default UI.

However, Classic UI can be disabled by attribute `node['icinga2']['classic_ui']['enable']`.


**How to add users for icinga2 Classic UI**

Icinga2 Classic UI users is controlled by node Hash attribute `node['icinga2']['classic_ui']['users']`. This attribute accepts a `Hash` of `username => htpasswd(passwd)`, so that password is not available in plain text.

By default `icingaadmin` user is added with password `icingaadmin`:

	default['icinga2']['classic_ui']['users'] = {
	  'icingaadmin' => '$apr1$MZtKRLAy$AV9OiJ.V/mI9g30bHn9ol1'
	}

This can be overrided in wrapper cookbook or role or environment.

To add more users for icigna2 Classic UI auth, add new users to Hash attribute in the same wasy `icingaadmin` user is added

	default['icinga2']['classic_ui']['users']['user_name'] = '$apr1$MZtKRLAy$AV9OiJ.V/mI9g30bHn9ol1'

**Icinga2 Classic UI Authorization**


Icigna2 Classic UI User authorization is managed by below node Arrary attributes:

	node['icinga2']['classic_ui']['authorized_for_system_information']
	node['icinga2']['classic_ui']['authorized_for_configuration_information']
	node['icinga2']['classic_ui']['authorized_for_full_command_resolution']
	node['icinga2']['classic_ui']['authorized_for_system_commands']
	node['icinga2']['classic_ui']['authorized_for_all_services']
	node['icinga2']['classic_ui']['authorized_for_all_hosts']
	node['icinga2']['classic_ui']['authorized_for_all_service_commands']
	node['icinga2']['classic_ui']['authorized_for_all_host_commands']

By simply adding users to above attributes will provide necessary access to the UI.



**How to add a guest user**

To add a guest user without any admin privileges, first add a `guest` user (with password `guest`)

	default['icinga2']['classic_ui']['users']['guest'] = '$apr1$cA/eVUgT$aIoWUPwV5uONJoYslb7lg0'

Then authorize `guest` user to view `Host/Service` status

	node['icinga2']['classic_ui']['authorized_for_all_services'] = %w(icingaadmin guest)
	node['icinga2']['classic_ui']['authorized_for_all_hosts'] = %w(icingaadmin guest)



**How to add an admin user**

To make a user `admin`, add the user to below node attributes:

	node['icinga2']['classic_ui']['authorized_for_system_information']
	node['icinga2']['classic_ui']['authorized_for_configuration_information']
	node['icinga2']['classic_ui']['authorized_for_full_command_resolution']
	node['icinga2']['classic_ui']['authorized_for_system_commands']
	node['icinga2']['classic_ui']['authorized_for_all_services']
	node['icinga2']['classic_ui']['authorized_for_all_hosts']
	node['icinga2']['classic_ui']['authorized_for_all_service_commands']
	node['icinga2']['classic_ui']['authorized_for_all_host_commands']



## Icinga Web2

Though icingaweb2 is currently in development phase, this cookbook is intended to use
icingaweb2 and recipe is in progress.



## Icinga2 Cluster Deployment

Currently this cookbook does **NOT** contain any information to setup icinga2 Cluster.



## Icinga2 Monitor a Chef Environment Nodes

This cookbook does not only provide management of Icinga2 server & objects, it also provides
automation around Chef environment.

Using LWRP `environment` a whole environment nodes can be added to `Host` objects with environment wide `Host` object parameters.

There are certain functionalities added to LWRP `environment`, like:

- define icinga2 `Host` parameters for an entire environment

- auto create `HostGroup` object for an entire chef environment

- auto create `HostGroup` object for node's `application` attribute to group nodes for an entire chef environment `application` type

- auto create `HostGroup` object for node's `cluster` attribute to group nodes for a chef environment `cluster`

- auto add chef node Cloud attributes as `Host` custom `vars`, currently only AWS EC2 attributes are supported, but is easy to extend the suport to other cloud providers

- auto create `HostGroup` list for a chef environment node

- limit a chef environment spreaded across multiple regions to icinga2 server region, e.g. in multi region ec2 production environment, one would want to setup an icinga2 server in region `us-east-1` just to monitor `us-east-1` nodes, but not the production nodes of other regions, like `ap-southeast-1`

- allow chef node to determine `host.address` from `node['fqdn']` DNS resolution instead of `node['ipaddress']` and either ignore chef node if failed to resolve DNS or fallback to use `node['ipaddress']` as `host.address`

- exclude a node by `run_list` role, not yet tested

- exclude a node by `run_list` role, not yet tested

- filter chef node if match certain node attributes

- override an environment and use an entire different `search_pattern`, this feature extends LWRP `environment` functionality to select nodes by a user given search pattern


Simply create a LWRP resource for a chef environment, to start monitoring all nodes in that environment. More details are added to examples.


## Icinga2 Host Custom Vars

**environment LWRP Host Vars**

LWRP `environment` resources sets Host custom vars for each node via node `Hash` attribute -
`node['icinga2']['client']['custom_vars']`. All defined `vars` will be added to `Host` object.


**host LWRP Host Vars**

When using `icinga2_host` LWRP, node custom vars will not be added automatically. There will be no search performed as the `Host` object could be different than a chef `Node`.

To add `Host` custom vars, use Hash attribute `custom_vars`.

A resource attribute will be added to `icinga2_host` LWRP to perform a search to fetch custom vars defined for a node, so that manual addition is not required.



## Client Setup

NRPE Client recipe has been removed from this cookbook.

Icinga2 Agent management will be added soon.


## LWRP Examples

Different LWRP usage examples are added to `examples` directory.

To configure icinga2 server, check `examples/icinga2_server` directory. More exaamples will be added here upon further testing.




## icinga2 LWRP Resources

Currently icinga2 cookbook supports below Objects LWRP Resources:

- icinga2_apilistener
- icinga2_applynotification
- icinga2_applyservice
- icinga2_checkcommand
- icinga2_endpoint
- icinga2_envhostgroup
- icinga2_environment
- icinga2_eventcommand
- icinga2_externalcommandlistener
- icinga2_gelfwriter
- icinga2_graphitewriter
- icinga2_host
- icinga2_hostgroup
- icinga2_notification
- icinga2_notificationcommand
- icinga2_scheduleddowntime
- icinga2_service
- icinga2_servicegroup
- icinga2_sysloglogger
- icinga2_timeperiod
- icinga2_user
- icinga2_usergroup
- icinga2_zone

Few of LWRP attributes which are required to create an icinga2 Object are not declared `:required => true` in LWRP in favour of creating icinga2 Object template.

Same LWRP resource used to create icinga2 `Object`, can also be used to create icinga2 `Template` as well.



## LWRP icinga2_environment

 To be added.


## LWRP icinga2_applydependency

 To be added.


## LWRP icinga2_applynotification

 To be added.


## LWRP icinga2_applyservice

 To be added.


## LWRP icinga2_checkcommand

 To be added.


## LWRP icinga2_downtime

 To be added.


## LWRP icinga2_envhostgroup

 To be added.


## LWRP icinga2_eventcommand

 To be added.


## LWRP icinga2_host

 To be added.


## LWRP icinga2_hostgroup

 To be added.


## LWRP icinga2_notification

 To be added.


## LWRP icinga2_notificationcommand

 To be added.


## LWRP icinga2_service

 To be added.


## LWRP icinga2_servicegroup

 To be added.


## LWRP icinga2_timeperiod

 To be added.


## LWRP icinga2_user

 To be added.


## LWRP icinga2_usergroup

 To be added.


## LWRP icinga2_zone

 To be added.


## Cookbook Advanced Attributes

* `default['icinga2']['disable_conf_d']` (default: `false`): disable icinga2 `conf.d` default configuration and use LWRP to manage icinga2 objects / templates

* `default['icinga2']['add_cloud_custom_vars']` (default: `true`): add cloud node attributes, limited cloud provider support is available

* `default['icinga2']['features_enabled_dir']` (default: `/etc/icinga2/features-enabled`): icinga2 enabled features location

* `default['icinga2']['features_available_dir']` (default: `/etc/icinga2/features-available`): icinga2 available features location

* `default['icinga2']['cluster_attribute']` (default: `nil`): icinga2 node cluster attribute name to add node cluster attribute and value to node vars

* `default['icinga2']['application_attribute']` (default: `nil`): icinga2 node application attribute name to add node application attribute and value to node vars

* `default['icinga2']['enable_cluster_hostgroup']` (default: `true`): creates icinga2 HostGroup Objects for environment clusters

* `default['icinga2']['enable_application_hostgroup']` (default: `true`): creates icinga2 HostGroup Objects for environment application types

* `default['icinga2']['enable_role_hostgroup']` (default: `false`): creates icinga2 HostGroup Objects for environment roles

* `default['icinga2']['limit_region']` (default: `true`): whether to limit monitoring to icinga2 server region, e.g. for ec2 collect nodes belongs to same region

* `default['icinga2']['use_fqdn_resolv']` (default: `false`): whether to determine node `address` from fqdn

* `default['icinga2']['failover_fqdn_address']` (default: `true`): whether to use ohai attribute `node['ipaddress']` if node fqdn does not exists

* `default['icinga2']['ignore_node_error']` (default: `false`): whether to ignore node errors if node fqdn, hostname and chef_environment is missing while collecting for a chef environment

* `default['icinga2']['ignore_resolv_error']` (default: `true`): whether to ignore node fqdn resolve errors while collecting for a chef environment

* `default['icinga2']['web_engine']` (default: `'apache'): icinga2 web server, currently supports only apache

* `default['icinga2']['install_nagios_plugins']` (default: `true`): install nagios plugins to icinga2 server and clients

* `default['icinga2']['features']` (default: `[available features]`): list of icinga2 available features

* `default['icinga2']['enable_features']` (default: `[checker command compatlog mainlog notification statusdata perfdata]`): icinga2 enabled features


## Cookbook Core Attributes

* `default['icinga2']['version']` (default: `2.2.2-1`): icinga2 version

* `default['icinga2']['conf_dir']` (default: `/etc/icinga2`): icinga2 configuration location

* `default['icinga2']['conf_d_dir']` (default: `/etc/icinga2/conf.d`): icinga2 conf.d directory location

* `default['icinga2']['pki_dir']` (default: `/etc/icinga2/pki`): icinga2 pki directory location

* `default['icinga2']['scripts_dir']` (default: `/etc/icinga2/scripts`): icinga2 script directory location

* `default['icinga2']['zones_dir']` (default: `/etc/icinga2/zones.d`): icinga2 zones.d directory location

* `default['icinga2']['databag']` (default: `'icinga2'): icinga2 databag name, currently not used

* `default['icinga2']['objects_d']` (default: `'objects.d`): cookbook created icinga2 Object/Templates resources directory name

* `default['icinga2']['objects_dir']` (default: `/etc/icinga2/objects.d`): cookbook created icinga2 Object/Templates resources directory location

* `default['icinga2']['run_dir']` (default: `/var/run/icinga2`): icinga2 run directory

* `default['icinga2']['run_cmd_dir']` (default: `/var/run/icinga2/cmd`): icinga2 location for process `icinga2.cmd`

* `default['icinga2']['cache_dir']` (default: `/var/cache/icinga2`): icinga2 cache directory location

* `default['icinga2']['spool_dir']` (default: `/var/spool/icinga2`): icinga2 spool directory location

* `default['icinga2']['lib_dir']` (default: `/var/lib/icinga2`): icinga2 lib directory location

* `default['icinga2']['log_dir']` (default: `/var/log/icinga2`): icinga2 core process log directory location

* `default['icinga2']['cache_dir']` (default: `/var/cache/icinga2`): icinga2 cache directory location

* `default['icinga2']['service_name']` (default: `icinga2'`): icinga2 process name*

* `default['icinga2']['service_config_file']` (default: `/etc/default/icinga2`): icinga2 * process configuration file

* `default['icinga2']['plugins_dir']` (default: `/usr/lib/nagios/plugins`): icinga2 plugins directory location

* `default['icinga2']['custom_plugins_dir']` (default: `/opt/icinga2_custom_plugins`): icinga2 custom plugins directory

* `default['icinga2']['admin_user']` (default: `icingaadmin`): icinga2 admin user

* `default['icinga2']['user']` (default: `icinga`): icinga2 user

* `default['icinga2']['group']` (default: `icinga`): icinga2 user group

* `default['icinga2']['cmdgroup']` (default: `icingacmd`): icinga2 cmd user group


## Cookbook Icinga2 Constants Attributes


* `default['icinga2']['server']['constants']['PluginDir']` (default: `node['icinga2']['plugins_dir']`): icinga2 plugins directory location

* `default['icinga2']['server']['constants']['ManubulonPluginDir']` (default: `node['icinga2']['plugins_dir']`): icinga2 plugins directory location

* `default['icinga2']['server']['constants']['TicketSalt']` (default: `ch2ng3m3`): icinga2 default TicketSalt key


## Cookbook Icinga2 Host Object default Attributes

* `default['icinga2']['server']['object']['host']['import']` (default: `'generic-host`)

* `default['icinga2']['server']['object']['host']['max_check_attempts']` (default: `3`)

* `default['icinga2']['server']['object']['host']['check_period']` (default: `nil`)

* `default['icinga2']['server']['object']['host']['check_interval']` (default: `1800`)

* `default['icinga2']['server']['object']['host']['retry_interval']` (default: `60`)

* `default['icinga2']['server']['object']['host']['enable_notifications']` (default: `true`)

* `default['icinga2']['server']['object']['host']['enable_active_checks']` (default: `true`)

* `default['icinga2']['server']['object']['host']['enable_passive_checks']` (default: `false`)

* `default['icinga2']['server']['object']['host']['enable_event_handler']` (default: `true`)

* `default['icinga2']['server']['object']['host']['enable_flapping']` (default: `true`)

* `default['icinga2']['server']['object']['host']['enable_perfdata']` (default: `true`)

* `default['icinga2']['server']['object']['host']['event_command']` (default: `nil`)

* `default['icinga2']['server']['object']['host']['flapping_threshold']` (default: `nil`)

* `default['icinga2']['server']['object']['host']['volatile']` (default: `nil`)

* `default['icinga2']['server']['object']['host']['check_command']` (default: `hostalive`)

* `default['icinga2']['server']['object']['host']['zone']` (default: `nil`)

* `default['icinga2']['server']['object']['host']['command_endpoint']` (default: `nil`)

* `default['icinga2']['server']['object']['host']['zone_attribute']` (default: `icinga2_zone`)




## Cookbook Ulimit Attributes

 * `default[:icinga2][:limits][:memlock]` (default: `unlimited`): Icinga2 service user memory limit

 * `default[:icinga2][:limits][:nofile]` (default: `48000`): Icinga2 service user file limit

 * `default[:icinga2][:limits][:nproc]` (default: `unlimited`): Icinga2 service user process limit


## Cookbook Repository Attributes

* `default['icinga2']['yum']['description']` (default: `ICINGA Stable Release'):

* `default['icinga2']['yum']['mirrorlist']` (default: `nil`):

* `default['icinga2']['yum']['gpgcheck']` (default: `true`):

* `default['icinga2']['yum']['enabled']` (default: `true`):

* `default['icinga2']['yum']['gpgkey']` (default: `http://packages.icinga.org/icinga.key`):

* `default['icinga2']['yum']['action']` (default: `:create`):

* `default['icinga2']['yum']['baseurl']` (default: ``):

* `default['icinga2']['apt']['repo']` (default: `ICINGA Stable Release`):

* `default['icinga2']['apt']['uri']` (default: `http://ppa.launchpad.net/formorer/icinga/ubuntu`):

* `default['icinga2']['apt']['distribution']` (default: `node['lsb']['codename']`):

* `default['icinga2']['apt']['keyserver']` (default: `keyserver.ubuntu.com`):

* `default['icinga2']['apt']['components']` (default: `[main]`):

* `default['icinga2']['apt']['deb_src']` (default: `true`):

* `default['icinga2']['apt']['repo_key']` (default: `http://packages.icinga.org/icinga.key`):

* `default['icinga2']['apt']['action']` (default: `:add`):


## Cookbook Classic UI CGI Core Attributes

* `default['icinga2']['classic_ui']['version']` (default: `2.2.2-1`): icinga2 classic-ui package version

* `default['icinga2']['classic_ui']['gui_version']` (default: `1.12.0-0`): icinga2 gui package version

* `default['icinga2']['classic_ui']['web_root']` (default: `/usr/share/icinga`): icinga2 web doc root directory location

* `default['icinga2']['classic_ui']['home_dir']` (default: `/etc/icinga`): icinga2 classic ui configuration home directory location

* `default['icinga2']['classic_ui']['conf_dir']` (default: `/etc/icinga`): icinga2 classic ui configuration directory location

* `default['icinga2']['classic_ui']['log_dir']` (default: `/var/log/icinga`): icinga2 classic ui log directory location

* `default['icinga2']['classic_ui']['cgi_log_dir']` (default: `/var/log/icinga/gui`): icinga2 gui log directory location


## Cookbook Classic UI CGI User Access Attributes

* `default['icinga2']['classic_ui']['users']` (default: `{ 'icingaadmin' => '$apr1$MZtKRLAy$AV9OiJ.V/mI9g30bHn9ol1' }`): Hash List of user => password (md5) for class ui access

* `default['icinga2']['classic_ui']['authorized_for_system_information']` (default: `[node['icinga2']['admin_user']]`): Array list of users authorized for system information

* `default['icinga2']['classic_ui']['authorized_for_configuration_information']` (default: `[node['icinga2']['admin_user']]`): Array list of users authorized for configuration information

* `default['icinga2']['classic_ui']['authorized_for_full_command_resolution']` (default: `[node['icinga2']['admin_user']]`): Array list of users authorized for full command resolution

* `default['icinga2']['classic_ui']['authorized_for_system_commands']` (default: `[node['icinga2']['admin_user']]`): Array list of users authorized for system commands

* `default['icinga2']['classic_ui']['authorized_for_all_services']` (default: `[node['icinga2']['admin_user']]`): Array list of users authorized for all services

* `default['icinga2']['classic_ui']['authorized_for_all_hosts']` (default: `[node['icinga2']['admin_user']]`): Array list of users authorized for all hosts

* `default['icinga2']['classic_ui']['authorized_for_all_service_commands']` (default: `[node['icinga2']['admin_user']]`): Array list of users authorized for all service commands

* `default['icinga2']['classic_ui']['authorized_for_all_host_commands']` (default: `[node['icinga2']['admin_user']]`): Array list of users authorized for all host commands


## Cookbook Classic UI CGI Configuration Attributes

* `default['icinga2']['classic_ui']['cgi']['standalone_installation']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['physical_html_path']` (default: `/usr/share/icinga`)

* `default['icinga2']['classic_ui']['cgi']['url_html_path']` (default: `/icinga`)

* `default['icinga2']['classic_ui']['cgi']['url_stylesheets_path']` (default: `/icinga/stylesheets`)

* `default['icinga2']['classic_ui']['cgi']['http_charset']` (default: `utf-8`)

* `default['icinga2']['classic_ui']['cgi']['refresh_rate']` (default: `60`)

* `default['icinga2']['classic_ui']['cgi']['refresh_type']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['escape_html_tags']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['result_limit']` (default: `50`)

* `default['icinga2']['classic_ui']['cgi']['show_tac_header']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['use_pending_states']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['first_day_of_week']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['suppress_maintenance_downtime']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['action_url_target']` (default: `main`)

* `default['icinga2']['classic_ui']['cgi']['notes_url_target']` (default: `main`)

* `default['icinga2']['classic_ui']['cgi']['use_authentication']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['use_ssl_authentication']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['lowercase_user_name']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['authorized_for_system_information']` (default: `node['icinga2']['classic_ui']['authorized_for_system_information'].join(',')`)

* `default['icinga2']['classic_ui']['cgi']['authorized_for_configuration_information']` (default: `node['icinga2']['classic_ui']* ['authorized_for_configuration_information'].join(',')`)

* `default['icinga2']['classic_ui']['cgi']['authorized_for_full_command_resolution']` (default: `node['icinga2']['classic_ui']['authorized_for_full_command_resolution'].join(',')`)

* `default['icinga2']['classic_ui']['cgi']['authorized_for_system_commands']` (default: `node['icinga2']['classic_ui']['authorized_for_system_commands'].join(',')`)

* `default['icinga2']['classic_ui']['cgi']['authorized_for_all_services']` (default: `node['icinga2']['classic_ui']['authorized_for_all_services'].join(',')`)

* `default['icinga2']['classic_ui']['cgi']['authorized_for_all_hosts']` (default: `node['icinga2']['classic_ui']['authorized_for_all_hosts'].join(',')`)

* `default['icinga2']['classic_ui']['cgi']['authorized_for_all_service_commands']` (default: `node['icinga2']['classic_ui']['authorized_for_all_service_commands'].join(',')`)

* `default['icinga2']['classic_ui']['cgi']['authorized_for_all_host_commands']` (default: `node['icinga2']['classic_ui']['authorized_for_all_host_commands'].join(',')`)

* `default['icinga2']['classic_ui']['cgi']['show_all_services_host_is_authorized_for']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['show_partial_hostgroups']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['show_partial_servicegroups']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['default_statusmap_layout']` (default: `5`)

* `default['icinga2']['classic_ui']['cgi']['status_show_long_plugin_output']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['display_status_totals']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['highlight_table_rows']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['add_notif_num_hard']` (default: `28`)

* `default['icinga2']['classic_ui']['cgi']['add_notif_num_soft']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['use_logging']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['cgi_log_file']` (default: `node['icinga2']['classic_ui']['cgi_log_dir']/icinga-cgi.log`)

* `default['icinga2']['classic_ui']['cgi']['cgi_log_rotation_method']` (default: `d`)

* `default['icinga2']['classic_ui']['cgi']['cgi_log_archive_path']` (default: `node['icinga2']['classic_ui']['cgi_log_dir']`)

* `default['icinga2']['classic_ui']['cgi']['enforce_comments_on_actions']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['send_ack_notifications']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['persistent_ack_comments']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['lock_author_names']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['default_downtime_duration']` (default: `7200`)

* `default['icinga2']['classic_ui']['cgi']['set_expire_ack_by_default']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['default_expiring_acknowledgement_duration']` (default: `86_400`)

* `default['icinga2']['classic_ui']['cgi']['default_expiring_disabled_notifications_duration']` (default: `86_400`)

* `default['icinga2']['classic_ui']['cgi']['tac_show_only_hard_state']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['show_tac_header_pending']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['exclude_customvar_name']` (default: `PASSWORD,COMMUNITY`)

* `default['icinga2']['classic_ui']['cgi']['exclude_customvar_value']` (default: `secret`)

* `default['icinga2']['classic_ui']['cgi']['extinfo_show_child_hosts']` (default: `)

* `default['icinga2']['classic_ui']['cgi']['tab_friendly_titles']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['object_cache_file']` (default: `/var/cache/icinga2/objects.cache`)

* `default['icinga2']['classic_ui']['cgi']['status_file']` (default: `node['icinga2']['cache_dir']/status.dat`)

* `default['icinga2']['classic_ui']['cgi']['resource_file']` (default: `node['icinga2']['classic_ui']['conf_dir']/resource.cfg`)

* `default['icinga2']['classic_ui']['cgi']['command_file']` (default: `node['icinga2']['run_dir']/cmd/icinga2.cmd`)

* `default['icinga2']['classic_ui']['cgi']['check_external_commands']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['interval_length']` (default: `60`)

* `default['icinga2']['classic_ui']['cgi']['status_update_interval']` (default: `10`)

* `default['icinga2']['classic_ui']['cgi']['log_file']` (default: `node['icinga2']['log_dir']/compat/icinga.log`)

* `default['icinga2']['classic_ui']['cgi']['log_rotation_method']` (default: `h`)

* `default['icinga2']['classic_ui']['cgi']['log_archive_path']` (default: `node['icinga2']['log_dir']/compat/archives`)

* `default['icinga2']['classic_ui']['cgi']['date_format']` (default: `us`)


## Cookbook Dependencies

* `ulimit` cookbook
* `apache2` cookbook
* `yum` cookbook
* `apt` cookbook


## Contributing

1. Fork the repository on Github
2. Create a named feature branch (like `add_component_x`)
3. Write your change
4. Write tests for your change (if applicable)
5. Run the tests (`rake`), ensuring they all pass
6. Write new resource/attribute description to `README.md`
7. Write description about changes to PR
8. Submit a Pull Request using Github


## Copyright & License

Authors:: Virender Khatri and [Contributors]

<pre>
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
</pre>


[Icinga2]: https://www.icinga.org/
[Chef]: https://www.chef.io/
[Contributors]: https://github.com/vkhatri/chef-icinga2/graphs/contributors
[Install]: https://github.com/icinga/icinga2/