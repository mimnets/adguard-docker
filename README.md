# adguard-docker
Adguard docker compose install

# Create a folder
```
mkdir adguard
cd adguard
```

# Make the docker-compose.yml file
```
services:
  adguardhome:
    image: adguard/adguardhome
    container_name: adguardhome
    restart: unless-stopped
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "80:80/tcp"
      - "3000:3000/tcp"
    volumes:
      - ./workdir:/opt/adguardhome/work
      - ./confdir:/opt/adguardhome/conf
```

# Docker Run
```
docker compose up -d
```

# If you face any error like - "Port 53 is already in use"

```
# systemd-resolved এর ডিফল্ট স্টাব লিংকার বন্ধ করুন
sudo mkdir -p /etc/systemd/resolved.conf.d
echo -e "[Resolve]\nDNSStubListener=no" | sudo tee /etc/systemd/resolved.conf.d/no-stub.conf

# সিমলিংকটি লোকাল কনফিগারে পয়েন্ট করুন যেন সার্ভার নিজে ইন্টারনেট পায়
sudo rm /etc/resolv.conf
sudo ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf

# সার্ভিসটি রিস্টার্ট করুন
sudo systemctl restart systemd-resolved
```
# Run again
```
docker compose up -d
```

# By default AdGuard Home will show some IP addresses to setup into your local network devices or local computer to monitor - you can follow this steps for MikroTik router
- You need to find your docker host IP address; Like - where your adguard installed on docker

## Bengali Instructions
```
পদ্ধতি ১: পুরো নেটওয়ার্কের জন্য একসাথে (DHCP Server - সবচেয়ে ভালো উপায়)
এই পদ্ধতিতে মিক্রোটিক রাউটার আপনার নেটওয়ার্কের সমস্ত পিসি, ল্যাপটপ এবং মোবাইলকে স্বয়ংক্রিয়ভাবে AdGuard Home-এর আইপি ডিএনএস (DNS) হিসেবে সাপ্লাই করবে।

১. Winbox দিয়ে মিক্রোটিক রাউটারে লগইন করুন।
২. বাঁদিকের মেনু থেকে IP -> DHCP Server-এ যান।
৩. Networks ট্যাবে ক্লিক করুন।
৪. আপনার লোকাল নেটওয়ার্কের প্রোফাইলটির (সাধারণত 192.168.x.x/24 বা আপনার ল্যান সাবনেট) ওপর ডাবল ক্লিক করে ওপেন করুন।
৫. DNS Servers বক্সে আপনার সার্ভারের আইপি 192.168.22.111 লিখে দিন।
(যদি অন্য কোনো আইপি যেমন 8.8.8.8 বা 1.1.1.1 আগে থেকে থাকে, তবে সেগুলো কেটে দিন বা নিচের অ্যারো চিহ্ন ক্লিক করে দ্বিতীয় অপশনে ব্যাকআপ হিসেবে রাখতে পারেন)।
৬. Apply এবং OK ক্লিক করুন।

নোট: এই পদ্ধতি কার্যকর হওয়ার জন্য আপনার লোকাল ডিভাইসগুলোকে একবার ওয়াইফাই ডিসকানেক্ট করে আবার কানেক্ট করতে হবে অথবা ল্যান কেবল খুলে আবার লাগাতে হবে (যাতে ডিভাইসগুলো নতুন DNS আইপিটি রাউটার থেকে টেনে নিতে পারে)।

পদ্ধতি ২: রাউটারের নিজস্ব DNS পরিবর্তন করা (Alternative Way)
আপনি যদি চান মিক্রোটিক রাউটার নিজেই সমস্ত DNS কুয়েরি হ্যান্ডেল করবে এবং ব্যাকএন্ডে AdGuard Home-কে ব্যবহার করবে, তবে এই নিয়মটি ফলো করুন:

১. Winbox মেনু থেকে IP -> DNS-এ যান।
২. Servers বক্সে আপনার সার্ভারের আইপি 192.168.22.111 বসিয়ে দিন।
৩. Allow Remote Requests অপশনটিতে টিক চিহ্ন (Check) দিন।
৪. Apply এবং OK ক্লিক করুন।

গুরুত্বপূর্ণ (পদ্ধতি ২ এর জন্য): আপনি যদি পদ্ধতি ২ ব্যবহার করেন, তবে IP -> DHCP Server -> Networks-এ গিয়ে DNS Server বক্সে আপনার মিক্রোটিক রাউটারের নিজস্ব আইপি (যেমন: 192.168.22.1 বা আপনার গেটওয়ে আইপি) দিয়ে রাখতে হবে।
```

## English Instructions
```
Method 1: For the Entire Network at Once (DHCP Server - Recommended)
In this method, the MikroTik router automatically provides the AdGuard Home IP as the DNS server to all PCs, laptops, and mobile devices on your network.

Log in to your MikroTik router using Winbox.

From the left menu, navigate to IP -> DHCP Server.

Click on the Networks tab.

Double-click to open your local network profile (usually 192.168.x.x/24 or your LAN subnet).

In the DNS Servers box, enter your server IP: 192.168.22.111.
(If there are other IPs like 8.8.8.8 or 1.1.1.1 already listed, delete them, or click the down arrow to keep them as a backup in the second slot).

Click Apply and then OK.

Note: For this method to take effect, you need to disconnect and reconnect your local devices to the Wi-Fi, or unplug and plug back the LAN cable. This forces the devices to fetch the new DNS IP from the router.

Method 2: Changing the Router's Own DNS (Alternative Way)
If you prefer the MikroTik router to handle all DNS queries itself and use AdGuard Home in the backend, follow these steps:

From the Winbox menu, navigate to IP -> DNS.

In the Servers box, enter your server IP: 192.168.22.111.

Check the Allow Remote Requests box.

Click Apply and then OK.

Important (For Method 2): If you use this method, you must go to IP -> DHCP Server -> Networks and set the DNS Server box to your MikroTik router's own IP address (for example, 192.168.22.1 or your gateway IP).
```

## Extra info
```
যদি আপনি ISP DNS ব্যবহার করতে চান (ঐচ্ছিক):
১. আপনার AdGuard Home ড্যাশবোর্ডে লগইন করুন (http://192.168.22.111:3000)।
২. ওপরের মেনু থেকে Settings -> DNS settings-এ যান।
৩. Upstream DNS servers বক্সে নিচের মতো করে আপনার ISP-এর আইপি এবং অন্যান্য গ্লোবাল DNS যোগ করে দিতে পারেন:

Plaintext
https://dns.cloudflare.com/dns-query
123.200.0.254
203.76.96.5
8.8.8.8
৪. নিচের দিকে স্ক্রোল করে Apply বাটনে ক্লিক করুন।

আমার পরামর্শ (Best Practice):
ISP-এর সাধারণ DNS-এ অনেক সময় ওয়েবসাইট লোড হতে বেশি সময় নেয় এবং এগুলো এনক্রিপ্টেড (Secure) থাকে না। সবচেয়ে ভালো কানেক্টিভিটি এবং প্রাইভেসি পেতে Upstream DNS servers বক্সে শুধু নিচের এই এনক্রিপ্টেড লিংকগুলো ব্যবহার করুন:

Plaintext
https://dns.cloudflare.com/dns-query
https://dns.google/dns-query
এটি আপনার লোকাল নেটওয়ার্কের ইন্টারনেট ব্রাউজিংকে অনেক বেশি সিকিউর এবং ফাস্ট রাখবে।
```
