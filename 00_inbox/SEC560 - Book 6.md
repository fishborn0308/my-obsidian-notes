## **SEC560 | ENTERPRISE PENETRATION TESTING GIAC Penetration Tester (GPEN)**

**560.6**

CTF and Next Steps

![](SEC560_Book6_page_0_Picture_3.jpeg)

© 2025 SANS Institute. All rights reserved to SANS Institute.

PLEASE READ THE TERMS AND CONDITIONS OF THIS COURSEWARE LICENSE AGREEMENT ("CLA") CAREFULLY BEFORE USING ANY OF THE COURSEWARE (DEFINED BELOW) ASSOCIATED WITH THE SANS INSTITUTE COURSE. THIS IS A LEGAL AND ENFORCEABLE CONTRACT BETWEEN YOU (THE "USER") AND THE ESCAL INSTITUTE OF ADVANCED TECHNOLOGIES, INC. /DBA SANS INSTITUTE ("SANS INSTITUTE") FOR THE COURSEWARE. BY ACCESSING THE COURSEWARE, USER AGREES TO BE BOUND BY THE TERMS OF THIS CLA.

With this CLA, SANS Institute hereby grants User a personal, non-exclusive license to use the Courseware subject to the terms of this CLA. Courseware means all printed materials, including course books and lab workbooks, slides or notes, as well as any digital or other media, audio and video recordings, virtual machines, software, technology, or data sets distributed by SANS Institute to User for use in the SANS Institute course associated with the Courseware. User agrees that the CLA is the complete and exclusive statement of agreement between SANS Institute and User and that this CLA supersedes any oral or written proposal, agreement or other communication relating to the subject matter of this CLA.

BY ACCESSING THE COURSEWARE, USER AGREES TO BE BOUND BY THE TERMS OF THIS CLA. USER FURTHER AGREES THAT ANY BREACH OF THE TERMS OF THIS CLA MAY CAUSE IRREPARABLE HARM AND SIGNIFICANT INJURY TO SANS INSTITUTE, AND THAT SANS INSTITUTE MAY ENFORCE THESE PROVISIONS BY INJUNCTION (WITHOUT THE NECESSITY OF POSTING BOND), SPECIFIC PERFORMANCE, OR OTHER EQUITABLE RELIEF.

If User does not agree to the terms of this CLA, User should not access the Courseware. User may return the Courseware to SANS Institute for a refund, if applicable.

User may not copy, reproduce, re-publish, distribute, display, modify or create derivative works based upon all or any portion of the Courseware, in any medium, whether printed, electronic or otherwise, for any purpose, without the express prior written consent of SANS Institute. User may not sell, rent, lease, trade, share, or otherwise transfer the Courseware in any way, shape, or form to any person or entity without the express written consent of SANS Institute. Additionally, User may not upload, submit, or otherwise transmit Courseware to any artificial intelligence system, platform, or service for any purpose, regardless of whether the intended use is commercial, educational, or personal, without the express written consent of SANS Institute. User agrees that the failure to abide by this provision would cause irreparable harm to SANS Institute that is impossible to quantify. User therefore agrees to a base liquidated damages amount of \$5000.00 USD per item of Courseware infringed upon or fraction thereof. In addition, the base liquidated damages amount shall be doubled for any Courseware less than a year old as a reasonable estimation of the anticipated or actual harm caused by User's breach of the CLA. Both parties acknowledge and agree that the stipulated amount of liquidated damages is not intended as a penalty, but as a reasonable estimate of damages suffered by SANS Institute due to User's breach of the CLA.

If any provision of this CLA is declared unenforceable in any jurisdiction, then such provision shall be deemed to be severable from this CLA and shall not affect the remainder thereof. A written amendment or addendum to this CLA that is executed by SANS Institute and User may accompany this Courseware.

SANS Institute may suspend and/or terminate User's access to and require immediate return of any Courseware in connection with any (i) material breaches or material violation of this CLA or general terms and conditions of use agreed to by User, (ii) technical or security issues or problems caused by User that materially impact the business operations of SANS Institute or other SANS Institute customers, or (iii) requests by law enforcement or government agencies.

SANS Institute acknowledges that any and all software and/or tools, graphics, images, tables, charts or graphs presented in this Courseware are the sole property of their respective trademark/registered/copyright owners, including:

The Apple® logo and any names of Apple products displayed or discussed in this book are registered trademarks of Apple, Inc.

PMP® and PMBOK® are registered trademarks of PMI.

SOF-ELK® is a registered trademark of Lewes Technology Consulting, LLC. Used with permission.

SIFT® is a registered trademark of Harbingers, LLC. Used with permission.

VMware Workstation Pro®, VMWare Workstation Player®, VMWare Fusion®, and VMware Fusion Pro® are registered trademarks of VMware, Inc. Used with permission.

Governing Law: This CLA shall be governed by the laws of the State of Maryland, USA.

Courseware licensed to User under this CLA may be subject to export laws and regulations of the United States of America and other jurisdictions. User warrants he or she is not listed (i) on any sanction programs list maintained by the U.S. Office of Foreign Assets Control within the U.S. Treasury Department ("OFAC"), or (ii) denied party list maintained by the U.S. Bureau of Industry and Security within the U.S. Department of Commerce ("BIS"). User agrees to not allow access to any Courseware to any person or entity in a U.S. embargoed country or in violation of a U.S. export control law or regulation. User agrees to cooperate with SANS Institute as necessary for SANS Institute to comply with export requirements and recordkeeping required by OFAC, BIS or other governmental agency.

![](SEC560_Book6_page_2_Picture_0.jpeg)

![](SEC560_Book6_page_3_Figure_0.jpeg)

![](SEC560_Book6_page_4_Figure_0.jpeg)

# **SEC560 Capture the Flag**

•

- − These challenges will test your ability to apply the right knowledge at the right time
- − Bonus: Some challenges will test at the edges of what we discussed

### • **Agenda:**

- − CTF setup
- − Joining the event
- − Rules of Engagement and CTF hints
- − Initial Access

Section 6 is focused primarily on the CTF. Expect lab time, not lecture!

![](SEC560_Book6_page_6_Picture_1.jpeg)

### **The Scenario**

- After demonstrating business risk to Hiboxy, you've been referred to Lucadon Financial, which has recently acquired Halicron Bank
- Both Lucadon and Halicron are in scope for this CTF, with the goal of determining their susceptibility to a breach
- Networks in scope:
  - − 10.130.9.0/24 (start here)
  - − 10.130.10.0/24
  - − 10.130.11.0/24
  - − 10.130.12.0/24
  - − 10.130.13.0/24
- Entra ID tenants in scope:
  - − halicronbank.com
  - − (For Microsoft 365 only, with read-only email access)

![](SEC560_Book6_page_6_Picture_14.jpeg)

![](SEC560_Book6_page_6_Picture_17.jpeg)

### **The Two Pillars of the CTF**

You'll need OpenVPN connectivity and CTF access (ranges.io) to compete:

![](SEC560_Book6_page_7_Picture_4.jpeg)

- 1. Use your host to browse to https://connect.labs.sans.org (log in using SANS SSO if needed)
- 2. Download this event's **sec560-ctf-range.ovpn** file
- 3. Transfer .ovpn file to both VMs
- 4. Disconnect from Sections 1-5 **sec560-labs-range.ovpn**, connect with new OpenVPN profile

- 1. Use your host to browse to https://ranges.io
- 2. Click **Sign up with SANS** and register using SANS SSO
- 3. Register for the SEC560 OnDemand event using the twoword event code inside **My Labs** in your **SANS Portal**

# **Question Types and Hinting System**

- **Flag Questions**: If a question asks for a flag, it will already be in the format SEC560{some-text-here}
  - − You'll never have to add the curly braces yourself
  - − Found a flag, but don't see the question? You'll find the appropriate question soon
- **Integer Questions**: Answer with the numerical answer
  - − Example question: How many sections does the SEC560 courseware contain?
  - − Example answer: 6
- **Case-Sensitive Questions**: Answer with the correct case for the text
  - − Example: Fluffybunny12! is not the same password as FLUFFYbunny12!
- Hints are available for all non-trivia questions, and cost a trivial single point
  - − Don't hesitate to take a hint if you're stuck for more than 3-5 minutes
  - − Optimize for your learning, not some scoreboard

![](SEC560_Book6_page_9_Picture_2.jpeg)

### **Initial Game Hints**

- **Password Guessing**: There are only five main methods of initial access, but authentication is huge
  - − Hint: Plan for password guessing, which requires both username(s) and password(s).
  - − Where can you find usernames?
- **Authenticated Enumeration**: After authentication as **any** user, you should get an authoritative list of **all** users from that system
  - − Hint: Impacket's GetADUsers.py was featured in an early lab

![](SEC560_Book6_page_9_Picture_10.jpeg)

![](SEC560_Book6_page_9_Picture_12.jpeg)

### Reference:

• https://malcomvetter.medium.com/how-we-breached-your-network-755e40f52d85

# **Let the Games Begin!**

- You now have permission to begin the CTF against the SEC560 OpenVPN environment associated with this event:
  - − **10.130.9.0/24** (start here!)
  - − 10.130.10.0/24
  - − 10.130.11.0/24
  - − 10.130.12.0/24
  - − 10.130.13.0/24
  - − Microsoft 365 for halicronbank.com (you can log in, but no other attacks)
- Note: Take breaks as you need them. Sometimes time away from a given challenge is the most efficient way to make progress

![](SEC560_Book6_page_10_Picture_11.jpeg)

![](SEC560_Book6_page_11_Figure_0.jpeg)

![](SEC560_Book6_page_12_Picture_2.jpeg)

### **SEC560 Capture the Flag**

- **Objective:** Apply the skills learned throughout the week in a hands-on, teambased competition
  - − Today's challenges will test your ability to apply the right knowledge at the right time
  - − Bonus: Some challenges will test at the edges of what we discussed
- **Agenda:**
  - − CTF setup
  - − Team formation and communication
  - − Joining the event
  - − Rules of Engagement and CTF hints
  - − Initial Access

Section 6 is focused primarily on the CTF. Expect lab time, not lecture!

### **The Scenario**

- After demonstrating business risk to Hiboxy, you've been referred to Lucadon Financial, which has recently acquired Halicron Bank
- Both Lucadon and Halicron are in scope for this CTF, with the goal of determining their susceptibility to a breach
- Networks in scope:
  - − 10.130.9.0/24 (start here)
  - − 10.130.10.0/24
  - − 10.130.11.0/24
  - − 10.130.12.0/24
  - − 10.130.13.0/24
- Entra ID tenants in scope:
  - − halicronbank.com
  - − (For Microsoft 365 only, with read-only email access)

![](SEC560_Book6_page_13_Picture_14.jpeg)

![](SEC560_Book6_page_13_Picture_17.jpeg)

![](SEC560_Book6_page_14_Picture_2.jpeg)

### **The Two Pillars of the CTF**

You'll need OpenVPN connectivity and CTF access (ranges.io) to compete:

![](SEC560_Book6_page_14_Picture_5.jpeg)

- 1. Use your host to browse to https://connect.labs.sans.org (log in using SANS SSO if needed)
- 2. Download this event's **sec560-ctf-range.ovpn** file
- 3. Transfer .ovpn file to both VMs
- 4. Disconnect from Sections 1-5 **sec560-labs-range.ovpn**, connect with new OpenVPN profile

- 1. Use your host to browse to https://ranges.io
- 2. Click **Sign up with SANS** and register using SANS SSO
- 3. Register for your event and team with the three-word event code (detailed shortly)

![](SEC560_Book6_page_15_Picture_2.jpeg)

# **Team Formation and Slack Logistics**

- Maximum players per team: 5 (exceptions may apply at instructor discretion)
- Balance teams within a one player difference
  - − Example with 17 students: Three teams of four players, and one team with five
- We'll form teams via a Slack poll in the course channel
- The instructor will add you to a private team channel in Slack
  - − This is separate from the main course channel, and allows private communications between team members, including sharing files, screenshots, and tool output
  - − You can also use a Slack Huddle to talk to your teammates (muting otherwise)

![](SEC560_Book6_page_16_Figure_0.jpeg)

![](SEC560_Book6_page_17_Picture_2.jpeg)

# **Question Types and Hinting System**

- **Flag Questions**: If a question asks for a flag, it will already be in the format SEC560{some-text-here}
  - − You'll never have to add the curly braces yourself
  - − Found a flag, but don't see the question? You'll find the appropriate question soon
- **Integer Questions**: Answer with the numerical answer
  - − Example question: How many sections does the SEC560 courseware contain?
  - − Example answer: 6
- **Case-Sensitive Questions**: Answer with the correct case for the text
  - − Example: Fluffybunny12! is not the same password as FLUFFYbunny12!
- Hints are available for all non-trivia questions, and cost a trivial single point
  - − Don't hesitate to take a hint if you're stuck for more than 3-5 minutes
  - − Maximize learning for yourself and for your team

![](SEC560_Book6_page_18_Picture_2.jpeg)

### **Collaboration**

Progress amongst your team is automatically shared, but knowledge is not!

Minimum expectation: When you solve a question, post the question's title and a one-sentence summary of how you solved it to your team Slack channel.

![](SEC560_Book6_page_18_Figure_6.jpeg)

![](SEC560_Book6_page_19_Picture_2.jpeg)

### **Initial Game Hints**

- **Password Guessing**: There are only five main methods of initial access, but authentication is huge
  - − Hint: Plan for password guessing, which requires both username(s) and password(s)
  - − Where can you find usernames?
- **Authenticated Enumeration**: After authentication as **any** user, you should get an authoritative list of **all** users from that system
  - − Hint: Impacket's GetADUsers.py was featured in an early lab

![](SEC560_Book6_page_19_Picture_10.jpeg)

### Reference:

• https://malcomvetter.medium.com/how-we-breached-your-network-755e40f52d85

![](SEC560_Book6_page_20_Picture_2.jpeg)

# **Let the Games Begin!**

- You now have permission to begin the CTF against the SEC560 OpenVPN environment associated with this event:
  - − **10.130.9.0/24** (start here!)
  - − 10.130.10.0/24
  - − 10.130.11.0/24
  - − 10.130.12.0/24
  - − 10.130.13.0/24
  - − Microsoft 365 for halicronbank.com (you can log in, but no other attacks)
- Note: While we won't pause the CTF for breaks or lunch, you should still step away from the CTF, eat food, drink water, etc.

![](SEC560_Book6_page_20_Picture_12.jpeg)

![](SEC560_Book6_page_21_Figure_0.jpeg)

![](SEC560_Book6_page_22_Figure_0.jpeg)

What are your next steps in exploring cloud penetration testing? This course section is just a fraction of what cloud penetration testing is. For those interested in it, there are books on the subject for purchase. *Pentesting Azure Applications* from No Starch Press is one such book. There is a newer book titled *Penetration Testing Azure for Ethical Hackers*. There are also a few pages in *Gray Hat Hacking, Sixth Edition* that contain cloud penetration testing labs. SANS offers training in the SEC588 course focused on cloud penetration testing. There's even a project called Purple Cloud to make your own lab environment with both on-prem AD:DS and cloud-hosted Entra ID with synchronization. We hope you enjoyed this section of the course.

### Reference:

• https://github.com/iknowjason/PurpleCloud

# **Next Steps: GIAC Exam Strategy**

- As with anything, start by understanding the system and its rules:
  - − Two practice exams (anytime, anywhere) and one proctored exam
  - − Proctored exam can be taken at Pearson VUE centers or online via ProctorU
- High-level strategy: Use the practice exams to inform your study and readiness.
  - − Use practice exams realistically (w/ paper books), take the real exam if you get 85+%
  - − If < 85%: Use the practice exam output (1-5 stars per topic) to guide your study
- Specific tactics: Time is limited (~2 minutes/question), so triage accordingly
  - − Skip questions if you're unsure of the answer and can't find the section in 60 seconds
  - − Answer questions immediately if you're > 80% sure (save that time for others)

### **Understand the Exam Rules**

The exam consists of 82 questions to be completed in 180 minutes, including both multiple-choice

You have access to two practice exams to take at your convenience, along with a single proctored exam that can be done at a Pearson VUE center or online via ProctorU.

# **High-Level Study Strategy**

Use practice exams to gauge readiness. If you achieve above 85%, consider taking the actual exam—you should feel confident you have enough knowledge to do so safely (the 10% above 75% is a safety margin).

If you achieve below 85%, focus on weaker areas based on the practice exam's star ratings (1–5 stars per topic, where each star is approximately 20% of the possible score for that topic).

Online format.

# **Time Management Tactics**

Given the time limit of about 2 minutes per question, prioritize efficiently.

Skip questions if you're unsure and can't find the information within 60 seconds.

Answer immediately if you're over 80% sure, saving time for harder questions. Incremental time spent on that question probably isn't worth it compared to using that time on questions you're unsure of.

### Reference:

• https://www.giac.org/certifications/penetration-tester-gpen/

# **Next Steps: Home Lab**

- Life gives us interesting questions labs help us find answers w/o danger
- A simple lab you use is better than a complex one that you don't use
  - − Often, that's just 1-3 virtual machines (attacker, victim, DC)
- Prefer local VMs (less cost, better availability) via VMware/VBox/Proxmox
- Windows client and server trials are convenient and free for labs

![](SEC560_Book6_page_24_Figure_9.jpeg)

Labs aren't for showing off, despite some popular subreddits. Instead, they're for ongoing learning without the risk of testing inside production environments.

While it's tempting to overbuild your lab, those labs are too commonly broken and unused. Think instead of a good home lab like a stereotypical mechanic's car: not visually appealing, but functional.

Cloud-hosted lab environments are valid and are sometimes necessary (e.g., Purple Cloud or generally labs involving Entra ID). However, many cloud hosting providers charge amounts per VM that are expensive for self-funded labs. Additionally, the best lab is the one you have handy, such as the computer you've used for this courseware.

For Windows clients and servers, trials are available, convenient, and free. Although Microsoft might send you a few emails for a Windows Server trial, it's otherwise free and works well for lab environments.

### References:

- https://bit.ly/kickasslab
- https://www.blackhillsinfosec.com/build-a-home-lab-equipment-tools-and-tips/
- https://leanpub.com/avatar2
- https://www.microsoft.com/en-us/windows-server/trial
- https://www.microsoft.com/en-us/software-download/windows11

![](SEC560_Book6_page_25_Figure_0.jpeg)

Many labs are designed as offline exercises, meaning none of the remote targets are used, so you can practice using only the two SEC560 virtual machines. Many of these labs can even be done without an internet connection.

However, if any remote host (10.130.10.0/24 or 10.130.11.0/24 for Sections 1-5) is used anywhere in the

emailed questions, and the exact step-by-step instructions you have in your labs to work, you'll need the OnDemand bundle for continued access.

There are advantages, however, to learning not just how to break systems but also how to build them and how to fix them. Careful reading of the lab directions will usually reveal the usernames, passwords, IP addresses, and, in general, the configurations necessary to build your own target to replicate the one used in the lab. This work is not just overhead—it's also valuable time spent in better understanding common environments. For many of the targets, simply swapping out the IP address and credentials will suffice, though the exact tool outputs (e.g., from Seatbelt) will differ.

### Reference:

• https://www.sans.org/ondemand/

![](SEC560_Book6_page_26_Picture_0.jpeg)

![](SEC560_Book6_page_26_Picture_1.jpeg)

# **Next Steps: Game of Active Directory**

- Too often, "intentionally vulnerable" VMs leave exploitation solely as an exercise for the reader
- Walk before you run: There are more guided environments than you have time for
- GOAD provides:
  - − Two forests, three domains
  - − AD:CS
  - − SCCM (optional)
  - − Dozens of vulns
  - − Documented exploit paths
- Build it automatically, w/ support for:
  - − Win/Linux/macOS
  - − Many virtualization platforms

![](SEC560_Book6_page_26_Picture_14.jpeg)

Game of Active Directory (GOAD) is a well-maintained project to create a lab environment designed for comprehensive Active Directory exploitation practice. It provides not just a vulnerable environment but a wonderful lab base that can be reused and modified, making it a valuable resource for continuous learning.

Unlike simpler vulnerable VMs, GOAD offers a structured, guided environment that focuses on learning and practicing Active Directory exploitation. This environment is ideal for "walking before you run," allowing controlled exploration of vulnerabilities in a realistic setup.

GOAD creates an Active Directory environment with two forests and three domains, as well as AD Certificate Services (AD:CS) and optional SCCM configurations. This environment includes dozens of vulnerabilities and pre-documented exploit paths, which help to identify and exploit AD-specific weaknesses.

GOAD can be built on various operating systems (Windows, Linux, and macOS) and virtualization platforms like VMware, VirtualBox, and Proxmox. It also allows for deployment to an Azure environment, though, as discussed before, this can be expensive. Still, the flexibility is a nice feature in itself.

### Reference:

• https://github.com/Orange-Cyberdefense/GOAD

![](SEC560_Book6_page_27_Picture_0.jpeg)

General inquiries: info@sans.org Registration: registration@sans.org Tuition: tuition@sans.org Press: press@sans.org 301-654-SANS (7267)

# **Contacts and Resources**

**AUTHOR** Jeff McJunkin **AUTHOR**

Jon Gorenflo

j e ff @ r o g u e v a l l e y i n f o s e c . c o m jon@at t ac k d. c om

@j eff m c j u n k i n

@flakp a k et

**OFFENSIVE OPS RESOURCES**

s a n s . o r g / o ff e n s i v e - o p e r a t i o n s

@SANSOffensive

### **Jeff McJunkin**

Jeff McJunkin is the founder of Rogue Valley Information Security, a consulting firm specializing in penetration testing and red team engagements. Jeff found the offensive side of cybersecurity very alluring during one of the first penetration tests of his career. Feeling the challenge of host defenses like AV and centralized logging, and, at the time, knowing nothing about AV evasion or avoiding events that are likely to cause alerts, he found it all very exciting. The challenge of successfully accomplishing the goal of that pen test, using essentially only native tools, was addictive for Jeff. He was hooked. Since those first penetration tests, Jeff has gone on to become an expert in the field, doing assessments for Fortune 100 companies, architecting two major versions of Core NetWars Experience, and contributing a vast amount of material to SANS Penetration Testing.

His company's website can be found at https://roguevalleyinfosec.com/.

### **Jon Gorenflo**

Jon Gorenflo is the founder of ATTACKD, a cybersecurity consulting firm dedicated to helping organizations of all sizes think like attackers and proactively secure their environments. Whether you're a startup, a small business, or a global enterprise, Jon brings real-world offensive security insights that are practical, accessible, and actionable.

He is also a Principal Instructor with the SANS Institute and the co-author of SEC560: Enterprise Penetration Testing, a leading course on advanced ethical hacking and red team operations. With more than 20 years of combined experience in IT, penetration testing, incident response, and security training, Jon has worked with a diverse range of organizations to uncover critical vulnerabilities and build resilient defenses. In addition, Jon serves as the executive director of Hackers Teaching Hackers (HTH), a grassroots cybersecurity conference that emphasizes hands-on learning and community connection.

You can learn more about ATTACKD at https://attackd.com/ and HTH at https://hthackers.com/