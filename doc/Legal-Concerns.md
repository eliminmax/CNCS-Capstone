# LEGAL CONCERNS AND IMPLICATIONS

## The Concern

For this project I am researching the structure of ELF - the Executable and Linkable Format. As part of this research, I am getting hands on — that is to say, I am looking at real-world examples of ELF files, and deconstructing how exactly they work. This has massive risk of legal liability should I do it wrong.

More specifically, I studied the Ehdr (ELF Header) of [BusyBox](https://www.busybox.net/), a lightweight implementation of many standard UNIX utilities. It is not the only one that I am looking at. There are legal issues that come about from reverse engineering software, and the limits are often unclear, as they are shaped by conflicting court rulings that attempt to interpret and apply multiple areas of law, and some of the most relevant laws in question, in the USA at least, are poorly-written and leave room for rather significant ambiguities.

This has implications on the scope of this project. For example, there are several systems that use ELF executables, in a locked-down, proprietary format.These include every Nintendo home console from the N64 through the Wii U, every PlayStation console, the Sega Dreamcast, and several cell phones from the pre-smartphone era. Those are all practically off-limits for the purposes of this project. The only ways to access the ELF files on those systems for study would be either to pirate them, or to violate [17 U.S. Code § 1201](https://uscode.house.gov/view.xhtml?req=granuleid:USC-prelim-title17-section1201&num=0&edition=prelim#1201_1), which begins as follows:

> (a) Violations Regarding Circumvention of Technological Measures.——
>   (1)
>     (A) No person shall circumvent a technological measure that effectively controls access to a work protected under this title.

While there are exceptions to that laid out within the section in question, they are very narrow, and would be unlikely to apply to this project.

Thankfully, the Electronic Frontier Foundation provides a practical guide to help navigate the legal pitfalls involved with reverse engineering software, titled [Coders' Rights Project Reverse Engineering FAQ](https://www.eff.org/issues/coders/reverse-engineering-faq). It explains how to avoid the pitfalls surrounding reverse engineering.

## Mitigation of legal risk

The main way that I am mitigating the risk of legal liability is rather simple. I am exclusively looking at ELF files that are in one of two categories: either they are posted freely on the internet as examples of ELF files, or they are released under licenses that are designed with the explicit intention of allowing the type of studying and reverse engineering that this project entails.

The way I'm going to do that is simple — I am only going to study "real-world" ELF executables that are made available as part of Debian.

Version 1.2 of the [Debian Social Contract](https://www.debian.org/social_contract), ratified by the Debian project on 1 October 2022, opens up with the following statement:

> Debian, the producers of the Debian system, have created the Debian Social Contract. The Debian Free Software Guidelines (DFSG) part of the contract, initially designed as a set of commitments that we agree to abide by, has been adopted by the free software community as the basis of the Open Source Definition.

It then explains what Debian's *'"Social Contract"* with the Free Software Community' actually is. It explains that for a work to be included in Debian, it must adhere to the *Debian Free Software Guidelines* — a part of the social contract that explains what "free" means in this context - it is about freedom, not cost, to be clear. Any program that is released under DFSG-compliant terms would be acceptable

## Citations

  United States, Congress, House. United States Code. Title 17, section 1201, Office of the Law Revision Counsel, 23 Oct. 2022, https://uscode.house.gov/view.xhtml?req=granuleid:USC-prelim-title17-section1201&num=0&edition=prelim#1201_1.
  Jue, Aaron. "Coders' Rights Project Reverse Engineering FAQ." Electronic Frontier Foundation, Electronic Frontier Foundation, 13 May 2017, https://www.eff.org/issues/coders/reverse-engineering-faq.
  Debian. "Debian Social Contract", Version 1.2, Debian, 1 Oct. 2022, https://www.debian.org/social_contract.
