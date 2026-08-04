# Introduction

This wiki is dedicated to explaining OpenSIPS. (A very fast SIP proxy server).

# References

1. [SIP](http://0.0.0.0:4567/gollum/create/SIP)
2. [OpenSIPS](https://www.opensips.org/)
3. [OpenSIPS Github](https://github.com/OpenSIPS/OpenSIPS)
4. [Build OpenSIPS](http://0.0.0.0:4567/gollum/create/Build-OpenSIPS)


***
# PCSCF


# Architecture

1. Single node mode (Dev only)
2. Cluster mode (Prod)

# Configuration

# Service


***

# UAS/UAC location

## Architecure

The architecture is pretty unique since an additional OpenSIPS server needs to be added in the environment in prder to receive REGISTER/OPTIONS messages forwarded by the OpenSIPS servers that actually receive them and reply to them woth the proper response messages.


UAS --(REGISTER)--> Pcscf (OpenSIPS relay()) --(REGISTER)--> OpenSIPS (Event handler) 


## Configuration
### PCSCF

1. /etc/opensips/opensips.cfg
```
#
# $Id: opensips.cfg 8758 2012-02-29 11:59:26Z vladut-paiu $
#
# OpenSIPS residential configuration script
#     by OpenSIPS Solutions <team@opensips-solutions.com>
#
# This script was generated via "make menuconfig", from
#   the "Residential" scenario.
# You can enable / disable more features / functionalities by
#   re-generating the scenario with different options.#
#
# Please refer to the Core CookBook at:
#      http://www.opensips.org/Resources/DocsCookbooks
# for a explanation of possible statements, functions and parameters.
#


####### Global Parameters #########

log_level=2
log_stderror=no
log_facility=LOG_LOCAL0

children=4

/* uncomment the next line to enable the auto temporary blacklisting of 
   not available destinations (default disabled) */
#disable_dns_blacklist=no

/* uncomment the next line to enable IPv6 lookup after IPv4 dns 
   lookup failures (default disabled) */
#dns_try_ipv6=yes

/* comment the next line to enable the auto discovery of local aliases
   based on revers DNS on IPs */
auto_aliases=no

server_signature=no # FFT disallow Server header

advertised_address="141.101.55.19"

#set module path
mpath="/usr/lib/x86_64-linux-gnu/opensips/modules"

loadmodule "proto_udp.so"
loadmodule "proto_tcp.so"
loadmodule "proto_tls.so"
modparam("proto_tls", "tls_max_msg_chunks", 8)
modparam("proto_tls", "cert_check_on_conn_reusage", 1)

listen=udp:141.101.55.19:5060
listen=tcp:141.101.55.19:5060
listen=tls:141.101.55.19:5061
#HEPv1, v2, v3 listener
listen = hep_udp:141.101.55.19:6062
listen = hep_tcp:141.101.55.19:6062

loadmodule "tls_mgm.so"

modparam("tls_mgm", "certificate",    "/etc/opensips/tls/server/wildcard.sip-preprod.openvno.net.crt")
modparam("tls_mgm", "private_key",    "/etc/opensips/tls/server/wildcard.sip-preprod.openvno.net.key")
modparam("tls_mgm", "ca_list",        "/etc/ssl/certs/ca-certificates.crt")

modparam("tls_mgm", "client_domain_avp", "tls_client_dom") # client certificates used must match this variable or default is used
modparam("tls_mgm", "tls_handshake_timeout", 1000) # some servers can be slow like sip.pstnhub.microsoft.com
tcp_connect_timeout=1000

####### Modules Section ########

#### SIGNALING module
loadmodule "signaling.so"

#### StateLess module
loadmodule "sl.so"

#### Transaction Module
loadmodule "tm.so"
modparam("tm", "fr_timeout", 12)
modparam("tm", "fr_inv_timeout", 200)
modparam("tm", "T1_timer", 250)
modparam("tm", "wt_timer", 12)
modparam("tm", "restart_fr_on_each_reply", 1)
modparam("tm", "onreply_avp_mode", 1)

#### MAX ForWarD module
loadmodule "maxfwd.so"

#### SIP MSG OPerationS module
loadmodule "sipmsgops.so"

#### FIFO Management Interface
loadmodule "mi_fifo.so"
modparam("mi_fifo", "fifo_name", "/var/run/opensips/opensips_fifo")


#### URI module
loadmodule "uri.so"
modparam("uri", "use_uri_table", 0)

#### USeR LOCation module
loadmodule "usrloc.so"
modparam("usrloc", "nat_bflag", "6")
modparam("usrloc", "working_mode_preset", "sql-only")
# usrloc's regen_broken_contactid allow version upgrades to change values, used when Opensips starts
modparam("usrloc", "regen_broken_contactid", 1)

#### REGISTRAR module
loadmodule "registrar.so"
modparam("registrar", "received_avp", "$avp(42)")
modparam("registrar", "tcp_persistent_flag", "TCP_PERSIST_DURATION")

/* uncomment the next line not to allow more than 10 contacts per AOR */
modparam("registrar", "max_contacts", 5)
modparam("registrar", "retry_after", 30) # unit is seconds

#### ACCounting module
#loadmodule "acc.so"

#### Media proxies modules
loadmodule "rtpengine.so"
modparam("rtpengine", "rtpengine_sock", "udp:localhost:2223")

loadmodule "db_postgres.so"
loadmodule "auth.so"
loadmodule "auth_db.so"
loadmodule "textops.so"
loadmodule "permissions.so"
loadmodule "domain.so"
loadmodule "drouting.so"
loadmodule "nathelper.so"
loadmodule "avpops.so"

modparam("auth_db|permissions|domain|acc|usrloc|drouting|avpops|tls_mgm", "db_url", "postgres://opensips:iApg4jEk@hss-preprod-proxy.sip.openvno.net:6432/opensips_prod")
#modparam("auth_db|permissions|domain|acc|usrloc|drouting|avpops|tls_mgm", "db_url", "postgres://postgres:iApg4jEk@hss-preprod-proxy.sip.openvno.net:6432/provtest")
modparam("auth_db", "calculate_ha1", no)
modparam("auth_db", "password_column", "ha1")
modparam("auth_db", "password_column_2", "ha1b")
modparam("auth_db", "load_credentials", "$avp(ha1)=ha1;$avp(ha1b)=ha1b")
# use_domain désactivé pour compatibilité avec les PCSCF 2.0
modparam("auth_db", "use_domain", 0)
modparam("auth", "username_spec", "$avp(username)")
modparam("auth", "password_spec", "$avp(password)")
modparam("auth", "calculate_ha1", no)

modparam("domain", "db_mode", 1) # Use caching

# ------- drouting params -------
modparam("drouting", "use_domain", 0)
modparam("drouting", "probing_interval",10)
modparam("drouting", "persistent_state", 0) # Mode persistant non fonctionnel en 1.11.1
modparam("drouting", "probing_from", "sip:drouting-pinger@141.101.55.19")
modparam("drouting", "probing_method", "OPTIONS")
modparam("drouting", "drc_table", "dr_carriers")
modparam("drouting", "default_group", 1)

# ------- nathelper params -------
modparam("nathelper", "natping_interval", 30)
modparam("nathelper", "ping_nated_only", 1)
modparam("nathelper", "received_avp", "$avp(42)")
modparam("nathelper", "sipping_bflag", "6")
modparam("nathelper", "sipping_from", "sip:natpinger@sip.openvno.net")

loadmodule "exec.so"
modparam("exec", "time_to_kill", 5)

#### Ratelimit module
loadmodule "ratelimit.so"
modparam("ratelimit", "timer_interval", 1)

#### Localcache module
loadmodule "cachedb_local.so"
modparam("cachedb_local", "cache_collections", "default=12")
modparam("cachedb_local", "cache_clean_period", 36000)

loadmodule "uac_auth.so"
loadmodule "uac.so"
# UAC restore mode is set to auto for easier From transformation between SIP and TEL URIs
modparam("uac", "restore_mode", "auto")

# UAC module requires rr module
loadmodule "rr.so"
# append_fromtag enabled because needed for auto UAC restore mode
modparam("rr", "append_fromtag", 1)

loadmodule "topology_hiding.so"
loadmodule "dialog.so"
# uac_replace_(from|to) needs dialog if topology_hiding is used
modparam("topology_hiding", "force_dialog", 1)
modparam("dialog", "default_timeout", 18000)

# Send event to subscribers
loadmodule "event_datagram.so"

loadmodule "perl.so"
modparam("perl", "filename", "/opt/local/bin/opensips_alphalink.pl")
modparam("perl", "modpath", "/usr/lib/x86_64-linux-gnu/opensips/perl")

# Tracing module
##### debut joute par Tite ######
# Tracing module
#loadmodule "siptrace.so"
#modparam("siptrace", "trace_on", 0)
#modparam("siptrace", "trace_id", "[1]uri=postgres://opensips:iApg4jEk@hss-preprod-1.sip.openvno.net:5432/opensips;table=sip_trace")
loadmodule "proto_hep.so"
#modparam("proto_hep", "hep_id", "[hid] 10.44.66.36:9060; transport=udp; version=2")
modparam("proto_hep", "hep_id", "[hid] 172.16.5.73:9060; transport=udp; version=3")
modparam("proto_hep", "hep_port", 6062)
modparam("proto_hep", "hep_capture_id", 51)
loadmodule "siptrace.so"
modparam("siptrace", "trace_on", 1)
#modparam("siptrace", "trace_on", 0)
#modparam("siptrace", "trace_id", "[1]uri=postgres://opensips:iApg4jEk@hss-preprod-1.sip.openvno.net:5432/opensips;table=sip_trace")
#trace en mode encapsulation HEP
modparam("siptrace", "trace_id", "[1]uri=hep:hid")
#modparam("siptrace", "trace_id", "[2]uri=file:/temp/sipcapture.pcap")
#modparam("siptrace", "file_mode", 0644)
#trace en mode SIP
#modparam("siptrace", "trace_id", "[1]uri=sip:10.44.66.36:9060")
#HEPv2 == timestamp will be included to HEP header
##### Fin ajoute par Tite #####
#loadmodule "siptrace.so"
#modparam("siptrace", "trace_on", 0)
#modparam("siptrace", "trace_id", "[1]uri=postgres://opensips:iApg4jEk@hss-preprod-1.sip.openvno.net:5432/opensips;table=sip_trace")

####### Routing Logic ########

# main request routing logic

route{
  $var(trace_id) = "1";
  $var(user) = "osip_user@opensips.org";
  #$var(corr) = $(ci{md5});
  #$var(correlation) = $(ci{s.sha1});
  sip_trace("$var(trace_id)", "d", "sip|xlog", "$var(user)"); # Capture les messages et les envoie à Homer via HEP  
  if (check_source_address("0", "$avp(707)", "pcscf-c5-1")) {
    if($avp(707) == "MRFC5") {
      $avp(request_from_mrf) = true;
    }
  } else {
    if (is_method("OPTIONS")) {
      if ($si == '141.101.55.19') {
        xlog("L_INFO", "monit's check -> OK\n");
        force_rport();
        sl_send_reply("200", "OK");
        exit;
      }
    }
    xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::403::$ru::IP forbidden or account not allowed on this PCSCF\n");
    sl_send_reply("403", "Forbidden");
    exit;
  }
  route(check_flood);

  $avp(nat)="";

  # test falgs (16: if source port match port of first Via (5060 if not present), 4: if first Via is a private IP address, 2: if first Via's received parameter match source IP address, 1: if Contact header host is a private IP address)
  # flag 16 is not relevant when ephemeral ports are used
  if ($proto == 'udp' && nat_uac_test("23") || nat_uac_test("7")) {
    setbflag(6);
    force_rport();
    $avp(nat)="NAT";
  }

  route(check_invalid);

  if (has_totag() && !is_method("CANCEL")) { # CANCEL should not have a TO tag (3261 9.1), if it has process as if it has not or it would confuse dialog/topology_hiding
    # sequential request withing a dialog should
    # take the path determined by record-routing

    xlog("L_INFO", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::xxx::$ru::to_tag\n");

    $avp(pre_topohiding_ru)=$ru;
    if (!topology_hiding_match() ) {
      if (is_method("ACK")) {
        if (t_check_trans()) {
          xlog("L_NOTICE", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::xxx::$ru::Topology hidding does not match but a transaction exists\n");
          # non loose-route, but stateful ACK; must be an ACK after a 487 or e.g. 404 from upstream server
          t_relay();
          exit;
        } else {
          # ACK without matching transaction -> ignore and discard
          xlog("L_NOTICE", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::xxx::$ru::Topology hidding does not match and no transaction exists\n");
          exit;
        }
      }
      xlog("L_NOTICE", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::xxx::$ru::Topology hidding does not match\n");
      send_reply("404","Not found");
    } else {
      if ($dlg_val(domain) != null && $dlg_val(domain) != "<null>") {
        xlog("L_INFO", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::xxx::$ru::Restoring AVP domain with value $dlg_val(domain)\n");
        $avp(domain) = $dlg_val(domain);
        set_advertised_address("$dlg_val(domain)"); # topology hiding use advertised_address
      }
      if ($dlg_val(nat) != null && $dlg_val(nat) != "<null>") {
        xlog("L_INFO", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::xxx::$ru::Restoring AVP nat and branch flag 6 because nat was enabled in this dialog\n");
        $avp(nat) = $dlg_val(nat);
        setbflag(6);
      }

      # In some rare race condition topology_hiding_match can match and fail to restore request-URI, in this case Opensips forward the message to itself until 513 "Message too big" and consume too much memory
      if ($avp(pre_topohiding_ru) == $ru) {
        xlog("L_NOTICE", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::xxx::$ru::Failed to restore URI from topology hiding\n");
        exit;
      }
      route(1);
    }
    exit;
  } else {
    if (is_method("INVITE")) {
      # TODO: move after authentication, and explicitly create dialog
      topology_hiding("U");
    }
    if (is_method("ACK")) {
      if (t_check_trans()) {
        # Some servers do not place to_tag and Contact in responses like 404
        xlog("L_NOTICE", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::xxx::$ru\n");
        t_relay();
        exit;
      }
    }
  }

  # initial requests
  if (is_method("ACK")) {
    xlog("L_WARNING","C5 : Ignore orphan ACK\n");
    exit;
  }
  if (is_method("BYE")) {
    xlog("L_WARNING","C5 : Ignore orphan BYE\n");
    sl_send_reply("481", "Call Does Not Exist");
    exit;
  }

  # CANCEL processing
  if (is_method("CANCEL")) {
    if (t_check_trans()) {
      if (match_dialog("DID_NONE")) { # Contact is not used for CANCEL, no did, matching SIP
        if ($dlg_val(domain) != null && $dlg_val(domain) != "<null>") {
          xlog("L_INFO", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::xxx::$ru::Restoring AVP domain with value $dlg_val(domain)\n");
          $avp(domain) = $dlg_val(domain);
        }
      }
      xlog("L_NOTICE", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::xxx::$ru\n");
      t_relay();
      route(stop_media_session);
    } else {
      sl_send_reply("481", "Call Does Not Exist");
      xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::none::$rm::481::$ru::Call Does Not Exist\n");
    }
    exit;
  }

  t_check_trans();

  # requests for my domain

  if (is_method("PUBLISH|SUBSCRIBE")) {
    sl_send_reply("503", "Service Unavailable");
    exit;
  }

  if ($rU==NULL) && !is_method("REGISTER") {
    # request with no Username in RURI
    if (!is_method("OPTIONS")) { # pass if method is option
      xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::none::$rm::484::$ru::Address Incomplete\n");
      sl_send_reply("484","Address Incomplete");
      exit;
    }
  }

  if (is_method("INVITE")) {
    if ($fu !~ "^tel:" && ! is_uri_host_local()) {
      xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::none::$rm::488::$ru::Host URI is not myself\n");
      sl_send_reply("488","Not Acceptable here");
      exit;
    }
  }

  route(check_sip_dialog_trace);

#-------------------------------------------------------------------

  # check source address against ip field in address table and store its context_info field in $avp(707)
  # cluster name is also checked against pattern field to find if the account is allowed on this PCSCF
  # Opensips 1.11.6 need a group_id, using 0
  if($avp(707)=="MRFC5") { # MRF
    xlog("L_INFO", "C5 : source is a MRF\n");
    if (is_present_hf("X-AccountCode")) {
      $avp(account_code) = $hdr(X-AccountCode);
      route(update_account_parameters);
      xlog("L_INFO", "C5 : account_code=$avp(account_code)\n");
      remove_hf("X-AccountCode");
      remove_hf("X-CarrierCode");

      route(handle_headers_parameters);

      $avp(old_ru)=$rU;
      $rU=$avp(account_code);

      subst_uri('/;transport=[a-z]*//i'); # using dr_gateways set in dr_gateways

      if (do_routing("2", "W", , , "$avp(gw_attrs)")) {
        #TODO: use script variable instead of AVP for gw_attrs

        $rU=$avp(old_ru);
        xlog("L_INFO", "calling do_routing() MRF Section\n");

        if($avp(gw_attrs)=="nat=yes") {
          setbflag(6);
        }

        route(1);
        exit;
      }
      else {
        if (lookup("location")) {#  dans module registrar:est-ce que rU est dans des contacts de location. bool
          xlog("L_INFO", "C5 : dans boucle if lookup location\n");
          setbflag(1); # not to apply failover in failure_route[1]
          $rU=$avp(old_ru);
          t_on_branch("registrar_branching");
          $avp(final_request_uri)=true; # must dial Contact registered by UA, request-URI must not be modified
          route(1);
          exit;
        }
        else {
          xlog("L_ERR","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::480::Not found in location : not connected or unexisting Erreur 480 -> could be error 604\n");
          sl_send_reply("480", "Temporarily unavailable : Not registered");
          exit;
        }
      }
    }
    else {
      xlog("L_ERR","C5::$avp(nat)::$proto:$si:$sp::none::$rm::400::X-AccountCode absent, return 400 Bad request\n");
      sl_send_reply("400", "Bad request : missing X-AccountCode");
      exit;
    }
  } else if ($(avp(707){s.substr,0,3}) == "D_I") { # interconnection with domain accounts
    if (is_method("OPTIONS")) {
      xlog("L_INFO","C5::$avp(nat)::$proto:$si:$sp::none::$rm::200::$ru\n");
      sl_send_reply("200", "OK");
      exit;
    }

    if (is_method("INVITE")) {
      route(get_account_from_domain);
      if ($avp(account_code) == null) {
        xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::403::$ru::Domain $rd is not allowed for $avp(707)\n");
        sl_send_reply("403", "Domain not allowed");
        exit;
      }
      route(update_account_parameters);
      if (!rl_check("$avp(account_code)", "$(avp(max_rate){s.int})", "TAILDROP")) {
        xlog("L_WARNING","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::480::$ru::CPS Limit Exceeded (limit is $avp(max_rate))\n");
        sl_send_reply("480", "CPS Limit Exceeded");
        exit;
      }
      if ($fu =~ "^tel:") { # URI is between <>, no need to check name
        uac_replace_from("sip:$fU@sip.openvno.net");
      }
    }

    if (is_method("REGISTER")) {
      sl_send_reply("405", "Method Not Allowed");
      xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::403::$ru::Domain account not allowed to use method REGISTER\n");
      exit;
    }

    remove_hf("X-AccountCode");
    remove_hf("X-CarrierCode");
    append_hf("X-AccountCode: $avp(account_code)\r\n");

    subst_uri('/;transport=[a-z]*//i'); # using dr_gateways set in dr_gateways

    if (do_routing("$(avp(group_id){s.int})", "W")) {
      xlog("L_INFO","C5 : drouting OK\n");
      route(add_session_id);
      route(1);
      exit;
    }
    else {
      xlog("L_ERR","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::503::Error while processing drouting\n");
      sl_send_reply("503", "Service Unavailable");
      exit;
    }
  } else if($(avp(707){s.substr,0,3}) == "REG") { # account with user/password and REGISTER
    if (is_method("REGISTER")) {
      # authenticate the REGISTER requests (uncomment to enable auth)
      if(!search("^Contact:[ ]*\*") && isbflagset(6)) {
         xlog("L_INFO","C5 : NAT detected\n");
         fix_nated_register();
      }

      if ($aU == null) {
        www_challenge("sip-preprod.openvno.net", "0");
        exit;
      }

      $avp(account_code) = $(aU{s.select,0,@});

      if ($avp(account_code) == $aU) {
        cache_fetch("local","ha1_$avp(account_code)",$avp(password));
        if ($avp(password) == null || $avp(password) == "<null>") {
          www_authorize("sip-preprod.openvno.net", "subscriber");
          $var(auth_rc)=$rc;
          $avp(password) = $avp(ha1);
          cache_store("local","ha1_$avp(account_code)","$avp(password)",3600);
        } else {
          $avp(username)=$aU;
          pv_www_authorize("sip-preprod.openvno.net");
          $var(auth_rc)=$rc;
        }
      } else {
        xlog("L_INFO","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::UAC using user@domain in authorization\n");
        cache_fetch("local","ha1b_$avp(account_code)",$avp(password));
        if ($avp(password) == null || $avp(password) == "<null>") {
          www_authorize("sip-preprod.openvno.net", "subscriber");
          $var(auth_rc)=$rc;
          $avp(password) = $avp(ha1b);
          cache_store("local","ha1b_$avp(account_code)","$avp(password)",3600);
        } else {
          $avp(username)=$aU;
          pv_www_authorize("sip-preprod.openvno.net");
          $var(auth_rc)=$rc;
        }
      }
      if ($var(auth_rc) < 0) {
        switch($var(auth_rc)) {
            case -1:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::403::Invalid User\n");
                sl_send_reply("403","Authentication failure");
                exit;
            break;
            case -2:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::403::Invalid Password\n");
                sl_send_reply("403","Authentication failure");
                exit;
            break;
            case -3:
                xlog("L_INFO","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::401::Stale Nonce\n");
            break;
            case -4:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::401::No Credentials\n");
            break;
            case -5:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::500::Generic Error\n");
                sl_send_reply("500","Internal server Error");
                exit;
            break;
            default:
               xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::403::Return Code ($rc)\n");
                sl_send_reply("500","Internal server Error");
        }
        www_challenge("sip-preprod.openvno.net", "0");
        exit;
      }

      if (!db_check_from()) {
        xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::403::UserID and AuthenticateID mismatch\n");
        sl_send_reply("403","Forbidden : Authentication failure");
        exit;
      }
      
      append_hf("X-NAT: $avp(nat)\r\n");
      # 0x02 - do not internally generate and send a "477 Send failed (477/TM)" SIP reply in case of a global forwarding failure (i.e. forwarding for each branch has failed due to internal errors, bad R-URI, bad message, lack of network reachability, etc.). 
      t_relay("udp:172.16.5.72:5060", "0x02");
      
      if ($proto == "tcp" || $proto == "tls") {
        setflag(TCP_PERSIST_DURATION);
      }
      if (!save("location")) {
        switch($rc) {
        case -2:
          xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::503 (Too many registrations)\n");
          exit;
        default:
          sl_reply_error(); #a revoir
          exit;
        }
      }
      xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::200\n");
      exit;
    } else if(is_method("INVITE")) {
      if ($aU == null) {
        proxy_challenge("sip-preprod.openvno.net", "0");
        exit;
      }

      $avp(account_code) = $(aU{s.select,0,@});

      if ($avp(account_code) == $aU) {
        cache_fetch("local","ha1_$avp(account_code)",$avp(password));
        if ($avp(password) == null || $avp(password) == "<null>") {
          proxy_authorize("sip-preprod.openvno.net", "subscriber");
          $var(auth_rc)=$rc;
          $avp(password) = $avp(ha1);
          cache_store("local","ha1_$avp(account_code)","$avp(password)",3600);
        } else {
          $avp(username)=$aU;
          pv_proxy_authorize("sip-preprod.openvno.net");
          $var(auth_rc)=$rc;
        }
      } else {
        xlog("L_INFO","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::UAC using user@domain in authorization\n");
        cache_fetch("local","ha1b_$avp(account_code)",$avp(password));
        if ($avp(password) == null || $avp(password) == "<null>") {
          proxy_authorize("sip-preprod.openvno.net", "subscriber");
          $var(auth_rc)=$rc;
          $avp(password) = $avp(ha1b);
          cache_store("local","ha1b_$avp(account_code)","$avp(password)",3600);
        } else {
          $avp(username)=$aU;
          pv_proxy_authorize("sip-preprod.openvno.net");
          $var(auth_rc)=$rc;
        }
      }
      if ($var(auth_rc) < 0) {
        switch($var(auth_rc)) {
            case -1:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::403::$ru::Invalid User\n");
                sl_send_reply("403","Proxy authentication failure");
                exit;
            break;
            case -2:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::403::$ru::Invalid Password\n");
                sl_send_reply("403","Proxy authentication failure");
                exit;
            break;
            case -3:
                xlog("L_INFO","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::407::$ru::Proxy stale Nonce\n");
            break;
            case -4:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::407::$ru::Proxy No Credentials\n");
            break;
            case -5:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::500::$ru::Proxy Generic Error\n");
                sl_send_reply("500","Internal server Error");
                exit;
            break;
            default:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::500::$ru::INVITE - Return Code ($rc)\n");
                sl_send_reply("500","Internal server Error");
                exit;
        }
        proxy_challenge("sip-preprod.openvno.net", "0");
        exit;
      }

      route(update_account_parameters);
      if (!rl_check("$avp(account_code)", "$(avp(max_rate){s.int})", "TAILDROP")) {
        xlog("L_WARNING","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::480::$ru::CPS Limit Exceeded (limit is $avp(max_rate))\n");
        sl_send_reply("480", "CPS Limit Exceeded");
        exit;
      }
      if ($fu =~ "^tel:") { # URI is between <>, no need to check name
        uac_replace_from("sip:$fU@sip.openvno.net");
      }

      consume_credentials();
      remove_hf("X-AccountCode");
      remove_hf("X-CarrierCode");
      append_hf("X-AccountCode: $avp(account_code)\r\n");

      subst_uri('/;transport=[a-z]*//i'); # using dr_gateways set in dr_gateways

      if (do_routing("$(avp(group_id){s.int})", "W")) {

        xlog("L_INFO", "calling do_routing() REG Section\n") ;
        route(add_session_id);
        route(1);
        exit;
      }
      xlog("L_WARNING","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::503::Service Unavailable routing with DR: went wrong\n");
      sl_send_reply("503","Service Unavailable");
      exit;
    } else if (is_method("OPTIONS")) {
      # TODO : gestion du nat
      sl_send_reply("200", "OK");
      exit;
    }
  } else { # IP account whithout user/password and no REGISTER
    $avp(account_code)=$avp(707);

    xlog("L_INFO", "C5 : source is external '$avp(account_code)'\n");

    if (is_method("INVITE")) {
      route(update_account_parameters);
      if (!rl_check("$avp(account_code)", "$(avp(max_rate){s.int})", "TAILDROP")) {
        xlog("L_WARNING","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::480::$ru::CPS Limit Exceeded (limit is $avp(max_rate))\n");
        sl_send_reply("480", "CPS Limit Exceeded");
        exit;
      }
      if ($fu =~ "^tel:") { # URI is between <>, no need to check name
        uac_replace_from("sip:$fU@sip.openvno.net");
      }
    }

    if (is_method("REGISTER")) {
      sl_send_reply("403", "Forbidden");
      xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::403::$ru::IP account not allowed to use method REGISTER\n");
      exit;
    }

    if (is_method("OPTIONS")) {
      sl_send_reply("200", "OK");
      exit;
    }

    remove_hf("X-AccountCode");
    remove_hf("X-CarrierCode");
    append_hf("X-AccountCode: $avp(account_code)\r\n");

    subst_uri('/;transport=[a-z]*//i'); # using dr_gateways set in dr_gateways

    if (do_routing("$(avp(group_id){s.int})", "W")) {
      xlog("L_INFO","C5 : drouting OK\n");
      route(add_session_id);
      route(1);
      exit;
    }
    else {
      xlog("L_ERR","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::503::Error while processing drouting\n");
      sl_send_reply("503", "Service Unavailable");
      exit;
    }
  }
}

local_route {
  if ($proto == "tls" && is_method("OPTIONS")) {
    append_hf("Contact: <sip:sip-preprod.openvno.net>\r\n"); # Some equipements want a Contact header in OPTIONS, drouting does not have an option for it
  }
}

route[1] {
  xlog("L_INFO", "C5 : route1 ($fu/$si/$rP) -> ($rm/$ru/$Ri/$Rp)\n");
  if (is_method("BYE")) {
    route(stop_media_session);
  } else {
    if (isbflagset(6)) {
      $avp(nat)="NAT";
      $dlg_val(nat) = "NAT";
      fix_nated_contact();
      route(start_media_session);
    } else if ($avp(media_proxy) == 'forced' || $dlg_val(media_session_engaged) == 'true') {
      route(start_media_session);
    }
  }

  if (is_method("INVITE")) {
    route(check_srtp);
    xlog("L_INFO","C5 : INVITE ($rU)\n");
    t_on_reply("1");
    $avp(failover_counter) = 0;
    t_on_failure("1");
  } else {
    t_on_reply("non_invite_replies");
    t_on_failure("non_invite_failures");
  }

  route(handle_p_early_media_on_request);
  route(cleanup_headers);

  if (!t_relay()) {
    sl_reply_error();
    xlog("L_ERR", "C5 : t_relay failed\n");
    exit;
  }
  xlog("L_NOTICE", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::xxx::$ru\n");
  exit;
}

onreply_route[1] {
  xlog("L_INFO", "C5 : onreply_route[1]\n");
  route(check_srtp);
  if (isbflagset(6)) {
    $avp(nat)="NAT";
    fix_nated_contact();
  }

  if ($dlg_val(domain) != null && $dlg_val(domain) != "<null>") {
    set_advertised_address("$dlg_val(domain)"); # topology hiding use advertised_address
  }

  route(drop_not_allowed_provisional_responses);
  route(handle_p_early_media_on_reply);
  route(cleanup_headers);

  if ((isbflagset(6) || $avp(media_proxy) == 'forced' || $dlg_val(media_session_engaged) == 'true') && ($rs=~"(180)|(183)|2[0-9][0-9]")) {
    route(start_media_session);
  }
  exit;
}

onreply_route[non_invite_replies] {
  route(cleanup_headers);
}

failure_route[1] {
  xlog("L_NOTICE", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::$T_reply_code::$ru:: entering failure_route[1]\n");

  if (t_was_cancelled()) {
    route(stop_media_session);
    exit;
  }

  if (isbflagset(1)) {
    xlog("L_INFO", "C5 : UAC unavailable\n");
    route(stop_media_session);
    if (t_check_status("(408)|(5[0-9][0-9])")) {
      xlog("L_INFO", "C5 : rewriting fail-over SIP status code to 480\n");
      t_reply("480", "Temporary Unavailable");
    }
    exit;
  }

  if (t_check_status("(408)|(5[0-9][0-9])")) {
    if ($avp(failover_counter) >= 1) {
      xlog("L_ERR", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::failover limit reached\n");
      route(stop_media_session);
      t_reply ("480", "Temporary Unavailable");
      exit;
    }
    $avp(failover_counter) = $avp(failover_counter) + 1;
    if(goes_to_gw("1")) {
      xlog("L_NOTICE", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::would have disable gw\n");
    }

    $avp(old_ru)=$rU;
    if (use_next_gw()) {
      t_on_failure("1");
      $rU=$avp(old_ru);
      if (!t_relay()) {
        xlog("L_ERR", "C5 : t_relay failed checking next gateway\n");
        route(stop_media_session);
        exit;
      }
      xlog("L_NOTICE", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::xxx::$ru\n");
    } else {
      xlog("L_ERR", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::drouting failover failed or no more active gws\n");
      route(stop_media_session);
      t_reply ("480", "Temporary Unavailable");
      exit;
    }
  } else { # $rs != (408)|(5[0-9][0-9])
    route(stop_media_session);
    exit;
  }
}

failure_route[non_invite_failures] {
  exit; # nothing here at the moment
}

branch_route[registrar_branching] {
  xlog("L_INFO", "C5 : Rewriting branch $ru with incomming requestURI's user part\n");
  # Rewriting requestURI's user part containing account_code with incomming requestURI's user part (which is the number for trunk accounts)
  $rU=$avp(old_ru);
}

route[get_account_from_domain] { # matching request-URI domain to account, should not be used for untrusted sources
  cache_fetch("local","account_$rd",$avp(account_code));
  if ($avp(account_code) == null) {
    avp_db_query("SELECT account_code FROM domain WHERE domain='$rd'", "$avp(account_code)");
    cache_store("local","account_$rd","$avp(account_code)",3600);
  }
}

route[update_account_parameters] {
  cache_fetch("local","max_rate_$avp(account_code)",$avp(max_rate));
  cache_fetch("local","media_proxy_$avp(account_code)",$avp(media_proxy));
  cache_fetch("local","group_id_$avp(account_code)",$avp(group_id));
  cache_fetch("local","encryption_$avp(account_code)",$avp(encryption));
  cache_fetch("local","domain_$avp(account_code)",$avp(domain));
  if ($avp(max_rate) == null || $avp(media_proxy) == null || $avp(group_id) == null || $avp(encryption) == null || $avp(domain) == null) {
    avp_db_query("SELECT a.max_rate,a.media_proxy,a.group_id,a.encryption,d.domain FROM account AS a LEFT JOIN domain AS d ON a.account_code = d.account_code WHERE a.account_code='$avp(account_code)' AND (d.location IS NULL OR d.location = 'pcscf-c5-1')", "$avp(max_rate),$avp(media_proxy),$avp(group_id),$avp(encryption),$avp(domain)");
    cache_store("local","max_rate_$avp(account_code)","$avp(max_rate)",3600);
    cache_store("local","media_proxy_$avp(account_code)","$avp(media_proxy)",3600);
    cache_store("local","group_id_$avp(account_code)","$avp(group_id)",3600);
    cache_store("local","encryption_$avp(account_code)","$avp(encryption)",3600);
    cache_store("local","domain_$avp(account_code)","$avp(domain)",3600);
  }
  if ($avp(domain) != null && $avp(domain) != "<null>") {
    xlog("L_INFO", "Set advertised address to $avp(domain) for $avp(account_code)\n");
    $dlg_val(domain) = $avp(domain);
    set_advertised_address("$avp(domain)");
  } else {
    xlog("L_INFO", "No domain to set advertised address for $avp(account_code)\n");
  }
}

route[handle_p_early_media_on_request] {
  if ($hdrcnt(P-Early-Media) == 0 && has_body("application/sdp")) {
    # assuming there is only one audio and is the first media
    if (is_method("INVITE")) {
      append_hf("P-Early-Media: supported\r\n", "CSeq");
    } else if (is_method("UPDATE")) {
      append_hf("P-Early-Media: sendrecv\r\n", "CSeq");
    }
  }
}

route[handle_p_early_media_on_reply] {
  if ($hdrcnt(P-Early-Media) == 0 && has_body("application/sdp")) {
    # assuming there is only one audio and is the first media
    if (t_check_status("180|183")) {
      append_hf("P-Early-Media: sendrecv\r\n", "CSeq");
    }
  }
}

route[drop_not_allowed_provisional_responses] {
  if (t_check_status("181")) { # Asterisk seems to generate 181 after forwarding 480 for some reason, FFT disallow that
    drop;
  }
}

# This function should only be used in outbound on initial INVITE
# MRF don't need this and need non anonymous/unavailable From URI
route[handle_headers_parameters] {
  if (!has_totag()) {
    # RFC 4967, add parameter user=phone on Request-URI, From, To SIP URIs if userpart is a number
    # Only one call to uac_replace_from/uac_replace_to is allowed
    if ($ru !~ "^tel:" && $avp(final_request_uri) != true && $(ru{uri.param,user}) == null) {
      add_uri_param("user=phone");
    }
    # Completion de l'anonymat vers les comptes C5
    if (is_privacy("id") || is_privacy("user")) {
      uac_replace_from("\"Anonymous\"","sip:anonymous@anonymous.invalid");
    } else if ($(fU{s.tolower}) == "unavailable") {
      uac_replace_from("sip:unavailable@unknown.invalid");
    } else if ($fu !~ "^tel:" && $(fu{uri.param,user}) == null) {
      uac_replace_from("$fu;user=phone");
    }
    if ($tu !~ "^tel:" && $avp(final_request_uri) != true && $(tu{uri.param,user}) == null) {
      uac_replace_to("$tu;user=phone");
    }
  }
}

route[cleanup_headers] {
  remove_hf("Route"); # topology_hiding() does not remove pre-loaded Route
  remove_hf("X-Asterisk-HangupCause");
  remove_hf("X-Asterisk-HangupCauseCode");
  remove_hf("Server");
  remove_hf("User-Agent");
  remove_hf("Date");
  remove_hf("Session-ID");
  if ($hdrcnt(Allow) != 0) {
    remove_hf("Allow");
    append_hf("Allow: INVITE, ACK, CANCEL, OPTIONS, BYE\r\n", "CSeq");
  }
  ##### Issam test
  #remove_hf("Identity");
  ##### End test
  if (is_present_hf("Supported")) {
    remove_hf("Supported");
    if (list_hdr_has_option("Supported", "timer")) {
      append_hf("Supported: timer\r\n", "Allow");
    }
  }
}

route[add_session_id] {
  if ($hdrcnt(Session-ID) == 0) {
    perl_exec("set_session_id", "pcscf-c5-1");
    append_hf("Session-ID: $avp(session_id)\r\n", "CSeq");
  }
}

route[check_srtp] {
  if (has_body("application/sdp") && search_body("m=audio [0-9]+ RTP/SAVP")) {
    if ($T_reply_code == 0) { # Request
      $avp(uac_srtp_supported)=true;
    } else { # Reply
      $avp(uas_srtp_supported)=true;
    }
  }
}

route[start_media_session] {
  if (has_body("application/sdp")) {
    cache_fetch("local","encryption_$avp(account_code)",$avp(encryption));
    xlog("L_INFO", "Start of media session\n");
    $var(rtpengine_flags) = 'RTP';
    if ($(avp(encryption){param.exist,srtp})) {
      $dlg_val(encryption_enabled) = 'true'; # avp variables are only present in transaction, re-INVITE create another transaction, non-string values are not supported
    }
    if ($avp(request_from_mrf) && $T_reply_code == 0 && $dlg_val(encryption_enabled) == 'true') { # Request in call from MRF towards an account known to support SRTP
      $var(rtpengine_flags) = 'SRTP SDES-no-NULL_HMAC_SHA1_32 SDES-no-NULL_HMAC_SHA1_80';
    } else if ($T_reply_code != 0 && $avp(uac_srtp_supported)) { # Reply to a call from a client supporting SRTP towards a MRF
      # could check account preferences instead but it's better to match what UAC offered
      $var(rtpengine_flags) = 'SRTP SDES-no-NULL_HMAC_SHA1_32 SDES-no-NULL_HMAC_SHA1_80';
      $dlg_val(encryption_enabled) = 'true'; # avp variables are only present in transaction, re-INVITE create another transaction, non-string values are not supported
    }
    if ($avp(media_session_offered)) {
      $var(media_proxy_rc) = rtpengine_answer("$var(rtpengine_flags) ICE=force");
    } else {
      $var(media_proxy_rc) = rtpengine_offer("$var(rtpengine_flags) ICE=force");
      $avp(media_session_offered) = true;
    }
    if ($var(media_proxy_rc)) {
      $dlg_val(media_session_engaged) = 'true'; # avp variables are only present in transaction, re-INVITE create another transaction, non-string values are not supported
    } else {
      xlog("L_ERR", "C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::media proxy error ($var(media_proxy_rc))\n");
    }
  }
}

route[stop_media_session] {
  xlog("L_INFO","End of media session\n");
  rtpengine_delete();
}

route[check_invalid] {
  if (!mf_process_maxfwd_header("10")) {
    sl_send_reply("483","Too Many Hops");
    exit;
  }
  if ($ml >=  4096 ) {
    sl_send_reply("513", "Message too big");
    exit;
  }
  if (is_present_hf("Replaces")) { # RFC 3891, Asterisk does not handle replace calls with its AGI
    sl_send_reply("501", "Not Implemented");
    exit;
  }
}

route[check_flood] {
  if (!is_method("ACK") && !is_method("BYE") && $avp(707)!="MRFC5") {
    cache_fetch("local","max_req_rate",$avp(max_req_rate));
    # check "<null>" not to use invalid cache data
    if ($avp(max_req_rate) == null || $avp(max_req_rate) == "<null>") {
      avp_db_query("SELECT max_req_rate FROM configuration WHERE host IN ('pcscf-c5-preprod-1b','pcscf-c5-1')", "$avp(max_req_rate)");
      if ($avp(max_req_rate) == null || $avp(max_req_rate) == "<null>") {
        $avp(max_req_rate) = 50;
      }
      cache_store("local","max_req_rate","$avp(max_req_rate)",3600);
   }

    $var(flood_pipe_name) = "flood_" + $avp(707);
    if (!rl_check("$var(flood_pipe_name)", "$(avp(max_req_rate){s.int})", "TAILDROP")) {
      xlog("L_INFO","C5::$avp(nat)::$proto:$si:$sp::<null>::$rm::xxx::$ru::Flood detected\n");
      #xlog("L_INFO","C5::$avp(nat)::$proto:$si:$sp::<null>::$rm::403::$ru::Flood detected\n");
      #sl_send_reply("403", "Request Limit Exceeded");
      #exit;
    }
  }
}

route[check_sip_dialog_trace] {
  if (is_method("INVITE")) {
    cache_fetch("local","trace_called_$rU",$avp(trace_active));
    if ($avp(trace_active) == null) {
      cache_fetch("local","trace_calling_$fU",$avp(trace_active));
    }
    if ($avp(trace_active) != null) {
      xlog("L_NOTICE", "Starting SIP trace for Call-ID $ci ($fU => $rU)\n");
      sip_trace("1", "d");
    }
  }
}

# vim: ts=2:sw=2

```
2. The part that relays the REGISTER to OpenSIPS (Event handler)
```
  } else if($(avp(707){s.substr,0,3}) == "REG") { # account with user/password and REGISTER
    if (is_method("REGISTER")) {
      # authenticate the REGISTER requests (uncomment to enable auth)
      if(!search("^Contact:[ ]*\*") && isbflagset(6)) {
         xlog("L_INFO","C5 : NAT detected\n");
         fix_nated_register();
      }

      if ($aU == null) {
        www_challenge("sip-preprod.openvno.net", "0");
        exit;
      }

      $avp(account_code) = $(aU{s.select,0,@});

      if ($avp(account_code) == $aU) {
        cache_fetch("local","ha1_$avp(account_code)",$avp(password));
        if ($avp(password) == null || $avp(password) == "<null>") {
          www_authorize("sip-preprod.openvno.net", "subscriber");
          $var(auth_rc)=$rc;
          $avp(password) = $avp(ha1);
          cache_store("local","ha1_$avp(account_code)","$avp(password)",3600);
        } else {
          $avp(username)=$aU;
          pv_www_authorize("sip-preprod.openvno.net");
          $var(auth_rc)=$rc;
        }
      } else {
        xlog("L_INFO","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::UAC using user@domain in authorization\n");
        cache_fetch("local","ha1b_$avp(account_code)",$avp(password));
        if ($avp(password) == null || $avp(password) == "<null>") {
          www_authorize("sip-preprod.openvno.net", "subscriber");
          $var(auth_rc)=$rc;
          $avp(password) = $avp(ha1b);
          cache_store("local","ha1b_$avp(account_code)","$avp(password)",3600);
        } else {
          $avp(username)=$aU;
          pv_www_authorize("sip-preprod.openvno.net");
          $var(auth_rc)=$rc;
        }
      }
      if ($var(auth_rc) < 0) {
        switch($var(auth_rc)) {
            case -1:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::403::Invalid User\n");
                sl_send_reply("403","Authentication failure");
                exit;
            break;
            case -2:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::403::Invalid Password\n");
                sl_send_reply("403","Authentication failure");
                exit;
            break;
            case -3:
                xlog("L_INFO","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::401::Stale Nonce\n");
            break;
            case -4:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::401::No Credentials\n");
            break;
            case -5:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::500::Generic Error\n");
                sl_send_reply("500","Internal server Error");
                exit;
            break;
            default:
               xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::403::Return Code ($rc)\n");
                sl_send_reply("500","Internal server Error");
        }
        www_challenge("sip-preprod.openvno.net", "0");
        exit;
      }

      if (!db_check_from()) {
        xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::403::UserID and AuthenticateID mismatch\n");
        sl_send_reply("403","Forbidden : Authentication failure");
        exit;
      }
      
      append_hf("X-NAT: $avp(nat)\r\n");
      # 0x02 - do not internally generate and send a "477 Send failed (477/TM)" SIP reply in case of a global forwarding failure (i.e. forwarding for each branch has failed due to internal errors, bad R-URI, bad message, lack of network reachability, etc.). 
      t_relay("udp:172.16.5.72:5060", "0x02");
      
      if ($proto == "tcp" || $proto == "tls") {
        setflag(TCP_PERSIST_DURATION);
      }
      if (!save("location")) {
        switch($rc) {
        case -2:
          xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::503 (Too many registrations)\n");
          exit;
        default:
          sl_reply_error(); #a revoir
          exit;
        }
      }
      xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::200\n");
      exit;
    } else if(is_method("INVITE")) {
      if ($aU == null) {
        proxy_challenge("sip-preprod.openvno.net", "0");
        exit;
      }

      $avp(account_code) = $(aU{s.select,0,@});

      if ($avp(account_code) == $aU) {
        cache_fetch("local","ha1_$avp(account_code)",$avp(password));
        if ($avp(password) == null || $avp(password) == "<null>") {
          proxy_authorize("sip-preprod.openvno.net", "subscriber");
          $var(auth_rc)=$rc;
          $avp(password) = $avp(ha1);
          cache_store("local","ha1_$avp(account_code)","$avp(password)",3600);
        } else {
          $avp(username)=$aU;
          pv_proxy_authorize("sip-preprod.openvno.net");
          $var(auth_rc)=$rc;
        }
      } else {
        xlog("L_INFO","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::UAC using user@domain in authorization\n");
        cache_fetch("local","ha1b_$avp(account_code)",$avp(password));
        if ($avp(password) == null || $avp(password) == "<null>") {
          proxy_authorize("sip-preprod.openvno.net", "subscriber");
          $var(auth_rc)=$rc;
          $avp(password) = $avp(ha1b);
          cache_store("local","ha1b_$avp(account_code)","$avp(password)",3600);
        } else {
          $avp(username)=$aU;
          pv_proxy_authorize("sip-preprod.openvno.net");
          $var(auth_rc)=$rc;
        }
      }
      if ($var(auth_rc) < 0) {
        switch($var(auth_rc)) {
            case -1:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::403::$ru::Invalid User\n");
                sl_send_reply("403","Proxy authentication failure");
                exit;
            break;
            case -2:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::403::$ru::Invalid Password\n");
                sl_send_reply("403","Proxy authentication failure");
                exit;
            break;
            case -3:
                xlog("L_INFO","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::407::$ru::Proxy stale Nonce\n");
            break;
            case -4:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::407::$ru::Proxy No Credentials\n");
            break;
            case -5:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::500::$ru::Proxy Generic Error\n");
                sl_send_reply("500","Internal server Error");
                exit;
            break;
            default:
                xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$Au::$rm::500::$ru::INVITE - Return Code ($rc)\n");
                sl_send_reply("500","Internal server Error");
                exit;
        }
        proxy_challenge("sip-preprod.openvno.net", "0");
        exit;
      }

      route(update_account_parameters);
      if (!rl_check("$avp(account_code)", "$(avp(max_rate){s.int})", "TAILDROP")) {
        xlog("L_WARNING","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::480::$ru::CPS Limit Exceeded (limit is $avp(max_rate))\n");
        sl_send_reply("480", "CPS Limit Exceeded");
        exit;
      }
      if ($fu =~ "^tel:") { # URI is between <>, no need to check name
        uac_replace_from("sip:$fU@sip.openvno.net");
      }

      consume_credentials();
      remove_hf("X-AccountCode");
      remove_hf("X-CarrierCode");
      append_hf("X-AccountCode: $avp(account_code)\r\n");

      subst_uri('/;transport=[a-z]*//i'); # using dr_gateways set in dr_gateways

      if (do_routing("$(avp(group_id){s.int})", "W")) {

        xlog("L_INFO", "calling do_routing() REG Section\n") ;
        route(add_session_id);
        route(1);
        exit;
      }
      xlog("L_WARNING","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::503::Service Unavailable routing with DR: went wrong\n");
      sl_send_reply("503","Service Unavailable");
      exit;
    } else if (is_method("OPTIONS")) {
      # TODO : gestion du nat
      sl_send_reply("200", "OK");
      exit;
    }
```

### OpenSIPS (Event Handler)

1. /etc/opensips/opensips.cfg
```
####### Global Parameters #########

log_level=2
log_stderror=no
log_facility=LOG_LOCAL0

children=4

/* uncomment the next line to enable the auto temporary blacklisting of 
   not available destinations (default disabled) */
#disable_dns_blacklist=no

/* uncomment the next line to enable IPv6 lookup after IPv4 dns 
   lookup failures (default disabled) */
#dns_try_ipv6=yes

/* comment the next line to enable the auto discovery of local aliases
   based on revers DNS on IPs */
auto_aliases=no

server_signature=no # FFT disallow Server header

#set module path
mpath="/usr/lib/x86_64-linux-gnu/opensips/modules"

loadmodule "proto_udp.so"

listen=udp:172.16.5.72:5060

####### Modules Section ########

### Amar - Adding event modules
#loadmodule "event.so"
#loadmodule "event_flatstore.so"   # or kafka / rabbit / REST / etc

#### SIGNALING module
loadmodule "signaling.so"

#### StateLess module
loadmodule "sl.so"

#### Transaction Module
loadmodule "tm.so"
modparam("tm", "fr_timeout", 12)
modparam("tm", "fr_inv_timeout", 200)
modparam("tm", "T1_timer", 250)
modparam("tm", "wt_timer", 12)
modparam("tm", "restart_fr_on_each_reply", 1)
modparam("tm", "onreply_avp_mode", 1)

#### MAX ForWarD module
loadmodule "maxfwd.so"

#### SIP MSG OPerationS module
loadmodule "sipmsgops.so"

#### FIFO Management Interface
loadmodule "mi_fifo.so"
modparam("mi_fifo", "fifo_name", "/var/run/opensips/opensips_fifo")

#### URI module
loadmodule "uri.so"
modparam("uri", "use_uri_table", 0)

#### USeR LOCation module
loadmodule "usrloc.so"
modparam("usrloc", "working_mode_preset", "single-instance-no-db")
### Amar - Adding usrloc params
modparam("usrloc", "timer_interval", 1) # this is used to scan DB and remove AOR entries exactly at expiry
#modparam("usrloc", "cseq_delay", 0) # This is used to not allow refresh REGISTERs

#### REGISTRAR module
loadmodule "registrar.so"

loadmodule "auth.so"
loadmodule "textops.so"
loadmodule "avpops.so"

# Send event to subscribers
loadmodule "event_datagram.so"

####### Routing Logic ########

# main request routing logic

route {
  if (is_method("OPTIONS")) {
    if ($si == '172.16.5.72') {
      force_rport();
    }
    sl_send_reply("200", "OK");
    exit;
  }
  if (!is_method("REGISTER")) {
    xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::501 Not Implemented");
    sl_send_reply("501","Not Implemented");
  }

  # Not checking save() return code, Opensips already logs errors, main Opensips should not receive anything
  save("location", "r"); # r flag make Opensips does not reply with 200 Ok
  exit;
}

# vim: ts=2:sw=2
```
2. /etc/opensips/opensipsctlrc
```
# $Id$
#
# The OpenSIPS configuration file for the control tools.
#
# Here you can set variables used in the opensipsctl and opensipsdbctl setup
# scripts. Per default all variables here are commented out, the control tools
# will use their internal default values.

## your SIP domain
# SIP_DOMAIN=opensips.org

## chrooted directory
# $CHROOT_DIR="/path/to/chrooted/directory"

## database type: MYSQL, PGSQL, ORACLE, DB_BERKELEY, DBTEXT, or SQLITE
## by default none is loaded
# If you want to setup a database with opensipsdbctl, you must at least specify
# this parameter.
# DBENGINE=MYSQL

## database port (PostgreSQL=5432 default; MYSQL=3306 default)
# DBPORT=3306

## database host
# DBHOST=localhost

## database name (for ORACLE this is TNS name)
# DBNAME=opensips

# database path used by dbtext, db_berkeley, or sqlite
# DB_PATH="/usr/local/etc/opensips/dbtext"

## database read/write user
# DBRWUSER=opensips

## password for database read/write user
# DBRWPW="opensipsrw"

## engine type for the MySQL/MariaDB tabels (default InnoDB)
# MYSQL_ENGINE="MyISAM"

## database super user (for ORACLE this is 'scheme-creator' user)
# DBROOTUSER="root"

# user name column
# USERCOL="username"

## for testing / development
#ETCDIR=etc/

# SQL definitions
# If you change this definitions here, then you must change them 
# in db/schema/entities.xml too.
# FIXME

# FOREVER="2020-05-28 21:32:15"
# DEFAULT_ALIASES_EXPIRES=$FOREVER
# DEFAULT_Q="1.0"
# DEFAULT_CALLID="Default-Call-ID"
# DEFAULT_CSEQ="13"
# DEFAULT_LOCATION_EXPIRES=$FOREVER


# Program to calculate a message-digest fingerprint 
# MD5="md5sum"

# awk tool
# AWK="awk"

# gdb tool
# GDB="gdb"

# grep tool
# GREP="grep"

# sed tool
# SED="sed"


# Describe what additional tables to install. Valid values for the variables
# below are yes/no/ask. With ask (default) it will interactively ask the user
# for an answer, while yes/no allow for automated, unassisted installs.
#

# If to install tables for the modules in the EXTRA_MODULES variable.
# INSTALL_EXTRA_TABLES=ask

# If to install presence related tables.
# INSTALL_PRESENCE_TABLES=ask

# Define what module tables should be installed.
# If you use the postgres database and want to change the installed tables,
# then you must also adjust the STANDARD_TABLES or EXTRA_TABLES variable 
# accordingly in the opensipsdbctl.base script.

# opensips standard modules
# STANDARD_MODULES="standard acc domain group permissions usrloc 
#                   msilo alias_db uri_db speeddial avpops auth_db dialog 
#                   dispatcher dialplan drouting nathelper load_balancer"

# opensips extra modules
# EXTRA_MODULES="imc cpl siptrace domainpolicy carrierroute userblacklist 
#                 b2b registrant call_center fraud_detection cachedb_sql"


## type of aliases used: DB - database aliases
## - default: none
# ALIASES_TYPE="DB"

## control engine: FIFO or UNIXSOCK
## - default FIFO
CTLENGINE=FIFO

## path to FIFO file
OSIPS_FIFO="/var/run/opensips/opensips_fifo"

## MI_CONNECTOR control engine: FIFO, UNIXSOCK, UDP, XMLRPC
# MI_CONNECTOR=FIFO:/tmp/opensips_fifo
# MI_CONNECTOR=UNIXSOCK:/tmp/opensips.sock
# MI_CONNECTOR=UDP:192.168.2.133:8000
# MI_CONNECTOR=XMLRPC:192.168.2.133:8000

## check ACL names; default on (1); off (0)
# VERIFY_ACL=1

## ACL names - if VERIFY_ACL is set, only the ACL names from below list
## are accepted
# ACL_GROUPS="local ld int voicemail free-pstn"

## verbose - debug purposes - default '0'
# VERBOSE=1

## do (1) or don't (0) store plaintext passwords
## in the subscriber table - default '1'
# STORE_PLAINTEXT_PW=0

## do not display the output highlighted
# NOHLPRINT=1

## OPENSIPS START Options
## PID file path - default is: /var/run/opensips.pid
# PID_FILE=/var/run/opensips.pid

## Extra start options - default is: not set
# example: start opensips with 64MB share memory: STARTOPTIONS="-m 64"
# STARTOPTIONS=

```

## Events

### REGISTRAR

REGISTRAR UAS/UAC is a type of of User-Agents that uses REGISTER method to log its location with a SIP Proxy server.

In order to keep track of UAS/UAC location, OpenSIPS uses a mecanism to log the location of each REGISTER in it's DB/cache/memory. The mecanism is based on events. An event is triggered when an entry in the table location is detected.
The events are stored in OpenSIPS DB as AOR.
```
AOR:: UAS1
        Contact:: sip:UAS1@10.1.1.1;transport=UDP;user=phone Q=
                ContactID:: 564779540772033556
                Expires:: 21
                Callid:: d3d5c9fab3fc13ad0d0c3c5938efb340@10.1.1.1
                Cseq:: 2146524753
                User-agent:: OXO052/035.001 GW_052/035.001
                State:: CS_NEW
                Flags:: 0
                Cflags::
                Socket:: udp:169.254.74.51:5060
                Methods:: 7279
```

#### REGISTER

When a REGISTER is recceived by OpenSIPS, it is challenged with 401/Unauthorized or 407/Proxy Authentication Required, the UAS sends a new REGISTER with Authorization header and this time if the UAS is authorized a 200 OK response is sent as response.

```
REGISTER sip:141.101.55.19:5060 SIP/2.0
Via: SIP/2.0/UDP 192.168.99.20:5091;rport;branch=z9hG4bKSG.JNlswMLATPGbKLBc99BEEow
From: <sip:0990000335832@sip-preprod.openvno.net>;tag=60ea7afb-4655-4d9f-951a-8f0452e27817
To: <sip:0990000335832@sip-preprod.openvno.net>
Call-ID: fixed-test-callid-12345@test
CSeq: 750121 REGISTER
Date: Tue, 04 Aug 2026 15:28:54 CEST
Contact: <sip:192.168.99.20:5091;transport=udp>;audio;class="business, personal";duplex="full, half";mobility="fixed";description="<OmniPCX Office>";methods="ACK, INVITE, CANCEL, BYE, REFER, NOTIFY, OPTIONS, PRACK, UPDATE";extensions="100rel, timer, from-change";schemes="sip"
Expires: 60
User-Agent: SIPExer v2.0.0
Max-Forwards: 10
Content-Length: 0
X-NAT: NAT.

SIP/2.0 401 Unauthorized
Via: SIP/2.0/UDP 192.168.99.20:5091;received=217.15.84.94;rport=5091;branch=z9hG4bKSG.JNlswMLATPGbKLBc99BEEow
From: <sip:0990000335832@sip-preprod.openvno.net>;tag=60ea7afb-4655-4d9f-951a-8f0452e27817
To: <sip:0990000335832@sip-preprod.openvno.net>;tag=124e.0808d8ce1c30ee5f7ed80f6ff7bb8e37
Call-ID: fixed-test-callid-12345@test
CSeq: 750121 REGISTER
WWW-Authenticate: Digest realm="sip-preprod.openvno.net", nonce="6a71e93400000c51fe89aa585bf0d67f3b20d640c749785d"
Content-Length: 0

REGISTER sip:141.101.55.19:5060 SIP/2.0
Via: SIP/2.0/UDP 192.168.99.20:5091;rport;branch=z9hG4bKSG.e370845f-8a84-443e-9f9a-79e252ce225b
From: <sip:0990000335832@sip-preprod.openvno.net>;tag=60ea7afb-4655-4d9f-951a-8f0452e27817
To: <sip:0990000335832@sip-preprod.openvno.net>
Call-ID: fixed-test-callid-12345@test
CSeq: 750122 REGISTER
Date: Tue, 04 Aug 2026 15:28:54 CEST
Contact: <sip:192.168.99.20:5091;transport=udp>;audio;class="business, personal";duplex="full, half";mobility="fixed";description="<OmniPCX Office>";methods="ACK, INVITE, CANCEL, BYE, REFER, NOTIFY, OPTIONS, PRACK, UPDATE";extensions="100rel, timer, from-change";schemes="sip"
Expires: 60
User-Agent: SIPExer v2.0.0
Max-Forwards: 10
Content-Length: 0
X-NAT: NAT.
Authorization: Digest username="0990000335832", realm="sip-preprod.openvno.net", nonce="6a71e93400000c51fe89aa585bf0d67f3b20d640c749785d", uri="sip:141.101.55.19:5060", algorithm=MD5, response="eb12e194e0a4cddd457500f1085ad75a"

SIP/2.0 200 OK
Via: SIP/2.0/UDP 192.168.99.20:5091;received=217.15.84.94;rport=5091;branch=z9hG4bKSG.e370845f-8a84-443e-9f9a-79e252ce225b
From: <sip:0990000335832@sip-preprod.openvno.net>;tag=60ea7afb-4655-4d9f-951a-8f0452e27817
To: <sip:0990000335832@sip-preprod.openvno.net>;tag=6818-49340d08fcc0f1405a89609167823507
Call-ID: fixed-test-callid-12345@test
CSeq: 750122 REGISTER
Contact: <sip:192.168.99.20:5091;transport=udp>;expires=60;received="sip:217.15.84.94:5091"
Content-Length: 0
```

#### Events 

Events are three types: 

1. INSERT
```
U 2025/11/27 10:36:44.636474 127.0.0.1:56349 -> 127.0.0.1:12345
E_UL_CONTACT_INSERT
aor::0990000348419
uri::sip:0990000348419@185.204.188.227:5065;rinstance=3A92D9725EFF4BBA63608EC211A9F55F
received::
path::
qval::18446744073709551615
socket::udp:169.254.74.51:5060
bflags::0
expires::1764236504
callid::Hrtb4QZimtc1Ak_HSKcIrA..
cseq::2
attr::
latency::0
```
2. DELETE
```
U 2025/11/27 10:38:30.989721 127.0.0.1:38121 -> 127.0.0.1:12345
E_UL_CONTACT_DELETE
aor::0990000362888
uri::sip:0990000362888@192.168.1.246;transport=UDP;user=phone
received::
path::
qval::18446744073709551615
socket::udp:169.254.74.51:5060
bflags::0
expires::1764236275
callid::6ff8912301a845aae931fa313d8cb6cc@192.168.1.246
cseq::2147431040
attr::
latency::0
```
3. UPDATE
```
U 2025/11/27 10:38:30.989721 127.0.0.1:38121 -> 127.0.0.1:12345
E_UL_CONTACT_UPDATE
aor::0990000362888
uri::sip:0990000362888@192.168.1.246;transport=UDP;user=phone
received::
path::
qval::18446744073709551615
socket::udp:169.254.74.51:5060
bflags::0
expires::1764236275
callid::6ff8912301a845aae931fa313d8cb6cc@192.168.1.246
cseq::2147431040
attr::
latency::0
```

### PROBING

PROBING UAS/UAC are types of User-Agents that uses OPTION METHOD to lof their location with a SIP proxy Server.
OpenSIPS 

#### OPTION

```
OPTIONS sip:141.101.55.19:5060 SIP/2.0
Via: SIP/2.0/UDP 192.168.99.20:5091;rport;branch=z9hG4bKSG.WS90vkc7uTSus7wvfLgW0EQ
From: <sip:0990000335832@sip-preprod.openvno.net>;tag=516aa4bb-b7d2-456a-91e2-b6d817899261
To: <sip:bob@localhost>
Call-ID: fixed-test-callid-12345@test
CSeq: 396727 OPTIONS
Date: Tue, 04 Aug 2026 15:34:00 CEST
User-Agent: SIPExer v2.0.0
Max-Forwards: 10
Content-Length: 0
X-NAT: NAT.

SIP/2.0 200 OK
Via: SIP/2.0/UDP 192.168.99.20:5091;received=217.15.84.94;rport=5091;branch=z9hG4bKSG.WS90vkc7uTSus7wvfLgW0EQ
From: <sip:0990000335832@sip-preprod.openvno.net>;tag=516aa4bb-b7d2-456a-91e2-b6d817899261
To: <sip:bob@localhost>;tag=124e.621cb9786b251e3fa76e8b072389bad6
Call-ID: fixed-test-callid-12345@test
CSeq: 396727 OPTIONS
Content-Length: 0

```

#### Events

* STATUS
```
U 2025/12/11 12:07:37.565509 127.0.0.1:37118 -> 127.0.0.1:12345
E_DROUTING_STATUS
partition::Default
gwid::3373
address::sip.pstnhub.microsoft.com:5061;transport=tls
status::inactive
```

## Tools
### Sipexer
#### REGISTER

> sipexer -laddr :5091 -register -vl 2 -co -com -ex 60 -fuser 0990000335832 -fdomain sip-preprod.openvno.net -fv 'touser:phone' -cb -ap "initsys" -fv 'contactparams:audio;class="business, personal";duplex="full, half";mobility="fixed";description="<OmniPCX Office>";methods="ACK, INVITE, CANCEL, BYE, REFER, NOTIFY, OPTIONS, PRACK, UPDATE";extensions="100rel, timer, from-change";schemes="sip"' -xh "X-NAT: NAT." -fv 'callid:fixed-test-callid-12345@test'  udp:141.101.55.19:5060


#### OPTIONS

> sipexer -laddr :5091 -vl 2 -co -com   -fuser 0990000335832 -fdomain sip-preprod.openvno.net   -ap "initsys"   -xh "X-NAT: NAT."   -fv 'callid:fixed-test-callid-12345@test'   udp:141.101.55.19:5060
