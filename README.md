# Programming Project 02: Reproducing DNS research

## Overview
In this project, you will reproduce one of the results from the networking research paper "[Exploring EDNS-Client-Subnet Adopters in your Free Time](https://conferences.sigcomm.org/imc/2013/papers/imc163s-streibeltA.pdf)." In particular, you will quantify the fraction of top domains that support the Enhanced Domain Name System (EDNS) Client Subnet (ECS) option. 

### Learning objectives
After completing this project, you should be able to:
* Construct Domain Name System (DNS) requests
* Interpret DNS responses
* Design a simple network measurement experiment based on an experimental design described in a research paper

## Getting Started
Clone your git repository on the `tigers` servers.

### Important Advice
This project is **deliberately underspecified** to help prepare you for conducting your own research project during the second half of the semester. You have developed an extensive array of problem solving, documentation reading, and testing and debugging skills throughout the computer science courses you have taken at Colgate. This project is an opportunity to showcase your computer science prowess.

**If you get stuck, come talk to me** during office hours, before/after class, or arrange an appointment. I can help you brainstorm how to proceed.

You will be writing code that communicates with actual domain name servers (DNS) on the Internet. It is important that you **abide by certain measurement best practices** to avoid your measurements from interfering with the normal operation of these DNS servers or other network infrastructure. **You MUST**:

* **Test your code on a small scale** before conducting large scale experiments—for example, make sure loops in your code work correctly, such that you do not unintentionally send a large volume of requests due to infinite or other overly aggressive looping
* **Prefer to use a local, caching recursive resolver** explicitly set up for this project and only communicate directly with other DNS servers when necessary—for example, send queries to the local recursive resolver (`127.0.0.1`) to determine name server IP addresses and only directly contact other name servers when you are trying to determine whether a name server supports ECS

Conducting DNS queries for 1 million domains takes time. **You should have a working Python program at least a few days before the deadline** to ensure there is sufficient time to gather and analyze data.

## Objective
Your goal is to reproduce the results from the first two paragraphs of Section 3.2 of the paper "[Exploring EDNS-Client-Subnet Adopters in your Free Time](https://conferences.sigcomm.org/imc/2013/papers/imc163s-streibeltA.pdf#page=3)." In particular, you want to quantify **what fraction of popular domains support ECS**.

The Alexa Top 1 Million dataset is no longer freely available, so you should use the [Cisco Umbrella 1 Million](https://umbrella.cisco.com/blog/2016/12/14/cisco-umbrella-1-million/) instead.

You should use the same methodology as the paper to determine whether a domain supports ECS: "re-send the same ECS query with three different prefix lengths. If the scope is non-zero for one of the replies, [mark the domain name] as ECS-enabled."

If a domain is an alias for another domain, then you should simply categorize the domain as an alias and not bother to check whether the domain supports ECS. If you are unable to determine or communicate with a domain's authoritative name server (NS), then you should simply categorize the domain as inaccessible and not bother to check whether the domain supports ECS.

You should write one or more Python programs that use the [dnspython](http://www.dnspython.org/) package to issue DNS queries and interpret DNS responses. Consult the `dnspython_demo.py` script included in your git repo and the [dnspython documentation](http://www.dnspython.org/docs/1.16.0/) as you write your program(s).

## Suggestions
To identify an NS that is the authoritative NS for a particular domain name, you should issue queries to the local recursive resolver that has been set up for this project: `127.0.0.1`, port `8053`. To check if a domain supports ECS, you will need to directly query the authoritative NS for that domain. (The local recursive resolver strips out the ECS option, so you cannot use the local recursive resolver for this purpose.)

The paper showed that Google supports ECS, and the campus network engineers have affirmed that Colgate does not support ECS. Thus, you can use these two domains (`www.google.com` and `www.colgate.edu`, respectively) to test whether your code properly detects whether a domain supports ECS.

When you are running your script to measure the ECS usage of 1 million domains, you should run it using [screen](https://linuxize.com/post/how-to-use-linux-screen/). This will ensure your script will keep running on the server, even if your SSH session is terminated. As noted above, make sure you test your script with a small number of domains (e.g., a thousand), before you run it on all 1 million.

Issuing DNS queries for 1 million domains will take a long time. Fortunately, the task can be easily parallelized. One strategy is to divide the file of domains into multiple chunks, and run your program on each chunk.

You should practice defensive programming. In other words, make sure your program handles failures (e.g., bad DNS response, no DNS response, etc.) gracefully and does not crash. This will help ensure that your script doesn’t crash halfway through the 1 million domains. It is okay to report that you weren’t able to determine whether some of the 1 million domains support ECS due to errors; just be clear what percentage of domains had a failure. 

## Submission instructions
You should update the `results.md` file with:
1. Instructions for duplicating your experiments: e.g., What Python program(s) should be run? What parameters do they take? Do any other programs/shell commands need to be run?
2. A one paragraph summary of your findings: in essence, this should be your own version of the second paragraph of Section 3.2 of the paper

In your repository, you should commit:
1. The updated `results.md`
2. Any Python program(s) you have written to conduct the experiments
3. A list of domain names that support ECS