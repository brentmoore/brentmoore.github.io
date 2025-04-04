### Executive Summary

Our project team identified several cultural and prioritization issues across the organization. Reliability, security, and maintainability are often afterthoughts. We struggle to say no to customer requests for new capabilities, causing us to neglect our strategic imperatives. There are fundamental issues that we need to resolve within our organization. One way to ensure that the organization focuses on reliability rather than new capabilities is to dedicate engineering resources specifically to reliability.

Working within our existing organizational structure, we propose the creation of Site Reliability as a service. The service would fall under the Help, Support, and Operations Portfolio (Mary Stevens) and be provided by a new group within the Support and Operations Core Support Team (Mark Phillips). We believe Mary and Mark have the vision required to make site reliability engineering successful. Site reliability engineering is a well-defined term within the tech industry that was initially popularized by Google; they published a [canonical handbook](https://sre.google/books/) describing what it means to be a site reliability engineer (SRE). In short, SRE is what you get when you treat operations as if it’s a software problem—their sole focus is to deliver reliability, or a satisfying customer experience, in a scalable and sustainable manner. Individual applications would be onboarded by SREs on a one-by-one basis; the handbook describes this in detail (literally "handing over the pager"). OIT would staff this team with senior engineers. This is a role that is entirely new to OIT. The role involves considerable technical depth and breadth and necessarily involves some after-hours support expectations. As such, the role is usually associated with additional compensation[^1]. We would likely poach engineers from across OIT and thus impact existing project timelines.

To facilitate SREs supporting applications, we will eventually need Platform Engineers to build out an internal developer platform (IDP) that presents a "golden path" or "paved road" for the entire lifecycle of applications. The more our applications use common functionality, the easier they are to support and secure at scale. Note the order of operations, though. If we build out an IDP before maturing an SRE program, we risk building the wrong things. Some portions of this effort can still be parallelized. For example, we expect our IDP to primarily support _containerized_ applications, which are different than our existing platform team's virtual machine based managed domain offering. The more that teams can containerize applications, the more prepared the applications will be for onboarding onto the IDP and being supported by SREs.

We anticipate that this IDP will be composed of RedHat OpenShift (managed Kubernetes) and [additional functionality](https://platformengineering.org/platform-tooling) that we buy or build. In many cases, we already have components of an IDP that we know and love. As we build out and consolidate within our IDP, it will allow us to provide support across the application lifecycle in an increasingly hybrid and multi-cloud environment.

### Introduction

###### Project Definition

As part of [PRJ-11013](https://support.byu.edu/pm_project.do?sysparm_query=number=PRJ-11013), Nick Turley formed a cross-functional team and instructed us to investigate the interactions between Application Engineering, Platform, and Operations. We additionally investigated interactions with Security. Leadership was intentionally kept out of the project team, as he wanted the proposed solution to be driven by the folks feeling the pains of our current state.

Our task was to:

1.  Evaluate people, process, and tools

2.  Identify what's required to introduce platform engineering principles and establish a foundational internal developer platform (IDP)

3.  Propose a solution

###### Approach

To make sure that we understood our problem space correctly, we discussed the issues among ourselves, researched industry trends, and additionally interviewed nearly 20 people in management across OIT's portfolio, resource, and support teams.

We initially had a hard time drafting this proposal because we were given [clear objectives and a prescribed set of deliverables](https://byu.sharepoint.com/:w:/s/PlatformEngineeringProject/EcHxgp6-taBNuHiH3eALbycBSe2850AkrbPsPdgzTryjZA?e=zmndM2), but those deliverables didn't match the solution that we would suggest based on our personal observations and interview findings. Interestingly, the problem statement for this emergent project didn’t match our observations, either. Based on the problem statement, OIT leadership is under the assumption that Platform and Application Engineering teams have good collaboration and that the real issue is process and tooling related. Our findings show that the teams don’t interact unless forced; they aren’t incentivized to collaborate. Our proposal aims to resolve the base issue that’s impeding our organizational cohesion and collaboration: incentive misalignment.

###### What do we mean by incentive misalignment?

Application engineering teams don’t feel the value that platform engineers provide. They default to deploying to Amazon Web Services (AWS). To instead deploy into the on-prem environment provided by our platform team, application engineers give up freedom and introduce additional communication overhead. In AWS, they have flexibility in the resources they can deploy, coupled with a mature CI/CD experience. Our on-prem offering is comparatively immature. From an application engineer’s perspective, the value proposition of our platform engineering team is unclear: do they give up many capabilities and delay their projects to cultivate technological sameness that they don’t personally benefit from?

Likewise, to application engineers, the value proposition of our operations team is unclear. In our interviews, we repeatedly heard that our operations team doesn’t operate systems. (Rather, they primarily provide 1<sup>st</sup> and 2<sup>nd</sup> tier end-user support and forward information to engineers when systems are broken.) Operations teams shield engineers from end-user interactions, but apart from that, application engineers don’t feel the value. Instead of giving operations the ability to restart systems, it often makes more sense for engineers to detect problematic states and automatically handle them without requiring a human to press a button. The issues that application engineers feel our operations team can handle are different than the issues that application engineers would like to offload; they don’t feel that our operations team is equipped to handle complex issues.

When teams don’t feel the value of interaction, they aren’t incentivized to collaborate.

To provide a counterpoint to this line of thought about incentives, application engineers have recently been feeling pain around cultivating and sustaining the expertise needed to deploy and maintain their systems. That pain is exactly what centralized platform and operations teams are intended to resolve. In other words, traffic’s finally gotten bad enough that they’re willing to ride public transit. This indicates that application engineering teams are primed for suggestions and improvements in this space.

As we improve incentives and align on vision and goals, we believe that we will be able to improve in terms of customer experience and system reliability. OIT will be more prepared to adequately support our growing hybrid IT complexity, and we’ll be better equipped to provide cohesive support throughout the application lifecycle, from development to operations.

In this proposal, we outline a path that follows industry trends that will allow us to align incentives, improve our system reliability, and ultimately rein in technology sprawl through the establishment of an internal developer platform. Our end goal is an IDP that provides self-service opportunities and automations that embed security and architectural guardrails into our development lifecycle, but industry trends suggest that building out a site reliability engineering team is a prerequisite to an internal developer platform.

### Overview of Industry Trends

Our proposal is informed by overall industry trends, specifically within the tech industry.

In traditional software development, companies organized teams into separate silos based on their roles, such as development, operations, quality assurance, and so on. This allowed teams to build up expertise within specific portions of an application’s lifecycle. The downsides of this approach were communications barriers, slow development cycles, and increased risk of errors due to handoffs between teams. Reliability was an afterthought because the teams building applications were often different than the teams running the applications.

To combat these challenges, the tech industry moved towards a set of practices now known as DevOps. The overall idea was simple: you build it, you run it. The people building potentially buggy applications were now the same people on the hook for after-hours product support. Teams handled the entire application lifecycle from development to operations, hence the term DevOps. This movement emphasized increasing deployment frequency and reliability, which was done by streamlining and automating the building, testing, and deploying of code changes (continuous integration and continuous deployment, or CI/CD) and by treating infrastructure as code (IaC).

At OIT, at about the same time as we pushed for cloud-based deployments within AWS, AppEng adopted a DevOps model.[^2] Anything that application engineers deployed to AWS, they were expected to operate. Historically, this was because our platform and operations teams lacked depth of knowledge in AWS. This model was different than our split-responsibility model for applications deployed to our own data center. To facilitate feature delivery velocity, application engineering teams took advantage of cloud offerings and developed mature CI/CD and IaC practices that are unmatched by our on-premises offering. Unfortunately, application engineering teams haven’t yet matured in observability and operational capabilities for AWS-based applications.

Following the trend of adopting DevOps practices, larger technology companies found it unsustainable for development teams to own the entire application lifecycle for increasingly many services. Development teams struggled. Their cognitive load was immense. Teams needed to have experts in all lifecycle phases, across a diverse set of applications. Companies found that operational capabilities tended to be dismissed because teams were incentivized to enhance products, not maintain them. There was no glory (and there were no promotions to be had) in keeping products running. This mirrors what we see in OIT.

The tech industry’s response was a push for site reliability engineering, a practice where a subset of engineers is specifically dedicated to operational concerns, like reliability and maintainability. The idea is that site reliability engineers specialize in operating the set of applications that provide the most business value to the organization, so that those applications won’t be neglected. Development teams could then hand off operational concerns for some of their applications once those applications had become reasonably self-sustaining.

<img src="media/media/image1.jpg" style="width:3.48958in;height:5in" />As site reliability engineering matured, organizations identified that it was easier for SREs to maintain a larger set of applications if they had specialized tooling and if the applications looked more alike. This is an obvious conclusion, but SREs are likely to be the biggest consumers and thus strongest champions for unified infrastructure and tooling. Unified infrastructure should exist to serve the needs of site reliability engineers.  
  
As a direct result, platform engineering teams then emerged to satisfy the needs of site reliability engineers (and any application engineers that would eventually like their applications to be owned by SREs, i.e., all of them). This was the industry trend, and it happened in this order for a reason. Companies that attempted to build out a common internal development platform before maturing their site reliability engineering organizations often built the wrong things[^3]. Their platforms didn’t correctly address needs[^4], such as reliability. Their platforms went unused.

We’ve learned from tech industry peers that we need a mature site reliability engineering team *before* building an internal developer platform, so that the SRE team’s needs can inform what the platform team builds. The diagram above should be viewed as a *maturity model*, rather than a comparison of DevOps, SRE, and Platform Engineering.

That said, there are aspects of an internal developer platform where we already have a clear direction, like the need to support containers. We should improve our support for containerized applications, even as we work to mature our site reliability engineering organization. The industry has an [ecosystem built around Kubernetes](https://landscape.cncf.io/) that allows organizations to support containerized applications in hybrid and multi-cloud environments. This is the direction we need to take, given [our anticipated future needs](). Containers provide several benefits, including a convenient abstraction that provides a clear division of responsibilities between application and infrastructure teams. There is a clear industry trend to build container-based applications.

### Interviews

As mentioned in the introduction, in addition to our own personal observations and understanding of industry trends, we conducted interviews to ensure we understood the problems at OIT. We felt that as individual contributors, we might not have a complete perspective.

###### Interview Format

We interviewed nearly 20 people (mostly management) from across the organization. Our goal was to sample a broader perspective on the problem space. We asked the following questions:

1. What are your organization’s major objectives?

2. What are the major obstacles or pain points your team faces in meeting those objectives?

3. We’ve been tasked to explore the interactions between Platform, AppEng, and Ops. In your opinion, what does the interaction look like between those groups? Where do you feel like it could be improved?

4. Do you have any projects or initiatives in this space that you feel we should be aware of?

###### Interview Findings

We specifically analyzed the answers to question number two to determine how broadly specific pain points were felt across the organization. Here is a summary of the pain points expressed the most by interviewees.

| Finding / Pain Point | Breadth of Finding |
|----|:--:|
| We don't interact with each other unless we have to | 8 |
| Confusion about who does what/not enough distinction between roles and teams | 6 |
| Work gets done by contacting people personally instead of following processes / Projects coming through a "back door" | 6 |
| Unable to audit systems / low visibility into infrastructure and system configurations | 5 |
| Not enough automation/standardization | 5 |
| Everyone needs to know earlier / Getting people on the same page | 4 |
| Reliability is an afterthought | 4 |
| Unclear responsibility model when supporting campus | 4 |

A more comprehensive table, showing all the pain points expressed and which areas felt them, can be found in the addendum.

###### Conclusions about Interview Findings

To come up with a plan to address the identified pain points, we attempted to identify deeper root causes for each.

<table>
<colgroup>
<col style="width: 49%" />
<col style="width: 50%" />
</colgroup>
<thead>
<tr>
<th>Finding / Pain Point</th>
<th>Root Causes</th>
</tr>
</thead>
<tbody>
<tr>
<td>We don't interact with each other unless we have to</td>
<td><ul>
<li><p>Poor incentives to collaborate</p></li>
<li><p>Conway’s Law[^5]</p></li>
<li><p>Organizational inertia</p></li>
<li><p>Lack of standardized process for executing work</p></li>
</ul></td>
</tr>
<tr>
<td>Confusion about who does what/not enough distinction between roles and teams</td>
<td><ul>
<li><p>Lack of globally shared work prioritization</p></li>
<li><p>Lack of standardized process for executing work</p></li>
<li><p>Lack of clear job role definitions</p></li>
</ul></td>
</tr>
<tr>
<td>Work gets done by contacting people personally instead of following processes / Projects coming through a "back door"</td>
<td><ul>
<li><p>Lack of globally shared work prioritization</p></li>
<li><p>Lack of standardized process for executing work</p></li>
</ul></td>
</tr>
<tr>
<td>Unable to audit systems / low visibility into infrastructure and system configurations</td>
<td><ul>
<li><p>Lack of standardized tooling</p></li>
<li><p>Lack of standardized process for executing work</p></li>
</ul></td>
</tr>
<tr>
<td>Not enough automation/standardization</td>
<td><ul>
<li><p>OIT primarily focuses on feature delivery</p></li>
</ul></td>
</tr>
<tr>
<td>Everyone needs to know earlier / Getting people on the same page</td>
<td><ul>
<li><p>Lack of globally shared work prioritization</p></li>
</ul></td>
</tr>
<tr>
<td>Reliability is an afterthought</td>
<td><ul>
<li><p>OIT primarily focuses on feature delivery</p></li>
</ul></td>
</tr>
<tr>
<td>Unclear responsibility model when supporting campus</td>
<td><ul>
<li><p>Conway’s Law</p></li>
<li><p>Service Managers are focused on executing current projects, so they don’t have time to improve campus offerings and relationships</p></li>
</ul></td>
</tr>
</tbody>
</table>

### Proposed Solution Objective and Roadmap

###### Formalize a System Reliability Service Offering

We need a dedicated focus, from an engineering perspective, on system reliability. We propose that the Help, Support, and Operations Portfolio own this service.

###### Form an SRE Team

We propose that this team be formed inside the Operations Resource Pool.

We have some engineers that would likely be a good fit for an SRE team, but this would be a role change for them (that they may or may not find acceptable) and it would clearly impact their existing work. The obvious candidates for an SRE role are the usual people who hop on P1 calls, even when an issue isn’t directly related to their work. To narrow that list down a bit, the ideal candidates are also the folks who wouldn’t otherwise be better fits for a platform engineering team (that we’ll discuss the details of later in this document). Rick Winscot and Joe Seegmiller could make excellent SREs. Jamie Visker would also be an ideal candidate if he wasn’t an even better fit for a platform engineering role (again, discussion will follow).

We’ll list some of an SRE team’s potential responsibilities.

- Maintain a clear process to have SREs onboard applications.

- For any applications that SREs have onboarded:

  - SREs own the pager. Incidents go to them.

  - SREs handle ongoing routine patches.

    - If a specific patch requires considerable effort, the SREs may consider handing an application back, temporarily.

  - SREs operate applications within service level objectives (SLOs). They own non-functional requirements and are empowered to make system improvements to, for example, ensure that a system scales appropriately.

  - SREs make an application’s “error budget” visible to development teams.

    - This allows development teams to balance feature delivery with system reliability. (If new features frequently cause SLO breaches, the SRE team will work with the portfolio to get the application handed back to application engineering for further development until SLOs can be consistently met.)

  - SREs are empowered to improve non-functional characteristics of a system, e.g., by swapping out a database, as their bandwidth permits.

<!-- -->

- Define an internal developer platform:

  - We don’t want to buy or build the wrong things. We want to make sure that we meet needs. SREs will be pivotal in helping us understand what’s needed to operate systems reliably, at scale.

  - SREs can and should consolidate tools to support the applications that they own.

    - Early on, we anticipate consolidation around observability tools so that SREs can measure service level indicators (SLIs) against objectives (SLOs) and agreements (SLAs).

  - They will likely identify pain points in supporting production applications: certain processes or issues occur so frequently or cause so much toil that our SRE team would be able to produce a prioritized wishlist of items they would like resolved.

    - As those wishlist items are resolved, the site reliability engineers have more capacity to take on additional applications. Our site reliability offering becomes more scalable.

    - As those wishlist items are resolved, we’re handling things that directly impact our ability to run production systems in a reliable way.

    - As those wishlist items are resolved, we’re removing friction from across the application lifecycle, or in other words, we’re building out an internal developer platform.

  - Resolve their own pain points (as bandwidth allows) or coordinate with engineering teams for solutions.

- Advocate for other development teams to consolidate tools and improve reliability

  - SRE handoffs are simplified when applications already use the same tools as the systems that SREs are supporting

    - This becomes a useful “carrot” to consolidate around an internal developer platform. The IDP exists to serve SREs, but ultimately can be expanded.

###### Develop an SRE Handoff Review Process

A production readiness review process, sometimes called an SRE handoff review, should be created. There is much prior art on the subject[^6], but to summarize, this process ensures that SREs are a good fit for the application (i.e., can they deliver value?) and that the application is reasonably prepared for SRE support. As we mature in site reliability engineering, we’ll be able to “shift left” with part of this process and design applications in such a way that they’ll be primed for SRE onboarding. Readiness reviews involve SREs working with application teams to develop KPIs to help measure the value provided by SREs and to help determine if/when newly-developed features negatively affect SLIs such that SREs should hand applications back.

This SRE handoff review would complement existing scorecards/checklists at OIT. The difference is that SRE teams own a subset of applications, but we expect every application to be scored. They have different focuses. The SRE handoff review helps determine if an application is ready to be handed off *today* (or soon). The scorecards give us a sense of what *future* development might be necessary.

###### Build an on-premises Kubernetes Offering

Kubernetes is the de facto standard for operating in hybrid and multi-cloud environments. It’s a great tool for building [distributed systems](https://brooker.co.za/blog/2024/06/04/scale.html). We fully anticipate that our SREs will need to support applications running in containers on Kubernetes. There is an ecosystem around Kubernetes that can facilitate an application’s entire lifecycle. We intend to capitalize on that.

We can start on this path immediately, to begin to gain expertise. We suggest that we learn to operate Kubernetes by starting in a single location, Provo, before jumping into a cloud-spanning deployment.

We expect Kubernetes to form the base of what will eventually be our internal developer platform. On top of Kubernetes, we expect to eventually offer databases, a secrets store, and more. But again, we don’t want to do this prematurely. SRE priorities should inform our prioritization of ecosystem value-adds.

###### Containerize existing applications

Once we have Kubernetes up and running, at least at a basic level, it should enable us to run containers on-prem in a maintainable fashion.

Containers provide us with a cleaner separation of responsibilities when compared with virtual machines (VMs). In our present state, our on-prem offering is primarily to give customers VMs. Those are often treated as *pets, not* *cattle*[^7] where people create bespoke, non-repeatable solutions. These were the #2, #4, and #5 most frequently cited issues in our interviews, and they were mentioned by everyone that regularly interacts with our current platform. Containers help us resolve each of these issues.

To explain the point about separation of responsibilities, we often see tight coupling between a VM’s operating system (OS) and its application runtime. Our typical operating system, RedHat Enterprise Linux, ships with a certain version of PHP. Some of our applications expect certain versions of PHP, so PHP version upgrades can break our applications. Operating system updates thus force us to modify application code.

To explain the point about repeatable solutions, if an application running in a VM needs some new permissions, the task is traditionally accomplished by SSHing in (read: connecting to the VM) and doing the adjustment manually. As a result, it can be nearly impossible to determine what’s been done in any individual VM; those manual changes are undocumented and difficult to recreate later without scouring a VM to determine what’s different about that VM’s particular configuration.

Containers simplify both issues. It's less ergonomic to make one-off adjustments to container images, so developers typically find it much easier to treat containers as cattle, not pets. And containers decouple operating system patches from application runtimes, which enables a much cleaner separation of responsibilities between our platform engineers and our application engineers.

We have many VMs that would benefit from containerization[^8]. Luckily, our application engineers are already very familiar with containers. They frequently build and deploy containers... to AWS. Deploying to Kubernetes on-prem would be a relatively small adjustment from their regular workflow.

Based on our interview findings, we have platform engineers that like performing routine tasks. We have application engineers that know how to containerize applications. We believe these teams could work together to containerize many of the applications currently running in VMs and reduce our maintenance burden going forward. This would be a large undertaking, but we believe the benefits would be worth it in the long term.

###### Kubernetes Cloud PoC

Once we’ve developed experience with operating Kubernetes, the Compute, Storage, and Reliability Portfolio should conduct a proof-of-concept to determine what the Kubernetes offering will look like in our public cloud environments.

Azure is currently not a fully-fleshed-out service offering. This PoC could be tacked onto our existing project to solidify our Azure service offering. As we expand Kubernetes to allow us to run workloads in Azure, we would then have more of a footprint. In time, that footprint may enable us to interoperate with other Azure services more seamlessly.

In AWS, we’ve historically been using AWS’s managed container-as-a-service tool, Fargate. A logical next step would be to determine what the transition from AWS Fargate to Kubernetes-in-AWS might look like, since that would allow us to consolidate our control planes (i.e., the bits that handle deployments, scaling, observability, and so forth).

###### Create a Platform Engineering Team, in the Modern Sense

In most cases, our current Platform team looks like what the industry would call a Private Cloud team, focusing on operating an on-premises data center. Modern implementations of a Platform team focus on delivering the underlying services that SREs (and Application Engineering teams) use to support applications across their entire lifecycles.

If you squint, the modern definition sounds a lot like the Developer team in Application Engineering. It also sounds somewhat like our existing Platform team. And it sounds, maybe, like our monitoring team. As we go down this path, we anticipate realignment in these teams and their corresponding portfolios.

A platform engineering team’s purpose is to [design and build toolchains and workflows that enable self-service capabilities for software engineering organizations ... covering the operational necessities of the entire lifecycle of an application](https://platformengineering.org/blog/what-is-platform-engineering). There is currently not any one team within the organization which possesses the combination of skills required to accomplish this purpose. We believe that, eventually, we’ll want to get there.

To do so, we expect that we would likely split our current “platform” team into a Private Cloud team (We could perhaps call them “infrastructure engineering”, but that’s a term that’s nearly as overloaded as “platform”.) and a Platform team (in the modern sense). We would then snag folks from our current-day Developer team in Application Engineering and put them on the new Platform team. We would likely also *repurpose* folks from our current-day monitoring team. (We anticipate that in an era where observability is primarily self-service and we’ve consolidated around a SaaS solution like Dynatrace, we would only have minimal administration needs for the tool itself.)

###### Build out an Internal Developer Platform

Let’s look back at our diagram from before, but clarify some things as they relate to OIT, specifically.

<img src="media/media/image2.png" style="width:6.5in;height:5.95834in" />  
The purple box, Unified Infrastructure, typically refers to a public cloud provider. In our case, it would be infrastructure that’s hybrid and multi-cloud, provided through the abstraction of Kubernetes (or to be more specific, RedHat‘s managed version, OpenShift). You’ll note that at first, it’s primarily the applications supported by SREs that are built on the unified infrastructure. But as time goes on, all sorts of applications are on top of the unified infrastructure.

Next, let’s look at the blue box as we go through this maturity model. Originally, it’s labelled “SRE Tooling”. That’s because it’s likely built or purchased/integrated by the SREs at first. Then, once it’s matured, it becomes a service offering of its own that’s consumed by SREs and other teams.

The idea of building out an internal developer platform (IDP) is really the idea of maturing, crystallizing, and scaling our use of SRE tooling. You’ll note that the diagram emphasizes that an IDP facilitates application development from build-time to production operation. It’s an ecosystem, built on top of our unified infrastructure, Kubernetes.

We currently have some things that look somewhat like an IDP. Within Application Engineering, for the subset of applications that are deployed to AWS (using all sorts of buzzwords like GitHub Actions and Terraform), we’re reasonably mature for *portions* of an application’s lifecycle. We especially lack maturity at the tail end of an application’s lifecycle, for observability and operation.

We want to take that expertise, consolidate it, and apply it to all the applications that we operate (not just those in AWS) by using Kubernetes and maturing our platform there.

Our current tooling for developing, deploying, and operating services contains many redundant capabilities due to each deployment location being architected independently. This redundant tooling results in wasted effort, drastically reducing the amount of value that our limited resources can provide. Consolidation is the solution. The way to do that is with Kubernetes and its ecosystem.

Here’s a diagram showing roughly what our current state looks like:

<img src="media/media/image3.png" style="width:5in;height:2.95833in" />

It’s not super apparent from the diagram, but we were trying to point out that we have multiple overlapping solutions that vary based on where an application’s deployed.

For our C-Framework, code is built in “pet” VMs and pushed to production through a scheduled cron job. Infrastructure is not handled with code. Most code goes without automated testing. Visibility into deployments is largely limited to the engineers that get the email notifications. We happened into observability because C-Framework systems had so many consumers that somebody would scream. To this day, database connections occasionally die and are not self-healing, causing us to manually intervene and restart a VM. To debug, we investigate reverse proxy logs, literally pay people to stare at database connection health, and SSH into VMs (the “chewy” boxes) to see if any of the C-Framework instances are screaming at us and in an obviously broken state. Things have significantly improved in terms of observability and debuggability with our rollout of Dynatrace, but the overall lifecycle story still isn’t good.

Other applications have worse stories. Across campus, we’re lucky if applications have a dev environment that’s not production. A common occurrence is that developers put applications into VMs and never patch them. Those applications scare us.

Another scary case is with our PHP applications. We mostly YOLO code straight to production, literally by SSHing into VMs, replacing code, and restarting applications. This is an immature process for the first half of the application lifecycle. We’re lucky that these applications have reasonably good observability by virtue of being deployed on-premises; we can investigate issues with Dynatrace and we have useful logs from our reverse proxies and Tyk.

One of our better stories is with applications deployed to AWS. We’re reasonably mature with our automated build, test, and deploy processes. Everyone at OIT can get visibility into those processes. But then we’re less mature with observing and operating the applications in AWS, since logs and other tools that would be useful for debugging are often sequestered away in AWS accounts that only a few developers (and nobody in Ops) have access to. Observability is improving gradually as we expand usage of Dynatrace to these applications, but the ability to *operate* systems is still siloed away.

Each of these stories is about application lifecycles that use completely different sets of tools. We want to consolidate the tools and not have a dozen half-baked solutions (at best!). SREs should be able to provide clearer direction, but among other things, we anticipate consolidating mostly onto RedHat OpenShift, solidifying our deployment processes around containers, and consolidating observability tools from the 6-ish we have now down to Dynatrace, CrowdStrike/Humio, and the stuff that’s built into OpenShift. (For all we know, we might even be able to consolidate further.)

Here’s a guess at what a future state might look like. Note the dramatically simplified left side of the diagram and the fact that the right side of the diagram shows our different deployment locations (or “resource planes”) using roughly the same set of tools:

<img src="media/media/image4.png" style="width:4.33336in;height:2.57292in" />

This will take time (years) to fully transition.

###### Standardize responsibility models for product lifecycle

Our current application lifecycles are often poorly understood and executed. DevOps practitioners have a model that we find helpful when looking at lifecycles. We found it extremely difficult to even identify responsible parties for many steps. When we could clearly identify responsible parties, these were mostly individuals (not teams), and were identified via folk knowledge, not documentation.

<img src="media/media/image5.jpg" style="width:5.70843in;height:3.45842in" />

<img src="media/media/image6.jpg" style="width:3.89585in;height:2.38545in" />

In our proposed future state, we anticipate that development teams will own some applications and SREs will own others. To clarify this to portfolio leadership and other stakeholders, we anticipate that for every application, we will need to indicate what *team* is responsible for each portion of the lifecycle.

(Note that for applications where SREs own the pager, development teams may still do some feature development. For more reading on this, look up “error budgets”.)

Here are some diagrams showing how the responsibilities might be divided in the future for some selected applications:

<img src="media/media/image7.jpg" style="width:5.6355in;height:2.94796in" />

<img src="media/media/image8.jpg" title="Inserting image..." style="width:5.35425in;height:3.28127in" />

###### Conclusion

We believe this proposal is promising, but these are obviously some big changes and there’s a mountain of work ahead of us.

We hope that with this clear direction, we’ll be able to make traction soon. We believe that we can make real progress along the Traditional Development -> DevOps -> SRE -> Platform Engineering maturity model that we’ve laid out. We believe that we’ve outlined actionable next steps (deploying Kubernetes, creating an SRE team, and containerizing applications) and clearly indicated what needs to follow (building out an IDP, consolidating and performing a lot of migrations, and expanding Kubernetes to public clouds).

We hope that organizational issues, like creating a role definition for SREs and staffing the team, won’t impede this effort.

### Addendum

###### A Note to Leadership

Many interviews identified a problem in communication from Leadership and Portfolios. While not out of scope for the problem space, we believe our team lacks the necessary context and insight to address these problems, but we have highlighted a few key takeaways:

1. **Leadership Misidentification:** Leadership identifies the wrong issues affecting productivity, focusing on developer productivity instead of addressing the decision-making process within OIT.

2. **Decision-Making Process:** The decision-making process within OIT is flawed and not aligned with strategic objectives, leading to delays, lack of accountability, and unavailability of services.

3. **Commitment and Capacity:** OIT struggles to keep commitments due to taking on more work than it can manage, which affects integrity and delivery dates.

4. **Priority Alignment:** While certain projects like Workday are theoretically prioritized, in practice, multiple tasks are assigned, leading to conflicts and delays.

5. **Lack of Retirement Planning:** There's a lack of structured processes for retiring outdated systems, leaving it up to engineers and resulting in inefficiencies.

6. **Student Success and Campus Alignment:** OIT's role in enabling student success is ambiguous, with unclear objectives and alignment with campus needs.

###### Workload planning and process

OIT currently has a disconnect between portfolio teams planning projects, workload, training needed and the implementation by resource pools and teams. The issue becomes apparent to engineers when a project that has been planned a year in advance finally becomes actionable by the teams/individuals assigned. Currently there is no defined process of how project work gets broken into tasks, stories, and “sprints”.

Portfolios have inconsistent success when it comes to determining what the portfolio customers need. Part of this problem stems from service managers taking on an additional role of project managers. This means that all the time dedicated to running projects cannot be used to meet with customers to determine if we are meeting the technological needs for a given product or service. This means that we tend to end up building solutions that don’t fully meet and satisfy our customers.

The lack of time given to service managers to find the customer stories leads to resource pool management attempting to take on this workload. Resource managers and directors begin to act as an extension of portfolio causing confusion for developers and engineers.

To help solve this problem strictly defining and standardizing the portfolio and manger roles will help process to flow through this management level.

Additionally, there needs to be a clear understanding and consistent use of tasks, stories, incidents and other work types in communicating work to engineers. Too often, engineers are confused by priorities and objectives when enhancement requests are submitted as incidents because service managers want to communicate a higher priority. Conversely, when work is communicated through the expected channels, we lose the communication of the priority of the work.

###### Previous SRE Attempts

In 2019, OIT had an “SRE team” that grew out of Operations. Jared Lau led the team and was supported by Jess Lyons and Kevin Crook. As the name implies, the team was created with the goal to improve OIT’s reliability. Because of Jared’s background, that goal was interpreted as “improve OIT’s monitoring.” As a result, the approach the team took was to deploy Humio (a log aggregator), cause a handful of applications to ship logs there, and then kill off Kibana (another log aggregator) in early 2020.

At the risk of committing the [No True Scotsman fallacy](https://en.wikipedia.org/wiki/No_true_Scotsman), this team was not a true SRE team. They would be unrecognizable as an SRE team when compared to the canonical definition provided by Google. The team never owned any user-facing production environments. There were no pager handoffs involved. They were never responsible for a system’s reliability. Instead, they took a tool and tried to convince developers to use the tool.

We want to avoid making the same mistake. We risk doing the wrong things if we’re not addressing the needs of the people supporting production. We need an SRE team *that owns production applications* before building out an internal developer platform (of which a log aggregator may be a small part).

Platform and Application Engineering teams have tried to fill this void for years. Those efforts have been futile without organizational support, e.g., in the form of an org chart change and leadership backing.

Our Platform team performs automated OS patches for Linux managed domain VMs and then creates standard changes that they then call “SRE RFC Updates,” likely because regular patches are a function that might otherwise be provided by SREs. Platform can’t do more because they don’t own the applications they're maintaining; it’s hard for them to do anything beyond OS patches without also being responsible for application internals. For them to do anything further would require a change in the org chart.

Application Engineering previously attempted to separate enhancement and maintenance work by creating “SpecOps teams” that focus entirely on handling incidents and maintenance. Experiencing the symptoms of burnout and the loss of expertise on SpecOps teams, AppEng opted to re-form product-centric teams that again enhanced and operated systems.

Application Engineering’s attempt failed because of incorrect staffing, expectations, and compensation. They need leadership support to resolve each root cause. SREs are typically senior engineers, not a rotating cast of student employees. SREs need to carefully manage the toil involved in on-call rotations, which is typically achieved by having ~6 SREs rotate through on-call and sustainably onboarding applications such that only 30-50% of their time is spent on reactive efforts (with the balance going to proactive efforts like development and coaching others). Leadership needs to shield SREs as they protect that balance. And finally, SREs are typically compensated for their breadth/depth of knowledge and relatively-poor work/life balance from on-call rotations. (Obviously, this requires leadership to be supportive of their increased compensation.) By following those typical practices, we can mitigate burnout and knowledge loss and thus avoid running into the same issues that Application Engineering faced.

###### How does Site Reliability Engineering Work?

Applications are onboarded according to the business value that SREs could provide. Some applications deliver immense business value but require significant effort to maintain, others deliver less value but require hardly any effort to maintain. Both of those cases might be good candidates.

###### Why do we keep using the term “observability” instead of “monitoring?”

Monitoring is reactive in nature; observability is proactive in nature. Monitoring gives current health, alerts, and a baseline for usage of an application. Observability goes a step further and tracks the interdependencies and dependencies of an application. This broader scope can catch problems not application specific but are tied to dependencies. It can also catch issues with interdependencies within an application that with regular monitoring the problem is not apparent. Teams should have a mindset of observability rather than monitoring.

###### Comprehensive List of Interview Findings

<table style="width:100%;">
<colgroup>
<col style="width: 32%" />
<col style="width: 3%" />
<col style="width: 3%" />
<col style="width: 3%" />
<col style="width: 3%" />
<col style="width: 3%" />
<col style="width: 3%" />
<col style="width: 3%" />
<col style="width: 3%" />
<col style="width: 3%" />
<col style="width: 3%" />
<col style="width: 3%" />
<col style="width: 3%" />
<col style="width: 3%" />
<col style="width: 3%" />
<col style="width: 3%" />
<col style="width: 3%" />
<col style="width: 3%" />
<col style="width: 3%" />
</colgroup>
<thead>
<tr>
<th>Finding / Pain Point</th>
<th style="text-align: center;"><blockquote>
<p>Breadth of Finding</p>
</blockquote></th>
<th style="text-align: center;"><blockquote>
<p>Sean McHenry</p>
</blockquote></th>
<th style="text-align: center;"><blockquote>
<p>Debra Biser</p>
</blockquote></th>
<th style="text-align: center;"><blockquote>
<p>Adam Stout</p>
</blockquote></th>
<th><blockquote>
<p>Matthew Hailstone</p>
</blockquote></th>
<th><blockquote>
<p>Mary Stevens</p>
</blockquote></th>
<th style="text-align: center;"><blockquote>
<p>Marisa Vaiaoga</p>
</blockquote></th>
<th><blockquote>
<p>Joe Hadfield</p>
</blockquote></th>
<th><blockquote>
<p>Jill Romboy</p>
</blockquote></th>
<th><blockquote>
<p>Jason Douglas</p>
</blockquote></th>
<th><blockquote>
<p>Curtis Hastings</p>
</blockquote></th>
<th style="text-align: center;"><blockquote>
<p>Jason Williams</p>
</blockquote></th>
<th style="text-align: center;"><blockquote>
<p>Mark Epling</p>
</blockquote></th>
<th style="text-align: center;"><blockquote>
<p>Dan Cunningham</p>
</blockquote></th>
<th><blockquote>
<p>Mark Phillips</p>
</blockquote></th>
<th><blockquote>
<p>Chris Johnson</p>
</blockquote></th>
<th style="text-align: center;"><blockquote>
<p>Sherwin Harris</p>
</blockquote></th>
<th><blockquote>
<p>Rhett Bulkley</p>
</blockquote></th>
</tr>
</thead>
<tbody>
<tr>
<td>We don't interact with each other unless we have to</td>
<td style="text-align: center;">8</td>
<td style="text-align: center;">x</td>
<td style="text-align: center;">x</td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td></td>
<td></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;">x</td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td style="text-align: center;">x</td>
<td></td>
</tr>
<tr>
<td>Confusion about who does what/not enough distinction between roles and teams</td>
<td style="text-align: center;">6</td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;">x</td>
<td></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td></td>
</tr>
<tr>
<td>Work gets done by contacting people personally instead of following processes / Projects coming through a "back door"</td>
<td style="text-align: center;">6</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td></td>
</tr>
<tr>
<td>Unable to audit systems / low visibility into infrastructure and system configurations</td>
<td style="text-align: center;">5</td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;">x</td>
<td></td>
<td style="text-align: center;">x</td>
<td></td>
</tr>
<tr>
<td>Not enough automation/standardization</td>
<td style="text-align: center;">5</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;">x</td>
<td></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td></td>
</tr>
<tr>
<td>Everyone needs to know earlier / Getting people on the same page</td>
<td style="text-align: center;">4</td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
</tr>
<tr>
<td>Reliability is an afterthought</td>
<td style="text-align: center;">4</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;">x</td>
<td></td>
<td style="text-align: center;"></td>
<td>x</td>
</tr>
<tr>
<td>Unclear responsibility model when supporting campus</td>
<td style="text-align: center;">4</td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;">x</td>
<td></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td>x</td>
</tr>
<tr>
<td>Some individuals or teams always at capacity (cloud team?)</td>
<td style="text-align: center;">3</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;">x</td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
</tr>
<tr>
<td>Processes don't necessarily focus on customers</td>
<td style="text-align: center;">3</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td>x</td>
</tr>
<tr>
<td>Vertical communication (leadership &lt;-&gt; engineers)</td>
<td style="text-align: center;">3</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td>x</td>
</tr>
<tr>
<td>Development of an app is not done with the server or platform in mind. No thought on how the pieces work together.</td>
<td style="text-align: center;">3</td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
</tr>
<tr>
<td>Inability to say no; maintain/secure/solidify instead of add features</td>
<td style="text-align: center;">3</td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td>x</td>
</tr>
<tr>
<td>Resistance to modernization</td>
<td style="text-align: center;">2</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
</tr>
<tr>
<td>Lack of desire to upskill/Lack of or losing people with knowledge or skills needed</td>
<td style="text-align: center;">2</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
</tr>
<tr>
<td>Operations team doesn't really "operate"</td>
<td style="text-align: center;">2</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td></td>
<td style="text-align: center;">x</td>
<td></td>
</tr>
<tr>
<td>Work prioritization methods are unclear</td>
<td style="text-align: center;">2</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td>x</td>
</tr>
<tr>
<td>TSC doesn't provide much context when relaying issues</td>
<td style="text-align: center;">1</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
</tr>
<tr>
<td>App Eng is doing their own infrastructure and not relying on platform/high cognitive load</td>
<td style="text-align: center;">1</td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
</tr>
<tr>
<td>Work slows down at the beginning of the year due to planning processes--assignments are unclear</td>
<td style="text-align: center;">1</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
</tr>
<tr>
<td>Tight integrations instead of reasonable abstractions</td>
<td style="text-align: center;">1</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
</tr>
<tr>
<td>Segregated portfolios and resource teams makes it hard to prioritize work</td>
<td style="text-align: center;">1</td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;">x</td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td style="text-align: center;"></td>
<td></td>
<td></td>
<td style="text-align: center;"></td>
<td></td>
</tr>
</tbody>
</table>

---

[^1]: According to levels.fyi, the [median total compensation for a software engineer in the US is $178,000](https://www.levels.fyi/t/software-engineer?countryId=254) and the [median total compensation for a site reliability engineer in the US is $197,500](https://www.levels.fyi/t/software-engineer/focus/site-reliability-sre?countryId=254) as of May 3rd, 2024.

[^2]: While OIT’s application engineering department developed DevOps capabilities, much of BYU campus still operates in a traditional software development model. Campus is often short-staffed and poorly equipped to manage an application’s entire lifecycle. To mature campus’s software engineering practices, we would recommend that they work on DevOps capabilities before moving onto SRE practices or platform engineering. Of course, one way to do this would be by increasing centralization within OIT.

[^3]: See Pitfalls 5 and 8 from [Charity Majors’ presentation on platform engineering](https://www.infoq.com/presentations/platform-engineering-teams/).

[^4]: See survey results from the [2024 State of DevOps Report: The Evolution of Platform Engineering](https://www.puppet.com/system/files/pup-2024-sodor-report.pdf).

[^5]: Conway’s Law is the idea that organizations, who design systems, are constrained to produce designs which are copies of the communication structures of these organizations.

[^6]: Google’s publicly-available books suggest processes:  
- [The SRE handbook describes a Production Readiness Review and other processes](https://sre.google/sre-book/evolving-sre-engagement-model/)  
- [The SRE workbook gives advice on SRE interaction over an application's lifecycle from planning to retirement](https://sre.google/workbook/engagement-model/)

Google's SRE blog discusses pager handoffs:  
- Why an SRE team would or wouldn't choose to onboard a new application  
- What an SRE team would want to learn about an application angling for SRE support, and what kind of improvements they want to see before considering it for take-over  
- What happens once the SREs agree to take the pager (and the cases where SREs would give it back)

[^7]: “The servers in today's data center are like puppies -- they've got names and when they get sick, everything grinds to a halt while you nurse them back to health... [You should manage your] servers like cattle -- you number them, and when they get sick and you have to shoot them in the head, the herd can keep moving. It takes a family of three to care for a single puppy, but a few cowboys can drive tens of thousands of cows over great distances, all while drinking whiskey.” - Joshua McKenty, in 2013

[^8]: As of June 4, 2024, BYU has almost 2000 VMs. Of those, OIT runs 844 Linux VMs and 385 Windows VMs. We believe that 89 of our Linux VMs are appliances, or off-the-shelf tools that we purchased that *must* be run in VMs. That implies that about 90% of our Linux VMs could potentially be containerized.

As for our Windows-based VMs, the story is more complicated. About a third of our Windows-based VMs are running MS SQL Server. While these could probably be containerized, doing so doesn’t produce as clear of a benefit as containerizing other applications. We don’t have a strong Microsoft development presence, which implies that the remaining two-thirds are running purchased applications. Any vendor that releases a containerizable version of their app for Windows probably also has a version for Linux that we would choose to run instead. In summary, we believe that moving our Windows workloads from VMs to containers would require more effort and yield less value.
