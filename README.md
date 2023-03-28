# TA-TC-Sysmon

This addon is a complete rewrite and replacement of TA-Microsoft-Sysmon [1] and adds:

* speed-optimized searches via custom eventtypes and macros (tc_sysmon*) that
  are not just based on (very slow) EventID based searches. 
* workarounds that protect from props hijack done by Splunk_TA_windows 6.0.0,
  avoiding sourcetype rename and further extra processing of generic wineventlog
  props (providing no value).
* index-time operation that removes the extra (and static) header garbage caused
  by WinEventLog input that does not provide any value for sysmon logs, saving
  up to 50% of license usage and I/O load. (*)
* index-time sourcetyping to further optimize and categorize the events to
  sourcetype=sysmon:events:*
* sane action values for all event types for better datamodeling, not just using
  useless "allowed" value.


## Sysmon support

This addon is up-to-date with Sysmon 14.13, supporting all its event types.
Further upgrades are needed if new event IDs are introduced.


## Relation to TA-TC-Sysmon-Transforms

TA-TC-Sysmon-Transforms (not included) addon is a supplemental addon for
TA-TC-Sysmon and provides index-time operations, meant to be placed on Splunk
heavy forwarder (HF) nodes or indexers, which ever receives the traffic first
from splunk agents. Usage of the transforming addon is highly encouraged.

This parent package TA-TC-Sysmon contains the search-time knowledge objects, and
is meant to be placed on search heads.

Original events are expected to have sourcetype
XmlWinEventLog:Microsoft-Windows-Sysmon/Operational

If the index-time transform is not done, this package will rename the sourcetype
locally to sysmon:events.


## Compatibility with Splunk CIM 

This addon is fully compatible with Splunk CIM version 5.1 when used with
TA-TC-Sysmon-Transforms addon. See lookups/sysmon_eventcodes.csv for information
on what events are mapped to what CIM datamodels. All events except ID 24
"Clipboard changed" are currently mapped to a CIM datamodel.


## Sourcetypes

If the supplemental addon TA-TC-Sysmon-Transforms is installed on HF/indexers
(recommended), the following sourcetypes will be created:

* EventID 1: "sysmon:events:processcreated"
* EventID 2: "sysmon:events:filectimechanged"
* EventID 3: "sysmon:events:networkconnect"
* EventID 4: "sysmon:events:statechanged"
* EventID 5: "sysmon:events:processterminated"
* EventID 6: "sysmon:events:driverloaded"
* EventID 7: "sysmon:events:imageloaded"
* EventID 8: "sysmon:events:createremotethread"
* EventID 9: "sysmon:events:rawaccessread"
* EventID 10: "sysmon:events:processaccess"
* EventID 11: "sysmon:events:filecreated"
* EventID 12: "sysmon:events:registrycreatedel"
* EventID 13: "sysmon:events:registryvalueset"
* EventID 14: "sysmon:events:registryrename"
* EventID 15: "sysmon:events:filecreatestream"
* EventID 16: "sysmon:events:configchange"
* EventID 17: "sysmon:events:pipecreated"
* EventID 18: "sysmon:events:pipeconnected"
* EventID 19: "sysmon:events:wmifilterregistered"
* EventID 20: "sysmon:events:wmiconsumerregistered"
* EventID 21: "sysmon:events:wmiconsumerbindfilter"
* EventID 22: "sysmon:events:dnsquery"
* EventID 23: "sysmon:events:filedeleted"
* EventID 24: "sysmon:events:clipboardchanged"
* EventID 25: "sysmon:events:processtampering"
* EventID 26: "sysmon:events:filedeletedlog"
* EventID 27: "sysmon:events:fileblockexecutable"
* EventID 28: "sysmon:events:fileblockshredding"
* EventID 255: "sysmon:events:error"
* All the rest (new unknowns): "sysmon:events"

The addon works also with legacy data, with and without the Windows TA.


## Macros

These macros will efficiently search for the data and are not just based on slow
signature_id/EventID search:

* \`tc_sysmon\`
* \`tc_sysmon_process_created\`
* \`tc_sysmon_file_ctime_changed\`
* \`tc_sysmon_network_connect\`
* \`tc_sysmon_state_changed\`
* \`tc_sysmon_process_terminated\`
* \`tc_sysmon_driver_loaded\`
* \`tc_sysmon_image_loaded\`
* \`tc_sysmon_create_remote_thread\`
* \`tc_sysmon_raw_access_read\`
* \`tc_sysmon_process_access\`
* \`tc_sysmon_file_created\`
* \`tc_sysmon_registry\`
* \`tc_sysmon_registry_create_delete\`
* \`tc_sysmon_registry_value_set\`
* \`tc_sysmon_registry_rename\`
* \`tc_sysmon_file_stream_created\`
* \`tc_sysmon_configuration_changed\`
* \`tc_sysmon_pipe_created\`
* \`tc_sysmon_pipe_connected\`
* \`tc_sysmon_wmi_filter_registered\`
* \`tc_sysmon_wmi_consumer_registered\`
* \`tc_sysmon_wmi_consumer_bind_filter\`
* \`tc_sysmon_dns_query\`
* \`tc_sysmon_file_deleted\`
* \`tc_sysmon_clipboard_changed\`
* \`tc_sysmon_process_tampering\`
* \`tc_sysmon_error\`

Example usage:

* \`tc_sysmon\` host=foobar 

These macros search sysmon data from all indexes by default. It is recommended
to further optimize these by explicitly setting the index(es) to use in
\`tc_sysmon_indexes\` macro, or by changing it to use a lookup that
automatically generates the index list via tstats in advance. 



## Author

Markku Parviainen, 2019-2023


## References and thanks to

[1] https://github.com/splunk/TA-microsoft-sysmon

DNS response code signatures are based on
https://docs.microsoft.com/en-us/windows/win32/debug/system-error-codes--0-499-
