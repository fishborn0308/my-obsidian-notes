# Miniature Engagement, Recon, and Scanning

## Miniature Engagement

### The Mindset of Penetration Testing

![[スクリーンショット 2026-03-02 9.24.13.png]]

Think like a hacker
- Attacker mindset 
- Be pragmatic 
- Follow promising leads
Help defenders to improve
- Repeatable process 
- Document as you go 
- Minimize risk to production

Balance between these two is crucial for success

At the outset of this class, let's briefly explore the mindset of penetration testers and ethical hackers. That's what our job is: find flaws that enable attackers to do evil on target machines so that an organization can better understand its business risks and resolve vulnerabilities before mayhem ensues. However, to successfully achieve that goal, penetration testers and ethical hackers must maintain a mindset that involves two often contradictory-sounding concepts. First, a penetration tester or ethical hacker must be flexible and pragmatic, thinking outside the box. To be successful, you need to think differently than most traditional system administrators or network architects, trying to solve problems in often untraditional ways. But at the same time, as you wield your pragmatic style, you have to be thorough, methodical, and careful. To be valuable, your work must be understandable and reproducible so that the target organization can understand its vulnerabilities and risks and take action to mitigate the flaws. You need to take good notes and produce a high-quality report that presents your findings in a digestible form for people who don't perform penetration testing or ethical hacking professionally—people who may not share your pragmatic, "think-differently" mindset. Some people struggle with this mindset, erring by allowing one side to dominate over the other. However, many people can resolve this conflict between these two mindsets, thus balancing them. To be a successful penetration tester, you need to strive for this balance.

### Vulnerability, Exploit, Threat, Risk

![[スクリーンショット 2026-03-02 9.23.58.png]]

#### Vulnerability 
A pen tester's goal is to exploit security vulnerabilities, but what is a vulnerability? RFC 4949 defines it as: "A flaw or weakness in a system's design, implementation, or operation and management that could be exploited to violate the system's security policy." Simply put, we can define it as a flaw or weakness that can be exploited by a threat actor. Most people associate vulnerabilities with memory corruption flaws (e.g., buffer overflows), but that is only a portion of all the security vulnerabilities. Vulnerabilities include, but are not limited to: 

- Misconfiguration
- Design and architectural flaws 
- Missing audit features 
- Weak/guessable passwords 
- Default credentials 

There are many other categories of vulnerabilities that are not listed above. MITRE created the Common Weaknesses Enumeration, which is a list of hardware and software weakness types. This isn't to be confused with CVE (Common Vulnerabilities and Exposures), which lists specific flaws in a specific piece of software. 

#### Exploit 
An exploit is a code or technique that takes advantage of a vulnerability. Here are a number of examples: 

- Exploit a vulnerability in memory management and allow the attacker to gain remote access to the system 
- Use existing functionality to upload code and execute it, such as uploading a web shell to a web server using a feature that allows users to upload a resume or avatar 
- An attacker changes their name to JavaScript code, exploiting the weak input filtering on the website
- Run an executable that executes privileged operations on the system, thereby escalating access SEC560 | Enterprise Penetration Testing Vulnerability, Exploit, Threat, Risk A flaw or weakness that can be exploited by a threat actor Examples: Buffer overflow, misconfiguration, design flaws Vulnerability Code or technique that takes advantage of a vulnerability Examples: Public exploit code, upload web shell to server Exploit An agent or actor that can cause harm Examples: Human attacker, worm, user clicking on links Threat Potential for loss or damage Often calculated as: Risk = Likelihood * Impact Risk 9 10 There are many different ways exploits can take advantage of vulnerabilities. In fact, the same vulnerability may be exploited differently by different threats depending on their end goal. We'll discuss exploits and exploit classes in more depth later in this course. 

#### Threat
A threat is an agent or actor that can cause harm. More broadly, it is anything that can cause harm. A threat could be the organized crime attacker attempting to steal credit card numbers, but it also could be the user who accidentally clicks the wrong button. A threat doesn't even have to be human. A worm is a threat since the code is what is propagating and causing damage. Similarly, our own security tools can be considered a threat in certain contexts. A web application vulnerability scanner will crawl the website and submit data. If the scanner is pointed at a production website with production data, what would happen if the scanner "clicked" the delete button? Scanners will attempt to avoid sensitive actions, but things like these do happen. This is one of the reasons it is important to have backups and to have test systems. 

#### Risk
Risk is defined as the potential for loss or damage. Risk is often calculated as (Probability of Occurrence) * (Potential Impact). The potential impact is a number we can calculate with a reasonable degree of certainty; however, the likelihood is a much more contentious number. Often, the likelihood (and ultimately the risk value itself) comes down to the expert opinion of the security team. The team uses its collective knowledge and experience to determine the risk and ultimately provide guidance to the business. Balancing the risk with organizational needs is one of the fundamental goals of the security team. Unfortunately, many security teams are viewed as roadblocks—hindering the organization instead of enabling it to develop and progress securely. As a penetration tester, part of your role is to estimate the risk related to the vulnerabilities you uncover. This is a powerful and important role. Junior testers will often overestimate the risk of vulnerabilities. Their reports will often be top-heavy, where most of the risks are categorized as high or critical. As penetration testers mature, they learn to understand the risk and how it plays into the organizational processes. Understanding the risks and identifying them accordingly helps the organization prioritize remediation efforts. We can simplify the risk by thinking about it as the intersection of threat, vulnerability, and risk. If we can reduce or eliminate one of these pieces, the risk is reduced or eliminated, respectively. We can limit access to the system, preventing a threat from accessing the service. Similarly, if the system no longer has sensitive data (Personally Identifiable Information / PII, passwords, etc.), then there is no risk since it provides the attacker no value, and no data can be lost, stolen, or destroyed. We can implement filtering tools to prevent an exploit from working. For example, we could use a WAF (web application firewall) to filter the characters of what would trigger the vulnerability. Similarly, an Intrusion Prevention System (IPS) could prevent specific actions against the target system that would trigger an exploit. In both cases, the vulnerability still exists, but it can't be exploited. Note: Be careful with this approach, as attackers will often change signatures or encoding to bypass these tools. Finally, we could remove the risk by patching the vulnerability.

### Pen Test, Red Team, Purple Team, Vuln Assessment, Audit

![[スクリーンショット 2026-03-02 9.29.16.png]]

#### Penetration Testing
Penetration testing focuses on identifying vulnerabilities in a target that could allow an attacker to penetrate the computer or network and steal, damage, or corrupt data. Penetration testers (pen testers) will use tools similar to the malicious attackers. Sometimes, the malicious actors even take tools and techniques from penetration testers. As the name implies, penetration testing requires penetration (if possible). A test that forbids penetration is not a penetration test. Sometimes, targets ask for a penetration test but then disallow exploitation, password guessing, and other activities that would allow the testers to verify flaws and find second-order vulnerabilities. These handcuffed tests are more akin to vulnerability assessments.

#### Red Team
Red teams are focused on testing the effectiveness of the entire security program. They emulate the Tactics, Techniques, and Procedures (TTPs) of real-world adversaries with the goal of measuring weaknesses in defense, detection, and response. Red teams use many of the same tools as penetration testers; however, they have a different focus. The red team is focused on identifying deficiencies in the blue team's (defenders) ability to detect, respond, and eradicate a threat. A red team only uses vulnerabilities that help them accomplish their goal, while penetration testers are most often tasked with finding many vulnerabilities. Penetration testing is focused on finding flaws, understanding their business risks, and helping the organization improve its security stance. The red team's primary job is to help make the blue team better equipped to detect and respond to attacks. To put it simply, penetration testing focuses on the defenses (technology), and the red team focuses on the defenders (people).

#### Purple Team 
Purple teams are cross-functional teams where the red and blue teams work together. The closer the interaction, the better. This type of assessment includes metrics on blue's ability to identify, detect, and SEC560 | Enterprise Penetration Testing Pen Test, Red Team, Purple Team, Vuln Assessment, Audit Identify flaws that attackers could cause business harm with (testing preventive controls) Pen Test Red Team Designed to test detection and response capabilities Cross-functional team of red and blue teamers, and/or tightening the feedback loop between offense and defense Purple Team Identify, quantify, and rank vulnerabilities (no exploitation) Vulnerability Assessment Security Audit Audit implies testing against a particular set of standards 11 12 respond to an attack. The testing should be documented to show advances in detection and response capabilities. These metrics can be used to justify investments in tools and training.

#### Vulnerability Assessment
Many people use the phrases vulnerability assessments and security assessments to describe the work done by penetration testers and ethical hackers, but there is a subtle distinction between a penetration test and a security assessment. A penetration test is focused on getting in or stealing data. The emphasis is on penetrating the target environment by exploiting discovered vulnerabilities. Vulnerability assessments and security assessments are focused on finding vulnerabilities, often without regard to actually exploiting them and getting in. Thus, penetration testing often goes deeper, with its goal of taking over systems and stealing data, whereas security and vulnerability assessments are broader, involving the process of looking for security flaws. These assessments also often include policy and procedure reviews, which are usually not included in penetration testing. The table on the previous slide shows the differences between the different types of assessments. Of course, each can vary to some degree, with penetration testing having the greatest variability. A penetration tester is often tasked with finding all vulnerabilities, even those that aren't exploitable.

#### Security Audit 
Finally, we have the phrase security audit. An audit implies that we are measuring things against a fixed, predetermined, rigorous set of standards. These audits are almost always done with detailed checklists. Some penetration testing and ethical hacking organizations have created their own internal checklists of items that need to be covered in a test, but these checklists aren't as detailed as those for comprehensive audits. Our focus in this class is not on auditing. SANS has numerous other classes that address security audits in detail. Our focus is on ethical hacking and penetration testing.

#### Penetration Testing Goals

Penetration testing is demonstrating business risk from realistic attackers: 
- Modeling real-world adversaries 
- Finding vulnerabilities (ideally, before the adversary) 
- Exploiting those flaws under controlled circumstances 
- Determining business risk 
- Improving the organization's security

A formal definition of penetration testing is as follows:

>Penetration testing involves modeling the techniques used by real-world computer attackers to find vulnerabilities and, under controlled circumstances, to exploit those flaws in a professional, safe manner according to a carefully designed scope and Rules of Engagement to determine business risk and potential impact, all with the goal of helping the organization improve security practices.

The key is that the vulnerability identification and exploitation should always be business-focused. The penetration test findings should always focus on the organizational risk, not just the technical risk. Many organizations use ethical hacking and penetration testing to find security flaws before their adversaries do. After applying their security policies, procedures, and technology, organizations can use thorough penetration tests to see how effective their security is in light of an actual attack, albeit by friendly attackers. An added benefit of ethical hacking and penetration testing is that because they show real vulnerabilities and indicate what a malicious attacker might be capable of achieving, they can get management's attention. Decision makers, when presented with the carefully formulated results of a test in business terms, are more likely to provide resources and attention to improve the security stance of an organization.

### Types of Penetration Tests

- Network services test
	- One of the most common 
- Assumed breach test 
- Web application test 
- Social engineering test 
- Email-based or phone-based 
- Wireless security test 
	- Not just Wi-Fi 
- Physical security test 
- Product security test
	- Could be software package or hardware (e.g., IoT) 
	- Breaking or bypassing encryption on local data or intercepted traffic
- Enterprise penetration test
	- Pen testing the whole company 
	- If it can be in scope, it's in scope

There are numerous kinds of penetration tests:

- **Network services test:** One of the most common types of tests, it involves finding target systems on the network, looking for openings in their underlying operating systems and available network services, and then exploiting them remotely. Some of these network service tests happen remotely across the internet, targeting the organization's perimeter networks. Others are launched locally from the target's own facilities to evaluate the security of the internal network, seeing what kinds of vulnerabilities an internal user could discover.
- **Assumed breach test:** Designed to find vulnerabilities in the network once an attacker has gained access to a system in the network. This is a fantastic way to find issues in Active Directory permissions, file permissions (excessive sharing on file shares), and client-side software. 
- **Web application test:** These tests look for security vulnerabilities in web-based applications and APIs. 
- **Social engineering test:** Involves attempting to trick a user into revealing sensitive information, such as a password, or possibly convincing a user to click a link in an email. These tests are often conducted via email or over the phone, targeting selected users and evaluating processes, procedures, and user awareness. 
- **Wireless security test:** These tests involve exploring a target's physical environment to find unauthorized wireless access points or authorized wireless access points with security weaknesses. 
- **Physical security test:** This test looks for flaws in the physical security practices of a target organization. Testers might attempt to gain access to buildings and rooms or to take laptops, desktops, or recycling bins out of target facilities. A dumpster diving test is a variation of a physical security analysis. Physical testing must be conducted carefully to ensure that the testers do not get hurt or arrested during their work. 
- **Product security test:** This test focuses on one specific product. It could be hardware or software. In this kind of test, you look for security flaws in products that can be used in the tester's laboratory systems. Such tests look for flaws in the software, such as exploitable buffer overflow conditions, SEC560 | Enterprise Penetration Testing Types of Penetration Tests • Network services test − One of the most common Assumed breach test • Web application test • Social engineering test − Email-based or phone-based • Wireless security test − Not just Wi-Fi • Physical security test • Product security test − Could be software package or hardware (e.g., IoT) − Breaking or bypassing encryption on local data or intercepted traffic • Enterprise penetration test − Pen testing the whole company − If it can be in scope, it's in scope 14 15 privilege escalation flaws, and the exposure of unencrypted sensitive data. These tests could focus on bypassing or breaking the encryption of data stored on a local system or across the network. 
- **Enterprise penetration test:** This test has as its scope, to a first approximation, everything that the company has the authority to grant the penetration tester to test. Some exclusions may still apply, but the tester should emphasize that removing systems from the scope of the enterprise penetration test reduces the fidelity of the results.

### Attack Phases

![[スクリーンショット 2026-03-02 9.51.35.png]]

Both malicious attackers and professional penetration testers/ethical hackers apply various phases in their attacks. Attacks are often separated into these phases:

- Reconnaissance is the process of investigating the target organization to gather information about it from publicly available sources, such as domain registration services, websites, and so on. Some people include techniques such as social engineering and dumpster diving in the recon phase. 
- Scanning is the process of finding openings in the target organization, such as internet gateways, available systems, listening ports, and vulnerability lists. 
- In the Exploitation phase, attackers exploit target systems to compromise them, possibly getting control of them or causing a denial-of-service attack. 
- Post-exploitation is what happens after the initial compromise. Both penetration testers and malicious attackers use their access to pivot and move throughout the target environment.

Although legitimate tests often include the previously listed phases, malicious attackers often go further than the Rules of Engagement allow for a professional penetration test. The next phase, often used by a malicious attacker to maintain access to and control of a target machine, involves setting up the compromised machine so that the attacker can keep control over it, with techniques such as installing backdoors and planting rootkits. Malicious attackers also often use a final phase, Covering the Tracks, in which they employ log editing, file hiding, and covert channels to hide their activities on a system. 

Please note that the best of the attackers (both the good guys and the evil ones) are pragmatists. They don't always proceed from reconnaissance to scanning to gaining access and so on. Sure, they use these steps, but they are likely to jump around among them as events and discoveries warrant. For example, during the recon phase, attackers may discover an exploitable flaw that they will use to gain access directly, temporarily bypassing scanning. Then, after they gain access to one machine, they may go back and start scanning. 

From a professional testing perspective, though, be careful when jumping out of order among these steps, making sure that you return to the earlier phases to conduct a comprehensive test.

### Recent Enterprise Breaches: Learning from Reality

- Change Healthcare 
	- Root cause: Missing MFA on public-facing Citrix server 
	- Impact: $2+ billion in damages with nation-wide disruption 
	- Lesson: Basic controls prevent catastrophic breaches 
- Snowflake 
	- Method: Infostealer → stolen credentials → 160+ victims 
	- Victims: Ticketmaster (560M records), AT&T, Santander Bank 
	- Lesson: Risks in your supply chain and third parties = risks to your enterprise
- Ransomware Evolution 
	- RansomHub: 434 victims in first year 
	- Healthcare: 92% of organizations targeted in last 12 months 
	- Average payment: $2.73 million (double/triple extortion now standard) 
	- Lesson: Ransomware remains #1 threat 
- Living Off The Land (LOL) 
	- Many incidents use only legitimate tools (PowerShell, WMI, CertUtil, rundll32) 
	- Even when using C2, attackers often "shell out" to native binaries such as whoami, nltest

### Lockheed Martin Cyber Kill Chain

- Pros:
	- Industry recognition 
	- Focus on attacker's actions and viewpoint 
	- Breaking the chain breaks the attack 
- Cons: 
	- For most orgs, they see nothing until steps 3 and 4

**Pros of the Cyber Kill Chain**
Industry Recognition: The Cyber Kill Chain model is well-regarded across the cybersecurity industry, known for its effectiveness in organizing the stages of cyber threats. 

Focus on Attacker's Actions: The model is structured around the actions of an attacker, allowing defenders to anticipate and counteract each stage of an attack effectively. 

Preventative Strategy: By identifying and disrupting any stage of the kill chain, it is possible to stop the attack process altogether. This "break the chain, break the attack" approach underlines the potential to mitigate threats before they reach their objectives.

**Cons of the Cyber Kill Chain** 
Visibility and Detection Challenges: One significant drawback of the model is that many organizations do 

the model). This late detection makes proactive defense challenging and often leaves organizations reactive to threats rather than preemptive.

### MITRE ATT&CK

- MITRE ATT&CK framework is a knowledge base of attack tactics based on realworld attackers
- Techniques are grouped together into tactics
	- Phishing and Valid Accounts techniques are part of the Initial Access tactic
	- Techniques have multiple procedures (PsExec.exe vs Metasploit's psexec)
- The framework includes Groups, which track sets of related activity to a common adversary group (such as APT1, FIN7, etc.)
- Great resource for learning specific tools and techniques as well as understanding the playbook of real-world attackers

MITRE ATT&CK (pronounced "attack") is a knowledge base of attack tactics based on real-world attackers. The framework includes many techniques (and sub-techniques) used by good and bad attackers. It is a valuable resource for learning new tools and techniques. The techniques are grouped together into "tactics." For example, the "Initial Access" tactic includes the techniques Exploit Public Facing Application, Phishing, and Valid Accounts, among others. 

The tactics and techniques are pieces used by an attacker. ATT&CK also includes "Groups," which is a set of related activities tracked to a common adversary group (e.g., APT1, FIN7). These provide the story of various breaches, which we can use to learn when and how to use specific attacks. 

The combination of the Groups and the Tactics/Techniques make ATT&CK a valuable resource to develop our own offensive skills. 

MITRE ATT&CK Matrix for Enterprise can be found at https://attack.mitre.org/.

### Attackers Want Your Data

- Breach: Unauthorized access to protected information
	- Some attacks begin and end in one step:
		- Open Amazon S3 buckets
		- Public-facing exploitable systems such as 2025's Microsoft SharePoint, Citrix NetScaler, Palo Alto GlobalProtect, Ivanti VPN, and SonicWall
		- Unprotected databases such as MongoDB
	- If the entire breach is a wget command, you have no time for detection and response!

Open Amazon S3 Buckets: Misconfigured S3 buckets are a common error that can lead to massive data leaks. These buckets, if not properly secured, can be accessed without authorization using simple web requests. 

Vulnerabilities in File Transfer Software: Tools like MOVEit and GoAnywhere, designed for secure file transfer, have had vulnerabilities discovered in recent years that could allow attackers to exploit them and gain unauthorized access. 

Unprotected Databases: Databases such as MongoDB that are left unprotected on the internet can be accessed and downloaded without needing sophisticated hacking techniques. 

Consequences of Quick Breaches: An entire data breach could be conducted with a single wget command, leaving virtually no time for detection and response. This goes to show the importance of proactive security measures and defense in depth. 

There are no walls so high, nor so strong, that they cannot be breached. We need time for our detection and response capabilities.

### Let's Keep Things Simple

![[スクリーンショット 2026-03-02 10.11.42.png]]

Here, we aim to simplify your understanding of the typical progression of an attack, breaking it down into three critical phases that reflect the attacker's pathway from initial entry to achieving their ultimate objectives. 

Attackers want your data. Since that data is often inside your environment, attackers need inside. Since that data requires privileges that not everyone has access to, attackers often need privileges as well.

**Initial Access** 
The first step for an attacker is gaining access to the internal network or systems. This can be accomplished through various methods, such as phishing, exploiting vulnerabilities, or using stolen credentials.

**Privileges** 
Once inside, attackers often seek to escalate their privileges to gain higher levels of access. This can involve exploiting system weaknesses, configuration errors, or using social engineering tactics to gain the necessary permissions to access more sensitive areas of the network.

**Actions on Objective**
The final phase involves the attackers executing their intended actions, which could be data theft, deploying malware, or conducting other malicious activities designed to fulfill their initial objectives. This phase represents the culmination of the attack process and is ultimately what the attackers aim to achieve through their efforts.

### Initial Access Eventually Becomes a Breach

![[スクリーンショット 2026-03-02 10.15.55.png]]

- Attackers inside your perimeter will eventually cause harm 
	- Much like a trespasser inside your building or an unchecked illness
- Once attackers have gained Initial Access, it starts a race: 
	- Attackers race to gain necessary Privileges for their Actions on Objective 
	- Defenders race to detect and respond (eradicating attackers' presence)
- Whoever wins the race wins the incident

This slide highlights the inevitability of harm once attackers breach your perimeter, comparing it to a trespasser in a building or an unchecked illness. Once inside, the attacker has started a race: 

Attackers race to escalate privileges and achieve their objectives, such as data theft or system disruption. 

Defenders, starting only when they detect the attacker's presence, must quickly respond to eradicate the attackers and prevent damage. 

The race begins only when defenders detect the breach, emphasizing the importance of detective controls for early detection. Whoever wins this race—attackers or defenders—determines the incident's outcome. Rapid detection and response are crucial to maintaining security.

### Preventing Breaches (1)

To prevent successful breaches, defenders need to both detect and respond to attackers who gain initial access, before attackers accomplish their goal.

![[スクリーンショット 2026-03-02 10.17.06.png]]

This slide emphasizes the need for defenders to quickly detect and respond to attackers to prevent successful breaches. It highlights the following points:

- Shorter Detection and Response Timeline: Defenders must rapidly detect and respond to threats to minimize potential damage. 
- Longer Privilege Escalation and Actions on Objective Timeline: Slowing down attackers' progress provides defenders more time to act.

The combination of quick detection and response with delaying attackers' actions leads to preventing breaches, illustrating the importance of efficient monitoring and swift defensive actions.

### Preventing Breaches (2)

To prevent successful breaches, defenders need to both detect and respond to attackers who gain initial access, before attackers accomplish their goal.

![[スクリーンショット 2026-03-02 10.18.24.png]]

This slide highlights the consequences of delayed detection and response in preventing breaches. It illustrates the following points:

- Longer Detection and Response Timeline: If defenders take too long to detect and respond to threats, it increases the likelihood of a successful breach. This is all too common due to failed detective controls, erroneous marking of alerts as false positives, shift changes or outsourced SOC offering differing capabilities, etc. 
- Shorter Privilege Escalation and Actions on Objective Timeline: Attackers quickly escalate privileges and achieve their objectives if not promptly countered.

Imagine an attacker gaining access via credential stuffing and VPN (or RDP), followed by Kerberoasting for privileges, followed by domain-wide ransomware. This sort of attack can happen in minutes, not hours or days.

### Defenders' Three Objectives

1. This slide outlines three key objectives for defenders to enhance their security posture: Reduce the number of ways attackers gain initial access: 
	- Consolidate remote support tools and monitor for unauthorized usage. 
	- Minimize the external attack surface by securing 1FA services and Exchange servers. 
	- Remove endpoint code execution methods like macros, AutoDDE, and Quick Assist 
2. Lower the time to detect and respond to an attacker: 
	- Focus on detecting post-exploitation activities early. 
	- Regularly test and improve response capabilities to quickly eradicate threats. 
	- Use deception technologies like Canarytokens to identify breaches early. 
3. Increase the time for an attacker to accomplish their goal: 
	- Remove easy targets for privilege escalation. 
	- Conduct penetration testing to identify and mitigate vulnerabilities. 

These objectives aim to prevent breaches by reducing entry points (so defenders have to respond less frequently), improving detection and response times (so defenders get early notifications of attacker's initial access), and making it harder for attackers to achieve their goals (to buy time for the detection and response capabilities of the defenders).

### Defenders' Objectives (1)

1. Reduce the number of ways attackers gain initial access 
2. Lower the time to detect and respond to attackers 
3. Increase the time for attackers to accomplish their goal

![[スクリーンショット 2026-03-02 10.24.01.png]]

This slide focuses on the first objective: Reduce the number of ways attackers gain initial access. 

No matter how many advantages you have, if you get in enough fights, you will lose. Seek to fight less often.

### Defenders' Objectives (2)

1. Reduce the number of ways attackers gain initial access
2. Lower the time to detect and respond to attackers
3. Increase the time for attackers to accomplish their goal

![[スクリーンショット 2026-03-02 10.26.39.png]]

This slide focuses on the second objective: Lower the time to detect and respond to attackers. 

Reducing the time it takes to detect and respond to threats is crucial for minimizing damage. The analogy of a shorter racetrack signifies the importance of speeding up detection and response capabilities. Faster detection and response can help prevent attackers from completing their objectives. 

Implement strategies like early detection of post-exploitation activities, regularly testing and improving response capabilities, and utilizing deception technologies like Canarytokens to quickly identify breaches. By doing so, defenders can stay ahead of attackers and reduce the impact of potential breaches.

### Defenders' Objectives (3)

1. Reduce the number of ways attackers gain initial access 
2. Lower the time to detect and respond to attackers 
3. Increase the time for attackers to accomplish their goal

![[スクリーンショット 2026-03-02 10.27.51.png]]

This slide focuses on the third objective: Increase the time for attackers to accomplish their goal. 

Extending the time it takes for attackers to complete their objectives gives defenders more time to detect and respond to threats. The longer racetrack symbolizes the need to make the attacker's path more complicated and time-consuming. 

Strategies include removing easy targets for privilege escalation and conducting regular penetration testing to identify and mitigate vulnerabilities. By increasing the difficulty and time required for attackers to achieve their goals, defenders can better manage and contain potential breaches, ultimately improving overall security.

### Ideal vs. Acceptable Timelines

- Defenders can't win unless they know about each intrusion! 
- Defenders' three jobs 
	- Ideal: Defenders can detect and respond (eradicating attackers' presence) before attackers have spread the infection 
	- Minimum: Defenders detect and respond before the breach 
- Incidents don't stop when attackers are detected! 
- "Shell is only the beginning": Carlos Perez's mantra for attackers 
- "Detection is only the beginning": Mantra for defenders

Ideally, defenders would detect and respond to threats quickly enough to eradicate attackers before they can spread. This requires highly effective monitoring and response capabilities but minimizes the damage done by the attacker, minimizes the amount of information attackers are able to gain before they're kicked out, and asymmetrically costs attackers more than defenders (as attackers often invest significantly into their initial access). 

Ultimately, though, defenders need to at least detect and respond before the breach occurs. Otherwise, what's the point of having human defenders? Again, no wall is so tall or so strong that it can't be breached. 

Detection is only the beginning of the response process. Continuous monitoring and action are required to fully address and eliminate threats. 

Defenders must be vigilant and proactive, knowing that successful defense depends on early detection and swift, effective response to every intrusion.

### Only Five Ways In (Initial Access)

Organizations have far more than just internet-facing services to attack:
1. Employees who can run code 
2. Remote methods of connectivity (VPN, VDI, RDP) 
3. Internal networks with DHCP
4. Third-party software 
5. Public-facing software (such as Exchange, MOVEit, JetBrains)

![[スクリーンショット 2026-03-02 10.31.26.png]]

Here, we show attackers five methods of initial access, illustrating how the attack surface of an organization is more than merely their public-facing assets.

1. Employees Who Can Run Code: Targeted primarily through phishing campaigns. Phishing aims to deceive employees into executing malicious code or revealing sensitive credentials, leveraging the access privileges of unsuspecting employees to infiltrate systems. Remember, phishing can be more than just via email! 
2. Remote Connectivity Methods (VPN, VDI, RDP): These are exploited by attackers logging on remotely. Vulnerabilities in remote access technologies or poor security practices (such as weak passwords and lack of multi-factor authentication) can allow unauthorized access to an organization's internal network. 
3. Internal Networks with DHCP: Attackers can insert rogue devices into the network. These devices can be configured to exploit DHCP functionalities to maliciously alter network configurations or intercept network traffic, compromising internal communication. 
4. Third-Party Software: Often a vector for supply chain attacks. Attackers exploit vulnerabilities in third-party software products that are integral to business operations to compromise downstream systems and data. 
5. Public-Facing Software (such as Exchange, MOVEit, JetBrains): Typically involved in "real" exploitation scenarios, where attackers directly exploit known vulnerabilities in software that is accessible from the internet, often resulting in data breaches or further network compromise.

Reference: https://malcomvetter.medium.com/how-we-breached-your-network-755e40f52d85

### Class Theme (Hiboxy Introduction)

- Hiboxy is our class's target company 
	- They make 3D-printed zippers; very hightech! 
- The CEO (Mark Cruz) wants to know what business risks he's facing
- What's the initial scope? Just Mark's card.

This class focuses on Hiboxy, a company that specializes in producing 3D-printed zippers, representing a significant advancement in textile manufacturing technology. The CEO, Mark Cruz, is concerned about the potential business risks that could impact his company. Understanding these risks is crucial for strategic planning and maintaining the competitive edge of his high-tech business.

1. Company Overview: Hiboxy stands out in the manufacturing sector with its innovative approach to producing zippers using 3D printing technology. This method likely presents unique challenges and opportunities, from production efficiency to intellectual property concerns. 
2. CEO's Concern: Mark Cruz, as the CEO, has expressed a desire to thoroughly understand the risks facing Hiboxy. His proactive approach suggests a focus on both immediate operational risks and longterm strategic vulnerabilities that could influence the company's success. 
3. . Initial Scope: The focus is initially narrowed to understanding the risks as they pertain directly to the CEO, Mark Cruz, possibly indicating a personal approach to leadership and decision-making within the company.

We also see Mark's business card, possibly obtained via a first meeting at a conference.

### Reconnaissance (in Brief)

- Reconnaissance: Exploration to find useful information for a given purpose 
	- Invest in some recon before the first scoping call! 
	- How does email flow to the client? 
		- On-premises Exchange? Microsoft 365? ProofPoint? 
	- What's the username format? Password requirements? 
	- What's the email address format? Breached credentials? 
	- Any single-factor authentication services exposed to the Internet? 
		- Especially those backed by on-prem Active Directory!

The slide emphasizes the criticality of conducting reconnaissance before the initial scoping call with a client. This initial investigation lays the groundwork for a more informed and effective engagement strategy.

1. Purpose of Reconnaissance: The aim is to gather actionable intelligence that can be used to tailor the approach to the specific environment and vulnerabilities of the client. 
2. Key Areas of Focus:
	- Email Systems: Understanding the email infrastructure, such as whether the client uses Onpremises Exchange, Microsoft 365, or ProofPoint, helps in assessing potential vectors for phishing or direct compromise. 
	- Authentication Protocols: Information on username and password formats and any recent breaches of credentials can guide the development of penetration testing tactics focused on credential stuffing or brute force attacks 
	- Authentication Security: Identifying if there are any single-factor authentication systems, particularly those tied to on-prem Active Directory, exposed to the internet. These systems can often be targeted for initial access due to their lower security compared to multi-factor systems.

This reconnaissance phase is vital for identifying the weakest links and potential attack vectors within a client's infrastructure, thereby enabling a more targeted and effective security assessment.

### Features Are Forever

![[スクリーンショット 2026-03-02 10.38.33.png]]

"Hackers don't break in. We log in." 

Cybersecurity expert Keren Elazari succinctly captures a critical aspect of contemporary cybersecurity challenges on the podcast "How Real Is It?". 

This quote shows the evolution of hacking tactics from forceful entry (e.g., breaking through security barriers, such as exploitation of public-facing systems) to subtler methods, such as phishing, social engineering, or using stolen credentials. These tactics exploit the normal functionalities of systems ("features") rather than their weaknesses ("bugs"), which can make them harder to detect and prevent. 

Organizations must therefore adopt a more comprehensive approach to security, one that goes beyond patching vulnerabilities to include managing and securing user identities and access privileges. 

The emphasis on logging in rather than breaking in shows that cybersecurity is increasingly about understanding and mitigating risks associated with user behavior and access management. 

Reference: https://www.youtube.com/watch?v=6BqpU4V0Ypk

### Finding Single-Factor Auth

![[スクリーンショット 2026-03-02 10.40.08.png]]

**Microsoft 365:** Often used by organizations for collaboration and communication, Microsoft 365 might be secured with only a password, making it a prime target for credential stuffing or phishing attacks.

**Remote Desktop Protocol (RDP):** Critical for remote administration but also a common entry point for attackers if only protected by 1FA, due to the direct access it provides to a system's desktop environment.

**Virtual Private Networks (VPNs):** While VPNs secure the transmission of data across networks, using 1FA can make them vulnerable to interception and unauthorized access, particularly if compromised credentials are used.

**Microsoft Exchange:** As a widely used email server, Exchange systems secured with 1FA are at risk of being compromised, leading to potential data breaches and access to sensitive communications.

**Active Directory:** Federation Services (ADFS): This service extends the capabilities of Active Directory to systems outside the corporate firewall and is particularly sensitive because it handles authentication across these boundaries. Entra ID is sometimes configured to use on-prem Active Directory for authentication via ADFS, which means Active Directory is essentially exposed externally.

**Enterprise Websites:** Often public-facing and therefore accessible to attackers, websites that use 1FA for administrative access can be easily compromised, leading to defacement, data theft, or worse.

Identifying and upgrading these systems to use multi-factor authentication (MFA) can significantly reduce the risk of unauthorized access and is a recommended step in strengthening an organization's security posture.

### NTLMRecon

- NTLMRecon scans URLs, hosts, and subnets for HTTP endpoints that perform NTLMSSP authentication
	- − Similar to Nmap's http-ntlm-info.nse script 
- Scans 50+ paths and submits fake authentication requests
- Gathers the following information about identified hosts
	- AD Domain Name
	- Server name 
	- DNS Domain Name 
	- FQDN 
	- Parent DNS Domain 
- Output is saved to a CSV file
NTLMRecon: https://github.com/pwnfoo/NTLMRecon/

NTLMRecon scans URLs, hosts, and subnets for HTTP endpoints that perform NTLMSSP authentication. While Nmap's http-ntlm-info.nse scrip provides similar information, it requires additional configuration to check specific paths, pages, or subdirectories for NTLMSSP authentication. NTMLRecon includes a list of 50 common paths without additional configuration, but it also allows users to specify a custom wordlist with **--wordlist**. 

To install and configure NTLMRecon, clone it from GitHub and run setup.py: 

`$ git clone https://github.com/pwnfoo/NTLMRecon.git` 
`$ cd NTLMRecon` 
`$ python3 setup.py install`

### Attacking NTLMSSP

- Internal Attacks 
	- HTTP NTLM endpoints are vulnerable to relay attacks 
- External Attacks 
	- Hosts that allow NTLM authentication are limited to single-factor auth 
	- Target these systems with credential stuffing and password spray attacks 
	- NTLM also leaks domain and server information, which is often for the internal domain 
- NTLMRecon will identify potential targets

```
$ ntlmrecon --input 10.130.10.0/24 --outfile ntlmrecon_10.130.10.0.csv [+] Brute-forcing 50 endpoints on https://10.130.10.1 [+] Brute-forcing 50 endpoints on https://10.130.10.2 [+] Brute-forcing 50 endpoints on https://10.130.10.3 [+] Brute-forcing 50 endpoints on https://10.130.10.4 [+] https://10.130.10.4/CertServ has NTLM authentication enabled!
```

Identifying NTLMSSP endpoints is helpful both internally and externally. On internal networks, NTLSMSSP endpoints can be targeted with relay attacks because, unlike SMB, there is no way to prevent an NTLM relay to an HTTP endpoint. Externally, NTLMSSP endpoints provide an opportunity for multifactor authentication (MFA) bypass. NTLM does not support MFA, so targeting exposed NTLMSSP endpoints with password sprays and credential stuffing attacks can be very effective.

### Credential Stuffing, in Brief (1)

1. People reuse passwords (often 1–5 pw patterns/person) 
2. Sites are compromised 
3. Organizations use 1FA externally

The result? Third-party breaches affect companies via employees' reused passwords.

![[スクリーンショット 2026-03-02 10.48.42.png]]

A widespread behavior among internet users is the reuse of passwords across multiple platforms. It's common for individuals to use the same one to five password patterns across various services. This habit significantly increases vulnerability because once one password is compromised, it can potentially provide access to several accounts owned by the same user.

Websites and services often suffer data breaches, leading to large volumes of user credentials being exposed. The attackers for these breaches don't have to release the breached credentials publicly, but they commonly do. These credentials can include usernames, passwords, and other personally identifiable information (PII).

Many organizations still rely on single-factor authentication for external access to systems, which only requires a username and password. This form of authentication does not provide sufficient security against the types of attacks that exploit stolen or reused credentials. 

The combination of these three elements leads to a situation where third-party breaches have a domino effect, impacting not just the directly breached service but also other organizations where the same credentials are reused. The comic panels in the slide humorously contrast the common misconception of hacking—as a dramatic physical infiltration—with the reality that many hacks are simply about exploiting known credentials across different systems. 

Don't worry, there will be future xkcd references in class! 

Obligatory xkcd reference: https://xkcd.com/2176/

### Credential Stuffing, in Brief (2)

![[スクリーンショット 2026-03-02 10.49.38.png]]

Here, we show a step-by-step example of a credential stuffing attack, showing how easy it can be using publicly available breach data and toolsets.

1. Credential Harvesting: The attacker begins by extracting potential credentials from a compiled list of breached data (COMB.txt) that specifically targets email addresses at the fake domain 'archer.zzz'. The command uses filters and saves these credentials into a file named 'creds.txt'. 
2. Credential Verification: Before attempting the actual attack, the first three credentials are verified to check their format and potential usability. This step ensures that the credentials are correctly formatted and plausible before they are used in an attack. 
3. Executing the Attack: Using the hydra tool, a well-known software for performing automated login attempts, the attacker tries to log into the 'krg.archer.zzz' mail server using the credentials saved in 'creds.txt'. The output shows that one of the login attempts was successful, indicating that the password for 'sarcher@archer.zzz' was 'Sploosh123!'.

The successful login highlights the critical vulnerability associated with reused passwords and singlefactor authentication systems. This demonstration underscores the importance of using multi-factor authentication and maintaining rigorous password policies to defend against such attacks. Additionally, it shows the practical application of tools available for such purposes, reminding organizations of the need to secure their systems against credential stuffing by regularly updating their cybersecurity measures.

### Credential Stuffing (The Commands)

![[スクリーンショット 2026-03-02 10.51.37.png]]

```
$ rg -iN '^[^@]+@archer.zzz:' COMB.txt > creds.txt 
$ head –n 3 creds.txt 
$ hydra –C creds.txt krg.archer.zzz imap
```

Here, we look at the commands used in a credential stuffing attack, specifically tailored to exploit email credentials from a breach compilation targeting the domain 'archer.zzz'.

1. Command for Extracting Credentials: The 'rg' (Ripgrep) command is utilized to search through a large dataset (COMB.txt), which contains breached email and password combinations. The parameter --iN specifies a case-insensitive search, focusing on lines that start with one or more characters not including the '@' symbol, followed by '@archer.zzz:', identifying email addresses from a specific domain. 
2. Verifying Credentials: The head -n 3 creds.txt command is used to quickly verify the format and plausibility of the top three entries in the 'creds.txt' file, ensuring that the data extracted is correctly formatted and usable for the next step in the attack. 
3. Using Hydra for Credential Stuffing: The last command, hydra -C, commands Hydra to attempt logins on the 'krq.archer.zzz' mail server using the IMAP protocol with the credentials listed in 'creds.txt'. This illustrates the practical use of Hydra to automate the login process using discovered credentials, highlighting the tool's efficiency in testing multiple combinations quickly.

### Software for Testing: Prepackaged Suites

- SANS Slingshot includes many of the tools to get you started in pen testing
- Other Linux distributions can also be helpful 
	- Kali Linux by Offensive Security: https://www.kali.org/ 
	- Parrot Linux from Parrot Security: https://parrotsec.org/ 
	- Black Arch Linux: https://blackarch.org/ 
	- Ubuntu or Debian with PTF: https://github.com/trustedsec/ptf

Remember, we've tested the provided Linux and Windows VMs, which have the lab directions and other files needed for the labs!

First, you need software for your testing regimen. With this course, you received a copy of the SEC560 Linux virtual machine. Furthermore, this VMware image includes tools pre-installed and, in many cases, preconfigured so that you can apply them directly in your own testing. 

Another useful source of tools is the bootable Linux distributions various people have made freely available, loaded with useful assessment and attack tools. A solid set of tools is included in Kali Linux, created and maintained by Offensive Security. Numerous similar Linux images for pen testing are also available, but Kali is one of the best because of its comprehensive set of tools, compatibility with a wide range of hardware, and carefully designed organization and layout.

### An Important Note: Command Prompts

![[スクリーンショット 2026-03-02 10.58.27.png]]

- We work with numerous different shells and switch often 
- Be aware of shells within shells: Linux → Metasploit → Meterpreter → … 
- The labs and notes indicate the prompts 
	- Windows cmd.exe: C:\> 
	- Windows PowerShell: PS C:\> 
	- Linux (non-root): $ or % 
	- Linux (root): # 
	- msfconsole: msf > 
	- Meterpreter: meterpreter >

Please make sure you enter commands at the right prompt!

Throughout this course, we use numerous different shells, both in our operating system and within Metasploit. We frequently change between these different shells as we switch back and forth between Linux and Windows, as well as within different aspects of Metasploit. Sometimes, even on a single page in the book, you use two or even three different types of shells to do something and then observe the results. 

All the labs and notes were carefully written to indicate the proper shell you are supposed to use at any given time by including the shell prompt right before each command you are supposed to type. That is, each lab command is preceded by the prompt indicating which shell to use. The shell types you encounter throughout this class include:

Double-check at each lab step that you are entering the proper command into the proper shell. Otherwise, a given lab step will not work for you properly.

### Networking

- VPN address will be 10.254.X.X 
	- tun0 on Linux, Ethernet2 on Windows 
- NAT addresses: will vary depending on your system! 
	- It will commonly be 172.16.X.X or 192.168.X.X, but it could be different 
- Network mask and DNS are set automatically 
- In your VMs, we have helpers that show your IP address in the UI 
	- Windows IP addresses can lag by up to a minute

![[スクリーンショット 2026-03-02 11.02.14.png]]

All the VMs will use DHCP to dynamically acquire IP addresses and network information.

Your VMs will be configured to use NAT, where the network connectivity will be shared with the computer, and the VMs are not directly accessible on the local network. The VMs will be able to communicate with each other.

Many of the labs will require that you connect to the SANS-hosted infrastructure using a VPN. To configure the VPN, follow the directions in your workbook (electronic or paper).

You will not use your host system in any of the lab networks; however, your host will get an IP address on the lab network (for in-person classes), so make sure you have the latest software patches and host protections in place.

### Remote Connectivity via VPN

![[スクリーンショット 2026-03-02 11.09.02.png]]

Depending on how you are using your local VM, you may need to use the IP address for the local interface or the VPN interface. If you are setting up a connection directly between your VMs, then use the address assigned to the eth0 interface on Linux or the Ethernet0 interface on Windows. 

If you need to have a remote target connect to your VM, such as an exploit callback, use your Windows or Linux tun0 interface and associated IP address or the Ethernet 2 interface on Windows.

## Building an Infrastructure

- For Pen Testing
	- Attack software 
	- Hardware 
	- Network infrastructure
- For Testing Tools and Techniques
	- Target software 
		- Active Directory domain controller
		- Target hosts and services 
	- Hardware 
	- Network infrastructure

Your first time running a tool shouldn't be in someone else's production network!

![[スクリーンショット 2026-03-02 11.22.03.png]]

Before we begin pen testing, we need systems from which we can test and systems that we can use as a test lab, such as for testing new tools and techniques. These two pieces will likely be separate, but we need the systems, nonetheless. Your needs may differ from what is presented here. This is designed to be a baseline infrastructure, which you can expand or modify based on your needs.

### Building a Lab

Test new tools and techniques in your lab! A broken lab is fine; breaking production systems is a big problem!

- Use VMs: Easily revert, store, clone, and build systems 
- Servers: Active Directory, File Servers, IIS; Multiple Linux distros 
- End user: Windows 11, Ubuntu, Fedora 
- Licensing:
	- Linux: Typically free. RHEL requires a paid subscription, but Fedora is very similar 
	- Windows: Use free trials or pay for a license

Virtual machines are a great way for offensive personnel to build, test, revert quickly, and clone test systems. The VMs can be run in the cloud, on the tester's laptop, or even on old hardware. 

You should test new tools in a laboratory environment to make sure you understand how they work and their potential impacts on a target machine. Such laboratory testing and analysis is especially vital for free tools downloaded from the internet because of concerns about quality, the potential to crash a target, and hidden functionality that could compromise the test systems. 

It is important to have a good lab that consists of many of the same systems and software you will see in the target environment. Obviously, the lab can't contain all possible combinations or all software, but a representative sampling is good for our purposes. 

We must have systems that resemble a wide range of target networks. Most networks use a Windows Domain and, therefore, a Domain Controller (we'll discuss attacking the Windows Domain later in this course). Similarly, most of these networks include Windows file servers and web servers. You could combine these two roles into one system to save on physical resources and licensing. Of course, the most widely used end-user system in the enterprise is Windows, so we'll need one of those systems in our lab. 

Unfortunately, using Windows in a lab is more difficult due to licensing. You can pay for licenses and can have as many different targets as you like, including older server and end-user systems. Many people have much tighter budgetary constraints that limit them to free systems only. We do not recommend or condone stealing or otherwise bypassing the legal licensing requirements set by Microsoft (or other vendors). Microsoft offers free VMs for testing; however, it is limited to the latest operating system, and the licensing window is limited. 

On the Linux side, we can use a range of free Linux systems. For example, Ubuntu is a common free Linux distribution found in enterprises. While most of the installs and updates for Linux are free, some are not. For example, Red Hat updates are not free, but it is very similar to Fedora.

### Systems Used for Internal Testing

Use a "leave behind" or "drop box" system for long-running tasks and remote access.
- Physical: Laptop or Minicomputer 
	- Use full disk encryption or wipe the device before return shipping 
	- Note: Internal policies often allow corporate-owned devices on the network but require extra paperwork for third-party-owned devices 
- Virtual: Custom VM
	- Target is responsible for destruction 
	- Requires internal team to provision, set up, and configure

If you're physically on the same network as the targets, use Bridged networking, not NAT!

Next, we need the actual systems that will be used for the testing. The needs for internal testing and external testing are different. 

For internal testing, we can use a "leave behind" or "drop box" for long-running tasks. We can also use this for remote-internal or pseudo-internal testing, which allows the penetration testers to work remotely but have internal access to their desired testing systems. The drop box could be physical, such as a laptop or a minicomputer. If you use full disk encryption (FDE) on the system, the target will then have to unlock the disk when initially booted or on reboot. Alternatively, the host OS could be booted without a key, and then the sensitive client data could be stored in an encrypted container using a key known to the pen testers. If there is any unencrypted data on the system, ensure it is properly scrubbed before shipping. You never want to ship unencrypted sensitive target data through the mail or a public shipping company. If that system is lost, it could be considered a reportable breach. 

To get around the shipping issue, some third-party testers will sell or expense the drop box to the client as part of the contract. This also solves another common issue: internal access. Some organizations have strict policies as to what systems are allowed on the network. Corporate assets are allowed on the network, but third-party-owned assets require extra verification, documentation, and paperwork. If the organization owns the physical drop box device, then it is often easier to get the device on the network. 

You could create a custom VM configured to connect back to the pen testers, and no shipping is ever required. However, this often requires extra action from the internal team to provision, set up, and configure the VM.

### Dedicated Test Systems

Use unique, dedicated systems for each client
- Ideally, even unique systems per engagement
- Reduces likelihood of cross-contaminated reports or going out of scope 
- Many testers use VMs since it is easier to start clean 
- Do not use your day-to-day, surfing, or email system for testing 
- Testing systems will not have security tools, such as firewalls or AV/EDR
- External Testing: Build a new system in the cloud for each test

Scrub client data and testing VMs at the conclusion of a test.

Next, you need the actual systems that will be doing the testing. We recommend that you use systems that are dedicated to testing and are unique for each engagement.

- Disabled defenses: The attack systems will not have defensive software such as AV/EDR, and it will often have its firewall disabled. These modifications decrease the defensive strength of the system should it come under attack from an adversary. You don't want these features disabled on your day-today system. 
- Uptime: The testing systems will often need to be available and online for weeks or months. If you were to run the attack from your laptop (or even a VM on your laptop), the test could be impacted if you carry your laptop to another location (network change) or perform your day-to-day tasks (installing software, rebooting, patching). 
- Prevent data leakage: If you use unique systems for each test, it is less likely that the data from one test will end up in the data from another test. This is especially important for third-party testers where the test data is likely to be from different organizations where such an action could be considered a breach of confidentiality. Such cross-contamination could lead to embarrassment, loss of trust, and even legal action.

At the conclusion of a test, be sure to clean the target data from the test systems. You can't lose data you don't have.

### Sources for Free Tools and Exploits

- Exploit-DB: exploit-db.com 
	- Sorted by remote, local, web app, denial of service, shellcode, and papers 
- MITRE CVE Repository: cve.org 
	- Latest information about vulnerabilities 
- GitHub: github.com 
	- Search by CVE or vulnerability name 
	- Warning: Some people lie on the internet

We are not endorsing these sites or the tools they distribute. Remember to be careful! Always test exploits and tools in your lab!

Although there are numerous exploit and attack tool repositories on the internet, some of the most comprehensive archives that are updated on a regular basis include the Exploit Database and Packet Storm Security. Other sites come and go on a regular basis, but these sites are long-standing and tend to have relatively higher-quality tools. 

The Exploit Database (https://www.exploit-db.com/, or exploit-db for short) is maintained by the same group that maintains Kali Linux, Offensive Security. Its site hosts more than 10,000 exploits and sorts them into useful categories, such as Remote Exploits, Local Exploits, Web Applications, Denial of Service/Proof of Concept, Shellcode, and Papers. For each exploit in these categories, it lists the platform (Windows, Linux, PHP, and so on) and the author. 

Note that we are not endorsing these sites or the tools that they distribute. These sites have been quite controversial, and you need to be careful with any code you download. Be careful downloading tools from sources you have not vetted, as malicious attackers routinely release fake exploits that will actually compromise your attacking system (https://www.bleepingcomputer.com/news/security/fake-microsoftexchange-proxynotshell-exploits-for-sale-on-github/). 

Beyond the tool and exploit sites, numerous vulnerability research sites are also available. Although these sites do not distribute exploit code freely, they do publish information about vulnerabilities. These detailed vulnerability descriptions are invaluable in letting a tester know that there is an issue with a system type or service version discovered in a test. Even though an exploit might not be available (in fact, an exploit may have never been publicly released or even created), the tester still needs to understand the vulnerabilities so that they can be included in the test report. 

Some of the best sites with vulnerability research and detailed descriptions are the following sites:
- The United States Computer Emergency Readiness Team (US-CERT) (https://www.cisa.gov/newsevents/cybersecurity-advisories), maintained by the US Department of Homeland Security (DHS) 
- The Common Vulnerabilities and Exposures (CVE) repository operated by MITRE (https://cve.mitre.org/)
















