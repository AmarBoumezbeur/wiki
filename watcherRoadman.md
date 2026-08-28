# Introduction

This page is dedicated to the roadmap of the project Watcher.

# Process
1. OnCall:
    * Support: OnCall enginer is responsible for receiving requests from Support, NOC, SI, AdminSI and even VoIP
team.
    * Incidents resolution: OnCall is responsible for taking ownership and anaysing the issues in details, then present it to the tema in order for all peers to participate and evaluate the issues, while the while goal is to find a safe, not risky and easy solution.
2. Study-Research (Page): 
    * Page:
        * The problem
        * THe current scene
        * Goal
        * Solutions
        * Appendixes
        * Generate PDP (ALphalink)
    * Tasks
        *  Conduct research and propose solutions
        *  Round of peer reviews
* Design
    * HLD/LLD or just one:
        * Page:
            * Provide a summary about the problem or request, current scene and the goals of the chosen solution
            * Designs (Architectures, general flows, networking ..., etc)
            * Specifications
            * Roadmap
            * Appendixes
        * Tasks: 
            * Round of peers reviews
            * Present to management
* Pre-Implementation
    * Devs
    * Testing
* Deployement

# Template
## RCA

Field

Value

Observations

System Under Review

MRF1 and MR2

MRF-1/2 had some CPU spikes. Not typically seen for these two machines.

Incident Date / Reporting Date

Wednesday 26/08/2026 between 11:50 - 2:20 / Reported Thursday 27/08/2026 17:15 (VoIP Channel) 

OnCall Engineer should've seen this

Review Date

Friday 28/08/2026 between 14:00 - 17:00 

A day later because the OnCall was absent in the morning

Analyst (s)

Amar

OnCall

Scope

No particular scope since this incident didn't impact the clients

More scope need to be defined after the analysis

Overall Risk Rating (High/Medium/Low)

Low

Since no impact has been detected

Go-no-Go Recommendations

TO BE REVIEWED

/

Overview:

Observation : 

Time: 

Scope :

Root-Cause

Analysis

Action Plan

Recommended:

Optional

Conclusion



Technical Analysis

Analysis Summary







Field

Value

Observations

System Under Review

MRF1 and MR2

MRF-1/2 had some CPU spikes. Not typically seen for these two machines.

Incident Date / Reporting Date

Wednesday 26/08/2026 between 11:50 - 2:20 / Reported Thursday 27/08/2026 17:15 (VoIP Channel) 

OnCall Engineer should've seen this

Review Date

Friday 28/08/2026 between 14:00 - 17:00 

A day later because the OnCall was absent in the morning

Analyst (s)

Amar

OnCall

Scope

No particular scope since this incident didn't impact the clients

More scope need to be defined after the analysis

Overall Risk Rating (High/Medium/Low)

Low

Since no impact has been detected

Go-no-Go Recommendations

TO BE REVIEWED

/

Specs







Machine

Resources

Observations

MRF-1

Type : Physical Machine

CPU : Intel(R) Xeon(R) CPU E5-2620 v4 @ 2.10GHz (Core 16)

RAM: 8Gb

STORAGE: 120Gb

/

MRF-2

Type : Physical Machine

CPU : Intel(R) Xeon(R) CPU E5-2620 v4 @ 2.10GHz (Core 16)

RAM: 8Gb

STORAGE: 120Gb

/

OverviewObservation : A CPU spike were seen on the level of the dashbaords of the MRF-1 and MRF-2.



Time: 

MRF-1: 1 spike was seen on Wednesday 26/08/2026 at 12:10:00 precisely.

MRF-2:  2 spike was seen on Wednesday between 26/08/2026 11:40:00 and 27/08/2026 12:30:00 precisely.

Scope : An early observation tells us that these times in which the spikes happen are at night and are not really client impactful, they could just scheduled jobs, schedules routines like backup or just linux internal system processes.

Root-Cause

Potential Cause:  scheduled jobs, scheduled routines like backup or just linux internal system processes.

Analysis

Data collection:

MRF-1

logs

Aug 26 12:10:00 mrf-1 asterisk[26394]: WARNING[29376][C-0004a146]: chan_sip.c:7606 in sip_write: Can't send 10 type frames with SIP write
Aug 26 12:10:03 mrf-1 snmpd[1328]: error on subcontainer 'ia_addr' insert (-1)
Aug 26 12:10:03 mrf-1 snmpd[1328]: error on subcontainer 'ia_addr' insert (-1)
Aug 26 12:10:03 mrf-1 snmpd[1328]: error on subcontainer 'ia_addr' insert (-1)
Aug 26 12:10:13 mrf-1 asterisk[26394]: WARNING[26421][C-0004a15a]: chan_sip.c:18388 in get_rdnis: Huh?  Not an RDNIS SIP header (tel:+41213237659)?
Aug 26 12:10:33 mrf-1 snmpd[1328]: error on subcontainer 'ia_addr' insert (-1)
Aug 26 12:10:33 mrf-1 snmpd[1328]: error on subcontainer 'ia_addr' insert (-1)
Aug 26 12:10:33 mrf-1 snmpd[1328]: error on subcontainer 'ia_addr' insert (-1)

MRF-2:

Logs:

Aug 26 12:17:21 mrf-2 sshd[9813]: rexec line 16: Deprecated option KeyRegenerationInterval
Aug 26 12:17:21 mrf-2 sshd[9813]: rexec line 17: Deprecated option ServerKeyBits
Aug 26 12:17:21 mrf-2 sshd[9813]: rexec line 28: Deprecated option RSAAuthentication
Aug 26 12:17:21 mrf-2 sshd[9813]: rexec line 35: Deprecated option RhostsRSAAuthentication
Aug 26 12:17:21 mrf-2 sshd[9813]: error: Could not load host key: /etc/ssh/ssh_host_dsa_key
Aug 26 12:17:21 mrf-2 sshd[9813]: Did not receive identification string from 169.254.2.20 port 40308
Aug 26 12:19:38 mrf-2 asterisk[4242]: WARNING[4269][C-0004a328]: chan_sip.c:18388 in get_rdnis: Huh?  Not an RDNIS SIP header (tel:+33325739798)?
Aug 26 12:25:01 mrf-2 CRON[10037]: pam_unix(cron:session): session opened for user root by (uid=0)
Aug 26 12:25:01 mrf-2 CRON[10038]: (root) CMD (command -v debian-sa1 > /dev/null && debian-sa1 1 1)
Aug 26 12:25:01 mrf-2 CRON[10037]: pam_unix(cron:session): session closed for user root
Aug 26 12:26:37 mrf-2 asterisk[4242]: WARNING[4269][C-0004a389]: chan_sip.c:18388 in get_rdnis: Huh?  Not an RDNIS SIP header (tel:+33545691604)?
Aug 26 12:27:20 mrf-2 sshd[10095]: rexec line 16: Deprecated option KeyRegenerationInterval
Aug 26 12:27:20 mrf-2 sshd[10095]: rexec line 17: Deprecated option ServerKeyBits
Aug 26 12:27:20 mrf-2 sshd[10095]: rexec line 28: Deprecated option RSAAuthentication
Aug 26 12:27:20 mrf-2 sshd[10095]: rexec line 35: Deprecated option RhostsRSAAuthentication
Aug 26 12:27:20 mrf-2 sshd[10095]: error: Could not load host key: /etc/ssh/ssh_host_dsa_key
Aug 26 12:27:21 mrf-2 sshd[10095]: Did not receive identification string from 169.254.2.20 port 55973
Aug 26 12:28:25 mrf-2 asterisk[4242]: WARNING[4269][C-0004a39e]: chan_sip.c:18388 in get_rdnis: Huh?  Not an RDNIS SIP header (tel:+33322750000)?
Aug 26 12:28:33 mrf-2 asterisk[4242]: WARNING[4269][C-0004a3a0]: chan_sip.c:18388 in get_rdnis: Huh?  Not an RDNIS SIP header (tel:+33322750000)?
Aug 26 12:35:01 mrf-2 CRON[10264]: pam_unix(cron:session): session opened for user root by (uid=0)
Aug 26 12:35:01 mrf-2 CRON[10265]: (root) CMD (command -v debian-sa1 > /dev/null && debian-sa1 1 1)
Aug 26 12:35:01 mrf-2 CRON[10264]: pam_unix(cron:session): session closed for user root
Aug 26 12:36:36 mrf-2 asterisk[4242]: WARNING[4269][C-0004a3f6]: chan_sip.c:18388 in get_rdnis: Huh?  Not an RDNIS SIP header (tel:+33545691604)?
Aug 26 12:37:19 mrf-2 sshd[10311]: rexec line 16: Deprecated option KeyRegenerationInterval
Aug 26 12:37:19 mrf-2 sshd[10311]: rexec line 17: Deprecated option ServerKeyBits
 

Real root cause : is not clear yet because:

MRF-1

The logs doesn't show anything specific at 12:00:00

The dashhboard show nothing as well.

MRF-2

The logs show a repetitive log à 11:30:00 et 12:00:00 mais pas de logs à 12:20:00 (Weird)

The dashhboard show nothing as well.

Analyse des logs:

chan_sip.c:7606 in sip_write: Can't send 10 type frames with SIP write

Asterisk is trying to send video frames (frame type 10 = AST_FRAME_VIDEO) over a SIP channel, but the channel cannot handle them. This usually happens when video is enabled in Asterisk but the remote endpoint or the negotiated codec doesn't support it, or the channel is in an audio-only state.

Huh? Not an RDNIS SIP header (tel:+41213237659)?

Asterisk received a SIP header containing redirecting number information (RDNIS / Diversion header), but it contains a tel: URI (tel:+41213237659) instead of the expected sip: URI format. chan_sip is strict about parsing these.

error on subcontainer 'ia_addr' insert (-1)

 (Net-SNMP) is failing to insert an interface address into its internal ia_addr subcontainer. This is a well-known, long-standing bug typically triggered when enumerating network interfaces—especially with IPv6 addresses, virtual interfaces, or containers.

Action Plan

Recommended:

Disable Asterisk video frames over SIP.

Look into SNMP error and fix it if possible.

Conclusion

Nothing unusual has been seen in the logs or the dashboards aprt from the Video Frames error and SNMP insert error, so probably this was related to an internal process or a heavy load (which is not shown in call count).