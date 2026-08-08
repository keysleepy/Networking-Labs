# Lab: FLSM — Subnetting by Network Requirement

## Objective

Apply Fixed-Length Subnet Masking (FLSM) to divide a given base network into a required number of equal-sized subnets, calculating the new subnet mask, address ranges, and usable host counts for each.

## Background

FLSM is used when a network design specifies **how many subnets** are needed, rather than how many hosts each subnet must support. Every subnet produced uses the **same** subnet mask — unlike VLSM, where mask length varies per subnet based on individual host requirements (see the [`VLSM`](../VLSM) lab in this repo).

| | FLSM | VLSM |
|---|---|---|
| Given | Number of networks needed | Number of hosts needed per subnet |
| Subnet mask | Same for every subnet | Different per subnet, sized to fit |
| Use case | Quick equal-sized split | Real-world designs with varying department sizes |

## Procedure

For a base network and a required subnet count `n`:

1. **Bits** — find the smallest number of bits where `2^bits ≥ n`
2. **NSM** (New Subnet Mask) = OSM (Original Subnet Mask) + Bits
3. **UF** (Usable Format) — write NSM in dotted decimal
4. **Increment** = `2^(32 - NSM)`, applied at the octet where the mask boundary falls
5. **Verify**:
   - Actual Net (total subnets created) = `2^(NSM - OSM)`
   - Usable Hosts per subnet = `2^(32 - NSM) - 2`
6. Build the address ranges by repeatedly adding the increment, starting from the base network address

---

## Lab Exercise 1 — Class A: `10.0.0.0/8`, 3,500 networks required

**Solution**

| Step | Value |
|---|---|
| Bits | 12 (`2^11 = 2048` too small → `2^12 = 4096 ≥ 3500`) |
| NSM | /8 + 12 = **/20** |
| UF | 255.255.**240**.0 |
| Increment | `2^(24-20) = 16` (3rd octet) |
| Actual Net | `2^(20-8) = 4,096` |
| Usable Hosts | `2^(32-20) - 2 = 4,094` |

**Verification — first 3 subnets**

| Network Address | Usable Range | Broadcast |
|---|---|---|
| 10.0.0.0 | 10.0.0.1 – 10.0.15.254 | 10.0.15.255 |
| 10.0.16.0 | 10.0.16.1 – 10.0.31.254 | 10.0.31.255 |
| 10.0.32.0 | 10.0.32.1 – 10.0.47.254 | 10.0.47.255 |

*Observation: with a /20 mask on an /8 base, the increment lands in the 3rd octet — the 2nd octet is fully consumed as network bits before reaching it.*

---

## Lab Exercise 2 — Class B: `172.16.0.0/16`, 100 networks required

**Solution**

| Step | Value |
|---|---|
| Bits | 7 (`2^7 = 128 ≥ 100`) |
| NSM | /16 + 7 = **/23** |
| UF | 255.255.**254**.0 |
| Increment | `2^(24-23) = 2` (3rd octet) |
| Actual Net | `2^(23-16) = 128` |
| Usable Hosts | `2^(32-23) - 2 = 510` |

**Verification — first 3 subnets**

| Network Address | Usable Range | Broadcast |
|---|---|---|
| 172.16.0.0 | 172.16.0.1 – 172.16.1.254 | 172.16.1.255 |
| 172.16.2.0 | 172.16.2.1 – 172.16.3.254 | 172.16.3.255 |
| 172.16.4.0 | 172.16.4.1 – 172.16.5.254 | 172.16.5.255 |

*Observation: with a /23 mask, the increment applies to the 3rd octet, not the 4th, since the mask crosses into that octet.*

---

## Lab Exercise 3 — Class C: `192.168.50.0/24`, 10 networks required

**Solution**

| Step | Value |
|---|---|
| Bits | 4 (`2^4 = 16 ≥ 10`) |
| NSM | /24 + 4 = **/28** |
| UF | 255.255.255.**240** |
| Increment | `2^(32-28) = 16` |
| Actual Net | `2^(28-24) = 16` |
| Usable Hosts | `2^(32-28) - 2 = 14` |

**Verification — first 4 subnets**

| Network Address | Usable Range | Broadcast |
|---|---|---|
| 192.168.50.0 | .1 – .14 | .15 |
| 192.168.50.16 | .17 – .30 | .31 |
| 192.168.50.32 | .33 – .46 | .47 |
| 192.168.50.48 | .49 – .62 | .63 |

---

## Conclusion

Completing these three exercises across all IP address classes reinforced the FLSM workflow and clarified a few points that are easy to get wrong:

- **Usable Hosts always subtracts from 32**, never from the original mask: `2^(32 - NSM) - 2`.
- **Actual Net compares NSM to OSM**: `2^(NSM - OSM)` — this shows how many total subnets were created, which may exceed what was actually required.
- The **increment doesn't always land in the last octet**. For Class A and Class B base networks, a large enough NSM can push the increment into the 2nd or 3rd octet instead of the 4th — always check where the mask boundary actually falls before building ranges.
- Any unused subnets created beyond the required count (e.g., Exercise 3 creates 16 subnets when only 10 are needed) are reserved for future network growth.
