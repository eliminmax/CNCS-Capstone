# SIM Communication Monitoring

## Why?

SIM cards are the keys to the functioning of mobile networks - literally. They are essentially standalone computers, which can be programmed in Java of all languages, and provide the necessary authentication for connections to take place at all. The idea of this project was to monitor communications between phones and networks by somehow intercepting communication with the SIM card. Having done more reading up on the structure of LTE and 5G networks, I now understand that things are not as straightforward as I thought.

That said, one possibility would be to set up my own mobile network to monitor traffic that way - there are open-source tools to do so, and many of them are documented on a blog known as ["Nick vs Networking"](https://nickvsnetworking.com/), and of all the necessary hardware, the most expensive part is a high-end PC - though that's not to say that it is cheap.

Considerations regarding licencing:

Within the USA, the Citizens Broadband Radio Service (CBRS) operates within the LTE spectrum, and is available for private use. Spectrum Allocation Systems (SAS) are used to prevent interference and limit congestion of radio waves. To actually set up a private LTE network, hardware wouldn't be the only expense, and Google is a licenced SAS with pricing that seems affordable (only $2.25 per residence per month). That said, I suspect that its geared towards medium to large businesses and device manufacturers, so it might not be that easy to actually deal with that side of things.

## Objectives

Using a programmable SIM card, a software-defined radio, and a decently powerful computer running software like Open5GS and Kamailio, both of which are explained in detail on Nick vs Networking, set up an LTE network, and capture 24 hours of traffic coming from various phones (phones must support band 48 to work with CBRS).


## Constraints

* Cost of equipment must be affordable - this alone renders this one highly unlikely to work.
