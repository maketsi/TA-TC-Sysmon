# TA-TC-Sysmon

This addon is a complete rewrite and replacement of TA-Microsoft-Sysmon [1] and adds:

* speed-optimized searches via custom eventtypes and macros (tc_sysmon*) that are not just based on (slow) EventID based searches.
* workarounds that protect from props hijack done by Splunk_TA_windows 6.0.0, avoiding sourcetype rename and processing of generic wineventlog props (providing no value).
* index-time operation that removes the extra (and static) header garbage caused by WinEventLog input, that does not provide any value for sysmon logs, saving up to 50% of license usage and I/O load.
* index-time sourcetyping to further optimize and categorize the events (sourcetype=sysmon:events:*)

Original events are expected to have sourcetype XmlWinEventLog:Microsoft-Windows-Sysmon/Operational


## Compatibility with Splunk CIM 

This is mostly compatible with Splunk CIM framework, with the following exception(s):

* process_path and file_path fields contain just the file path, excluding the file name, enabling ability to do directory statistics and analysis. You can get the full file path by concatenating this with 'process'.
* process = just the file name


## Sourcetypes

If this addon is installed on HF/indexers, the following sourcetypes will be created:

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
* EventID 255: "sysmon:events:error"

The addon works also with legacy data, with and without the Windows TA.


## Macros

These macros will efficiently search for the data and are not just based on signature_id/EventID search:

* `tc_sysmon`
* `tc_sysmon_process_created`
* `tc_sysmon_process_terminated`
* `tc_sysmon_image_loaded`
* etc.

Example usage:

* `tc_sysmon` host=foobar 

These macros search sysmon data from all indexes by default. It is possible to further optimize these by explicitly setting the
index(es) to use in `tc_sysmon_indexes` macro, or by changing it to use a lookup that automatically generates
the index list via tstats in advance. TODO: provide instructions on how to do that.


## Author

Markku Parviainen, 2019


## References

[1] https://github.com/splunk/TA-microsoft-sysmon
