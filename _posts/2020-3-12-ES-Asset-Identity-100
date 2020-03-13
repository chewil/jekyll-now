**Useful macros
*`assets`*
*`identities`*
	Use this macro to list all the users in the identities table 
*get_net_class(1)*
Definition:  lookup classification_lookup cidr as $field1$ output classification as $field1$_class
Arguments:   field1
App:  Splunk_TA_paloalto
- This is a custom macro created to get the subnet description of each IP address contained in the field provided.  

Example:    
*| `get_net_class("src_ip")`*     
- This will automatically create a new field called src_ip_class containing the subnet information.   If IP's not found, then the default "unknown" will be returned.   This means the IP address is not in a NYP managed network.

**Lookup tables
*App: SA-IdentityManagement*
- asset_lookup_by_str - assets
- asset_lookup_by_cidr - subnets
- identity_lookup_expanded - identities
- category_lookup - all categories from assets and identities tables
