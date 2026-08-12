# Introduction

# KPI
## Call Quality Metrics
* MOS (Mean Opinion Score) – 1–5 scale, target ≥4.0 for "good" quality. Usually derived via E-model (ITU-T G.107) from R-factor.
* R-factor – underlying score MOS is computed from (0–100). >80 = good, <70 = poor/complaints likely.
* Jitter – variation in packet arrival time. Keep under 30ms; above that, audio starts breaking up even with jitter buffers.
* Packet loss – target <1%. Above 3% is where MOS degrades sharply, especially with codecs like G.729.
* Latency / one-way delay – ITU recommends <150ms one-way for "acceptable," <400ms is the hard ceiling before conversational quality collapses.
* Round-trip time (RTT) – useful for correlating with echo issues.

## Call Completion / Reliability
* ASR (Answer Seizure Ratio) – % of calls answered vs. attempted. Low ASR often points to routing, carrier, or termination problems rather than quality issues.
* ACD (Average Call Duration) – abnormally short ACD can indicate quality-driven hangups; abnormally long can indicate stuck/zombie calls.
* NER (Network Effectiveness Ratio) – accounts for busy/no-answer as "network worked correctly," isolating true network failures.
* PDD (Post-Dial Delay) – time from dialing to ringback/183. High PDD is often a sign of downstream signaling bottlenecks or slow DNS/ENUM lookups.
* Call drop rate – % of calls that terminate abnormally mid-session (your BYE-drop investigation territory).
* CGR/CLR (Call Failure Rate / Loss Rate) – SIP error rate (4xx/5xx/6xx responses) as a % of total attempts.

## Signaling-Layer Health
* SIP response code distribution – tracking spikes in 486, 503, 480, 408, 487 etc. is often the fastest way to spot upstream/downstream issues.
* Registration success rate – % of REGISTER attempts resulting in 200 OK, critical for usrloc/contact binding health (relevant to your current OpenSIPS work).
* Transaction timeout rate – SIP retransmissions/timeouts (relevant given your UDP buffer overflow investigation).
Dialog/session state consistency – zombie dialogs, orphaned INVITEs, mismatched BYE counts.

## Infrastructure/Capacity
* CPS (Calls Per Second) – load metric for signaling servers (OpenSIPS/Asterisk), important for capacity planning and DoS detection.
* Concurrent call/channel count – vs. licensed/provisioned capacity.
* CPU/memory/socket utilization on SIP proxies – especially file descriptor and UDP socket buffer usage given your recent overflow debugging.
* RTP port range utilization – on RTPengine, exhaustion causes silent call failures.
* Trunk/gateway utilization – per-carrier or per-PSTN gateway concurrent channel usage vs. capacity.

## Fraud/Security-adjacent
* Anomalous call pattern detection – sudden spikes to premium/international destinations (toll fraud).
* Failed auth attempt rate – brute-force REGISTER/INVITE attempts against SIP endpoints.