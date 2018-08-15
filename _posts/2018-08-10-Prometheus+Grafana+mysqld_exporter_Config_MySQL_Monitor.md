---
layout:     post
title:      Prometheus Grafana mysqld_exporter搭建MySQL监控系统
subtitle:   MySQL监控系统
date:       2018-07-17
author:     Spencer
header-img: img/post-bg-coffee.jpeg
catalog: true
tags:
    - MySQL
    - Prometheus
    - Grafana
    - mysqld_exporter
---

# Prometheus Grafana mysqld_exporter 搭建MySQL监控系统
## 相关链接
Grafana:<https://grafana.com/grafana/download?platform=mac>
Prometheus:<https://prometheus.io/download/>

## 解压安装

```shell
tar -zxvf prometheus-2.3.2.darwin-amd64.tar.gz
tar -zxvf grafana-5.2.2.darwin-amd64.tar.gz
tar -zxvf mysqld_exporter-0.11.0.darwin-amd64.tar.gz
```

## 启动Grafana

```shell
➜  monitor cd grafana-5.2.2/bin
➜  bin ./grafana-server
INFO[08-10|10:01:07] Starting Grafana                         logger=server version=5.2.2 commit=aeaf7b2 compiled=2018-07-25T19:17:28+0800
INFO[08-10|10:01:07] Config loaded from                       logger=settings file=/Users/spencerchang/work/monitor/grafana-5.2.2/conf/defaults.ini
INFO[08-10|10:01:07] Path Home                                logger=settings path=/Users/spencerchang/work/monitor/grafana-5.2.2
INFO[08-10|10:01:07] Path Data                                logger=settings path=/Users/spencerchang/work/monitor/grafana-5.2.2/data
INFO[08-10|10:01:07] Path Logs                                logger=settings path=/Users/spencerchang/work/monitor/grafana-5.2.2/data/log
INFO[08-10|10:01:07] Path Plugins                             logger=settings path=/Users/spencerchang/work/monitor/grafana-5.2.2/data/plugins
INFO[08-10|10:01:07] Path Provisioning                        logger=settings path=/Users/spencerchang/work/monitor/grafana-5.2.2/conf/provisioning
INFO[08-10|10:01:07] App mode production                      logger=settings
INFO[08-10|10:01:07] Initializing SqlStore                    logger=server
INFO[08-10|10:01:07] Connecting to DB                         logger=sqlstore dbtype=sqlite3
INFO[08-10|10:01:07] Starting DB migration                    logger=migrator
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create migration_log table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create user table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add unique index user.login"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add unique index user.email"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="drop index UQE_user_login - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="drop index UQE_user_email - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Rename table user to user_v1 - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create user table v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index UQE_user_login - v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index UQE_user_email - v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="copy data_source v1 to v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Drop old table user_v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add column help_flags1 to user table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update user table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add last_seen_at column to user"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create temp user table v1-7"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index IDX_temp_user_email - v1-7"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index IDX_temp_user_org_id - v1-7"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index IDX_temp_user_code - v1-7"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index IDX_temp_user_status - v1-7"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update temp_user table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create star table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add unique index star.user_id_dashboard_id"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create org table v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index UQE_org_name - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create org_user table v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index IDX_org_user_org_id - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index UQE_org_user_org_id_user_id - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update org table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update org_user table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Migrate all Read Only Viewers to Viewers"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create dashboard table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index dashboard.account_id"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add unique index dashboard_account_id_slug"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create dashboard_tag table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add unique index dashboard_tag.dasboard_id_term"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="drop index UQE_dashboard_tag_dashboard_id_term - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Rename table dashboard to dashboard_v1 - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create dashboard v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index IDX_dashboard_org_id - v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index UQE_dashboard_org_id_slug - v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="copy dashboard v1 to v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="drop table dashboard_v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="alter dashboard.data to mediumtext v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add column updated_by in dashboard - v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add column created_by in dashboard - v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add column gnetId in dashboard"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add index for gnetId in dashboard"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add column plugin_id in dashboard"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add index for plugin_id in dashboard"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add index for dashboard_id in dashboard_tag"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update dashboard table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update dashboard_tag table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add column folder_id in dashboard"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add column isFolder in dashboard"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add column has_acl in dashboard"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add column uid in dashboard"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update uid column values in dashboard"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add unique index dashboard_org_id_uid"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Remove unique index org_id_slug"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update dashboard title length"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add unique index for dashboard_org_id_title_folder_id"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create dashboard_provisioning"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Rename table dashboard_provisioning to dashboard_provisioning_tmp_qwerty - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create dashboard_provisioning v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index IDX_dashboard_provisioning_dashboard_id - v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index IDX_dashboard_provisioning_dashboard_id_name - v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="copy dashboard_provisioning v1 to v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="drop dashboard_provisioning_tmp_qwerty"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add check_sum column"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create data_source table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index data_source.account_id"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add unique index data_source.account_id_name"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="drop index IDX_data_source_account_id - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="drop index UQE_data_source_account_id_name - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Rename table data_source to data_source_v1 - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create data_source table v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index IDX_data_source_org_id - v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index UQE_data_source_org_id_name - v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="copy data_source v1 to v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Drop old table data_source_v1 #2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add column with_credentials"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add secure json data column"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update data_source table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update initial version to 1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add read_only data column"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create api_key table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index api_key.account_id"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index api_key.key"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index api_key.account_id_name"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="drop index IDX_api_key_account_id - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="drop index UQE_api_key_key - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="drop index UQE_api_key_account_id_name - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Rename table api_key to api_key_v1 - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create api_key table v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index IDX_api_key_org_id - v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index UQE_api_key_key - v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index UQE_api_key_org_id_name - v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="copy api_key v1 to v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Drop old table api_key_v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update api_key table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create dashboard_snapshot table v4"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="drop table dashboard_snapshot_v4 #1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create dashboard_snapshot table v5 #2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index UQE_dashboard_snapshot_key - v5"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index UQE_dashboard_snapshot_delete_key - v5"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index IDX_dashboard_snapshot_user_id - v5"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="alter dashboard_snapshot to mediumtext v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update dashboard_snapshot table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create quota table v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index UQE_quota_org_id_user_id_target - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update quota table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create plugin_setting table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index UQE_plugin_setting_org_id_plugin_id - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add column plugin_version to plugin_settings"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update plugin_setting table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create session table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Drop old table playlist table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Drop old table playlist_item table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create playlist table v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create playlist item table v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update playlist table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update playlist_item table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="drop preferences table v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="drop preferences table v3"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create preferences table v3"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update preferences table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create alert table v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index alert org_id & id "
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index alert state"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index alert dashboard_id"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create alert_notification table v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add column is_default"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index alert_notification org_id & name"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update alert table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update alert_notification table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Drop old annotation table v4"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create annotation table v5"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index annotation 0 v3"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index annotation 1 v3"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index annotation 2 v3"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index annotation 3 v3"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index annotation 4 v3"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update annotation table charset"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add column region_id to annotation table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Drop category_id index"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add column tags to annotation table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Create annotation_tag table v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add unique index annotation_tag.annotation_id_tag_id"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Update alert annotations and set TEXT to empty"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add created time to annotation table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add updated time to annotation table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add index for created in annotation table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add index for updated in annotation table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Convert existing annotations from seconds to milliseconds"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create test_data table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create dashboard_version table v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index dashboard_version.dashboard_id"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add unique index dashboard_version.dashboard_id and dashboard_version.version"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Set dashboard version to 1 where 0"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="save existing dashboard data in dashboard_version table v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="alter dashboard_version.data to mediumtext v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create team table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index team.org_id"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add unique index team_org_id_name"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create team member table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index team_member.org_id"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add unique index team_member_org_id_team_id_user_id"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Add column email to team table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create dashboard acl table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index dashboard_acl_dashboard_id"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add unique index dashboard_acl_dashboard_id_user_id"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add unique index dashboard_acl_dashboard_id_team_id"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="save default acl rules in dashboard_acl table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create tag table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index tag.key_value"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create login attempt table"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="add index login_attempt.username"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="drop index IDX_login_attempt_username - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="Rename table login_attempt to login_attempt_tmp_qwerty - v1"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create login_attempt v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="create index IDX_login_attempt_username - v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="copy login_attempt v1 to v2"
INFO[08-10|10:01:07] Executing migration                      logger=migrator id="drop login_attempt_tmp_qwerty"
INFO[08-10|10:01:08] Executing migration                      logger=migrator id="create user auth table"
INFO[08-10|10:01:08] Executing migration                      logger=migrator id="create index IDX_user_auth_auth_module_auth_id - v1"
INFO[08-10|10:01:08] Executing migration                      logger=migrator id="alter user_auth.auth_id to length 190"
INFO[08-10|10:01:08] Created default admin user: %v           logger=sqlstore admin=nil LOG15_ERROR="Normalized odd number of arguments by adding nil"
INFO[08-10|10:01:08] Initializing SearchService               logger=server
INFO[08-10|10:01:08] Initializing PluginManager               logger=server
INFO[08-10|10:01:08] Starting plugin search                   logger=plugins
INFO[08-10|10:01:08] Plugin dir created                       logger=plugins dir=/Users/spencerchang/work/monitor/grafana-5.2.2/data/plugins
INFO[08-10|10:01:08] Initializing InternalMetricsService      logger=server
INFO[08-10|10:01:08] Initializing AlertingService             logger=server
INFO[08-10|10:01:08] Initializing HTTPServer                  logger=server
INFO[08-10|10:01:08] Initializing CleanUpService              logger=server
INFO[08-10|10:01:08] Initializing NotificationService         logger=server
INFO[08-10|10:01:08] Initializing ProvisioningService         logger=server
INFO[08-10|10:01:08] Initializing RenderingService            logger=server
INFO[08-10|10:01:08] Initializing TracingService              logger=server
INFO[08-10|10:01:08] Initializing Stream Manager
INFO[08-10|10:01:08] HTTP Server Listen                       logger=http.server address=0.0.0.0:3000 protocol=http subUrl= socket=
```

### 访问Grafana

http://127.0.0.1:3000/
默认登录用户/密码：admin/admin

## 启动Prometheus

```shell
➜  monitor cd prometheus
➜  prometheus ./prometheus --config.file=prometheus.yml
level=info ts=2018-08-10T02:03:15.733159586Z caller=main.go:222 msg="Starting Prometheus" version="(version=2.3.2, branch=HEAD, revision=71af5e29e815795e9dd14742ee7725682fa14b7b)"
level=info ts=2018-08-10T02:03:15.733220203Z caller=main.go:223 build_context="(go=go1.10.3, user=root@5258e0bd9cc1, date=20180712-14:08:00)"
level=info ts=2018-08-10T02:03:15.733233027Z caller=main.go:224 host_details=(darwin)
level=info ts=2018-08-10T02:03:15.733245124Z caller=main.go:225 fd_limits="(soft=7168, hard=9223372036854775807)"
level=info ts=2018-08-10T02:03:15.73375288Z caller=web.go:415 component=web msg="Start listening for connections" address=0.0.0.0:9090
level=info ts=2018-08-10T02:03:15.733711726Z caller=main.go:533 msg="Starting TSDB ..."
level=info ts=2018-08-10T02:03:15.739025457Z caller=main.go:543 msg="TSDB started"
level=info ts=2018-08-10T02:03:15.73906661Z caller=main.go:603 msg="Loading configuration file" filename=prometheus.yml
level=info ts=2018-08-10T02:03:15.739888403Z caller=main.go:629 msg="Completed loading of configuration file" filename=prometheus.yml
level=info ts=2018-08-10T02:03:15.739904772Z caller=main.go:502 msg="Server is ready to receive web requests."
```

### 访问Prometheus

http://localhost:9090/

## 启动mysqld_exporter

```shell
➜  monitor cd mysqld_exporter
➜  mysqld_exporter vi .my.cnf
[client]

host = 127.0.0.1

user=root

password=xxxxx

➜  mysqld_exporter ./mysqld_exporter --config.my-cnf=./.my.cnf
INFO[0000] Starting mysqld_exporter (version=0.11.0, branch=HEAD, revision=5d7179615695a61ecc3b5bf90a2a7c76a9592cdd)  source="mysqld_exporter.go:206"
INFO[0000] Build context (go=go1.10.3, user=root@3d3ff666b0e4, date=20180629-15:01:12)  source="mysqld_exporter.go:207"
INFO[0000] Enabled scrapers:                             source="mysqld_exporter.go:218"
INFO[0000]  --collect.global_status                      source="mysqld_exporter.go:222"
INFO[0000]  --collect.global_variables                   source="mysqld_exporter.go:222"
INFO[0000]  --collect.slave_status                       source="mysqld_exporter.go:222"
INFO[0000]  --collect.info_schema.tables                 source="mysqld_exporter.go:222"
INFO[0000] Listening on :9104                            source="mysqld_exporter.go:232"
```

### 访问mysqld_exporter

http://127.0.0.1:9104/


## 在 Prometheus 中配置 mysqld_exporter
### exporter启动后，需要在 Prometheus 中正确的配置。修改 prometheus 目录中的prometheus.yml增加如下内容：

```YAML
#  - job_name: mysql

  - job_name: mysql

    static_configs:

      - targets: ['127.0.0.1:9104']

        labels:

          instance: sys
```

### 备注:

 * job_name,当前执行job的名字
 * targets，连接的主机
 * instance，数据库实例的标签明

### 重启Prometheus，点击导航栏中的status->targets可以看到，MySQL的exporter已经集成。

## Grafana 中配置 Prometheus
### 点击Add data source
![](https://spencerzhang.github.io/resource/15338689556992.jpg)
### 维护Name，Type
![](https://spencerzhang.github.io/resource/15338690099407.jpg)

### 点击Save & Test显示提示信息：Data source is working，Prometheus配置完成。
### 点击Back，回到首页

## 添加Dashboards,我们选择导入json配置文件，从<https://github.com/percona/grafana-dashboards>获取项目中的dashboards下载MySQL_Overview.json
也可直接下载：[MySQL_Overview.json](https://spencerzhang.github.io/resource/MySQL_Overview.json)
![](https://spencerzhang.github.io/resource/15338694253819.jpg)
### 点击Upload .json File,选择文件导入
![](https://spencerzhang.github.io/resource/15338697907398.jpg)

### Import导入成功后，即跳转到监控页面，配置完成。
![](https://spencerzhang.github.io/resource/15338712730656.jpg)

--EOF--