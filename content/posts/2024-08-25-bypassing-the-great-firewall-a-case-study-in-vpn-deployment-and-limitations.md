---
title: 'Bypassing the Great Firewall: A Case Study in VPN Deployment and Limitations'
author: Ao Zhang
type: post
date: 2024-08-26T04:10:16+00:00
url: /?p=1587
featured_image: /wp-content/uploads/2024/08/image-2-1.jpg
firehose_sent:
  - 1724645418
wordads_ufa:
  - s:wpcom-ufa-v4:1724645641
categories:
  - Occupation | 营营

---
China&#8217;s Great Firewall (GFW) is a formidable barrier, effectively restricting domestic access to external internet resources. While the broader implications of this censorship are beyond the scope of this post, I want to share a recent experiment that highlights some challenges in circumventing it.

The premise was straightforward: deploy a VPN server on an EC2 instance with a public IP address, and use that IP address within China to bypass the GFW. To expedite the process, I utilized a well-regarded repository for setting up an IPsec VPN: [GitHub &#8211; hwdsl2/setup-ipsec-vpn][1]. Using their CloudFormation template, I launched a `t2.micro` instance in the `ap-southeast-1` region. The deployment was seamless, and the output included a `.mobileconfig` file, easily importable into MacOS for VPN configuration.

However, after establishing the VPN connection, I noticed significant latency issues. Web pages were extremely slow to load, and ICMP tests revealed frequent timeouts.

Upon investigation, I found that AWS provides no network performance guarantees for `t2.micro` instances, which likely contributed to the poor performance. Upgrading to a t3.micro instance, which includes a minimum of 5 Gbps of bandwidth, resolved the issue, allowing smooth access to Facebook, which is otherwise blocked in China.

Next, I attempted to configure a VPN client, StrongSwan, as recommended by the repository. The AWS StackSet output provided a `.sswan` configuration file, but transferring this file via WeChat proved problematic. The file was consistently blocked during transmission, leading me to suspect that WeChat&#8217;s file filtering might be based on file extensions. Renaming the file to something less conspicuous (e.g., something.s1swan) didn&#8217;t resolve the issue until I rebooted WeChat, indicating that file filtering might also be session-based. FTP file servers could have been another solution but would require more setup.

Despite initial success, the VPN connection was disrupted after just two minutes and remained unusable for the next 24 hours. I suspect that the GFW detected and recorded the public IP address of my VPN server, leading to its subsequent blocking.

While it&#8217;s possible to circumvent this by rotating a pool of public IP addresses, I haven&#8217;t found an off-the-shelf solution that provides a user-friendly interface for non-technical users. Additionally, maintaining such a pool for personal use presents economic challenges.

Overall this quest hasn&#8217;t been successful, but may highlight some blockers in case you are considering the same.

 [1]: https://github.com/hwdsl2/setup-ipsec-vpn/tree/master