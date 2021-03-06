Per-Request Policy logging
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Per-Request Policy logging provides an in-depth view of the rule matching logic that is applied to each flow passing through SSL Orchestrator. The logging from this facility is very verbose, as can be seen from the example below, where a single HTTP GET request is sent to \https://www.example.com. Per-Request Policy log data is sent to the APM log (/var/log/apm).

As you look through the example log data, pay attention to the following:

- Each session has a unique identifier that you can filter on (ex. using grep), in this case it is: ``3a9ebee8``
- At the start of each session some basic client and server details are logged (ex. IPs and ports)
- Further into the session, additional flow details become available (look for ``Agent created variable name`` messages):

  - perflow.category_lookup.result.url, value: https://www.example.com/
  - perflow.ssl.client_cipher_name, value: ECDHE-RSA-AES128-GCM-SHA256
  - perflow.ssl.client_cipher_version, value: TLSv1.2
  - perflow.ssl.server_cipher_name, value: ECDHE-RSA-AES128-GCM-SHA256
  - perflow.ssl.server_cipher_version, value: TLSv1.2
  - perflow.l7_protocol_lookup.result, value: https
  - perflow.service_path, value: ssloSC_All_Services

- Log messages indicate successful or unsuccessful evaluation of each rule, such as:

  - ``Following rule (Fail) from item (Pinners_Rule) to item (Finance_Bypass)`` indicates the **Pinners Rule** rule did not match
  - ``Following rule (Fail) from item (Finance_Bypass) to item (ssloSC_All_Services)`` indicates the **Finance Bypass** rule did not match
  - This flow did not match the Pinners Rule or Finance Bypass so it was caught by the default "All Traffic" rule at the bottom of the Security Policy

Example Per-Request Policy log output:

.. code-block:: shell-session

  May  4 13:53:03 sslo1 notice tmm2[11526]: 01490505:5: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: SSLO Session
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.client.ip.address, value: 10.1.10.50
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.client.port, value: 61834
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.server.ip.address, value: 93.184.216.34
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.server.port, value: 443
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870002:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Starting per request access 
  policy (/Common/ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_per_req_policy)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (fallback) from 
  item (Start) to item (Session Check)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.session_check.session_found, value: 1
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870000:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed agent (/Common
  /ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_per_req_policy_act_session_check_ag) 
  with return status (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870007:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed expression
  (expr { [mcget {perflow.session_check.session_found}] == 1 }) from policy item 
  (Session Check) with return value (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (Session Found) 
  from item (Session Check) to item (Pinners_Rule)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870002:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Starting per request access 
  policy (/Common/ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_pinners_rule_macro_policy)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (fallback) from 
  item (In) to item (IP Protocol Check)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870007:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed expression 
  (expr {[ IP::protocol ] == 6 }) from policy item (IP Protocol Check) with return
  value (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (TCP) from item 
  (IP Protocol Check) to item (SSL Check)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870000:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed agent (/Common
  /ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_pinners_rule_act_ssl_check_ag) with 
  return status (Need more data to start policy)
  
  May  4 13:53:03 sslo1 notice tmm2[11526]: 01490505:5: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: ACCESS: SSLO session Accepted
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.ssl.bypass_default, value: 0
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870004:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Resuming per request access
  policy (/Common/ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_per_req_policy)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.ssl_check.ssl_found, value: 1
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870000:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed agent (/Common
  /ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_pinners_rule_act_ssl_check_ag) with 
  return status (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870007:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed expression 
  (expr { [mcget {perflow.ssl_check.ssl_found}] == 1 }) from policy item (SSL Check)
  with return value (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (SSL Found) from 
  item (SSL Check) to item (Category Lookup)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.category_lookup.failure, value: 0
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.agent_license.type, value: 0
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.category_lookup.result.hostname, value: www.example.com
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.category_lookup.result.url, value: https://www.example.com/
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.agent_category_lookup.type, value: 0
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.agent_license.type, value: 1
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870000:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed agent (/Common
  /ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_pinners_rule_act_sni_category_lookup_ag) 
  with return status (Need urldb data)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870004:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Resuming per request access policy 
  (/Common/ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_per_req_policy)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.agent_license.type, value: 3
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.category_lookup.result.primarycategory, value: 9
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.category_lookup.result.numcategories, value: 1
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.category_lookup.result.categories, value: | 9 |
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.category_lookup.result.numcategories, value: 1
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870012:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Request classification returned 
  1 categories: [| /Common/Information_Technology |] for URL: https://www.example.com/
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870000:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed agent (/Common
  /ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_pinners_rule_act_sni_category_lookup_ag) 
  with return status (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870007:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed expression 
  (expr { [mcget {perflow.category_lookup.result.categories}] contains "| 1903 |" }) 
  from policy item Category Lookup) with return value (Failed)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (fallback) from 
  item (Category Lookup) to item (Fail)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (Fail) from item 
  (Pinners_Rule) to item (Finance_Bypass)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870002:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Starting per request access policy 
  (/Common/ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_finance_bypass_macro_policy)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (fallback) from 
  item (In) to item (IP Protocol Check)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870007:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed expression 
  (expr { [IP::protocol ] == 6 }) from policy item (IP Protocol Check) with return
  value (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (TCP) from item 
  (IP Protocol Check) to item (Categorization)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870002:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Starting per request access 
  policy (/Common/ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_categorization_macro_policy)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (fallback) 
  from item (In) to item (SSL Check)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870000:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed agent (/Common
  /ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_categorization_act_ssl_check_ag) 
  with return status (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870007:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed expression 
  (expr {[mcget {perflow.ssl_check.ssl_found}] == 1 }) from policy item (SSL Check)
  with return value (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (SSL Found) from 
  item (SSL Check) to item (Category Lookup (SSL))
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.category_lookup.result.numcustomcategories, value: 0
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.category_lookup.result.primarycategory, value: 9
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.category_lookup.result.numcategories, value: 1
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.category_lookup.result.categories, value: | 9 |
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.agent_license.type, value: 3
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870000:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed agent (/Common
  /ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_categorization_act_category_lookup_ag) 
  with return status (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (fallback) from 
  item (Category Lookup (SSL)) to item (Out)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (Out) from item 
  (Categorization) to item (Category Branching)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870007:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed expression 
  (expr {[mcget {perflow.category_lookup.result.categories}] contains "| 68 |" }) from
  policy item (Category Branching) with return value (Failed)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (fallback) from 
  item (Category Branching) to item (Fail)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (Fail) from item 
  (Finance_Bypass) to item (ssloSC_All_Services)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870002:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Starting per request access 
  policy (/Common/ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_sslosc_all_services_macro_policy)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (fallback) from 
  item (In) to item (ssloS_CiscoFP)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870002:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Starting per request access 
  policy (/Common/ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_sslos_ciscofp_macro_policy)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (fallback) from 
  item (In) to item (IP Version Check)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870007:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed expression
  (expr {[ IP::version ] == 4 }) from policy item (IP Version Check) with return 
  value (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (IPv4) from item 
  (IP Version Check) to item (IP Protocol Check)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870007:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed expression 
  (expr { [expr { [ IP::protocol ] == 6 }]?0:[expr { [ IP::protocol ] == 17 }]?1:2 })
  from policy item (IP Protocol Check) with return value (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (TCP) from item 
  (IP Protocol Check) to item (Service Connect (ssloS_CiscoFP))
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.service_path, value: Service Connect (ssloS_CiscoFP)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870000:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed agent (/Common
  /ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_sslos_ciscofp_act_svc_connect_4_t_ag) 
  with return status (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (fallback) from 
  item (Service Connect (ssloS_CiscoFP)) to item (Out)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (Out) from item 
  (ssloS_CiscoFP) to item (ssloS_SquidProxy)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870002:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Starting per request access policy 
  (/Common/ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_sslos_squidproxy_macro_policy)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (fallback) from 
  item (In) to item (IP Version Check)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870007:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed expression 
  (expr {[ IP::version ] == 4 }) from policy item (IP Version Check) with return 
  value (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (IPv4) from item 
  (IP Version Check) to item (L7 Protocol Lookup)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870000:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed agent (/Common
  /ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_sslos_squidproxy_act_protocol_lookup_4_ag) 
  with return status (Need more data to start policy)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870004:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Resuming per request access policy 
  (/Common/ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_per_req_policy)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870000:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed agent (/Common
  /ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_sslos_squidproxy_act_protocol_lookup_4_ag) 
  with return status (Need more data to start policy)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.ssl.client_cipher_name, value: ECDHE-RSA-AES128-GCM-SHA256
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.ssl.client_cipher_version, value: TLSv1.2
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.ssl.server_cipher_name, value: ECDHE-RSA-AES128-GCM-SHA256
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.ssl.server_cipher_version, value: TLSv1.2
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870004:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Resuming per request access 
  policy (/Common/ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_per_req_policy)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.l7_protocol_lookup.result, value: https
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870000:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed agent (/Common
  /ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_sslos_squidproxy_act_protocol_lookup_4_ag) 
  with return status (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870007:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed expression 
  (expr { [mcget {perflow.l7_protocol_lookup.result}] == "https" || [mcget {
  perflow.l7_protocol_lookup.result}] == "http" || [mcget {
  perflow.l7_protocol_lookup.result}] == "http-connect" }) from policy item
  (L7 Protocol Lookup) with return value (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule 
  (HTTP(S) and HTTP Connect) from item (L7 Protocol Lookup) to item 
  (Service Connect (ssloS_SquidProxy))
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.service_path, value: Service Connect (ssloS_CiscoFP) | 
  Service Connect (ssloS_SquidProxy)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870000:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed agent (/Common
  /ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_sslos_squidproxy_act_svc_connect_4_t_ag) 
  with return status (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (fallback) from 
  item (Service Connect (ssloS_SquidProxy)) to item (Out)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (Out) from item 
  (ssloS_SquidProxy) to item (Variable Assign)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.service_path, value: ssloSC_All_Services
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870000:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed agent (/Common
  /ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_sslosc_all_services_act_variable_assign_ag) 
  with return status (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (fallback) from 
  item (Variable Assign) to item (Out)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870001:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Following rule (Out) from item 
  (ssloSC_All_Services) to item (Allow)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870003:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Agent created variable name: 
  perflow.agent_ending.result, value: 1
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870000:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Executed agent (/Common
  /ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_per_req_policy_end_allow_ag) with 
  return status (Successful)
  
  May  4 13:53:03 sslo1 info tmm2[11526]: 01870009:6: /Common/sslo_f5labs_explicit.app
  /sslo_f5labs_explicit_accessProfile:Common:3a9ebee8: Execution of per request access 
  policy (/Common/ssloP_f5labs_explicit.app/ssloP_f5labs_explicit_per_req_policy) done 
  with ending type (Allow)