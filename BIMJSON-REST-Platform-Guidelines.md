# BIMJSON REST Platform-Building Guidelines

Version 0.1, [GitHub repository](https://github.com/vdubya/BIMJSON)

## Introduction
First off, we need to understand what is meant by the term **'platform'** for the organization.
In todays world, the term 'platform' is such an overused buzzword that it is largely meaningless.
Platforms exist at many levels in the technology stack: hardware, datacenter, database,
messaging, etc. and can also be used to describe ecosystems of user experiences.

The view presented here is decidedly business focused: the 'platform' is really the method
of exposing and growing the digital business model and its offerings. So, the following ground rules
define the platform as the digital server-side, back-end data and functionality, leaving the user
experiences to be crafted (whether mobile, web or other) on top of that business functionality.

This API-centric definition of 'platform' enables:

* Rapid user experience revision
* Consistent user experience across devices
* Better channel adaptation
* Faster and cheaper third-party integrations
* Increased innovation capabilities.

## Guidelines

###1. **Mobile** applications are a first-class citizen and consumer of the platform.
1. Making mobile a priority increases ease of use and adoption for everyone.
###2. The platform exposes its data and functionality via a complete set of **RESTful APIs**.
1. An API-based strategy maximizes reuse and flexibility.
###3. All service interfaces and events are designed from the ground up to be **public facing**.
1. Causes design for ease of use, flexibility and reuse.
###4. Each team of developers access all APIs using their team's own **registered developer key**.
1. Improves security, auditability.
###5. There is a **single token source** and format for all APIs.
1. Assists in ease of use and ensures all functionality is available to consumers.
###6. The platform will notify interested observers of all resource state changes via **fully-composed events**.
1. Enables synchronization asynchronously (for all create, update, delete state changes).
###7. All APIs use a **single identity** to represent the consumer / user.
1. Enables analytics and reasoning about consumers.
###8. All APIs have a **consistent look and feel**, and are documented at a single, publicly-available location.
1. Maximizes reuse and ease of use.
###9. No private interfaces. **No backdoors**. No shared databases across teams (Observe 'Bounded Contexts').
1. Ensures the public interfaces are sound.
###10. APIs are deployed in a **distributed, always-on** architecture (multi-node, multi-zone, and multi-region) with a discipline towards resilience.
1. Horizontal scale on commodity hardware, global reach. Account for the inevitable failures.

______

Derived from: [RestCheatSheet/api-cheat-sheet](https://github.com/RestCheatSheet/api-cheat-sheet#api-design-cheat-sheet)

See also: [BIMJSON REST API Guidelines](https://github.com/vdubya/BIMJSON/blob/master/BIMJSON-REST-API-Guidelines.md)
