# Summary of Fixes for Error Code 86420 on Real NDS Hardware and melonDS

Created: 2026-05-04  
Target: Real Nintendo DS hardware / melonDS  
Target error: `86420`  
Not covered: Other emulators, Wii, Dolphin, DeSmuME, etc.

---

## Conclusion

Error code `86420` usually means that **a communication route cannot be established with the other player**.

It tends to occur not during the server login itself, but when communication with another player is required, such as battles, trades, joining sessions, or friend connections.

The main causes are **NAT**, **blocked UDP communication**, **firewalls**, **CGNAT**, and **hotspot/tethering restrictions**.

According to Nintendo-related explanations, `86420` is treated as an error caused by the NAT function in either your own or the other player’s network environment. In other words, it is not always your side that is at fault. The other player’s NAT can also cause it.

---

## What 86420 Means

`86420` refers to a situation like this:

```text
Server connection: May succeed
Wi-Fi connection test: May succeed
Friend list/login: May succeed
Battle/trade/joining session: Fails
Result: 86420
```

The important point is that **being able to communicate with the server** and **being able to communicate with another player** are different things.

In Nintendo DS Wi-Fi communication, depending on the game or feature, some functions only require server-based communication. On the other hand, features such as battles, trades, and joining sessions may require a communication route similar to P2P with the other player.

Because of this, the following symptoms can occur normally:

```text
GTS works
Rankings and some server features work
However, direct battles or direct trades fail with 86420
```

In this case, looking only at DNS often does not solve the problem.

---

## What NAT Is

Simply put, NAT is **a system that converts private IP addresses inside your home or mobile network into a global IP address on the Internet side**.

Example:

```text
NDS / PC / smartphone
  ↓
Private IP such as 192.168.x.x
  ↓ NAT translation
Global IP
  ↓
Internet
```

For normal web browsing or video streaming, this usually does not cause problems because communication is started from the inside to the outside.

However, for NDS battles, trades, and joining sessions, it may be necessary to receive returning UDP communication from the other side. Depending on the NAT type, the communication route may not be created.

When this happens, `86420` is likely to appear.

---

## Main Causes of 86420

| Cause | Details | Real NDS | melonDS |
| --- | --- | --- | --- |
| Strict NAT | UDP communication from the other side does not return | Yes | Yes |
| UDP blocked | UDP is restricted by the router or connection | Yes | Yes |
| UPnP disabled | Automatic port opening does not work | Yes | Yes |
| Wrong port forwarding target | The fixed IP has changed and forwarding goes to another device | Yes | Yes |
| Windows Firewall | Communication from melonDS.exe is blocked | No | Yes |
| Double router | Two layers of routers prevent external access | Yes | Yes |
| CGNAT | Additional NAT is applied by the ISP/mobile carrier | Yes | Yes |
| Hotspot | Devices cannot be directly reached from outside | Yes | Yes |
| Other player’s NAT | Even if your side is fine, the other side can fail | Yes | Yes |

---

## First Things to Check

Check in this order.

1. Confirm that you and the other player are using the same game and the same server environment
2. Confirm that the Wi-Fi connection test succeeds
3. Enable UPnP on the router
4. Set a fixed local IP for the real NDS or the PC running melonDS
5. For melonDS, allow `melonDS.exe` through Windows Firewall
6. Check whether you are behind a double-router setup
7. Check whether the router’s WAN-side IP is a global IP
8. Use DMZ or port forwarding to isolate whether NAT/UDP is the cause
9. Try a fixed-line connection instead of a hotspot
10. Check whether the same error occurs with another opponent

---

# Prioritized Checklist

## Real NDS Hardware

Checking from top to bottom makes it easier to narrow down the cause.

- [ ] The Wi-Fi connection test succeeds
- [ ] The DNS being used is correct
- [ ] The NDS local IP has been fixed
- [ ] UPnP has been enabled on the router
- [ ] There is no double-router setup
- [ ] The WAN-side IP is not in a CGNAT range
- [ ] If needed, the NDS IP was temporarily placed in DMZ for testing
- [ ] A fixed-line connection was tested instead of a hotspot
- [ ] The same error was checked with another opponent

---

## melonDS

With melonDS, in addition to the NDS-side settings, Windows, PC, and router-side settings are important.

- [ ] The Wi-Fi settings in melonDS are correct
- [ ] You are connected to the same server as the other player
- [ ] The firmware settings are correct
- [ ] The in-game connection test succeeds
- [ ] `melonDS.exe` has been allowed through Windows Firewall
- [ ] melonDS has been allowed in security software
- [ ] The PC local IP has been fixed
- [ ] UPnP has been enabled on the router
- [ ] The port forwarding or DMZ target is set to the PC’s IP
- [ ] VPN was disabled and tested
- [ ] The WAN-side IP is not in a CGNAT range
- [ ] There is no double-router setup
- [ ] The same error was checked with another opponent

---

# Cause Isolation Flowchart

```text
86420 appears
  ↓
Does the connection test succeed?
  ├─ No → Check DNS / Wi-Fi settings / access point compatibility
  └─ Yes
       ↓
Can you enter the server?
  ├─ No → Check DNS / server settings / firmware settings
  └─ Yes
       ↓
Do only battles, trades, or joining sessions fail?
  ├─ No → Another cause is possible
  └─ Yes
       ↓
Are you using melonDS?
  ├─ Yes → Check Windows Firewall, PC fixed IP, UPnP, and VPN
  └─ No → Check NDS fixed IP, UPnP, DMZ, and router NAT
       ↓
Does DMZ improve the issue?
  ├─ Yes → Port forwarding / NAT settings are highly likely to be the cause
  └─ No
       ↓
Is the WAN-side IP in a CGNAT range?
  ├─ Yes → The connection-side restriction is highly likely to be the cause
  └─ No
       ↓
Does it work with another opponent?
  ├─ Yes → The other player’s NAT is highly likely to be the cause
  └─ No → Recheck your router / connection / server settings
```

## How to Read the Flowchart

`86420` can occur during battles, trades, or joining sessions even if the connection test and server login succeed.

In that case, it is efficient to check UDP communication, NAT, UPnP, DMZ, CGNAT, and the other player’s NAT in order, rather than only checking DNS.

---

# For Real NDS Hardware

## 1. First, Make the Connection Test Pass

On real NDS hardware, the connection test in the game or system Wi-Fi settings must succeed first.

Items to check:

- You are using an access point that the NDS can connect to
- You are using the 2.4 GHz band
- You are using an encryption method supported by the NDS
- DNS settings match the WFC/Wiimmfi-style server you are using
- IP address, subnet mask, and gateway are correct
- The access point is not rejecting old devices

However, a successful connection test does not prove that `86420` is fixed.

The connection test mainly checks whether the device can reach the server. It does not guarantee that UDP communication with another player can be established.

---

## 2. Set a Fixed Local IP for the NDS

If you try port forwarding or DMZ, the settings become invalid if the NDS IP address changes.

Fix the IP using DHCP reservation on the router, or set it manually on the NDS.

Example:

```text
NDS IP Address: 192.168.1.50
Subnet Mask:    255.255.255.0
Gateway:        192.168.1.1
Primary DNS:    DNS for the WFC/Wiimmfi-style server being used
Secondary DNS:  Set if needed
```

It is safer to use an IP address outside the router’s DHCP range.

Example:

```text
Router DHCP range: 192.168.1.100 to 192.168.1.200
Fixed IP for NDS:  192.168.1.50
```

---

## 3. Enable UPnP

If the router supports UPnP, it may be able to create the communication route automatically.

Look for settings like these in the router settings page:

```text
UPnP
Universal Plug and Play
UPnP IGD
Automatic port opening
```

After enabling it, reconnect the NDS and try a battle, trade, or joining session.

If UPnP improves the issue, it is easier than manual port forwarding.

However, UPnP may not work depending on the router or connection.

---

## 4. Use DMZ for Isolation

If you put the fixed IP of the NDS into DMZ, incoming communication from outside can be forwarded broadly to the NDS.

Example:

```text
DMZ Host: 192.168.1.50
```

If `86420` improves with DMZ, the cause is very likely related to NAT, UDP, or ports.

Possible causes if DMZ improves the issue:

- Port forwarding was insufficient
- The port forwarding target IP was wrong
- UDP was blocked
- UPnP was not working properly

Possible causes if DMZ does not improve the issue:

- You are behind CGNAT
- You are behind a double-router setup
- The hotspot does not allow incoming communication from outside
- The other player’s NAT is causing the failure
- Server/game/opponent compatibility issue

DMZ is useful for isolating the cause, but it is better to use it as a test to check whether the issue improves rather than as a permanent setup.

---

## 5. Check for a Double-Router Setup

In a double-router setup, configuring only the router near the NDS will not allow outside communication to reach it.

Common setup:

```text
Internet
  ↓
ONU / home gateway
  ↓
Retail router
  ↓
NDS
```

In this case, NAT exists in two layers.

How to check:

1. Check the WAN-side IP of the retail router
2. If that IP is a private IP such as `192.168.x.x`, `172.16.x.x to 172.31.x.x`, or `10.x.x.x`, there is likely a double-router setup
3. Check whether NAT is also enabled on the upstream ONU/home gateway

Possible solutions:

- Put one of them into bridge/AP mode
- Configure port forwarding on both the upstream and downstream routers
- Set the NDS-side router as the DMZ target of the upstream router

---

## 6. Check Whether You Are Behind CGNAT

CGNAT means NAT is being applied even further outside your home router, on the ISP side.

In this case, even if you configure port forwarding or DMZ on your home router, incoming communication from outside may not reach you.

How to check:

1. Check the WAN-side IP in the router’s admin page
2. Compare it with the global IP shown in a browser
3. If the two are different, CGNAT is possible
4. If the WAN-side IP is in the `100.64.0.0 to 100.127.255.255` range, CGNAT is highly likely
5. If the WAN-side IP is `10.x.x.x`, `172.16.x.x to 172.31.x.x`, or `192.168.x.x`, there is NAT upstream

If you are behind CGNAT, there is much less you can do on the real NDS side.

In this case, switching to a fixed-line connection, a connection that provides a global IP, or a network that supports port forwarding is usually more effective.

---

# For melonDS

## 1. With melonDS, Also Check the PC Network

With melonDS, communication is handled by the PC rather than the NDS itself, so PC-side settings are important in addition to the real NDS checks.

Main areas to check:

- Windows Firewall
- Security software
- PC local IP
- Router UPnP
- Router port forwarding/DMZ
- Whether a VPN is being used
- Whether a hotspot/tethering connection is being used

---

## 2. Allow melonDS.exe Through Windows Firewall

With melonDS, if Windows Firewall blocks communication, it can cause `86420`.

Check the following:

```text
Windows Security
  ↓
Firewall & network protection
  ↓
Allow an app through firewall
  ↓
Allow melonDS.exe
```

Ideally, allow it according to the type of network you are using.

```text
Private network: Allow
Public network: Allow if needed
```

If the network is treated as “Public,” communication may be restricted more strictly.

For a home connection, setting the Windows network profile to “Private network” usually makes communication easier.

---

## 3. Set a Fixed Local IP for the PC

With melonDS, the port forwarding or DMZ target is not the NDS. It is **the PC running melonDS**.

Example:

```text
PC IP Address: 192.168.1.60
Subnet Mask:   255.255.255.0
Gateway:       192.168.1.1
```

Assigning a fixed IP to the PC’s MAC address on the router is the most stable method.

---

## 4. Try UPnP or Port Forwarding on the Router

Try UPnP or port forwarding for the PC running melonDS.

Priority order:

1. Enable UPnP
2. Set a fixed local IP for the PC
3. Allow melonDS.exe through the firewall
4. If it still fails, use DMZ for isolation
5. If DMZ improves the issue, return to port forwarding only for the required range

Because `86420` often involves UDP communication, you need to check UDP as well as TCP.

---

## 5. Check Security Software Restrictions

Even if Windows Firewall allows it, another security program may be blocking communication.

Items to check:

- Whether melonDS.exe is blocked
- Whether UDP communication is blocked
- Whether the game/emulator is treated as a low-trust app
- Whether VPN/proxy/traffic monitoring features are interfering

Instead of temporarily disabling all protection, it is safer to first allow `melonDS.exe` on an app-by-app basis.

---

## 6. Notes About VPN Use

A VPN can sometimes be a workaround depending on the situation.

However, using an ordinary VPN does not guarantee that the problem will be fixed.

Cases where a VPN may improve the issue:

- The VPN allows externally reachable UDP communication
- The VPN supports port forwarding
- Both players are inside the same VPN and can communicate directly within it
- Your normal connection is behind CGNAT, and the VPN has better NAT conditions

Cases where a VPN may make things worse:

- The VPN is also Strict NAT
- UDP is restricted
- Port forwarding is unavailable
- Latency or packet loss increases
- The VPN has poor compatibility with melonDS/WFC-style communication

In other words, a VPN is a way to try a different network route, but it is not a guaranteed fix for 86420.

---

# About Hotspots/Tethering

## Conclusion: It Is Often Difficult to Avoid 86420 on a Hotspot

With smartphone hotspots or tethering, connected devices receive private IP addresses.

Example:

```text
NDS / PC
  ↓
Smartphone hotspot
  ↓
Smartphone network-side NAT
  ↓
Carrier-side CGNAT
  ↓
Internet
```

In this setup, communication that needs to directly reach the NDS or PC from outside is easily blocked.

Especially on mobile networks, carrier-side CGNAT is often used, and users cannot configure port forwarding.

Because of this, hotspots commonly result in the following:

```text
Wi-Fi connection test: Succeeds
Server login: Succeeds
Battle/trade/joining session: 86420
```

---

## Things Worth Trying on a Hotspot

It is not always completely impossible.

Things worth trying:

1. Try another smartphone connection
2. Try another carrier
3. Switch between 5G and 4G
4. Use a VPN to change the route
5. Use a VPN that supports port forwarding
6. Try fixed-line Wi-Fi
7. Instead of asking the other player to change their connection, compare results with another opponent

However, if the hotspot side cannot do port forwarding, fundamental improvement is difficult.

---

# For MPH

In Metroid Prime Hunters, `86420` can be caused not only by your side, but also by the other player’s NAT.

In other words, even if your own settings are mostly correct, the connection can still fail if the other player is on Strict NAT, CGNAT, a hotspot, or a connection with UDP restrictions.

For MPH, it is easiest to think of it like this:

```text
Your NAT: OK
Other player’s NAT: NG
Result: 86420 may occur
```

```text
Your NAT: NG
Other player’s NAT: OK
Result: 86420 may occur
```

```text
Your NAT: OK
Other player’s NAT: OK
Result: Connection is more likely to succeed
```

In short, MPH’s `86420` is not an error that will always disappear just by fixing your own settings.

It is more accurate to view it as a state where UDP hole punching is failing somewhere across your side, the other player’s side, the router, the connection, or the server route.

---

# Symptom-Based Checks

## The Wi-Fi Connection Test Succeeds, but 86420 Appears

Areas to check:

- NAT
- UDP
- UPnP
- Port forwarding
- CGNAT
- Other player’s NAT

Changing only DNS often does not fix this.

---

## GTS or Some Online Features Work, but Battles/Trades Fail

This is a very typical `86420` symptom.

Areas to check:

- P2P communication
- UDP hole punching
- Router NAT type
- Other player’s network environment

The fact that server-based features work does not prove that direct communication works.

---

## It Fails on a Hotspot

Areas to check:

- Smartphone network-side CGNAT
- Hotspot NAT restrictions
- A setup where the device cannot be reached from outside
- Whether UDP/port forwarding is available when using a VPN

With hotspots, users cannot change router settings, so they are harder to fix than fixed-line connections.

---

## Only melonDS Fails

Areas to check:

- Windows Firewall
- Security software
- Whether melonDS.exe is allowed
- PC fixed IP
- Whether the router forwarding target is the PC
- VPN/proxy
- Whether the network profile is set to Public

---

## Only Real NDS Hardware Fails

Areas to check:

- Whether the access point is compatible with the NDS
- DNS settings
- NDS fixed IP
- Router UPnP
- Whether the DMZ target is the NDS IP
- Connection restrictions for old Wi-Fi devices

---

# Shortest Isolation Steps

To narrow down the cause quickly, the following order is easiest.

## Real NDS Hardware

```text
1. Confirm that the connection test succeeds
2. Fix the NDS IP
3. Enable UPnP
4. Temporarily test DMZ pointed to the NDS IP
5. Compare the router WAN IP with the global IP
6. If CGNAT/double router is present, treat it as a connection-side issue
7. Try with another opponent
8. Try a fixed-line connection instead of a hotspot
```

## melonDS

```text
1. Allow melonDS.exe through Windows Firewall
2. Fix the PC IP
3. Enable UPnP
4. Temporarily test DMZ pointed to the PC IP
5. Compare the router WAN IP with the global IP
6. Check VPN/proxy/security software
7. Try with another opponent
8. Try a fixed-line connection instead of a hotspot
```

---

# Which Situations Are More Likely to Improve?

| Situation | Chance of improvement | Reason |
| --- | --- | --- |
| Home fixed-line connection + manageable router | High | UPnP/DMZ/port forwarding can be tested |
| Double router | Medium | May improve if the setup is corrected |
| Windows Firewall is the cause | High | Often improves by allowing melonDS.exe |
| Only the other player has Strict NAT | Medium to low | Your side alone cannot fully fix it |
| CGNAT | Low | Incoming traffic will not arrive even if the home router forwards ports |
| Smartphone hotspot | Low | Direct external access to the device is difficult |
| VPN with port forwarding | Medium | May improve if the conditions match |
| Ordinary VPN | Unstable | Depends on the VPN-side NAT/UDP restrictions |

---

# Common Misunderstandings

## Will Changing DNS Fix 86420?

DNS may be necessary in some cases, but DNS is often not the main cause of `86420`.

DNS is a setting used to find the destination server.

It does not guarantee UDP communication or NAT traversal with another player.

---

## If the Connection Test Passes, Does That Mean 86420 Will Not Appear?

No. It can still appear.

A successful connection test mainly confirms that you can reach the server.

Whether P2P communication or UDP communication with another player can be established is separate.

---

## Does 86420 Mean My Settings Are Wrong?

Not necessarily.

86420 can be caused by NAT on your side or the other player’s side.

In battle games such as MPH, the other player’s connection conditions can also cause it.

---

## Can Hotspots Be Fixed by Changing Settings?

Sometimes, but it is often difficult.

With hotspots and mobile networks, devices receive private IP addresses and may also be behind carrier-side CGNAT.

In that case, users cannot configure settings that allow direct communication from outside to reach the device.

---

# Summary

The easiest way to understand `86420` is not as a simple DNS mistake or server outage, but as **a NAT/UDP-related error where a communication route with the other player cannot be created**.

The especially important points are:

- NAT on either your side or the other player’s side can cause it
- It can occur even if the connection test succeeds
- GTS and similar features may work while only battles/trades fail
- On real NDS hardware, fixed IP, UPnP, DMZ, double-router checks, and CGNAT checks are important
- On melonDS, Windows Firewall and PC-side fixed IP are also important
- Hotspots/tethering are often difficult because of CGNAT and private IP effects
- VPNs can be useful under certain conditions, but ordinary VPNs may not fix the issue
