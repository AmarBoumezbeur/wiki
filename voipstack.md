# Introduction

# Study/Reaserch

## Description
VoIP stack is a important layer of OSP networks, it provides services related to communication, visio conferencing and multimedia exchanges for users with low latency, high bandwidth and efficient mecanisms.

To build a VoIP stack, it os important to note that it can be achieved using two phylosophie :

1. Propretary Solutions: this way a commercial solution is bought with money where many vendors propose different solutions at different prices with different services. Example, Ericsson, Cisco and more. Most vendors utilize IMS logic.
2. OpenSource solutions: this way software if provided free of charge but it's maintained by the developers through donations. The software can be installed on linux systems and can also be modified by the user to meet their needs. OpenSource software soesn't follow the IMS architecure by design, instead multiple softwares can be used to design an IMS like system for production.


## Problem

1. As a simple user wanting to learn, it is expensive to buy a commercial product to test and play around
2. As a simple user using Opensource software is free of charge but it must be mastered in order to be able to create the best VoIP stack for learning and probably be commercialised in the future.

## Objectives

1. Design a small lab and start small but grow.
2. Hardware : Buy cheap VPS systems like Ikoula or others to spend less and learn more.
3. Software : Learn the software along the way for effecient outcome.

## Solutions

### Hardware

1. Ikoula VPS
    * Loging : 
        * Username: boumezbeur.amar@hotmail.com
        * Password: keepass
    * Asterisk : frhb103098flex.ikexpress.com
        * CPU : 2 vCPUs
        * RAM : 1GB
        * STORAGE : 20 GB
        * OS : Debian 12 (Bookwork)
        * Networking
            * IP :  178.170.25.229/24
    * OpenSips : frhb103099flex.ikexpress.com
        * CPU : 2 vCPUs
        * RAM : 1GB
        * STORAGE : 20 GB
        * OS : Debian 12 (Bookwork)
        * Networking
            * IP : 178.170.25.243/24
2. OVH (Maybe later) for HA and redanduncy.
    * Username: boumezbeur.amar@hotmail.com
    * Password: keepass
    * Opensips : TOBEDEFINED
        * CPU : 2 vCPUs
        * RAM : 1GB
        * STORAGE : 20 GB
        * OS : Debian 12 (Bookwork)
        * Networking
            * IP :  
    * Asterisk : TOBEDEFINED
        * CPU : 2 vCPUs
        * RAM : 1GB
        * STORAGE : 20 GB
        * OS : Debian 12 (Bookwork)
        * Networking
            * IP:  

### Software

1. Linux
2. Opensips
2. Asterisk
3. Sipexer

### Design

![voip]()

### Functionnal design