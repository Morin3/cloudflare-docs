---
pcx_content_type: configuration
title: Network policies
weight: 3
---

# Network policies

{{<Aside type="note">}}

To enable this feature, download and deploy the [WARP client](/cloudflare-one/connections/connect-devices/warp/deployment/) on your devices.

{{</Aside>}}

With Cloudflare Zero Trust, you can configure policies to control network-level traffic leaving your endpoints. Using network selectors like IP addresses and ports, your policies will control access to any network origin. Because Cloudflare Zero Trust [integrates with your identity provider](/cloudflare-one/identity/idp-integration/), it also gives you the ability to create identity-based network policies. This means you can now control access to non-HTTP resources on a per-user basis regardless of where they are or what device they access that resource from.

A network policy consists of an **Action** as well as a logical expression that determines the scope of the action. To build an expression, you need to choose a **Selector** and an **Operator**, and enter a value or range of values in the **Value** field. You can use **And** and **Or** logical operators to evaluate multiple conditions.

- [Actions](#actions)
- [Selectors](#selectors)
- [Comparison operators](#comparison-operators)
- [Value](#value)
- [Logical operators](#logical-operators)

{{<render file="gateway/_response.md" withParameters="query;;_Source IP_;;_Resolved IP_">}}

## Actions

Like actions in DNS and HTTP policies, actions in network policies define which decision you want to apply to a given set of elements. You can assign one action per policy.

### Allow

API value: `allow`

{{<details header="Available selectors">}}

**Traffic**

- [Application](#application)
- [Destination Continent IP Geolocation](#destination-continent)
- [Destination Country IP Geolocation](#destination-country)
- [Destination IP](#destination-ip)
- [Destination Port](#destination-port)
- [Detected Protocol](#detected-protocol)
- [Protocol](#protocol)
- [Proxy Endpoint](#proxy-endpoint)
- [SNI](#sni)
- [SNI Domain](#sni-domain)
- [Source Continent IP Geolocation](#source-continent)
- [Source Country IP Geolocation](#source-country)
- [Source Internal IP](#source-internal-ip)
- [Source IP](#source-ip)
- [Source Port](#source-port)
- [Virtual Network](#virtual-network)

**Identity**

- [SAML Attributes](#users)
- [User Email](#users)
- [User Group Emails](#users)
- [User Group IDs](#users)
- [User Group Names](#users)
- [User Name](#users)

**Device Posture**

- [Passed Device Posture Checks](#device-posture)

{{</details>}}

Policies with Allow actions allow network traffic to reach certain IPs or ports. For example, the following configuration allows specific users to reach a given IP address:

| Selector       | Operator | Value           | Logic | Action |
| -------------- | -------- | --------------- | ----- | ------ |
| Destination IP | in       | `92.100.02.102` | And   | Allow  |
| Email          | in       | `*@example.com` |       |        |

### Audit SSH

API value: `audit_ssh`

{{<details header="Available selectors">}}

**Traffic**

- [Application](#application)
- [Destination Continent IP Geolocation](#destination-continent)
- [Destination Country IP Geolocation](#destination-country)
- [Destination IP](#destination-ip)
- [Source Continent IP Geolocation](#source-continent)
- [Source Country IP Geolocation](#source-country)
- [Source Internal IP](#source-internal-ip)
- [Source IP](#source-ip)
- [Source Port](#source-port)
- [Virtual Network](#virtual-network)

**Identity**

- [SAML Attributes](#users)
- [User Email](#users)
- [User Group Emails](#users)
- [User Group IDs](#users)
- [User Group Names](#users)
- [User Name](#users)

**Device Posture**

- [Passed Device Posture Checks](#device-posture)

{{</details>}}

Policies with Audit SSH actions allow administrators to log SSH traffic. Gateway will detect SSH traffic over port `22`. For example, the following configuration logs SSH commands sent to a given IP address:

| Selector       | Operator | Value          | Action    |
| -------------- | -------- | -------------- | --------- |
| Destination IP | in       | `203.0.113.83` | Audit SSH |

For more information on SSH logging, refer to [Configure SSH proxy and command logs](ssh-logging/).

{{<Aside type="note">}}Gateway only audits SSH traffic over port `22`. Non-standard ports, including those specified with the [Destination Port selector](#destination-port), are not supported.{{</Aside>}}

### Block

API value: `block`

{{<details header="Available selectors">}}

**Traffic**

- [Application](#application)
- [Destination Continent IP Geolocation](#destination-continent)
- [Destination Country IP Geolocation](#destination-country)
- [Destination IP](#destination-ip)
- [Destination Port](#destination-port)
- [Detected Protocol](#detected-protocol)
- [Protocol](#protocol)
- [Proxy Endpoint](#proxy-endpoint)
- [SNI](#sni)
- [SNI Domain](#sni-domain)
- [Source Continent IP Geolocation](#source-continent)
- [Source Country IP Geolocation](#source-country)
- [Source Internal IP](#source-internal-ip)
- [Source IP](#source-ip)
- [Source Port](#source-port)
- [Virtual Network](#virtual-network)

**Identity**

- [SAML Attributes](#users)
- [User Email](#users)
- [User Group Emails](#users)
- [User Group IDs](#users)
- [User Group Names](#users)
- [User Name](#users)

**Device Posture**

- [Passed Device Posture Checks](#device-posture)

{{</details>}}

Policies with Block actions block network traffic from reaching certain IPs or ports. For example, the following configuration blocks all traffic directed to port 443:

| Selector         | Operator | Value | Action |
| ---------------- | -------- | ----- | ------ |
| Destination Port | in       | `443` | Block  |

{{<heading-pill style="early-access" heading="h4">}}WARP client block notifications{{</heading-pill>}}

{{<render file="gateway/_client-notifications.md">}}

### Network Override

API value: `l4_override`

{{<details header="Available selectors">}}

**Traffic**

- [Destination Continent IP Geolocation](#destination-continent)
- [Destination Country IP Geolocation](#destination-country)
- [Destination IP](#destination-ip)
- [Destination Port](#destination-port)
- [Protocol](#protocol)
- [SNI](#sni)
- [SNI Domain](#sni-domain)
- [Source Continent IP Geolocation](#source-continent)
- [Source Country IP Geolocation](#source-country)
- [Source Internal IP](#source-internal-ip)
- [Source IP](#source-ip)
- [Source Port](#source-port)
- [Virtual Network](#virtual-network)

**Identity**

- [SAML Attributes](#users)
- [User Email](#users)
- [User Group Emails](#users)
- [User Group IDs](#users)
- [User Group Names](#users)
- [User Name](#users)

**Device Posture**

- [Passed Device Posture Checks](#device-posture)

{{</details>}}

Policies with Network Override actions override traffic directed to, or coming from, certain IPv4/IPv6 addresses or ports. Destination IPs can be public IPs or private IPs connected to your Zero Trust network. For example, the following configuration overrides traffic sent to a public IP with a private IP based on a user’s identity:

| Selector       | Operator | Value           | Logic | Action           |
| -------------- | -------- | --------------- | ----- | ---------------- |
| Destination IP | in       | `95.92.143.151` | And   | Network Override |
| User Email     | in       | `*@example.com` | And   |                  |
| Override IP    |          | 10.0.0.1        |       |                  |

## Selectors

Gateway matches network traffic against the following selectors, or criteria.

### Application

{{<render file="gateway/selectors/_application.md" withParameters="network">}}

### Destination Continent

{{<render file="gateway/selectors/_destination-continent.md" withParameters="net.dst">}}

### Destination Country

{{<render file="gateway/selectors/_destination-country.md" withParameters="net.dst">}}

### Destination IP

{{<render file="gateway/selectors/_destination-ip.md">}}

### Destination Port

{{<render file="gateway/selectors/_destination-port.md">}}

### Detected Protocol

{{<render file="gateway/selectors/_protocol-detection.md">}}

### Device Posture

{{<render file="gateway/selectors/_device-posture.md">}}

### Protocol

{{<render file="gateway/selectors/_protocol.md">}}

{{<Aside type="note">}}

To enable Gateway filtering on TCP and UDP, go to **Settings** > **Network** > **Proxy**. Network policies apply to all enabled protocols unless you use the **Protocol** selector within a policy.

{{</Aside>}}

### Proxy Endpoint

{{<render file="gateway/selectors/_proxy-endpoint.md">}}

### SNI

{{<render file="gateway/selectors/_sni.md">}}

### SNI Domain

{{<render file="gateway/selectors/_sni-domain.md">}}

### Source Continent

The continent of the user making the request.
{{<render file="gateway/selectors/_source-continent.md" withParameters="net.src">}}

### Source Country

The country of the user making the request.
{{<render file="gateway/selectors/_source-country.md" withParameters="net.src">}}

### Source Internal IP

{{<render file="gateway/selectors/_source-internal-ip.md" withParameters="network;;net">}}

### Source IP

{{<render file="gateway/selectors/_source-ip-net.md">}}

### Source Port

{{<render file="gateway/selectors/_source-port.md">}}

### Users

{{<render file="gateway/selectors/_users.md">}}

### Virtual Network

{{<render file="gateway/selectors/_virtual-network.md" withParameters="net.vnet_id">}}

## Comparison operators

{{<render file="gateway/_comparison-operators.md">}}

{{<Aside type="note">}}

The _in_ operator allows you to specify IP addresses or networks using CIDR notation.

{{</Aside>}}

## Value

{{<render file="gateway/_value.md">}}

## Logical operators

{{<render file="gateway/_logical-operators.md" withParameters="**Identity** or **Device Posture**">}}
