# Training - Sec560 day1

## Miniature Engagement, Recon, and Scanning

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

### Penetration Testing
Penetration testing focuses on identifying vulnerabilities in a target that could allow an attacker to penetrate the computer or network and steal, damage, or corrupt data. Penetration testers (pen testers) will use tools similar to the malicious attackers. Sometimes, the malicious actors even take tools and techniques from penetration testers. As the name implies, penetration testing requires penetration (if possible). A test that forbids penetration is not a penetration test. Sometimes, targets ask for a penetration test but then disallow exploitation, password guessing, and other activities that would allow the testers to verify flaws and find second-order vulnerabilities. These handcuffed tests are more akin to vulnerability assessments.

### Red Team
Red teams are focused on testing the effectiveness of the entire security program. They emulate the Tactics, Techniques, and Procedures (TTPs) of real-world adversaries with the goal of measuring weaknesses in defense, detection, and response. Red teams use many of the same tools as penetration testers; however, they have a different focus. The red team is focused on identifying deficiencies in the blue team's (defenders) ability to detect, respond, and eradicate a threat. A red team only uses vulnerabilities that help them accomplish their goal, while penetration testers are most often tasked with finding many vulnerabilities. Penetration testing is focused on finding flaws, understanding their business risks, and helping the organization improve its security stance. The red team's primary job is to help make the blue team better equipped to detect and respond to attacks. To put it simply, penetration testing focuses on the defenses (technology), and the red team focuses on the defenders (people).

#### Purple Team 
Purple teams are cross-functional teams where the red and blue teams work together. The closer the interaction, the better. This type of assessment includes metrics on blue's ability to identify, detect, and SEC560 | Enterprise Penetration Testing Pen Test, Red Team, Purple Team, Vuln Assessment, Audit Identify flaws that attackers could cause business harm with (testing preventive controls) Pen Test Red Team Designed to test detection and response capabilities Cross-functional team of red and blue teamers, and/or tightening the feedback loop between offense and defense Purple Team Identify, quantify, and rank vulnerabilities (no exploitation) Vulnerability Assessment Security Audit Audit implies testing against a particular set of standards 11 12 respond to an attack. The testing should be documented to show advances in detection and response capabilities. These metrics can be used to justify investments in tools and training.

### Vulnerability Assessment
Many people use the phrases vulnerability assessments and security assessments to describe the work done by penetration testers and ethical hackers, but there is a subtle distinction between a penetration test and a security assessment. A penetration test is focused on getting in or stealing data. The emphasis is on penetrating the target environment by exploiting discovered vulnerabilities. Vulnerability assessments and security assessments are focused on finding vulnerabilities, often without regard to actually exploiting them and getting in. Thus, penetration testing often goes deeper, with its goal of taking over systems and stealing data, whereas security and vulnerability assessments are broader, involving the process of looking for security flaws. These assessments also often include policy and procedure reviews, which are usually not included in penetration testing. The table on the previous slide shows the differences between the different types of assessments. Of course, each can vary to some degree, with penetration testing having the greatest variability. A penetration tester is often tasked with finding all vulnerabilities, even those that aren't exploitable.

### Security Audit 
Finally, we have the phrase security audit. An audit implies that we are measuring things against a fixed, predetermined, rigorous set of standards. These audits are almost always done with detailed checklists. Some penetration testing and ethical hacking organizations have created their own internal checklists of items that need to be covered in a test, but these checklists aren't as detailed as those for comprehensive audits. Our focus in this class is not on auditing. SANS has numerous other classes that address security audits in detail. Our focus is on ethical hacking and penetration testing.

## Penetration Testing Goals

Penetration testing is demonstrating business risk from realistic attackers: 
- Modeling real-world adversaries 
- Finding vulnerabilities (ideally, before the adversary) 
- Exploiting those flaws under controlled circumstances 
- Determining business risk 
- Improving the organization's security

A formal definition of penetration testing is as follows:

>Penetration testing involves modeling the techniques used by real-world computer attackers to find vulnerabilities and, under controlled circumstances, to exploit those flaws in a professional, safe manner according to a carefully designed scope and Rules of Engagement to determine business risk and potential impact, all with the goal of helping the organization improve security practices.

The key is that the vulnerability identification and exploitation should always be business-focused. The penetration test findings should always focus on the organizational risk, not just the technical risk. Many organizations use ethical hacking and penetration testing to find security flaws before their adversaries do. After applying their security policies, procedures, and technology, organizations can use thorough penetration tests to see how effective their security is in light of an actual attack, albeit by friendly attackers. An added benefit of ethical hacking and penetration testing is that because they show real vulnerabilities and indicate what a malicious attacker might be capable of achieving, they can get management's attention. Decision makers, when presented with the carefully formulated results of a test in business terms, are more likely to provide resources and attention to improve the security stance of an organization.

## Types of Penetration Tests

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
- **Assumed breach test:** Designed to find vulnerabilities in the network once an attacker has gained access to a system in the network. This is a fantastic way to find issues in Active Directory permissions, file permissions (excessive sharing on file shares), and client-side software. • Web application test: These tests look for security vulnerabilities in web-based applications and APIs. • Social engineering test: Involves attempting to trick a user into revealing sensitive information, such as a password, or possibly convincing a user to click a link in an email. These tests are often conducted via email or over the phone, targeting selected users and evaluating processes, procedures, and user awareness. • Wireless security test: These tests involve exploring a target's physical environment to find unauthorized wireless access points or authorized wireless access points with security weaknesses. • Physical security test: This test looks for flaws in the physical security practices of a target organization. Testers might attempt to gain access to buildings and rooms or to take laptops, desktops, or recycling bins out of target facilities. A dumpster diving test is a variation of a physical security analysis. Physical testing must be conducted carefully to ensure that the testers do not get hurt or arrested during their work. • Product security test: This test focuses on one specific product. It could be hardware or software. In this kind of test, you look for security flaws in products that can be used in the tester's laboratory systems. Such tests look for flaws in the software, such as exploitable buffer overflow conditions, SEC560 | Enterprise Penetration Testing Types of Penetration Tests • Network services test − One of the most common • Assumed breach test • Web application test • Social engineering test − Email-based or phone-based • Wireless security test − Not just Wi-Fi • Physical security test • Product security test − Could be software package or hardware (e.g., IoT) − Breaking or bypassing encryption on local data or intercepted traffic • Enterprise penetration test − Pen testing the whole company − If it can be in scope, it's in scope 14 15 privilege escalation flaws, and the exposure of unencrypted sensitive data. These tests could focus on bypassing or breaking the encryption of data stored on a local system or across the network. • Enterprise penetration test: This test has as its scope, to a first approximation, everything that the company has the authority to grant the penetration tester to test. Some exclusions may still apply, but the tester should emphasize that removing systems from the scope of the enterprise penetration test reduces the fidelity of the results.