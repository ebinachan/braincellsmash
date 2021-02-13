# braincellsmash
Logstash hacks for Syslog


Copy combo.conf to /etc/logstash/conf.d/ and restart Logstash.

Tested with: https://grokdebug.herokuapp.com/


Cisco ASA:
```
logging enable
logging timestamp
logging buffer-size 1048576
logging monitor notifications
logging buffered warnings
logging trap notifications
logging asdm errors
logging device-id hostname
logging host inside CENT8GRAFANA2 17/5143

<164>Feb 13 2021 01:02:25 ASAGW-MULLE : %ASA-4-713903: IKE Receiver: Runt ISAKMP packet discarded on Port 4500 from 1.2.3.4:4503
<%{POSINT:pri}>%{MONTH:month} %{MONTHDAY:monthday} %{YEAR:year} %{TIME:time} %{DATA:hostname} : %%{DATA:facility}-%{POSINT:lvl}-%{DATA:facility_mnemonic}: %{GREEDYDATA:message}
```


Cisco IOSXR beta:
```
clock timezone EET 2
service timestamps log datetime localtime msec show-timezone year
service timestamps debug datetime localtime msec show-timezone year
no logging format rfc5424/bsd
logging 10.10.10.10 vrf default severity info port 5141
logging source-interface Loopback0
logging hostnameprefix __DISCOBEAR
logging suppress duplicates

#RFC
<190>1 2020 Dec 11 16:05:32.051 EET __DISCOBEAR config 65960 - - 5300: RP/0/RSP0/CPU0:config[65960]: %HA-HA_WD_LIB-6-UNREG_PROC_SUCCESS : Memory state un-registration is success
#Default XR
<187>883282: MAJDA702 RP/0/RP0/CPU0:2021 Feb 13 10:25:44.652 UTC: tcp[141]: %IP-TCP-3-BADAUTH : Invalid  digest from 1.2.3.4:20923 to 1.2.3.5:179 for vrf:default (0x60000000)
<%{POSINT:pri}>%{POSINT:seq}: %{DATA:hostname} %{DATA:slot}:(%{YEAR:year})? %{MONTH:month} %{MONTHDAY:monthday} %{TIME:time} (%{DATA:tz})?:%{DATA:job_id}: %%{DATA:facility}-%{INT:lvl}-%{DATA:facility_mnemonic} : %{GREEDYDATA:message}
```


Cisco IOS XE beta:
```
clock timezone EET 2 0
service timestamps debug datetime msec localtime show-timezone year
service timestamps log datetime msec localtime show-timezone year
service sequence-numbers
no logging count
logging trap debugging
logging origin-id hostname
logging host 10.10.10.10 transport udp port 5142

<189>995: CAT3850_12XS: 001046: Dec 13 2020 12:43:02.642 EET: %SYS-5-CONFIG_I: Configured from console by username on vty0
<%{POSINT:pri}>(%{POSINT:seq2}: )?(%{DATA:hostname}: )?(%{NUMBER:seq}: )?(\.)?%{DATA:log_date}: %%{DATA:facility}-%{DATA:lvl}-%{DATA:facility_mnemonic}: %{GREEDYDATA:message}
```


NE40:
```
info-center source default channel 0 log state off trap state off
info-center source default channel 4 log level debugging
info-center loghost 10.10.10.10 source-ip 1.1.1.1 port 5145 local-time
info-center timestamp log format-date precision-time millisecond
info-center timestamp trap format-date precision-time millisecond
        
<188>2020-12-12 11:09:22.341+02:00 NEHOSTNAME %%01MRM/4/SELFHEAL_VERIFY(l):CID=0x80de272b;The multicast business has been repaired by the self-healing operation.(CompName=Pimcore, Event=The Dns Oif of PIM Route, GrpAddr=239.253.0.0, SrcAddr=192.168.103.11, Instance=_public_, Param=The downstream interface was updated, ifx=12775, ifs=0x200, isDr=1, opt=ADD).
<%{POSINT:pri}>%{TIMESTAMP_ISO8601:log_date}%{SPACE}%{DATA:hostname}%{SPACE}%%%{DATA:facility}/%{POSINT:lvl}/%{DATA:facility_mnemonic}:%{DATA:cid};%{GREEDYDATA:message}
```


Nokia SAS:
```
/configure log syslog 1 log-prefix 7210SASMXPLAB1
/configure log syslog 1 description LOGSTASH
/configure log syslog 1 address 10.10.10.10
/configure log syslog 1 port 5144
/configure log log-id 85 time-format local

<186>Dec 12 18:52:49 2.2.2.86 7210SASMXPLAB1: 5427 Base SYSTEM-MAJOR-ssiSaveConfigSucceeded-2002 [Configuration Save Succeeds]:  Configuration file saved to: cf2:\\configR11.cfg\n
<%{POSINT:pri}>%{CISCOTIMESTAMP:log_date} %{DATA:hostip} %{DATA:hostname}: %{POSINT:seq} %{DATA:base} %{DATA:facility}-%{DATA:lvl}-%{DATA:facility_mnemonic}-%{GREEDYDATA:message}
```
