# JupyterHub as a Service - Getting Started

On-Demand Course-Related JupyterHubs for Research and Teaching.

## What is this and why do we have it?

JupyterHub as a Service (short: **JHaaS**) is a platform that can be used to offer courses and workshops based on Jupyter notebooks. JHaaS not only assumes the role of the pure organizer, but also allows registration of course participants, admission decisions for course leaders and the automatic deployment of user-defined JupyterHubs for each course.

On the one hand, this allows course leaders to use Jupyter notebooks that are perfectly tailored to their course, while at the same time relieving the burden on an institution's administrators, as the deployment and degration of the JupyterHubs is fully automated.

### The organizational process

![Overview](./assets/JHaaS_Diagram.svg)

The illustration shows the general life cycle of a JupyterHub for teaching via the JHaaS platform.

1. The course leader requests a course with their individual requirements.
2. The governance (e.g. the institution's administrators) decides whether the course leader's request is approved.
3. Once the request has been approved, the course leader can provide the course participants with an invitation link. Course participants can apply for the course. The course leader can approve or reject the individual applications.
4. At the beginning of the course, JHaaS automatically deploys the JupyterHub.
5. The JupyterHub can now be used to its full extent for the course. New participants can still apply for the course.
6. After the end of the course, JHaaS will degrade the JupyterHub.

### The technical process

![Architectural Overview](./assets/JHaaS_Arch_Overview.svg)

After a course leader has created a request and governance has approved it, the request is in the `ACCEPTED` state. The JHaaS Dispatcher is a cron job that regularly checks whether requests in this state are ready for deployment, i.e. whether the course is about to start.

If this is the case, the CronJob dispatches another job, the so-called Terraform Worker. This Terraform Worker contains a standardized Terraform configuration for deploying a JupyterHub as well as an individual configuration for the specific course.

The Terraform Worker writes the result, including some important information, to the deployment status (an object on s3). Other important information like the terraform state will be uploaded to a dedicated s3 bucket. The deployment can be succeeded or failed at this point. The dispatcher cronjob reads this result and updates the course in JHaaS accordingly.

If the deployment was successful, the status of the course changes to `DEPLOYED`, otherwise to `FAILED`. In the latter case, course instructors and administrators are informed of the failure by email and manual intervention is required. If the deployment was successful, the JupyterHub can be used to its full extent.

The dispatcher cronjob regularly checks whether requests in the `DEPLOYED` state are ready to be degraded, i.e. when the course is over. If this is the case, it will deploy another Terraform Worker job to degrade the JupyterHub. The terraform worker job again writes its result back to the status. The degration can be succeeded or failed at this point. The dispatcher cronjob reads this result and updates the course in JHaaS accordingly. If the degratiion was successful, the status of the course changes to `DEGRADED`, otherwise to `FAILED`. In the latter case, course instructors and administrators are informed of the failure by email and manual intervention is required. If the degration was successful, the life cycle of the JupyterHub is completed.

### More Resources

We created a Poster as well as an extended abstract as part of the CoRDI2023:

- [The Poster](./CoRDI2023-JHaaS-Poster.pdf)
- [The Extended Abstract](./CoRDI2023-JHaaS-Poster-Extended-Abstract.pdf)

## Get up and Running JHaaS

You should be familiar with Kubernetes, Helm and OpenTofu / Terraform to run JHaaS.

JHaaS consists of many components that interact with each other. In order to make deployment as simple as possible, we have put together an OpenTofu / Terraform configuration that makes deploying JHaaS easier.

### Requirements

To set up a minimal JHaaS setup, you need at least:

- A kubernetes Cluster for the JHaaS Portal and the JupyterHubs (may or may not be the same)
- A FQDN and a wildcard entry on subdomains under your FQDN
- A Mail Server (as there is non included)
- On your local machine: OpenTofu or Terraform

In Order to run JHaaS in production, you should have at least following components as external and backed up services instead of using the bundled versions:

- A Database (Preferable postgres)
- A s3 compatible object storage

### Get the configuration

### Set up your environment

### Deploy JHaaS

## In-Depth: Components of JHaaS

### Portal Backend
### Portal Frontend
### TF Worker

## In-Depth: Configuration of JHaaS

### TF Config
### Authentik Config
### Portal Helm Chart
### Deployment Config