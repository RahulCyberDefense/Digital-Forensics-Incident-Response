# #53: TheHive Project Task1-4

![image.png](image.png)

Welcome to TheHive Project Outline!

This room will cover the foundations of using the TheHive Project, a Security Incident Response Platform.

Specifically, we will be looking at:

- What TheHive is?
- An overview of the platform's functionalities and integrations.
- Installing TheHive for yourself.
- Navigating the UI.
- Creation of a case assessment.

Before we begin, ensure you download the attached file, as it will be needed for Task 5.

# Task 2: Introduction

TheHive Project is a scalable, open-source and freely available Security Incident Response Platform, designed to assist security analysts and practitioners working in SOCs, CSIRTs and CERTs to track, investigate and act upon identified security incidents in a swift and collaborative manner.

Security Analysts can collaborate on investigations simultaneously, ensuring real-time information pertaining to new or existing cases, tasks, observables and IOCs are available to all team members.

More information about the project can be found on [https://thehive-project.org/](https://thehive-project.org/) & their [GitHub Repo](https://github.com/TheHive-Project/TheHive).

![Image: Cases dashboard on TheHive by order of reported severity](image%201.png)

Image: Cases dashboard on TheHive by order of reported severity

TheHive Project operates under the guide of three core functions:

- **Collaborate:** Multiple analysts from one organisation can work together on the same case simultaneously. Through its live stream capabilities, everyone can keep an eye on the cases in real time.
- **Elaborate:** Investigations correspond to cases. The details of each case can be broken down into associated tasks, which can be created from scratch or through a template engine. Additionally, analysts can record their progress, attach artifacts of evidence and assign tasks effortlessly.
- **Act:** A quick triaging process can be supported by allowing analysts to add observables to their cases, leveraging tags, flagging IOCs and identifying previously seen observables to feed their threat intelligence.

# Task 3: TheHive Features & Integrations

TheHive allows analysts from one organisation to work together on the same case simultaneously. This is due to the platform's rich feature set and integrations that support analyst workflows. The features include:

- **Case/Task Management:** Every investigation is meant to correspond to a case that has been created. Each case can be broken down into one or more tasks for added granularity and even be turned into templates for easier management. Additionally, analysts can record their progress, attach pieces of evidence or noteworthy files, add tags and other archives to cases.
- **Alert Triage:** Cases can be imported from SIEM alerts, email reports and other security event sources. This feature allows an analyst to go through the imported alerts and decide whether or not they are to be escalated into investigations or incident response.
- **Observable Enrichment with Cortex:** One of the main feature integrations TheHive supports is Cortex, an observable analysis and active response engine. Cortex allows analysts to collect more information from threat indicators by performing correlation analysis and developing patterns from the cases. More information on [Cortex](https://github.com/TheHive-Project/Cortex/).
- **Active Response:** TheHive allows analysts to use Responders and run active actions to communicate, share information about incidents and prevent or contain a threat.
- **Custom Dashboards:** Statistics on cases, tasks, observables, metrics and more can be compiled and distributed on dashboards that can be used to generate useful KPIs within an organisation.
- **Built-in MISP Integration:** Another useful integration is with [MISP](https://www.misp-project.org/index.html), a threat intelligence platform for sharing, storing and correlating Indicators of Compromise of targeted attacks and other threats. This integration allows analysts to create cases from MISP events, import IOCs or export their own identified indicators to their MISP communities.

Other notable integrations that TheHive supports are [DigitalShadows2TH](https://github.com/TheHive-Project/DigitalShadows2TH) & [ZeroFox2TH](https://github.com/TheHive-Project/Zerofox2TH), free and open-source extensions of alert feeders from [DigitalShadows](https://www.digitalshadows.com/) and [ZeroFox](https://www.zerofox.com/) respectively. These integrations ensure that alerts can be added into TheHive and transformed into new cases using pre-defined incident response templates or by adding to existing cases.

1. **Which open-source platform supports the analysis of observables within TheHive?**
    
    ![1_rRtWukZqkfm-G1d415lByw.webp](1_rRtWukZqkfm-G1d415lByw.webp)
    
    **Answer:** `Cortex`
    

# Task 4: User Profiles & Permissions

TheHive offers an administrator the ability to create an organisation group to identify the analysts and assign different roles based on a list of pre-configured user profiles.

![Admin Console - Create Organisation](image%202.png)

Admin Console - Create Organisation

The pre-configured user profiles are:

- **admin:** full administrative permissions on the platform; can't manage any Cases or other data related to investigations;
- **org-admin:** manage users and all organisation-level configuration, can create and edit Cases, Tasks, Observables and run Analysers and Responders;
- **analyst:** can create and edit Cases, Tasks, Observables and run Analysers & Responders;
- **read-only:** Can only read, Cases, Tasks and Observables details;

![Admin Console -  Add User](image%203.png)

Admin Console -  Add User

Each user profile has a pre-defined list of permissions that would allow the user to perform different tasks based on their role. When a profile has been selected, its permissions will be listed.

![image.png](image%204.png)

The full list of permissions includes:

| **Permission** | **Functions** |
| --- | --- |
| **manageOrganisation (1)** | Create & Update an organisation |
| **manageConfig (1)** | Update Configuration |
| **manageProfile (1)** | Create, update & delete Profiles |
| **manageTag (1)** | Create, update & Delete Tags |
| **manageCustomField (1)** | Create, update & delete Custom Fields |
| **manageCase** | Create, update & delete Cases |
| **manageObservable** | Create, update & delete Observables |
| **manageALert** | Create, update & import Alerts |
| **manageUser** | Create, update & delete Users |
| **manageCaseTemplate** | Create, update & delete Case templates |
| **manageTask** | Create, update & delete Tasks |
| **manageShare** | Share case, task & observable with other organisations |
| **manageAnalyse (2)** | Execute Analyse |
| **manageAction (2)** | Execute Actions |
| **manageAnalyserTemplate (2)** | Create, update & delete Analyser Templates |

*Note that (1) Organisations, configuration, profiles and tags are global objects. The related permissions are effective only on the “admin” organisation. (2) Actions, analysis and template are available only if the Cortex connector is enabled.*

In addition to adding new user profiles, the admin can also perform other operations such as creating case custom fields, custom observable types, custom analyser templates and importing TTPs from the MITRE ATT&CK framework, as displayed in the image below.

![Imported list of ATT&CK Patterns](image%205.png)

Imported list of ATT&CK Patterns

Deploy the machine attached to follow along on the next task. Please give it a minimum of 5 minutes to boot up. It would be best if you connected to the portal via [http://MACHINE_IP/index.html](http://machine_ip/index.html) on the AttackBox or using your VPN connection.

Log on to the *analyst* profile using the credentials:

*Username: analyst@tryhackme.mePassword: analyst1234*

### Answer the questions below

1. **Which pre-configured account cannot manage any cases?**
    
    ![1_LBPLHflD7YZmC1RTTQnAqg.webp](1_LBPLHflD7YZmC1RTTQnAqg.webp)
    
    **Answer**: admin
    

1. **Which permission allows a user to create, update or delete observables?**
    
    ![1_4kkCi_ETZhmTRDjbbQACFQ.webp](1_4kkCi_ETZhmTRDjbbQACFQ.webp)
    
    **Answer:** manageObservable
    

1. **Which permission allows a user to execute actions?**
    
    ![1_eCATK_Vno7vw5D3BkWLwaw.webp](1_eCATK_Vno7vw5D3BkWLwaw.webp)
    
    **Answer:** manageAction
    

---

Task 5 solved in the next file!