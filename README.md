## Info / Talks

* https://www.youtube.com/watch?v=0AZVCGTxkTU
* https://www.youtube.com/watch?v=Z7n7mpPMaFo
* https://www.youtube.com/watch?v=BIXyIswUFCk
* https://www.youtube.com/watch?v=W9F4pn9Lngc
* https://www.youtube.com/watch?v=oHcHTFleNtg
* http://unikernel.com/files/2014-cacm-unikernels.pdf

## Vragen: wie is er bekend met Unikernels

## Docker?

When I was researching Unikernels in early 2016, I was surprised to learn that at that exact time Docker bought the company Unikernel Systems. How do docker and Unikernels align? What is Docker Inc.s interest in Unikernels.

Why? Look into this... What is the motivation...

## Long Time Ago

2 Choices:

All Features					No Features

* Linux
* FreeBSD		<---------->		MiniOS
* Windows

## Info: Type 1 vs Type 2 HyperVisors

## Unikernel

AKA Library OS

SINGLE ADDRESS SPACE, single process!

Lot's of Complexity
* Hard problems: complexity is difficult to get rid of
* Unnecessary complexity: adoption of technologies we don't understand, growth over time

Our systems (like POSIX) are VERY general, yet our applications (espec. in microservices) are VERY specific. POSIX was designed in an era before hypervisors and virtualisation were the standard.
Linux is probably the most compatible system ever made: it runs on anything, and anything runs on Linux. 

Our OS stack is full of permission checks from an era when many, many applications ran on the same system (Mainframes). Permission checks are a hard problem that maybe we don't need to solve

Many resources (RAM/Storage) are put into things we dont need (did you know your EC2 instances probably contain floppy disk drivers? :) ). Fun exercise: Boot a clean instance of Linux and watch that PID count rise! Applying patches and errata to these systems is time consuming, expensive and not that fun. Especially when you dont even need said systems.

We:
Made It Work
Made It Fase
Now: We need to Make It Right (By simplifying!)

Insight:
Compare a microservice Diagram to an OS. Seems like were duplicating what our OS provides

|MicroServices|UNIX/POSIX|
|-----|-----|
|Service Discovery (etcd, ZooKeeper, Consul)|Config files in `/etc`|
|Schedulers (Kubernetes, Mesos)|OS Process Management|
|RDBMs and Object Stores (Postgres, CouchDB, Amazon S3, Ceph)| File System Storage|
|Caches (Redis, Memcached)| Memory Storage|
|Polyglot Microservices|Classic OS Worker Processes|

Large of amount of duplication may lead to security problems. Very few enterprises apply actual DevOps patterns. End result: Dev team dumps sec patching problems with Ops team. Ops team applies patching while suffering from lack of understanding of the app.

A hypervisor is usually more secure: less targeted, often not exposed as much as Linux is. Furthermore, due to the extreme specific nature of each deployment, attacks have more trouble propagating over a network, as each attack has little to no guarantee that next system it discovers is in fact vulnerable. A compromised unikernel doesn't have a whole lot that can be abused. No shell, no disk, only application memory.

Per definition, the ownership of the runtime is in the hands of the Dev Team. This encourages a proper DevOps environment.

Even with Docker containers, were building on top of a platform that, essentially, shares everything. We just happen to have managed to build a wall using container technologies via kernel modules (cgroups etc.)

You're probably using some Unikernel technologies right now..... In Docker for Mac and Docker for Windows, they rely on Unikernel libraries (HyperKit, VPNKit, DataKit, etc.)

Unikernels compile your application down into a specialised operating system that includes only the functionality required on the target platform

Application Code + Operating System Libraries --> Standalone Unikernel

Unikernels are NOT trying to reinvent the General Purpose OS, neither do they try to make the point that Windows or Linux is bad.

* Specialised all the way down
* Dev is in complete control
* Works very, very well in environments that use true DevOps
* Possibly great benefits when testing 
	* 100% Isolation
	* No more "Works on my laptop"
	* Also no more "Works on my build server"
* Space for specialised optimisation increases dramatically, future academic research may yield very exciting opportunities for this   

## Difference between Unikernel and RTOS?

## Setups

Classic:

* Application Code (Source Code) 
* Application Libraries + Interpreter/Runtime + App Dependencies
* Shared Libraries + OS
* Machine

--> Compatibility problems revealed only at runtime

Compiled Language:

* Single Binary (Source + App libs + Runtime)
* Shared Libraries + OS
* Machine

--> Shared Libs can still be a pain
--> How do you build the software? In what environment do you build it?

Compiled Language + Static Linking:

* Single Binary (Source + App libs + Shared Libs + Runtime)
* OS
* Machine

--> Binary is bigger (Not a problem per se)
--> Still possible problems during the build fase


Docker to the Rescue!

* Application Image (Source + App libs + Shared Libs + Runtime + Base Image)
* OS
* Machine

--> Pretty damn good (with some caveats)!
--> App can be compiled inside docker container on startup, but then we lose fast boot time
--> We inherit a HUGE software stack to attain this isolation...

Let's Compile our App as an OS!

* Compiled Unikernel (Source + App libs + Shared Libs)
* Unikernel Runner / Hypervisor
* Machine

--> Our app runs directly on top of a hypervisor
--> Still the problem of build environment

## Stack

-- Docker

Application Config
Application
Language Runtime
Shard Libs
Docker Runtime
OS User Processes
OS Kernel
Virtual HW Drivers
Hypervisor
Hardware Drivers
Bare Metal

-- Unikernel

App Config
Unikernel App (Source, Drivers, Runtime)
Hypervisor
Hardware Drivers
Bare Metal

Unikernel: 

* less layers, 
* still gives isolation, 
* no unnecessary components,
* we can focus on the abstraction we are looking for: our application (even more so than docker),
* advanced: allows us to plug basic kernel systems (e.g. our own threading model, filesystem access, network interfaces, etc.)
* Less code --> Hopefully less bugs, easier to reason about, source level control of the application
* Takes the concept of immutable infrastructure to the next level (its all a binary, if you need a change --> recompile)

A simple Debian 5 System has 70,000,000+ lines of code! The Linux Kernel alone is monolithic and approaching 30,000,000 LOC, including a massive amount of drivers not needed by your application (and even many blobs of proprietary firmware these days)

## (Backwards compatible) POSIX Compliance vs. Library OS (Forward Compatible)

## Examples

* Use Unik with Rump to compile Go/Java/OCaml?
* Show what it does in VirtualBox etc.

## Why?

Money:

* Reduced Memory Footprint
* Very disk space required
* Probably less computational overhead

--> Important espec in Cloud Environments, where less used resources litterally means less money spent 

Speed:

* Reduced Memory Footprint
* No other processes draining CPU
* No OS schedulers interrupting (impact is debatable)

--> Faster load times, lower latencies

Security:

* Reduced code size
* Customized to application
* Hopefully stronger walls between components, because they all behave differently (a single attack doesnt necesseraly work on other components)

(Galois --company-- has alledgedly even used HaLVM to slow down an ongoing cyber attack: they spawned a huge swarm of Unikernels as a wall for the attack to replicate to, buying the company some much needed time to isolate their systems that contained actual secure data from the attack)

--> Less susceptible to GENERAL attacks. Possibly a huge win for internet facing services

## Why Not

* Some apps simply require a full OS stack (classic LAMP stacks or whatever). In that case, just use it.
* In many of the newer (language specific) Unikernel systems: not all packages are there for you. You might have to write some components yourself. Remember, there is a LOT of software for Linux. Abandoning Linux means abandoning that software.
* If a Unikernel is still going to ship many of the components that a traditional OS uses, you might actually end of saving not that much money

## Problems

* Not many battle tested stuff
* Common Linux infrastrucutre may be missing (config management, logging, monitoring)
* No hot patching machines, though with a modern setup this should not be a problem --> Build a new binary, shedule replacement of all instannces
* Debugging production problems may be hard (but forces you to think about error handling and prod debugging before actually deploying :) ). Rumprun allows gdb debugging? So potentially you CAN debug the entire OS...
* Usually you are developing something very specific for a project, little opportunities for code reuse. This makes pivoting a project hard, and might make unikernels brittle when it comes to big changes


## Use Cases

1:

* Highly flexible, highly mobile, on-demand network nodes
* Theyre nimble
* They scale efficiently

... Provided they allign well with your usecase.
Interesting rule of thumb: do you need a local disk? The requirement of a disk is a problem when expanding rapidly and massively.
If lots of persistent state across the system is not required: probably a good usecase.

2: 

* Embedded, single-host security appliances (see screenshot of Windows en Linux host). For example, sitting between DomU Windows en Dom0 Linux on Xen. Even if Windows is compromised, can still make things like eg encryption, tunneling, filtering unavoidable by forcing to pass through Unikernel security instance (this can even be split up into even smaller components like RNG, Key Management, etc. to give even finer grained access control)

3: 

Attack control in locally hosted setups (no cloud here). Firewalls are great, but they probably will be bypassed at some point. When an attack occurs, make them work harder, make them easier to detect.
Spawn massive amount of Unikernels that act like they're Linux or Windows host, but serve only to detect the intrusion. Attacker has to go through large NMAP chain, get reported when he jumps to the next host that a "detection unikernel". They serve as excellent "honeypots" 


## Projects

* OSv (Extremely stripped down version of FreeBSD, executes JVM applications hooray finally!). Bootable with grub.
* Rump Kernels & Rumprun (Extremely stripped down version of NetBSD with user space drivers. Your application is "baked in"). Bootable with grub. Rump has a package directory for many common packages. Apparently rump has threading but no forking of processes? Problem for stuff like nginx.
* HaLVM
* MirageOS
* Clive (for Golang)
* IncludeOS (C++)
* Erlang Ling: https://github.com/cloudozer/ling
* Unik
* Microsoft Drawbridge: primarily research project atm it seems. Picoprocesses + Lightweight Windows version. Not sure what the point of having a version of Windows in Unikernel land is...

Oddly enough there are no mature projects that use a Linux-based Unikernel (that I know of)

I have not included products that are licensed in ways I don't like

Many Unikernel Frameworks right now are language specific, though that seems to be changing quite fast.

## Future

* Embedded systems: lots of nonsensical buzz about "IoT" devices (iToaster 2.0 TM), very little effort has been made to solve some of the problems these systems inherit (Security, Speed, Resource Footprint). Connected to the internet, but not to each other. They are deployed into environments with no professional management (in other words: your home). Serious bugs that WILL be discovered WILL NEVER be fixed on embedded devices.
* Dead Code Elimination for even leaner binaries
* Improved re-implementations of classic pieces of OS stack in "safer" languages (OCaml TLS stack in MirageOS --> BTC Pinata)
* Docker as a company seems very interested into moving into the Unikernel space, beyond "managing Linux containers" and "managing Hyper-V containers" and into "managing Unikernels"
* Maybe some day Linux will no longer we used for Internet facing services in the future. Maybe we'll see the day that a service connected to the internet will no longer be allowed to have a user sign-in system
* Similar setup to AWS Lamba / "serverless architecture"
* Server as a Function: 
```
type Server[Req, Res] = Res -> Future[Res]
```

## IoT (Sigh)

Should our future really look like the one predicted by IoT advocates, we're going to be in big trouble...

As developers, we all know how our systems behave. Would you be comfortable with systems in your home security, car or medical appliances if it was build and deployed the way we do it today? Probably not.

Problems

* Security: Updating systems is really hard, so we should have as little parts to update as possible. Current solutions are systems like seL4.
* Reliability: Unsafe memory usage and segfaults in your pacemaker will kill you.
* Power: Unlike the systems we've been using for the past decade(s), resource consumption actually matters here. When people think of IoT, they think of hobbiest tools made using an Raspberry Pi. Make no mistake: your RPi is an extremely powerful system. It's a MicroProcessor, NOT a MicroController. Actual embedded systems have extremely constrained environments.

## What apps cannot run on a Unikernel

Some apps don't fit a Unikernel design very well. An example could be an app that writes to disk a lot (an extreme example would be something like Postgres).

You can do some things to remediate this. For example, when making a rump kernel, on the hypervisor level you can map all system calls in the way you want. One might have all filesystem calls routed to CEPH for example.