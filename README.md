# i-DoIt integration for zabbix

This Script will set bind the currently active zabbix triggers to the corresponding entry in i-doit.

## installation

### i-doit

To save the currently active triggers of a system in i-doit we need a new custom category in i-doit. Therefore this script will only work in the pro version of i-doit as the open version does not support custom fields. 

Therefore we create a custom category called "Active Zabbix Triggers". You can assign this category to all Object types that you want to link to zabbix objects. The "List category" field must be yes. There must be three textfield Fields called "Trigger Name","Trigger ID" and "Activation Time".

You have to write down the Constant for the category. Also you will need the constants for the fields. To get them you can view the source code of the web interface where you add a new entry to this category. When you inspect the code of the respective input fields you will find a data-identifier attribute. These have the format "form-constant::field-constant". The field-constant part is the one you need.

It is also important that the JSON-RPC API is enabled under "Interfaces / external data". "API access requires authentication (Method: idoit.login)" must be turned off because the script does not currently support that. You should write down the API-Key because you later need to insert this into the script.

### zabbix

On the zabbix side you first have to put the scripts i-doit-handler and example.config.sh in a location where the zabbix server can execute it. Then copy the example.config.sh script to config.sh and insert the respective information. 

After that you go to zabbix. Under "Configuration" -> "Actions" you add a new action. Name Subject and Message are not relvant but you have to enable the recovery message. The only condition should be "Maintenance status not in maintenance". In the "Operation" tab you have to add a new step. "Operation type" is "Remote command", "Target list" is "Curent host", "Type" is "Custom script", "Commands" is "`/path/to/i-doit-handler "{$IDOIT_OBJECTID}" "{TRIGGER.STATUS}" "{TRIGGER.ID}" "{TRIGGER.NAME}"`"

Lastly you have to have to set the Macro with the key "`{$IDOIT_OBJECTID}`" and the zabbix object ID as value for each zabbix host that you want to monitor in i-doit.
