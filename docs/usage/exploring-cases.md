# Cases

When you click on "Cases" in the left-side bar, you access all the "Cases" tabs, visible on the top bar on the left. By default, the user directly access the "Incident Responses" tab, but can navigate to the other tabs as well.

As Analyses, `Cases` can contain other objects. This way, by adding context and results of your investigations in the case, you will be able to get an up-to-date overview of the ongoing situation, and later produce more easily an incident report. 

From the `Cases` section, users can access the following tabs:

- `Incident Responses`: This type of Cases is dedicated to the management of incidents. An Incident Response case does not represent an incident, but all the context and actions that will encompass the response to a specific incident.
- `Request for Information`: CTI teams are often asked to provide extensive information and analysis on a specific subject, be it related to an ongoing incident or a particular trending threat. Request for Information cases allow you to store context and actions relative to this type of request and its response.
- `Request for Takedown`: When an organization is targeted by an attack campaign, a typical response action can be to request the Takedown of elements of the attack infrastructure, for example a domain name impersonating the organization to phish its employees, or an email address used to deliver phishing content. As Takedown needs in most case to reach out to external providers and be effective quickly, it often needs specific workflows. Request for Takedown cases give you a dedicated space to manage these specific actions.
- `Tasks`: In every case, you need tasks to be performed in order to solve it. The Tasks tab allows you to review all created tasks to quickly see past due date, or quickly see every task assigned to a specific user.
- `Feedbacks`: If you use your platform to interact with other teams and provide them CTI Knowledge, some users may want to give you feedback about it. Those feedbacks can easily be considered as another type of case to solve, as it will often refer to Knowledge inconsistency or gaps.

![Cases Default page is Incident Response](assets/cases-default-landing-page.png)

## Incident Response, Request for Information & Request for Takedown

### General presentation

Incident responses, Request for Information & Request for Takedown cases are an important part of the case management system in OpenCTI. Here, you can organize the work of your team to respond to cybersecurity situations. You can also give context to the team and other users on the platform about the situation and actions (to be) taken.

To manage the situation, you can issue `Tasks` and assign them to users in the platform, by directly creating a Task or by applying a Case template that will append a list of predefined tasks.

To bring context, you can use your Case as a container (like Reports or Groupings), allowing you to add any Knowledge from your platform in it. You can also use this possibility to trace your investigation, your Case playing the role of an Incident report. You will find more information about case management [here](case-management.md).

Incident Response, Request for Information & Request for Takedown are not STIX 2.1 Objects.

When clicking on the Incident Response, Request for Information & Request for Takedown tabs at the top, you see the list of all the Cases you have access to, in respect with your [allowed marking definitions](../administration/users.md). You can then search and filter on some common and specific attributes.

### Visualizing Knowledge within an Incident Response, Request for Information & Request for Takedown

When clicking on an Incident Response, Request for Information or Request for Takedown, you land on the Overview tab. The following tabs are accessible:

- Overview: Overview of Cases are slightly different from the usual (described [here](overview.md#overview-section)). Cases' Overview displays also the list of the tasks associated with the case. It also let you highlight Incident, Report or Sighting at the origin of the case. If other cases contains some Observables with your Case, they will be displayed as Related Cases in the Overview.
- Knowledge: a complex tab that regroups all the structured Knowledge contained in the Case, accessible through different views (See below for a dive-in). As described [here](overview.md#knowledge-section).
- Content: a tab to provide access to content mapping, suggested mapping and allows to preview, manage and write the deliverables associated with the Case. For example, an analytical report to share with other teams, a markdown file to feed a collaborative wiki, etc. As described [here](overview.md#content-section).
- Entities: A table containing all SDO (Stix Domain Objects) contained in the Case, with search and filters available. It also displays if the SDO has been added directly or through [inferences with the reasoning engine](inferences.md)
- Observables: A table containing all SCO (Stix Cyber Observable) contained in the Case, with search and filters available. It also displays if the SDO has been added directly or through [inferences with the reasoning engine](inferences.md)
- Data: as described [here](overview.md#data-section).

Exploring and modifying the structured Knowledge contained in a Case can be done through different lenses.

#### Graph View

![Graph View of a Case](assets/case-graph.png)

In Graph view, STIX SDO are displayed as graph nodes and relationships as graph links. Nodes are colored depending on their type. Direct relationship are displayed as plain link and inferred relationships in dotted link.
At the top right, you will find a series of icons. From there you can change the current type of view. Here you can also perform global action on the Knowledge of the Case. Let's highlight 2 of them:

- Suggestions: This tool suggests you some logical relationships to add between your contained Object to give more consistency to your Knowledge.
- Share with an Organization: if you have designated a main Organization in the platform settings, you can here share your Case and its content with users of another Organization.
At the bottom, you have many option to manipulate the graph:
- Multiple option for shaping the graph and applying forces to the nodes and links
- Multiple selection options
- Multiple filters, including a time range selector allowing you to see the evolution of the Knowledge within the Case.
- Multiple creation and edition tools to modify the Knowledge contained in the Case.


#### Timeline view

![Timeline view of a Case](assets/case-timeline.png)

This view allows you to see the structured Knowledge chronologically. This view is particularly useful in the context of a Case, allowing you to see the chain of events, either from the attack perspectives, the defense perspectives or both.
The view can be filtered and displayed relationships too.

#### Matrix view

If your Case contains attack patterns, you will be able to visualize them in a Matrix view.

### Restricting access to an Incident Response, Request for Information & Request for Takedown

#### Organization segregation

If you have designated a main Organization in the platform settings, you can share your Case and its content with users of an other Organization.

![containers-organization-sharing-button.png](assets%2Fcontainers-organization-sharing-button.png)

[read more about organization segregation](..%2Fadministration%2Forganization-segregation.md)

#### Authorized members

**Authorized members** allow to restrict access to an entity to certain users, groups, or organizations within the platform.

To define authorized members, you need to click on the '**Manage Access Restriction**' button. This button is visible if you have the '**Manage Authorized Members**' capability.

![containers-manage-access-restriction-button.png](assets%2Fcontainers-manage-access-restriction-button.png)


## Tasks

Tasks are actions to be performed in the context of a Case (Incident Response, Request for Information, Request for Takedown). Usually, a task is assigned to a user, but important tasks may involve more participants.

When clicking on the Tasks tab at the top of the interface, you see the list of all the Tasks you have access to, in respect with your [allowed marking definitions](../administration/users.md). You can then search and filter on some common and specific attributes of the tasks.

Clicking on a Task, you land on its Overview tab. For a Tasks, the following tabs are accessible:

- Overview: as described [here](overview.md#overview-section).
- Content: as described [here](overview.md#content-section).
- Data: as described [here](overview.md#data-section).
- History: as described [here](overview.md#history-section).


## Feedbacks

When a user fill a feedback form from its Profile/Feedback menu, it will then be accessible here.

This feature gives the opportunity to engage with other users of your platform and to respond directly to their concern about it or the Knowledge, without the need of third party software.

![Feedback Overview](assets/feedback-overview.png)

Clicking on a Feedback, you land on its Overview tab. For a Feedback, the following tabs are accessible:

- Overview: as described [here](overview.md#overview-section).
- Content: as described [here](overview.md#content-section). 
- Data: as described [here](overview.md#data-section).
- History: as described [here](overview.md#history-section).
