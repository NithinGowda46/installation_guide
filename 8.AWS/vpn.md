# AWS Client VPN Setup --- Complete Guide

This guide documents the complete setup used to connect a Mac to private
AWS resources through AWS Client VPN using mutual certificate
authentication.

## Architecture

``` text
Mac
 │
 │ AWS VPN Client
 │ Client certificate + private key
 ▼
AWS Client VPN Endpoint
 │
 │ Authorization: 10.18.0.0/16
 ▼
Private Subnet
 │
 ▼
Private EC2
 │
 └── Web application → Browser

Private EC2
 │
 ▼
NAT Gateway
 │
 ▼
Internet
```

------------------------------------------------------------------------

# 1. Prerequisites

You need:

-   AWS account
-   VPC
-   Private subnet
-   Private EC2 instance
-   AWS Client VPN
-   AWS VPN Client for macOS
-   Git
-   Easy-RSA
-   ACM certificate imported into the same AWS Region as the Client VPN
    endpoint

Example values used in this setup:

``` text
Region:              ap-southeast-1
VPC CIDR:            10.18.0.0/16
VPN Client CIDR:     172.16.0.0/22
Protocol:            UDP
Port:                443
```

The VPN client CIDR must not overlap with the VPC or other networks that
need to communicate with the VPN.

------------------------------------------------------------------------

# 2. Create certificates with Easy-RSA

## 2.1 Clone Easy-RSA

On your Mac:

``` bash
git clone https://github.com/OpenVPN/easy-rsa.git
```

Go into the Easy-RSA directory:

``` bash
cd easy-rsa/easyrsa3
```

------------------------------------------------------------------------

## 2.2 Initialize the PKI

``` bash
./easyrsa init-pki
```

This creates the `pki` directory.

------------------------------------------------------------------------

## 2.3 Create the Certificate Authority

``` bash
./easyrsa build-ca nopass
```

When prompted for the Common Name, use:

``` text
dev-vpn-ca
```

This creates:

``` text
pki/ca.crt
```

The CA is the root authority used to sign the VPN certificates.

------------------------------------------------------------------------

# 3. Create the VPN server certificate

Run:

``` bash
./easyrsa --san=DNS:server build-server-full server nopass
```

This creates:

``` text
pki/issued/server.crt
pki/private/server.key
```

Files:

-   `server.crt` --- server certificate
-   `server.key` --- server private key

Never share the private key.

------------------------------------------------------------------------

# 4. Create the VPN client certificate

Run:

``` bash
./easyrsa build-client-full client1.domain.tld nopass
```

This creates:

``` text
pki/issued/client1.domain.tld.crt
pki/private/client1.domain.tld.key
```

Files:

-   `client1.domain.tld.crt` --- client certificate
-   `client1.domain.tld.key` --- client private key

Never share the client private key.

For multiple employees/devices, create a separate certificate and
private key for each user/device.

Example:

``` bash
./easyrsa build-client-full employee1.domain.tld nopass
./easyrsa build-client-full employee2.domain.tld nopass
```

------------------------------------------------------------------------

# 5. Verify certificate files

Run:

``` bash
ls pki/ca.crt
ls pki/issued/server.crt
ls pki/private/server.key
ls pki/issued/client1.domain.tld.crt
ls pki/private/client1.domain.tld.key
```

You should have all five files.

------------------------------------------------------------------------

# 6. Import the server certificate into AWS ACM

Go to:

**AWS Console → Certificate Manager (ACM)**

Make sure you are in:

``` text
ap-southeast-1 — Singapore
```

Choose:

**Import certificate**

## Certificate body

Use:

``` text
pki/issued/server.crt
```

The contents must include:

``` text
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
```

## Private key

Use:

``` text
pki/private/server.key
```

The contents must include the complete private-key PEM headers and
footer.

Do not share the private key.

## Certificate chain

Use:

``` text
pki/ca.crt
```

Import the certificate.

------------------------------------------------------------------------

# 7. Create the AWS Client VPN endpoint

Go to:

**AWS Console → VPC → Client VPN endpoints → Create Client VPN
endpoint**

## Name

Example:

``` text
dev-vpn
```

## Client IPv4 CIDR

Use:

``` text
172.16.0.0/22
```

Example:

``` text
VPC:       10.18.0.0/16
VPN users: 172.16.0.0/22
```

These do not overlap.

------------------------------------------------------------------------

# 8. Configure authentication

Choose:

**Mutual authentication**

Select the ACM certificate used for the server certificate.

Configure the client certificate/CA as required by the
mutual-authentication setup.

The important concept is:

``` text
AWS Client VPN
       │
       ├── Server certificate
       │
       └── Client certificate authentication
```

The client device will later use:

``` text
client1.domain.tld.crt
client1.domain.tld.key
```

------------------------------------------------------------------------

# 9. Configure connection settings

Use:

``` text
Transport protocol: UDP
Port:               443
```

For an IPv4-only VPC setup, configure the endpoint for IPv4 rather than
requiring IPv6.

For initial IP-based testing, DNS servers can be left empty.

------------------------------------------------------------------------

# 10. Create the endpoint

Click:

**Create Client VPN endpoint**

The endpoint may initially show:

``` text
Pending-associate
```

After target network association it should become:

``` text
Available
```

------------------------------------------------------------------------

# 11. Associate the private subnet

Go to:

**Client VPN endpoint → Target network associations → Associate target
network**

Select:

``` text
VPC: vpc-0bbb28ed9808bdd5f
```

Then select the private subnet.

Example:

``` text
private1
```

Wait until the association becomes:

``` text
Associated
```

For higher availability, associate subnets in multiple Availability
Zones.

------------------------------------------------------------------------

# 12. Client VPN route

When the target network is associated, AWS automatically creates the VPC
route.

You should have a route equivalent to:

``` text
Destination: 10.18.0.0/16
Target:      associated target network
```

You normally do not need to manually create an identical VPC-local
route.

------------------------------------------------------------------------

# 13. Add the authorization rule

Go to:

**Client VPN endpoint → Authorization rules → Add authorization rule**

Use:

``` text
Destination network: 10.18.0.0/16
```

Select:

``` text
Allow access to all users
```

Add the rule.

This allows VPN clients to access resources in the VPC CIDR.

------------------------------------------------------------------------

# 14. Configure EC2 Security Groups

Your EC2 Security Group must allow the traffic you want from the VPN
clients.

For HTTP:

``` text
Type:   HTTP
Port:   80
Source: 172.16.0.0/22
```

For HTTPS:

``` text
Type:   HTTPS
Port:   443
Source: 172.16.0.0/22
```

For SSH:

``` text
Type:   SSH
Port:   22
Source: 172.16.0.0/22
```

Only open the ports you actually need.

Avoid unnecessarily using:

``` text
0.0.0.0/0
```

as the source.

------------------------------------------------------------------------

# 15. Make sure the private EC2 can access the Internet

AWS Client VPN gives your Mac access to the VPC.

It does NOT automatically give a private EC2 Internet access.

For package downloads such as:

``` bash
sudo apt update
```

or:

``` bash
sudo yum update
```

the private subnet needs a NAT Gateway.

The private subnet route table should contain:

``` text
Destination     Target
-------------   ----------------
10.18.0.0/16    local
0.0.0.0/0       NAT Gateway
```

Traffic:

``` text
Private EC2
    │
    ▼
Private Subnet
    │
    ▼
NAT Gateway
    │
    ▼
Internet
```

The NAT Gateway should be in a public subnet with a route to an Internet
Gateway.

------------------------------------------------------------------------

# 16. Download the Client VPN configuration

Go to:

**AWS Console → VPC → Client VPN endpoints**

Select the CURRENT active endpoint.

Choose:

**Download Client Configuration**

AWS downloads an `.ovpn` file, for example:

``` text
aws_vpn.ovpn
```

------------------------------------------------------------------------

# 17. Verify that the downloaded profile belongs to the correct endpoint

This is important if you have created more than one Client VPN endpoint.

Go to Downloads:

``` bash
cd ~/Downloads
```

Check the endpoint:

``` bash
grep "^remote " aws_vpn.ovpn
```

If there are multiple profiles, check each one.

Example:

``` bash
grep "^remote " aws_vpn.ovpn
grep "^remote " aws_vpn.ovpn
```

Make sure you use the file belonging to the CURRENT endpoint.

Do not accidentally use an old `.ovpn` profile.

------------------------------------------------------------------------

# 18. Create the final `.ovpn` profile

Assume the correct downloaded file is:

``` text
aws_vpn.ovpn
```

Run:

``` bash
cd ~/Downloads
```

Copy it:

``` bash
cp aws_vpn.ovpn dev-vpn.ovpn
```

If the actual filename is:

``` text
aws_vpn.ovpn
```

use:

``` bash
cp aws_vpn.ovpn dev-vpn.ovpn
```

------------------------------------------------------------------------

# 19. Add the client certificate and private key

The AWS downloaded configuration for mutual authentication needs the
client certificate and private key.

The easiest method for a single Mac is to embed them into the `.ovpn`
file.

Run this from Downloads:

``` bash
cat >> dev-vpn.ovpn <<EOF

<cert>
$(cat ~/easy-rsa/easyrsa3/pki/issued/client1.domain.tld.crt)
</cert>

<key>
$(cat ~/easy-rsa/easyrsa3/pki/private/client1.domain.tld.key)
</key>
EOF
```

This adds:

``` text
<cert>
CLIENT CERTIFICATE
</cert>

<key>
CLIENT PRIVATE KEY
</key>
```

to the profile.

The private key remains on your Mac.

Do not send the private key to anyone.

------------------------------------------------------------------------

# 20. Verify the final profile

Check certificates:

``` bash
grep -c "BEGIN CERTIFICATE" dev-vpn.ovpn
```

Expected:

``` text
2
```

Check private key:

``` bash
grep -c "BEGIN.*PRIVATE KEY" dev-vpn.ovpn
```

Expected:

``` text
1
```

Do not use commands that print the private key to the screen unless you
specifically need to inspect it locally.

------------------------------------------------------------------------

# 21. Install AWS VPN Client on macOS

Open the AWS VPN Client.

Go to:

**File → Manage Profiles → Add Profile**

Select:

``` text
dev-vpn.ovpn
```

The profile should import successfully.

If you see:

``` text
Failed to add profile.
The configuration file is missing authentication settings.
```

the client certificate/private key is probably missing from the `.ovpn`
profile.

Repeat Step 19.

------------------------------------------------------------------------

# 22. Connect to the VPN

In AWS VPN Client:

1.  Select your profile.
2.  Click **Connect**.
3.  Wait for:

``` text
Connected
```

Do not modify the profile while a connection attempt is running.

------------------------------------------------------------------------

# 23. Test access to the private EC2

Find the EC2:

**EC2 Console → Instances → select your instance**

Find:

``` text
Private IPv4 address
```

Example:

``` text
10.18.25.10
```

From your Mac:

``` bash
ping -c 4 10.18.25.10
```

If ping fails, that does not necessarily mean the VPN is broken because
ICMP may be blocked by the Security Group.

------------------------------------------------------------------------

# 24. Test the private web application

If the EC2 web server listens on port 80:

``` text
http://10.18.25.10
```

Or from Terminal:

``` bash
curl http://10.18.25.10
```

For port 8080:

``` text
http://10.18.25.10:8080
```

For HTTPS:

``` text
https://10.18.25.10
```

The browser should be able to reach the private server while the VPN is
connected.

------------------------------------------------------------------------

# 25. Test Internet/package access from the private EC2

SSH into the private EC2 through your VPN if SSH is allowed.

Test Internet connectivity:

``` bash
curl -I https://google.com
```

Test package repository connectivity:

``` bash
curl -I https://archive.ubuntu.com
```

For Ubuntu:

``` bash
sudo apt update
```

For Amazon Linux:

``` bash
sudo dnf update
```

or:

``` bash
sudo yum update
```

If these work, the private subnet's NAT configuration is working.

------------------------------------------------------------------------

# 26. Recommended traffic design

For your use case, the desired design is:

``` text
                    INTERNET
                       ▲
                       │
                 NAT Gateway
                       ▲
                       │
                Private Subnet
                       │
              ┌────────┴────────┐
              │                 │
         Private EC2       Other resources
              ▲
              │
       10.18.0.0/16
              │
        AWS Client VPN
              ▲
              │
        Encrypted VPN
              │
              ▲
             Mac
```

Your Mac can access private AWS resources without giving those resources
public IP addresses.

------------------------------------------------------------------------

# 27. Important security recommendations

## Use one client certificate per user/device

Do not give every employee the same:

``` text
client1.domain.tld.crt
client1.domain.tld.key
```

Create separate certificates:

``` bash
./easyrsa build-client-full employee1.domain.tld nopass
./easyrsa build-client-full employee2.domain.tld nopass
./easyrsa build-client-full employee3.domain.tld nopass
```

This allows individual certificates to be revoked if a laptop is lost or
an employee leaves.

## Protect private keys

Never share:

``` text
pki/private/server.key
pki/private/client1.domain.tld.key
```

Never commit them to Git.

Never upload them to public repositories.

## Keep EC2 private

For private applications, you do not need to give the EC2 a public IP
merely to access it from your Mac.

Use:

``` text
AWS Client VPN
```

for administrative/private access.

------------------------------------------------------------------------

# 28. Troubleshooting checklist

## VPN profile cannot be imported

Error:

``` text
The configuration file is missing authentication settings.
```

Check:

``` bash
grep -c "BEGIN CERTIFICATE" dev-vpn.ovpn
grep -c "BEGIN.*PRIVATE KEY" dev-vpn.ovpn
```

Expected:

``` text
2
1
```

------------------------------------------------------------------------

## VPN stays at "Establishing connection"

First verify that the `.ovpn` belongs to the current endpoint:

``` bash
grep "^remote " dev-vpn.ovpn
```

Then check:

-   Client VPN endpoint state = `Available`
-   Target network association = `Associated`
-   Authorization rule exists
-   Client certificate is valid
-   Server certificate is valid
-   Security Groups allow the desired traffic
-   Mac has Internet connectivity

Do not rely on pinging the base Client VPN hostname as a test when the
AWS profile uses:

``` text
remote-random-hostname
```

------------------------------------------------------------------------

## Private EC2 cannot download packages

Check the private subnet route table.

You need:

``` text
0.0.0.0/0 → NAT Gateway
```

Also verify:

-   NAT Gateway is available

-   NAT Gateway is in a public subnet

-   Public subnet has:

    ``` text
    0.0.0.0/0 → Internet Gateway
    ```

-   Network ACLs are not blocking traffic

-   EC2 Security Group allows required outbound traffic

-   DNS resolution is enabled

------------------------------------------------------------------------

## VPN connects but browser cannot reach EC2

Check:

1.  Correct private IP.

2.  Client VPN authorization rule:

    ``` text
    10.18.0.0/16
    ```

3.  EC2 Security Group.

4.  Application is actually listening:

    ``` bash
    sudo ss -lntp
    ```

5.  Correct application port.

6.  EC2 subnet/network ACL configuration.

------------------------------------------------------------------------

# 29. Useful commands summary

## Easy-RSA

``` bash
git clone https://github.com/OpenVPN/easy-rsa.git
cd easy-rsa/easyrsa3

./easyrsa init-pki

./easyrsa build-ca nopass

./easyrsa --san=DNS:server build-server-full server nopass

./easyrsa build-client-full client1.domain.tld nopass
```

## Verify certificates

``` bash
ls pki/ca.crt
ls pki/issued/server.crt
ls pki/private/server.key
ls pki/issued/client1.domain.tld.crt
ls pki/private/client1.domain.tld.key
```

## Prepare VPN profile

``` bash
cd ~/Downloads
```

``` bash
cp aws_vpn.ovpn dev-vpn.ovpn
```

Or, if the downloaded file has a suffix:

``` bash
cp aws_vpn.ovpn dev-vpn.ovpn
```

## Add client certificate/key

``` bash
cat >> dev-vpn.ovpn <<EOF

<cert>
$(cat ~/easy-rsa/easyrsa3/pki/issued/client1.domain.tld.crt)
</cert>

<key>
$(cat ~/easy-rsa/easyrsa3/pki/private/client1.domain.tld.key)
</key>
EOF
```

## Verify profile

``` bash
grep -c "BEGIN CERTIFICATE" dev-vpn.ovpn
```

``` bash
grep -c "BEGIN.*PRIVATE KEY" dev-vpn.ovpn
```

## Check endpoint in profile

``` bash
grep "^remote " dev-vpn.ovpn
```

## Test private EC2

``` bash
ping -c 4 <PRIVATE-IP>
```

``` bash
curl http://<PRIVATE-IP>
```

------------------------------------------------------------------------

# 30. Final checklist

``` text
[ ] Easy-RSA installed
[ ] PKI initialized
[ ] CA created
[ ] Server certificate created
[ ] Client certificate created
[ ] Server certificate imported into ACM
[ ] Client VPN endpoint created
[ ] IPv4 configuration selected
[ ] UDP 443 configured
[ ] Private subnet associated
[ ] Target network shows Associated
[ ] VPC route exists
[ ] Authorization rule 10.18.0.0/16 added
[ ] EC2 Security Group configured
[ ] Private subnet has NAT Gateway route
[ ] Client configuration downloaded
[ ] Correct endpoint verified in .ovpn
[ ] Client certificate added to .ovpn
[ ] Client private key added to .ovpn
[ ] Profile imported into AWS VPN Client
[ ] VPN shows Connected
[ ] Private EC2 reachable
[ ] Private web application accessible
[ ] Private EC2 can download packages
```

## Result

With this setup:

``` text
Mac
 │
 ├── Normal Internet → ISP/Wi-Fi
 │
 └── Private AWS traffic
          │
          ▼
    AWS Client VPN
          │
          ▼
     VPC 10.18.0.0/16
          │
          ▼
      Private EC2
```

The EC2 can remain private while authorized VPN users can access
internal applications and services.

------------------------------------------------------------------------

# 31. Other AWS VPN options

The AWS VPC console has several VPN-related options. For the setup in
this guide, **Client VPN endpoints** is the relevant service because it
connects individual laptops/users to private AWS resources.

## 31.1 Customer gateways

A **Customer Gateway (CGW)** represents the device or network on the
customer/on-premises side of a Site-to-Site VPN.

Example:

``` text
Company Office
     │
     │ VPN
     ▼
Customer Gateway
     │
     ▼
AWS
```

It is commonly used when connecting an office, data center, or another
external network to AWS.

## 31.2 Virtual private gateways

A **Virtual Private Gateway (VGW)** is an AWS-side VPN gateway attached
to a VPC. It is commonly used with Site-to-Site VPN connections.

Example:

``` text
Office
  │
  │ VPN
  ▼
Customer Gateway
  │
  ▼
Virtual Private Gateway
  │
  ▼
VPC
```

## 31.3 Site-to-Site VPN

**Site-to-Site VPN** connects an entire external network, such as a
company office, to an AWS VPC.

Example:

``` text
Company Office
     │
     │ Site-to-Site VPN
     ▼
AWS VPC
     │
     ├── Private EC2
     ├── Kubernetes
     └── Databases
```

Users normally do not need to install the AWS VPN Client individually
for this type of connection. The network-to-network VPN provides
connectivity according to the configured routing and security rules.

## 31.4 VPN concentrators

VPN concentrators are used for more specialized VPN architectures where
VPN connections need to be aggregated or managed centrally.

You do **not** need a VPN concentrator for the Client VPN setup in this
guide.

## 31.5 Client VPN endpoints

**Client VPN endpoints** provide individual users/devices with secure
VPN access to AWS resources.

Example:

``` text
Laptop 1 ─┐
Laptop 2 ─┤
Laptop 3 ─┤
Laptop 4 ─┤
   ...    ├── AWS Client VPN ── VPC
Laptop 50 ┘
```

This is the option used in this guide.

### Quick comparison

``` text
Individual laptops/users
        ↓
   Client VPN
        ↓
      VPC


Entire office/network
        ↓
  Site-to-Site VPN
        ↓
      VPC
```

For a setup where employees need to access private EC2, Kubernetes, Argo
CD, Prometheus, Grafana, or other internal AWS services from their
individual laptops, **Client VPN** is the appropriate option.
